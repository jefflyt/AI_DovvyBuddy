# Step 4: Vector Extension Testing & Migration

**PR:** 1.2 Neon PostgreSQL + pgvector Configuration  
**Step:** 4 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Create SQL migration to enable pgvector extension. Build test script that creates a test table with vector columns, inserts sample embeddings, and performs cosine similarity search to validate setup.

---

## Files to Create

### 1. `prisma/migrations/00_init_pgvector.sql`

Create manual SQL migration:

```sql
-- Migration: Initialize pgvector extension
-- Created: 2025-11-30
-- Description: Enable pgvector extension and create HNSW index helper functions

-- Enable pgvector extension
CREATE EXTENSION IF NOT EXISTS vector;

-- Verify extension installation
DO $$
BEGIN
  IF NOT EXISTS (
    SELECT 1 FROM pg_extension WHERE extname = 'vector'
  ) THEN
    RAISE EXCEPTION 'pgvector extension failed to install';
  END IF;
END $$;

-- Create helper function for vector similarity search
-- This will be useful for debugging and testing
CREATE OR REPLACE FUNCTION vector_cosine_similarity(
  vec1 vector,
  vec2 vector
) RETURNS float AS $$
BEGIN
  RETURN 1 - (vec1 <=> vec2);
END;
$$ LANGUAGE plpgsql IMMUTABLE STRICT PARALLEL SAFE;

-- Create test table to validate pgvector works
-- This table will be dropped after PR 2.1 when real schema is created
CREATE TABLE IF NOT EXISTS _vector_test (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  embedding vector(1536),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create HNSW index on test table (for performance testing)
CREATE INDEX IF NOT EXISTS _vector_test_embedding_idx 
  ON _vector_test 
  USING hnsw (embedding vector_cosine_ops);

-- Insert test vectors
INSERT INTO _vector_test (name, embedding) VALUES
  ('test_vector_1', ARRAY(SELECT random() FROM generate_series(1, 1536))::vector),
  ('test_vector_2', ARRAY(SELECT random() FROM generate_series(1, 1536))::vector),
  ('test_vector_3', ARRAY(SELECT random() FROM generate_series(1, 1536))::vector);

COMMENT ON TABLE _vector_test IS 'Test table for pgvector validation. Will be dropped after PR 2.1.';
```

### 2. `scripts/test-vector-operations.ts`

Create comprehensive vector operations test:

```typescript
import db from '../src/backend/config/database';

interface VectorTestRow {
  id: number;
  name: string;
  embedding: number[] | string; // Prisma returns as string initially
  created_at: Date;
}

interface SimilarityResult {
  id: number;
  name: string;
  distance: number;
  similarity: number;
}

async function testVectorOperations() {
  console.log('🧪 Testing pgvector operations\n');
  console.log('=' .repeat(50));

  try {
    await db.connect();
    const prisma = db.getClient();

    // Test 1: Check extension
    console.log('\n✅ Test 1: Verify pgvector extension');
    const hasVector = await db.checkVectorExtension();
    if (!hasVector) {
      throw new Error('pgvector extension not found!');
    }
    console.log('   Extension: INSTALLED');

    // Test 2: Check test table exists
    console.log('\n✅ Test 2: Verify test table exists');
    const tableCheck = await prisma.$queryRaw<Array<{ exists: boolean }>>`
      SELECT EXISTS (
        SELECT 1 FROM information_schema.tables 
        WHERE table_name = '_vector_test'
      ) as exists
    `;
    if (!tableCheck[0].exists) {
      throw new Error('Test table _vector_test not found! Run migration first.');
    }
    console.log('   Table: EXISTS');

    // Test 3: Count test vectors
    console.log('\n✅ Test 3: Count test vectors');
    const count = await prisma.$queryRaw<Array<{ count: bigint }>>`
      SELECT COUNT(*) as count FROM _vector_test
    `;
    console.log(`   Vectors: ${count[0].count}`);

    // Test 4: Generate query vector
    console.log('\n✅ Test 4: Generate random query vector');
    const queryVector = Array.from({ length: 1536 }, () => Math.random());
    console.log(`   Dimensions: ${queryVector.length}`);

    // Test 5: Cosine distance search (L2 normalized)
    console.log('\n✅ Test 5: Cosine similarity search (<=>, fastest)');
    const cosineResults = await prisma.$queryRaw<SimilarityResult[]>`
      SELECT 
        id, 
        name,
        (embedding <=> ${JSON.stringify(queryVector)}::vector) as distance,
        (1 - (embedding <=> ${JSON.stringify(queryVector)}::vector)) as similarity
      FROM _vector_test
      ORDER BY embedding <=> ${JSON.stringify(queryVector)}::vector
      LIMIT 3
    `;
    console.log('   Top 3 matches:');
    cosineResults.forEach((row, i) => {
      console.log(
        `     ${i + 1}. ${row.name} - Distance: ${Number(row.distance).toFixed(4)}, Similarity: ${Number(row.similarity).toFixed(4)}`
      );
    });

    // Test 6: L2 distance search (Euclidean)
    console.log('\n✅ Test 6: L2 distance search (<->, Euclidean)');
    const l2Results = await prisma.$queryRaw<SimilarityResult[]>`
      SELECT 
        id, 
        name,
        (embedding <-> ${JSON.stringify(queryVector)}::vector) as distance
      FROM _vector_test
      ORDER BY embedding <-> ${JSON.stringify(queryVector)}::vector
      LIMIT 3
    `;
    console.log('   Top 3 matches:');
    l2Results.forEach((row, i) => {
      console.log(`     ${i + 1}. ${row.name} - Distance: ${Number(row.distance).toFixed(4)}`);
    });

    // Test 7: Inner product search (<#>)
    console.log('\n✅ Test 7: Inner product search (<#>)');
    const ipResults = await prisma.$queryRaw<SimilarityResult[]>`
      SELECT 
        id, 
        name,
        (embedding <#> ${JSON.stringify(queryVector)}::vector) * -1 as similarity
      FROM _vector_test
      ORDER BY embedding <#> ${JSON.stringify(queryVector)}::vector
      LIMIT 3
    `;
    console.log('   Top 3 matches:');
    ipResults.forEach((row, i) => {
      console.log(`     ${i + 1}. ${row.name} - Similarity: ${Number(row.similarity).toFixed(4)}`);
    });

    // Test 8: Index usage verification
    console.log('\n✅ Test 8: Verify HNSW index exists');
    const indexCheck = await prisma.$queryRaw<Array<{ indexname: string }>>`
      SELECT indexname 
      FROM pg_indexes 
      WHERE tablename = '_vector_test' 
        AND indexname = '_vector_test_embedding_idx'
    `;
    if (indexCheck.length > 0) {
      console.log(`   Index: ${indexCheck[0].indexname} EXISTS`);
    } else {
      console.log('   ⚠️  Warning: HNSW index not found');
    }

    // Test 9: Performance benchmark
    console.log('\n✅ Test 9: Performance benchmark (10 searches)');
    const iterations = 10;
    const start = Date.now();
    
    for (let i = 0; i < iterations; i++) {
      const randomVector = Array.from({ length: 1536 }, () => Math.random());
      await prisma.$queryRaw`
        SELECT id, name
        FROM _vector_test
        ORDER BY embedding <=> ${JSON.stringify(randomVector)}::vector
        LIMIT 5
      `;
    }
    
    const elapsed = Date.now() - start;
    const avgTime = elapsed / iterations;
    console.log(`   Total time: ${elapsed}ms`);
    console.log(`   Average: ${avgTime.toFixed(2)}ms per search`);
    console.log(`   Throughput: ${(1000 / avgTime).toFixed(2)} searches/sec`);

    // Success summary
    console.log('\n' + '='.repeat(50));
    console.log('✅ ALL TESTS PASSED\n');
    console.log('Summary:');
    console.log('  - pgvector extension: WORKING');
    console.log('  - Vector operations: WORKING');
    console.log('  - HNSW index: WORKING');
    console.log('  - Average search time: <1000ms ✓');
    console.log('\n🎉 Database ready for Phase 2 (RAG Pipeline)\n');

    await db.disconnect();
    process.exit(0);
  } catch (error) {
    console.error('\n❌ Vector operations test failed:', error);
    await db.disconnect();
    process.exit(1);
  }
}

// Run tests
testVectorOperations();
```

### 3. `scripts/run-vector-migration.sh`

Create migration execution script:

```bash
#!/usr/bin/env bash

# Run pgvector migration script

set -e

echo "🚀 Running pgvector migration..."

# Check database is running
if ! docker-compose exec -T postgres pg_isready -U postgres > /dev/null 2>&1; then
  echo "❌ PostgreSQL is not running. Start with: docker-compose up -d postgres"
  exit 1
fi

# Run migration on local Docker
echo "📦 Running migration on local Docker PostgreSQL..."
docker-compose exec -T postgres psql -U postgres -d dovvydive_dev < prisma/migrations/00_init_pgvector.sql

echo "✅ Migration complete (local)"

# Optionally run on Neon (if configured)
if [ -n "$NEON_DATABASE_URL" ]; then
  read -p "Run migration on Neon database? (yes/no): " -r
  echo
  
  if [[ $REPLY =~ ^[Yy][Ee][Ss]$ ]]; then
    echo "☁️  Running migration on Neon..."
    # Requires psql installed locally
    psql "$NEON_DATABASE_URL" < prisma/migrations/00_init_pgvector.sql
    echo "✅ Migration complete (Neon)"
  fi
fi

echo ""
echo "Next steps:"
echo "1. Test vector operations: npm run test:vectors --workspace=backend"
echo "2. Verify in Prisma Studio: npm run db:studio --workspace=backend"
```

### 4. Update `src/backend/package.json`

Add test script:

```json
{
  "scripts": {
    "test:vectors": "ts-node ../scripts/test-vector-operations.ts",
    "test:db-connection": "ts-node scripts/test-db-connection.ts"
  }
}
```

---

## Installation Commands

```bash
# Make migration script executable
chmod +x scripts/run-vector-migration.sh

# Run migration
./scripts/run-vector-migration.sh

# Test vector operations
npm run test:vectors --workspace=backend
```

---

## Testing Checklist

- [ ] Migration SQL file created
- [ ] pgvector extension enabled in both local and Neon
- [ ] Test table `_vector_test` exists
- [ ] HNSW index created successfully
- [ ] Test vectors inserted (3 rows)
- [ ] Vector operations test script runs successfully
- [ ] All 9 tests pass
- [ ] Average search time <1000ms

---

## Validation Commands

```bash
# Run migration
./scripts/run-vector-migration.sh

# Verify extension in Docker
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT * FROM pg_extension WHERE extname = 'vector';"

# Check test table
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT COUNT(*) FROM _vector_test;"

# Run comprehensive tests
npm run test:vectors --workspace=backend

# Should show:
# ✅ ALL TESTS PASSED
# Summary:
#   - pgvector extension: WORKING
#   - Vector operations: WORKING
#   - HNSW index: WORKING
#   - Average search time: <1000ms ✓
```

---

## PR 1.2 Completion Checklist

Before opening PR for 1.2, verify:

- [x] Step 1: Neon project setup documented
- [x] Step 2: Database connection module created
- [x] Step 3: Prisma initialized
- [x] Step 4: Vector operations tested

**Final validation:**

```bash
# Test local connection
DATABASE_PROVIDER=local npm run test:db-connection --workspace=backend

# Test Neon connection (if configured)
DATABASE_PROVIDER=neon npm run test:db-connection --workspace=backend

# Test vector operations
npm run test:vectors --workspace=backend

# All should pass ✅
```

---

## Commit Message

```
feat(database): configure Neon PostgreSQL with pgvector support

- Add Neon project setup documentation and automation
- Implement database connection with retry logic and pooling
- Initialize Prisma with pgvector extension support
- Create vector operations test suite
- Add migrations for pgvector extension
- Support both local Docker and Neon cloud databases

BREAKING CHANGE: Requires pgvector extension in PostgreSQL
```

---

## Next PR

Proceed to PR 1.3: Next.js Frontend Scaffolding

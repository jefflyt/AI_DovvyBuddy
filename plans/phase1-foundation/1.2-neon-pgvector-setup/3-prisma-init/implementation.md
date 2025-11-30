# Step 3: Prisma Initial Setup

**PR:** 1.2 Neon PostgreSQL + pgvector Configuration  
**Step:** 3 of 4  
**Estimated Time:** 1 hour

---

## Goal

Initialize Prisma ORM with basic configuration pointing to PostgreSQL. Set up Prisma Client generation. Create initial schema file with datasource/generator blocks (no models yet - those come in PR 2.1).

---

## Files to Create

### 1. `prisma/schema.prisma`

Create initial Prisma schema:

```prisma
// Prisma schema file for DovvyDive
// Complete schema will be added in PR 2.1

generator client {
  provider = "prisma-client-js"
  previewFeatures = ["postgresqlExtensions"]
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
  extensions = [vector]
}

// Models will be added in PR 2.1
// This file establishes the connection and enables pgvector
```

### 2. `prisma/.gitignore`

```
# Prisma
*.db
*.db-journal
migrations/
!migrations/.gitkeep
dev.db*
```

### 3. `prisma/migrations/.gitkeep`

Create empty file to preserve directory structure:

```
# This file ensures the migrations directory is tracked by Git
```

### 4. Update `src/backend/package.json`

Add Prisma scripts (already included in Step 2, but verify):

```json
{
  "scripts": {
    "prisma:generate": "prisma generate",
    "prisma:studio": "prisma studio",
    "db:migrate": "prisma migrate dev",
    "db:migrate:deploy": "prisma migrate deploy",
    "db:push": "prisma db push",
    "db:seed": "ts-node prisma/seed.ts",
    "db:reset": "prisma migrate reset --force"
  }
}
```

### 5. Create test connection script

Create `src/backend/scripts/test-db-connection.ts`:

```typescript
import db from '../config/database';
import env from '../config/env';

async function testConnection() {
  console.log('🧪 Testing database connection...\n');

  console.log('📋 Configuration:');
  console.log(`  Provider: ${env.DATABASE_PROVIDER}`);
  console.log(`  Environment: ${env.NODE_ENV}\n`);

  try {
    // Connect
    await db.connect();

    // Health check
    const health = await db.healthCheck();
    console.log('💊 Health Check:', health);

    // Check pgvector
    const hasVector = await db.checkVectorExtension();
    console.log(`🧩 pgvector extension: ${hasVector ? '✅ Available' : '❌ Not found'}\n`);

    if (!hasVector) {
      console.log('⚠️  pgvector extension not enabled!');
      console.log('Run this SQL in your database:');
      console.log('  CREATE EXTENSION IF NOT EXISTS vector;\n');
    }

    // Disconnect
    await db.disconnect();

    console.log('✅ Connection test successful!');
    process.exit(0);
  } catch (error) {
    console.error('❌ Connection test failed:', error);
    process.exit(1);
  }
}

testConnection();
```

---

## Installation Commands

```bash
# Generate Prisma Client
cd src/backend
npx prisma generate

# Push schema to database (creates tables if any models exist)
npx prisma db push

# Open Prisma Studio (database GUI)
npx prisma studio
```

---

## Testing Checklist

- [ ] Prisma schema file created with datasource and generator
- [ ] pgvector extension configured in schema
- [ ] Prisma Client generates successfully
- [ ] Connection test script runs
- [ ] Can access Prisma Studio
- [ ] migrations directory structure in place

---

## Validation Commands

```bash
# Generate Prisma Client
npm run prisma:generate --workspace=backend

# Validate schema
cd src/backend && npx prisma validate

# Format schema
cd src/backend && npx prisma format

# Test database connection
cd src/backend && npx ts-node scripts/test-db-connection.ts

# Should output:
# ✅ Database connected successfully
# 💊 Health Check: { status: 'healthy', ... }
# 🧩 pgvector extension: ✅ Available
```

---

## Common Issues

**Issue:** Prisma Client generation fails  
**Solution:** Ensure DATABASE_URL is set in .env, run `npm install` first

**Issue:** "Extension vector not found"  
**Solution:** Run SQL `CREATE EXTENSION vector;` in your database

**Issue:** Connection refused  
**Solution:** Ensure Docker PostgreSQL is running: `docker-compose ps`

---

## Next Step

Proceed to Step 4: Vector Extension Testing & Migration

# Step 3: Docker Compose Configuration

**PR:** 1.1 Repository Setup & Docker Environment  
**Step:** 3 of 4  
**Estimated Time:** 2-3 hours

---

## Goal

Create Docker Compose configuration with three services: PostgreSQL (using `ankane/pgvector:latest`), backend (Node.js 20+), and frontend (Next.js dev server). Include health checks and proper networking.

---

## Files to Create

### 1. `docker-compose.yml`

Create at project root:

```yaml
version: '3.8'

services:
  # PostgreSQL with pgvector extension
  postgres:
    image: ankane/pgvector:latest
    container_name: dovvydive_postgres
    restart: unless-stopped
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: dovvydive_dev
    ports:
      - '5432:5432'
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./scripts/init-db.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ['CMD-SHELL', 'pg_isready -U postgres']
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - dovvydive_network

  # Backend API (Express.js)
  backend:
    build:
      context: .
      dockerfile: Dockerfile.backend
    container_name: dovvydive_backend
    restart: unless-stopped
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://postgres:password@postgres:5432/dovvydive_dev?schema=public
      BACKEND_PORT: 4000
    ports:
      - '4000:4000'
    volumes:
      - ./src/backend:/app/src/backend
      - ./src/shared:/app/src/shared
      - ./prisma:/app/prisma
      - /app/node_modules
      - /app/src/backend/node_modules
    depends_on:
      postgres:
        condition: service_healthy
    command: npm run dev --workspace=backend
    networks:
      - dovvydive_network
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:4000/api/v1/health']
      interval: 30s
      timeout: 10s
      retries: 3

  # Frontend (Next.js)
  frontend:
    build:
      context: .
      dockerfile: Dockerfile.frontend
    container_name: dovvydive_frontend
    restart: unless-stopped
    environment:
      NODE_ENV: development
      NEXT_PUBLIC_API_URL: http://localhost:4000/api/v1
      FRONTEND_PORT: 3000
    ports:
      - '3000:3000'
    volumes:
      - ./src/frontend:/app/src/frontend
      - ./src/shared:/app/src/shared
      - /app/node_modules
      - /app/src/frontend/node_modules
      - /app/src/frontend/.next
    depends_on:
      - backend
    command: npm run dev --workspace=frontend
    networks:
      - dovvydive_network

networks:
  dovvydive_network:
    driver: bridge

volumes:
  postgres_data:
```

### 2. `docker-compose.dev.yml`

Create development overrides:

```yaml
version: '3.8'

services:
  backend:
    environment:
      LOG_LEVEL: debug
    volumes:
      - ./src/backend:/app/src/backend:delegated
      - ./src/shared:/app/src/shared:delegated

  frontend:
    environment:
      NEXT_TELEMETRY_DISABLED: 1
    volumes:
      - ./src/frontend:/app/src/frontend:delegated
      - ./src/shared:/app/src/shared:delegated
```

### 3. `Dockerfile.backend`

Create backend Dockerfile:

```dockerfile
# Backend Dockerfile
FROM node:20.10.0-alpine AS base

# Install dependencies only when needed
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

# Copy package files
COPY package.json package-lock.json ./
COPY src/backend/package.json ./src/backend/
COPY src/shared/package.json ./src/shared/

# Install dependencies
RUN npm ci

# Development image
FROM base AS development
WORKDIR /app

# Copy dependencies from deps stage
COPY --from=deps /app/node_modules ./node_modules
COPY --from=deps /app/src/backend/node_modules ./src/backend/node_modules

# Copy application code
COPY . .

# Expose port
EXPOSE 4000

# Set working directory to backend
WORKDIR /app

# Start development server
CMD ["npm", "run", "dev", "--workspace=backend"]
```

### 4. `Dockerfile.frontend`

Create frontend Dockerfile:

```dockerfile
# Frontend Dockerfile
FROM node:20.10.0-alpine AS base

# Install dependencies only when needed
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

# Copy package files
COPY package.json package-lock.json ./
COPY src/frontend/package.json ./src/frontend/
COPY src/shared/package.json ./src/shared/

# Install dependencies
RUN npm ci

# Development image
FROM base AS development
WORKDIR /app

# Copy dependencies from deps stage
COPY --from=deps /app/node_modules ./node_modules
COPY --from=deps /app/src/frontend/node_modules ./src/frontend/node_modules

# Copy application code
COPY . .

# Expose port
EXPOSE 3000

# Set working directory
WORKDIR /app

# Start development server
CMD ["npm", "run", "dev", "--workspace=frontend"]
```

### 5. `.dockerignore`

Create Docker ignore file:

```
node_modules
npm-debug.log
.next
.git
.gitignore
README.md
.env
.env.local
dist
build
coverage
.vscode
.idea
*.swp
*.swo
.DS_Store
Thumbs.db
```

### 6. `scripts/init-db.sql`

Create database initialization script:

```sql
-- Initialize DovvyDive Database with pgvector extension

-- Enable pgvector extension
CREATE EXTENSION IF NOT EXISTS vector;

-- Verify extension
SELECT * FROM pg_extension WHERE extname = 'vector';

-- Create initial test table (will be replaced by Prisma migrations)
-- This confirms pgvector is working
CREATE TABLE IF NOT EXISTS _test_vectors (
  id SERIAL PRIMARY KEY,
  embedding vector(1536),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert test vector
INSERT INTO _test_vectors (embedding) 
VALUES (ARRAY(SELECT random() FROM generate_series(1, 1536))::vector);

-- Test vector similarity search
-- SELECT id, embedding <-> ARRAY(SELECT random() FROM generate_series(1, 1536))::vector AS distance
-- FROM _test_vectors
-- ORDER BY distance
-- LIMIT 1;

COMMENT ON TABLE _test_vectors IS 'Test table to verify pgvector extension is working. Will be dropped after Prisma migrations.';
```

---

## Installation Commands

Run these commands after creating the files:

```bash
# Build Docker images
docker-compose build

# Start all services
docker-compose up -d

# Check service status
docker-compose ps

# View logs
docker-compose logs -f

# Verify PostgreSQL pgvector extension
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT * FROM pg_extension WHERE extname = 'vector';"
```

---

## Testing Checklist

- [ ] `docker-compose build` completes successfully
- [ ] `docker-compose up` starts all 3 services
- [ ] PostgreSQL container is healthy
- [ ] pgvector extension enabled (check via SQL query)
- [ ] Backend container starts (even if showing errors about missing files - that's expected)
- [ ] Frontend container starts (even if showing errors about missing files - that's expected)
- [ ] Network connectivity between services works
- [ ] Volumes mounted correctly

---

## Validation Commands

```bash
# Check all containers running
docker-compose ps
# Should show postgres, backend, frontend all "Up"

# Test PostgreSQL connection
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT version();"

# Verify pgvector extension
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT * FROM pg_extension WHERE extname = 'vector';"

# Check test vector table
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT COUNT(*) FROM _test_vectors;"
# Should show 1 row

# View backend logs
docker-compose logs backend

# View frontend logs
docker-compose logs frontend

# Test health check (will fail until backend is implemented)
curl http://localhost:4000/api/v1/health
```

---

## Common Issues

**Issue:** Port 5432 already in use  
**Solution:** Stop local PostgreSQL service or change port in docker-compose.yml

**Issue:** Permission denied on volumes  
**Solution:** Ensure Docker has file sharing permissions for the project directory

**Issue:** Backend/Frontend containers exit immediately  
**Solution:** Expected at this stage - workspace packages don't exist yet. Will be fixed in PR 1.3 and 1.4.

**Issue:** pgvector extension not found  
**Solution:** Ensure using `ankane/pgvector:latest` image, not standard `postgres` image

---

## Next Step

Proceed to Step 4: Development Scripts & Documentation

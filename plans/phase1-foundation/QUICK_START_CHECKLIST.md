# Phase 1 Quick-Start Checklist

**Use this checklist to implement Phase 1 step-by-step**

---

## Prerequisites ✅

Before starting, ensure you have:

- [ ] Node.js 20.10.0+ installed (`node --version`)
- [ ] Docker Desktop installed and running
- [ ] Git installed
- [ ] Text editor/IDE (VS Code recommended)
- [ ] Terminal access

---

## PR 1.1: Repository Setup & Docker Environment

### Step 1: Monorepo Structure & Git Setup (1-2 hours)

```bash
# Create directory structure
mkdir -p src/backend/{config,routes,middleware,services,controllers,utils,types}
mkdir -p src/frontend/{app,components,hooks,lib,styles,types,utils}
mkdir -p src/shared/{types,utils,constants}
mkdir -p src/tests/{unit,integration,e2e}
mkdir -p docs/{setup,api,architecture,testing}
mkdir -p scripts data/{dive-sites,templates} prisma/migrations
```

**Files to create:**
- [ ] `.gitignore` (copy from implementation.md)
- [ ] `.env.example` (copy from implementation.md)
- [ ] `README.md` (copy from implementation.md)
- [ ] `.nvmrc` (contains: `20.10.0`)
- [ ] `.editorconfig` (copy from implementation.md)

**Verify:**
```bash
tree -L 2 src/
cat .env.example | grep DATABASE_URL
```

---

### Step 2: Root Package Configuration (1-2 hours)

**Files to create:**
- [ ] `package.json` (root workspace config)
- [ ] `tsconfig.json` (base TypeScript config)
- [ ] `.eslintrc.json` (ESLint config)
- [ ] `.prettierrc` (Prettier config)
- [ ] `.prettierignore`
- [ ] `playwright.config.ts`

**Install:**
```bash
npm install
```

**Verify:**
```bash
node --version  # Should be 20.10.0+
npm --version   # Should be 10.0.0+
npx eslint --version
npx prettier --version
```

---

### Step 3: Docker Compose Configuration (2-3 hours)

**Files to create:**
- [ ] `docker-compose.yml`
- [ ] `docker-compose.dev.yml`
- [ ] `Dockerfile.backend`
- [ ] `Dockerfile.frontend`
- [ ] `.dockerignore`
- [ ] `scripts/init-db.sql`

**Build and start:**
```bash
docker-compose build
docker-compose up -d
```

**Verify:**
```bash
docker-compose ps  # All services should be "Up"
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT * FROM pg_extension WHERE extname = 'vector';"
# Should show vector extension
```

---

### Step 4: Development Scripts & Documentation (1-2 hours)

**Files to create:**
- [ ] `scripts/dev.sh`
- [ ] `scripts/test.sh`
- [ ] `scripts/db-reset.sh`
- [ ] `scripts/clean.sh`
- [ ] `docs/setup/getting-started.md`

**Make executable:**
```bash
chmod +x scripts/*.sh
```

**Test:**
```bash
./scripts/dev.sh
# Should start all services
```

**PR 1.1 Complete!** ✅
- [ ] All 4 steps done
- [ ] Docker services running
- [ ] Scripts work
- [ ] Ready to commit

```bash
git checkout -b 1.1-repo-setup-docker
git add .
git commit -m "feat(infra): initialize monorepo with Docker Compose environment"
git push origin 1.1-repo-setup-docker
# Open PR on GitHub
```

---

## PR 1.2: Neon PostgreSQL + pgvector Configuration

### Step 1: Neon Project Setup (1 hour)

**Files to create:**
- [ ] `docs/setup/neon-setup.md`
- [ ] `scripts/create-neon-project.sh`

**Setup Neon:**
```bash
# Option 1: Manual (follow docs/setup/neon-setup.md)
# Option 2: Automated
chmod +x scripts/create-neon-project.sh
./scripts/create-neon-project.sh
```

**Verify:**
```bash
cat .env.neon | grep NEON_DATABASE_URL
# Should have connection string
```

---

### Step 2: Database Connection Configuration (2 hours)

**Files to create:**
- [ ] `src/backend/package.json`
- [ ] `src/backend/tsconfig.json`
- [ ] `src/backend/config/env.ts`
- [ ] `src/backend/config/database.ts`
- [ ] `src/backend/utils/dbHealth.ts`

**Install:**
```bash
npm install --workspace=backend
```

**Verify:**
```bash
npm ls --workspace=backend
npm run typecheck --workspace=backend
```

---

### Step 3: Prisma Initial Setup (1 hour)

**Files to create:**
- [ ] `prisma/schema.prisma`
- [ ] `prisma/.gitignore`
- [ ] `prisma/migrations/.gitkeep`
- [ ] `src/backend/scripts/test-db-connection.ts`

**Generate Prisma Client:**
```bash
npm run prisma:generate --workspace=backend
```

**Test connection:**
```bash
cd src/backend && npx ts-node scripts/test-db-connection.ts
# Should show: ✅ Database connected successfully
```

---

### Step 4: Vector Extension Testing (1-2 hours)

**Files to create:**
- [ ] `prisma/migrations/00_init_pgvector.sql`
- [ ] `scripts/test-vector-operations.ts`
- [ ] `scripts/run-vector-migration.sh`

**Run migration:**
```bash
chmod +x scripts/run-vector-migration.sh
./scripts/run-vector-migration.sh
```

**Test vectors:**
```bash
npm run test:vectors --workspace=backend
# Should show: ✅ ALL TESTS PASSED
```

**PR 1.2 Complete!** ✅
- [ ] All 4 steps done
- [ ] Neon configured
- [ ] Prisma working
- [ ] Vector operations tested
- [ ] Ready to commit

```bash
git checkout -b 1.2-neon-pgvector-setup
git add .
git commit -m "feat(database): configure Neon PostgreSQL with pgvector support"
git push origin 1.2-neon-pgvector-setup
# Open PR on GitHub
```

---

## PR 1.3: Next.js Frontend Scaffolding

### Step 1: Next.js Installation (1-2 hours)

**Files to create:**
- [ ] `src/frontend/package.json`
- [ ] `src/frontend/tsconfig.json`
- [ ] `src/frontend/next.config.js`
- [ ] `src/frontend/.eslintrc.json`
- [ ] `src/frontend/next-env.d.ts`
- [ ] `src/frontend/.gitignore`

**Install:**
```bash
npm install --workspace=frontend
```

**Verify:**
```bash
cd src/frontend && npx next --version
# Should show 14.0.4+
```

---

### Step 2: Tailwind CSS & Theme (1 hour)

**Files to create:**
- [ ] `src/frontend/tailwind.config.js`
- [ ] `src/frontend/postcss.config.js`
- [ ] `src/frontend/app/globals.css`
- [ ] `src/frontend/lib/utils.ts`
- [ ] `src/frontend/styles/theme.ts`

**Install:**
```bash
npm install --workspace=frontend tailwindcss autoprefixer postcss @tailwindcss/forms @tailwindcss/typography clsx tailwind-merge
```

---

### Step 3: App Router & Layout (SEE NEXT FILE - abbreviated here)

**Files to create:**
- [ ] `src/frontend/app/layout.tsx`
- [ ] `src/frontend/app/page.tsx`
- [ ] `src/frontend/app/chat/page.tsx`
- [ ] `src/frontend/components/Header.tsx`
- [ ] `src/frontend/components/Footer.tsx`

**Test:**
```bash
npm run dev --workspace=frontend
# Visit http://localhost:3000
```

---

### Step 4: Shared Components (SEE NEXT FILE - abbreviated here)

**Files to create:**
- [ ] `src/frontend/components/Button.tsx`
- [ ] `src/frontend/components/Card.tsx`
- [ ] `src/frontend/components/LoadingSpinner.tsx`
- [ ] `src/frontend/lib/constants.ts`

**PR 1.3 Complete!** ✅

---

## PR 1.4 & 1.5: Express Backend & i18n (abbreviated)

Follow implementation files when ready.

---

## Quick Commands Reference

```bash
# Start everything
./scripts/dev.sh

# Stop everything
docker-compose down

# Reset database
./scripts/db-reset.sh

# Clean all
./scripts/clean.sh

# Test vectors
npm run test:vectors --workspace=backend

# Run frontend
npm run dev --workspace=frontend

# Run backend
npm run dev --workspace=backend

# Lint all
npm run lint

# Type check all
npm run typecheck
```

---

## Completion Checklist

Phase 1 is complete when:

- [ ] PR 1.1 merged (Docker environment working)
- [ ] PR 1.2 merged (Database configured and tested)
- [ ] PR 1.3 merged (Frontend running)
- [ ] PR 1.4 merged (Backend API running)
- [ ] PR 1.5 merged (i18n working)
- [ ] `docker-compose up` starts all services
- [ ] Frontend accessible at localhost:3000
- [ ] Backend accessible at localhost:4000
- [ ] Language toggle works
- [ ] New developer can onboard in <30 minutes

---

**Estimated Total Time:** 10-14 days (2 weeks)

**Current Progress:** Track in IMPLEMENTATION_STATUS.md

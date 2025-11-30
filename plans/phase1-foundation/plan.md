# Phase 1: Foundation & Infrastructure Setup

**Timeline:** Week 1-2  
**PRs:** 5 (PR 1.1 through PR 1.5)  
**Goal:** Establish complete development environment, database, and project scaffolding with bilingual support foundation

---

## Overview

Phase 1 creates the foundational infrastructure for DovvyDive MVP. All subsequent phases depend on this foundation being solid and complete. Each PR is independently testable and provides immediate value to the development workflow.

**Key Decisions:**
- Monorepo structure for frontend/backend coordination
- Docker Compose for consistent local development
- Dual database configuration (Docker local + Neon cloud)
- Next.js 14 App Router for SSR capabilities
- Express.js with TypeScript for type-safe backend
- next-i18next for bilingual UI support

---

## PR Breakdown

### PR 1.1: Repository Setup & Docker Environment
**Branch:** `1.1-repo-setup-docker`  
**Complexity:** COMPLEX (4 steps)  
**Estimated Time:** 2-3 days  

Enable developers to spin up full stack locally with one command (`docker-compose up`).

[See detailed implementation: `/plans/phase1-foundation/1.1-repo-setup-docker/`]

---

### PR 1.2: Neon PostgreSQL + pgvector Configuration
**Branch:** `1.2-neon-pgvector-setup`  
**Complexity:** COMPLEX (4 steps)  
**Estimated Time:** 2-3 days  
**Depends on:** PR 1.1

Establish production database with vector search capabilities on Neon serverless PostgreSQL.

[See detailed implementation: `/plans/phase1-foundation/1.2-neon-pgvector-setup/`]

---

### PR 1.3: Next.js Frontend Scaffolding
**Branch:** `1.3-nextjs-scaffolding`  
**Complexity:** COMPLEX (4 steps)  
**Estimated Time:** 2-3 days  
**Depends on:** PR 1.1

Create frontend foundation with SSR support, responsive layout, and dive-themed styling.

[See detailed implementation: `/plans/phase1-foundation/1.3-nextjs-scaffolding/`]

---

### PR 1.4: Express Backend Scaffolding
**Branch:** `1.4-express-scaffolding`  
**Complexity:** COMPLEX (4 steps)  
**Estimated Time:** 2-3 days  
**Depends on:** PR 1.2

Create API foundation with error handling, request validation, and modular routing.

[See detailed implementation: `/plans/phase1-foundation/1.4-express-scaffolding/`]

---

### PR 1.5: i18n Framework Setup (next-i18next)
**Branch:** `1.5-i18n-setup`  
**Complexity:** COMPLEX (4 steps)  
**Estimated Time:** 2 days  
**Depends on:** PR 1.3

Enable bilingual UI with manual language toggle and localStorage persistence.

[See detailed implementation: `/plans/phase1-foundation/1.5-i18n-setup/`]

---

## Execution Order

### Week 1
**Day 1-3:** PR 1.1 (Repository Setup & Docker Environment)  
**Day 4-6:** PR 1.2 (Neon PostgreSQL + pgvector Configuration)

### Week 2
**Day 1-3:** PR 1.3 (Next.js Frontend Scaffolding) + PR 1.4 (Express Backend Scaffolding) *[Parallel]*  
**Day 4-5:** PR 1.5 (i18n Framework Setup)

---

## Phase Completion Criteria

Phase 1 is complete when:
- ✅ `docker-compose up` starts all services successfully
- ✅ PostgreSQL with pgvector running locally and Neon configured
- ✅ Frontend accessible at `http://localhost:3000`
- ✅ Backend accessible at `http://localhost:4000`
- ✅ Health check endpoint returns database status
- ✅ Language toggle switches UI between English and Chinese
- ✅ All environment variables templated in `.env.example`
- ✅ Setup documentation allows new developer onboarding in <30 minutes

---

## Common Issues & Solutions

### Docker Containers Not Starting
- **Issue:** Port conflicts (5432, 3000, 4000 already in use)
- **Solution:** Stop conflicting services or modify docker-compose port mappings

### Prisma Client Generation Fails
- **Issue:** DATABASE_URL not set or pgvector extension not enabled
- **Solution:** Verify .env file exists, run migration to enable pgvector

### Next.js Build Errors
- **Issue:** Node version mismatch (requires 20+)
- **Solution:** Use nvm to install Node 20.10.0, update .nvmrc

### Language Toggle Not Persisting
- **Issue:** localStorage blocked by browser privacy settings
- **Solution:** Test in normal mode (not incognito), check localStorage permissions

---

## Next Steps After Phase 1

Once Phase 1 is complete:
1. Move to Phase 2: Data Layer & RAG Pipeline (PR 2.1-2.4)
2. Begin manual dive site content curation in parallel
3. Schedule team walkthrough of development environment setup

# Step 4: Development Scripts & Documentation

**PR:** 1.1 Repository Setup & Docker Environment  
**Step:** 4 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Create convenience scripts for common development tasks and comprehensive documentation for onboarding new developers.

---

## Files to Create

### 1. `scripts/dev.sh`

Create development startup script:

```bash
#!/usr/bin/env bash

# Development startup script for DovvyDive

set -e

echo "🚀 Starting DovvyDive development environment..."

# Check if .env exists
if [ ! -f .env ]; then
  echo "⚠️  .env file not found. Copying from .env.example..."
  cp .env.example .env
  echo "✅ .env created. Please update with your actual API keys."
fi

# Check Docker is running
if ! docker info > /dev/null 2>&1; then
  echo "❌ Docker is not running. Please start Docker Desktop."
  exit 1
fi

# Start Docker Compose
echo "📦 Starting Docker containers..."
docker-compose up -d

# Wait for PostgreSQL to be healthy
echo "⏳ Waiting for PostgreSQL to be ready..."
timeout 60 bash -c 'until docker-compose exec -T postgres pg_isready -U postgres > /dev/null 2>&1; do sleep 2; done'

echo "✅ PostgreSQL is ready!"

# Show service status
echo ""
echo "📊 Service Status:"
docker-compose ps

# Show logs
echo ""
echo "📝 Streaming logs (Ctrl+C to exit)..."
docker-compose logs -f
```

### 2. `scripts/test.sh`

Create test execution script:

```bash
#!/usr/bin/env bash

# Test execution script for DovvyDive

set -e

echo "🧪 Running DovvyDive tests..."

# Parse arguments
TEST_TYPE=${1:-all}

case $TEST_TYPE in
  unit)
    echo "Running unit tests..."
    npm run test:unit
    ;;
  integration)
    echo "Running integration tests..."
    npm run test:integration
    ;;
  e2e)
    echo "Running E2E tests..."
    # Ensure services are running
    docker-compose up -d
    sleep 5
    npm run test:e2e
    ;;
  all)
    echo "Running all tests..."
    npm run test:unit
    npm run test:integration
    # docker-compose up -d
    # sleep 5
    # npm run test:e2e
    ;;
  *)
    echo "Usage: ./scripts/test.sh [unit|integration|e2e|all]"
    exit 1
    ;;
esac

echo "✅ Tests completed!"
```

### 3. `scripts/db-reset.sh`

Create database reset script:

```bash
#!/usr/bin/env bash

# Database reset script for DovvyDive

set -e

echo "⚠️  WARNING: This will delete all data in the database!"
read -p "Are you sure you want to continue? (yes/no): " -r
echo

if [[ ! $REPLY =~ ^[Yy][Ee][Ss]$ ]]; then
  echo "❌ Database reset cancelled."
  exit 1
fi

echo "🗑️  Resetting database..."

# Stop backend to release connections
docker-compose stop backend

# Drop and recreate database
docker-compose exec postgres psql -U postgres -c "DROP DATABASE IF EXISTS dovvydive_dev;"
docker-compose exec postgres psql -U postgres -c "CREATE DATABASE dovvydive_dev;"

# Re-run init script
docker-compose exec postgres psql -U postgres -d dovvydive_dev -f /docker-entrypoint-initdb.d/init.sql

# Run Prisma migrations (will be available after PR 1.2)
# npm run db:migrate --workspace=backend

echo "✅ Database reset complete!"

# Restart backend
docker-compose start backend
```

### 4. `scripts/clean.sh`

Create cleanup script:

```bash
#!/usr/bin/env bash

# Cleanup script for DovvyDive

set -e

echo "🧹 Cleaning up DovvyDive development environment..."

# Stop all containers
echo "Stopping Docker containers..."
docker-compose down

# Remove volumes (optional)
read -p "Remove Docker volumes (will delete database data)? (yes/no): " -r
echo

if [[ $REPLY =~ ^[Yy][Ee][Ss]$ ]]; then
  docker-compose down -v
  echo "✅ Volumes removed"
fi

# Clean node_modules (optional)
read -p "Remove node_modules folders? (yes/no): " -r
echo

if [[ $REPLY =~ ^[Yy][Ee][Ss]$ ]]; then
  find . -name "node_modules" -type d -prune -exec rm -rf '{}' +
  echo "✅ node_modules removed"
fi

# Clean build artifacts
echo "Removing build artifacts..."
rm -rf dist build .next coverage

echo "✅ Cleanup complete!"
```

### 5. `docs/setup/getting-started.md`

Create setup guide:

```markdown
# Getting Started with DovvyDive Development

This guide will help you set up the DovvyDive development environment on your local machine.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js**: v20.10.0 or higher
  - Download from [nodejs.org](https://nodejs.org/)
  - Or use [nvm](https://github.com/nvm-sh/nvm): `nvm install 20.10.0`
  
- **Docker Desktop**
  - Download from [docker.com](https://www.docker.com/products/docker-desktop/)
  - Ensure Docker daemon is running

- **Git**
  - Download from [git-scm.com](https://git-scm.com/)

## Quick Start

### 1. Clone the Repository

\`\`\`bash
git clone <repository-url>
cd AI_DovvyBuddy
\`\`\`

### 2. Install Dependencies

\`\`\`bash
npm install
\`\`\`

This will install dependencies for the root project and all workspaces (backend, frontend).

### 3. Configure Environment Variables

\`\`\`bash
cp .env.example .env
\`\`\`

Edit `.env` and add your API keys:

\`\`\`bash
# Required immediately
GOOGLE_API_KEY=your_google_api_key_here

# Required for Phase 4+
GOOGLE_MAPS_KEY=your_maps_key_here
WEATHER_API_KEY=your_weather_key_here
\`\`\`

**Getting API Keys:**
- Google AI API: [Google AI Studio](https://aistudio.google.com/)
- Google Maps API: [Google Cloud Console](https://console.cloud.google.com/)
- Weather API: [OpenWeatherMap](https://openweathermap.org/api)

### 4. Start Development Environment

\`\`\`bash
./scripts/dev.sh
\`\`\`

Or manually:

\`\`\`bash
docker-compose up
\`\`\`

### 5. Access the Application

- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:4000
- **Database**: localhost:5432

## Development Workflow

### Running Services

**Start all services:**
\`\`\`bash
npm run dev
# or
docker-compose up
\`\`\`

**Start services individually:**
\`\`\`bash
# Backend only
npm run dev:backend

# Frontend only
npm run dev:frontend
\`\`\`

**View logs:**
\`\`\`bash
docker-compose logs -f
docker-compose logs -f backend
docker-compose logs -f frontend
\`\`\`

### Database Management

**Access PostgreSQL:**
\`\`\`bash
docker-compose exec postgres psql -U postgres -d dovvydive_dev
\`\`\`

**Run Prisma migrations (after PR 1.2):**
\`\`\`bash
npm run db:migrate
\`\`\`

**Reset database:**
\`\`\`bash
./scripts/db-reset.sh
\`\`\`

**Verify pgvector extension:**
\`\`\`bash
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT * FROM pg_extension WHERE extname = 'vector';"
\`\`\`

### Testing

**Run all tests:**
\`\`\`bash
npm run test
\`\`\`

**Run specific test types:**
\`\`\`bash
npm run test:unit
npm run test:integration
npm run test:e2e
\`\`\`

**Using test script:**
\`\`\`bash
./scripts/test.sh unit
./scripts/test.sh e2e
\`\`\`

### Code Quality

**Lint code:**
\`\`\`bash
npm run lint
npm run lint:fix
\`\`\`

**Format code:**
\`\`\`bash
npm run format
npm run format:check
\`\`\`

**Type check:**
\`\`\`bash
npm run typecheck
\`\`\`

## Project Structure

\`\`\`
AI_DovvyBuddy/
├── src/
│   ├── backend/           # Express.js API server
│   ├── frontend/          # Next.js application
│   ├── shared/            # Shared TypeScript types
│   └── tests/             # Test suites
├── scripts/               # Development scripts
├── docs/                  # Documentation
├── prisma/                # Prisma schema & migrations
├── data/                  # Dive site content
├── docker-compose.yml     # Docker services
└── package.json           # Root workspace config
\`\`\`

## Common Tasks

### Add a New Dependency

**Backend dependency:**
\`\`\`bash
npm install <package-name> --workspace=backend
\`\`\`

**Frontend dependency:**
\`\`\`bash
npm install <package-name> --workspace=frontend
\`\`\`

**Root/shared dependency:**
\`\`\`bash
npm install <package-name> -D
\`\`\`

### Clean Environment

**Full cleanup:**
\`\`\`bash
./scripts/clean.sh
\`\`\`

**Manual cleanup:**
\`\`\`bash
# Stop containers
docker-compose down

# Remove volumes
docker-compose down -v

# Clean node_modules
rm -rf node_modules src/*/node_modules

# Clean build artifacts
rm -rf dist build .next
\`\`\`

## Troubleshooting

### Port Conflicts

If ports 3000, 4000, or 5432 are already in use:

1. Stop conflicting services
2. Or modify ports in `docker-compose.yml`

### Docker Permission Issues

On Linux, you may need to add your user to the docker group:

\`\`\`bash
sudo usermod -aG docker $USER
newgrp docker
\`\`\`

### Database Connection Failed

1. Ensure PostgreSQL container is running: `docker-compose ps`
2. Check logs: `docker-compose logs postgres`
3. Verify DATABASE_URL in `.env` matches docker-compose settings

### Node Version Mismatch

Use nvm to switch to correct version:

\`\`\`bash
nvm use
# or
nvm install 20.10.0
nvm use 20.10.0
\`\`\`

## Next Steps

After completing Phase 1 setup:

1. **PR 1.2**: Configure Neon database for production
2. **PR 1.3**: Next.js frontend scaffolding
3. **PR 1.4**: Express backend scaffolding
4. **PR 1.5**: i18n framework setup

## Support

- Review [Master Plan](../../plans/dovvydive-mvp/master_plan.md)
- Check [PSD](../psd/dovvydive-psd.md) for requirements
- Consult [TDD](../tdd/dovvydive-tdd.md) for architecture
- Contact team lead for access issues
\`\`\`

### 6. Make scripts executable

Create a helper script:

```bash
#!/usr/bin/env bash

# Make all scripts executable

chmod +x scripts/dev.sh
chmod +x scripts/test.sh
chmod +x scripts/db-reset.sh
chmod +x scripts/clean.sh

echo "✅ All scripts are now executable"
```

---

## Installation Commands

Run these commands after creating the files:

```bash
# Make scripts executable
chmod +x scripts/*.sh

# Test dev script (dry run)
./scripts/dev.sh

# Test that documentation is readable
cat docs/setup/getting-started.md
```

---

## Testing Checklist

- [ ] All scripts have execute permissions
- [ ] `dev.sh` starts Docker containers successfully
- [ ] `test.sh` recognizes different test types
- [ ] `db-reset.sh` requires confirmation before proceeding
- [ ] `clean.sh` provides cleanup options
- [ ] Setup guide is comprehensive and accurate
- [ ] All commands in documentation are correct

---

## Validation Commands

```bash
# Verify scripts are executable
ls -l scripts/

# Test dev script
./scripts/dev.sh
# Should start Docker containers and show logs

# Test database reset (cancel when prompted)
./scripts/db-reset.sh

# Verify documentation exists
ls -l docs/setup/

# Test that documentation is properly formatted
head -n 20 docs/setup/getting-started.md
```

---

## PR 1.1 Completion Checklist

Before opening PR for Step 1.1, verify:

- [x] Step 1: Monorepo structure created
- [x] Step 2: Root package configuration complete
- [x] Step 3: Docker Compose working
- [x] Step 4: Development scripts functional

**Final validation:**

```bash
# Start environment
./scripts/dev.sh

# Verify all services running
docker-compose ps

# Check PostgreSQL health
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT version();"

# Verify pgvector extension
docker-compose exec postgres psql -U postgres -d dovvydive_dev -c "SELECT * FROM pg_extension WHERE extname = 'vector';"

# Stop environment
docker-compose down
```

---

## Commit Message

When ready to commit PR 1.1:

```
feat(infra): initialize monorepo with Docker Compose environment

- Create monorepo structure with backend/frontend workspaces
- Add Docker Compose configuration with PostgreSQL (pgvector)
- Configure TypeScript, ESLint, Prettier for code quality
- Add development scripts for common tasks
- Create comprehensive setup documentation

BREAKING CHANGE: Initial repository setup
```

---

## Next PR

Proceed to PR 1.2: Neon PostgreSQL + pgvector Configuration

# Step 1: Monorepo Structure & Git Setup

**PR:** 1.1 Repository Setup & Docker Environment  
**Step:** 1 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Create the complete directory structure and Git configuration following AI workflow standards. Set up environment variable templates for all services.

---

## Files to Create

### 1. Root Directory Structure

Create these directories:
```bash
mkdir -p src/backend/{config,routes,middleware,services,controllers,utils,types}
mkdir -p src/frontend/{app,components,hooks,lib,styles,types,utils}
mkdir -p src/shared/{types,utils,constants}
mkdir -p src/tests/{unit,integration,e2e}
mkdir -p docs/{setup,api,architecture,testing}
mkdir -p scripts
mkdir -p data/{dive-sites,templates}
mkdir -p prisma/{migrations}
```

### 2. `.gitignore`

Create at project root:

```gitignore
# Dependencies
node_modules/
.pnp
.pnp.js

# Testing
coverage/
.nyc_output

# Next.js
.next/
out/
build/
dist/

# Production
.vercel
.render

# Misc
.DS_Store
*.pem

# Debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*

# Local env files
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Prisma
prisma/dev.db
prisma/dev.db-journal

# Docker
docker-compose.override.yml
.docker/

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
Thumbs.db
.Spotlight-V100
.Trashes
```

### 3. `.env.example`

Create environment variable template:

```bash
# Node Environment
NODE_ENV=development

# Database (Local Docker)
DATABASE_URL="postgresql://postgres:password@localhost:5432/dovvydive_dev?schema=public"

# Database (Neon - Production/Staging)
# NEON_DATABASE_URL="postgresql://user:password@ep-xxx.region.aws.neon.tech/dovvydive?sslmode=require"

# Backend Server
BACKEND_PORT=4000
BACKEND_URL=http://localhost:4000

# Frontend Server
NEXT_PUBLIC_API_URL=http://localhost:4000/api/v1
FRONTEND_PORT=3000

# Google AI APIs (Get from Google AI Studio)
GOOGLE_API_KEY=your_google_api_key_here

# Google Maps API (Get from Google Cloud Console)
GOOGLE_MAPS_KEY=your_google_maps_key_here

# Weather API (Get from OpenWeatherMap or WeatherAPI.com)
WEATHER_API_KEY=your_weather_api_key_here
WEATHER_API_PROVIDER=openweathermap

# Session Configuration
SESSION_SECRET=your_session_secret_change_in_production
SESSION_TTL_HOURS=24

# Rate Limiting
RATE_LIMIT_MAX_REQUESTS=100
RATE_LIMIT_WINDOW_HOURS=1

# Logging
LOG_LEVEL=info

# Redis (Optional - for rate limiting in production)
# REDIS_URL=redis://localhost:6379
```

### 4. `README.md`

Create project README at root:

```markdown
# DovvyDive - AI-Powered Dive Trip Planner

An intelligent chatbot that helps scuba divers discover, plan, and organize dive trips across Malaysia through natural conversation.

## Features

- 🤖 **AI-Powered Recommendations** using Google Gemini 2.5 Flash
- 🌏 **Bilingual Support** (English & Chinese)
- 🏊 **Safety-First** certification level matching
- 📅 **Smart Itineraries** with surface interval enforcement
- 🌤️ **Weather Insights** for optimal dive planning
- 🗺️ **50+ Malaysian Dive Sites** in knowledge base

## Tech Stack

- **Frontend:** Next.js 14+ (TypeScript), Tailwind CSS
- **Backend:** Node.js 20+, Express.js, Prisma ORM
- **AI:** Google ADK, Gemini 2.5 Flash
- **Database:** Neon PostgreSQL with pgvector
- **Infrastructure:** Docker, Vercel, Render

## Quick Start

### Prerequisites

- Node.js 20.10.0 or higher
- Docker Desktop
- Git

### Installation

1. **Clone the repository**
   \`\`\`bash
   git clone <repository-url>
   cd AI_DovvyBuddy
   \`\`\`

2. **Install dependencies**
   \`\`\`bash
   npm install
   \`\`\`

3. **Set up environment variables**
   \`\`\`bash
   cp .env.example .env
   # Edit .env with your actual API keys
   \`\`\`

4. **Start development environment**
   \`\`\`bash
   docker-compose up
   \`\`\`

5. **Access the application**
   - Frontend: http://localhost:3000
   - Backend API: http://localhost:4000
   - Database: localhost:5432

## Development

### Run locally (without Docker)

\`\`\`bash
# Terminal 1 - Backend
cd src/backend
npm run dev

# Terminal 2 - Frontend
cd src/frontend
npm run dev
\`\`\`

### Run tests

\`\`\`bash
npm run test
npm run test:e2e
\`\`\`

### Lint and format

\`\`\`bash
npm run lint
npm run format
\`\`\`

## Project Structure

\`\`\`
AI_DovvyBuddy/
├── docs/                    # Documentation
│   ├── psd/                # Product specs
│   ├── tdd/                # Technical design
│   └── setup/              # Setup guides
├── plans/                   # Implementation plans
├── src/
│   ├── backend/            # Express.js API
│   ├── frontend/           # Next.js app
│   ├── shared/             # Shared types/utils
│   └── tests/              # Test suites
├── scripts/                # Utility scripts
├── data/                   # Dive site content
├── prisma/                 # Database schema
├── docker-compose.yml
└── package.json
\`\`\`

## Documentation

- [Setup Guide](docs/setup/getting-started.md)
- [API Documentation](docs/api/README.md)
- [Architecture Overview](docs/architecture/system-design.md)

## Contributing

This is a private project. For development workflow, see [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Proprietary - All rights reserved
```

### 5. `.nvmrc`

Create Node version file:

```
20.10.0
```

### 6. `.editorconfig`

Create editor configuration:

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 2

[*.md]
trim_trailing_whitespace = false

[*.py]
indent_size = 4
```

---

## Testing Checklist

- [ ] All directories created successfully
- [ ] `.gitignore` excludes `node_modules/`, `.env`, and Docker volumes
- [ ] `.env.example` contains all required variables from master plan
- [ ] `README.md` provides clear quick start instructions
- [ ] `.nvmrc` specifies Node 20.10.0
- [ ] `.editorconfig` enforces consistent code style
- [ ] Project structure matches TDD specifications

---

## Validation Commands

```bash
# Verify directory structure
tree -L 3 src/

# Verify .env.example has all keys
cat .env.example | grep -E "^[A-Z_]+=" | wc -l
# Should show ~15+ environment variables

# Test .gitignore
git status
# Should not show .env or node_modules if they exist
```

---

## Common Issues

**Issue:** Permission denied creating directories  
**Solution:** Run with appropriate permissions or adjust parent directory ownership

**Issue:** .env.example missing required variables  
**Solution:** Cross-reference with Phase 2, 3, 4 PRs to ensure all future variables are templated

---

## Next Step

Proceed to Step 2: Root Package Configuration

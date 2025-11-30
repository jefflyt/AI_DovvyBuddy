# Step 1: Neon Project Setup & Documentation

**PR:** 1.2 Neon PostgreSQL + pgvector Configuration  
**Step:** 1 of 4  
**Estimated Time:** 1 hour

---

## Goal

Document the manual process of creating a Neon project via the Neon console, enabling the pgvector extension, and obtaining connection strings. Create automation script for team members.

---

## Files to Create

### 1. `docs/setup/neon-setup.md`

Create comprehensive Neon setup guide:

```markdown
# Neon PostgreSQL Setup Guide

This guide walks you through setting up a Neon serverless PostgreSQL database for DovvyDive.

## Why Neon?

- **Serverless**: Auto-scaling with usage-based pricing
- **Branching**: Database branching for development workflows
- **pgvector Support**: Native support for vector embeddings
- **Fast Cold Starts**: ~1 second activation time

## Prerequisites

- Neon account (sign up at [neon.tech](https://neon.tech))
- GitHub account (for OAuth login)

## Step-by-Step Setup

### 1. Create Neon Account

1. Go to [console.neon.tech](https://console.neon.tech)
2. Sign up with GitHub OAuth
3. Verify your email address

### 2. Create New Project

1. Click **"New Project"** in Neon Console
2. Configure project settings:
   - **Project Name**: `dovvydive-mvp`
   - **Region**: Choose closest to your users (e.g., `aws-ap-southeast-1` for Singapore/Malaysia)
   - **PostgreSQL Version**: `16` (latest stable)
   - **Compute Size**: `0.25 vCPU, 1 GB RAM` (sufficient for MVP)

3. Click **"Create Project"**

### 3. Enable pgvector Extension

1. Navigate to **SQL Editor** in Neon Console
2. Run the following SQL:

\`\`\`sql
CREATE EXTENSION IF NOT EXISTS vector;
\`\`\`

3. Verify extension is enabled:

\`\`\`sql
SELECT * FROM pg_extension WHERE extname = 'vector';
\`\`\`

You should see output showing the vector extension.

### 4. Get Connection String

1. Go to **Dashboard** → **Connection Details**
2. Copy the connection string for **Pooled connection**
3. Format should be:
   \`\`\`
   postgresql://[user]:[password]@[endpoint]/[database]?sslmode=require
   \`\`\`

4. Add to your `.env` file:
   \`\`\`bash
   NEON_DATABASE_URL="postgresql://user:password@ep-xxx-xxx.region.aws.neon.tech/neondb?sslmode=require"
   \`\`\`

### 5. Create Development Branch (Optional but Recommended)

Neon supports database branching for isolated development:

1. Go to **Branches** tab
2. Click **"Create Branch"**
3. Configure:
   - **Branch Name**: `development`
   - **Parent Branch**: `main`
   - **Compute**: Share compute with parent (to save costs)

4. Use development branch connection string for local development

### 6. Configure Connection Pooling

1. In **Connection Details**, ensure **Connection Pooling** is enabled
2. Use the **Pooled connection** string (not direct connection)
3. This prevents connection exhaustion during development

## Connection String Examples

**Production (main branch):**
\`\`\`
postgresql://alex:AbC123@ep-cool-darkness-123456.us-east-2.aws.neon.tech/neondb?sslmode=require
\`\`\`

**Development (dev branch):**
\`\`\`
postgresql://alex:AbC123@ep-cool-darkness-123456-dev.us-east-2.aws.neon.tech/neondb?sslmode=require
\`\`\`

## Security Best Practices

1. **Never commit connection strings to Git**
   - Use `.env` files (ignored by Git)
   - Store secrets in environment variables

2. **Use different databases for environments:**
   - Local: Docker PostgreSQL
   - Development: Neon development branch
   - Staging: Neon staging branch
   - Production: Neon main branch

3. **Rotate passwords regularly**
   - Update in Neon Console → Settings → Reset Password
   - Update `.env` files on all machines

4. **Enable IP allowlist (if needed)**
   - Neon Console → Settings → IP Allow
   - Add your server IPs for production

## Monitoring & Limits

### Free Tier Limits

- **Storage**: 512 MB
- **Compute Hours**: 191.9 hours/month (enough for MVP)
- **Connections**: Up to 100 concurrent

### Monitoring Usage

1. Dashboard → **Usage** tab
2. Track:
   - Storage usage
   - Compute hours
   - Data transfer

### Upgrade Path

When you need more resources:
1. Navigate to **Billing**
2. Upgrade to Pro plan ($19/month base + usage)

## Troubleshooting

**Issue: "connection refused"**
- Check connection string format
- Ensure `sslmode=require` is included
- Verify your IP is not blocked

**Issue: "too many connections"**
- Use pooled connection string
- Check connection pool settings in backend

**Issue: "extension vector not found"**
- Re-run `CREATE EXTENSION vector;`
- Verify PostgreSQL version is 12+

## Next Steps

After Neon setup:
1. Update `.env` with `NEON_DATABASE_URL`
2. Configure backend to use Neon for production
3. Run Prisma migrations against Neon database
\`\`\`

### 2. `scripts/create-neon-project.sh`

Create CLI automation script:

```bash
#!/usr/bin/env bash

# Automated Neon project setup using Neon CLI
# Requires: neonctl CLI installed (https://neon.tech/docs/reference/neon-cli)

set -e

echo "🚀 DovvyDive Neon Project Setup"
echo "================================"

# Check if neonctl is installed
if ! command -v neonctl &> /dev/null; then
    echo "❌ neonctl CLI not found"
    echo "📦 Install with: npm install -g neonctl"
    echo "🔗 Docs: https://neon.tech/docs/reference/neon-cli"
    exit 1
fi

# Check if logged in
if ! neonctl me &> /dev/null; then
    echo "🔐 Please log in to Neon CLI:"
    neonctl auth
fi

# Project configuration
PROJECT_NAME="dovvydive-mvp"
REGION="aws-ap-southeast-1"  # Singapore (closest to Malaysia)
PG_VERSION="16"

echo ""
echo "📋 Project Configuration:"
echo "  Name: $PROJECT_NAME"
echo "  Region: $REGION"
echo "  PostgreSQL: v$PG_VERSION"
echo ""

read -p "Proceed with project creation? (yes/no): " -r
echo

if [[ ! $REPLY =~ ^[Yy][Ee][Ss]$ ]]; then
    echo "❌ Setup cancelled"
    exit 1
fi

# Create project
echo "🏗️  Creating Neon project..."
PROJECT_ID=$(neonctl projects create \
    --name "$PROJECT_NAME" \
    --region "$REGION" \
    --pg-version "$PG_VERSION" \
    --output json | jq -r '.id')

echo "✅ Project created: $PROJECT_ID"

# Get connection string
echo "🔗 Getting connection string..."
CONNECTION_STRING=$(neonctl connection-string "$PROJECT_ID" --pooled)

# Enable pgvector extension
echo "🧩 Enabling pgvector extension..."
neonctl sql "$PROJECT_ID" --query "CREATE EXTENSION IF NOT EXISTS vector;"

# Verify extension
EXTENSION_CHECK=$(neonctl sql "$PROJECT_ID" --query "SELECT * FROM pg_extension WHERE extname = 'vector';" --output json)
if [[ $EXTENSION_CHECK == *"vector"* ]]; then
    echo "✅ pgvector extension enabled"
else
    echo "⚠️  Warning: pgvector extension may not be enabled. Verify manually."
fi

# Create development branch
echo "🌿 Creating development branch..."
DEV_BRANCH_ID=$(neonctl branches create \
    --project-id "$PROJECT_ID" \
    --name "development" \
    --output json | jq -r '.id')

DEV_CONNECTION_STRING=$(neonctl connection-string "$PROJECT_ID" --branch-id "$DEV_BRANCH_ID" --pooled)

echo "✅ Development branch created"

# Save to .env.neon (gitignored)
echo ""
echo "💾 Saving connection strings to .env.neon..."

cat > .env.neon << EOF
# Neon Connection Strings
# Generated: $(date)

# Production (main branch)
NEON_DATABASE_URL="$CONNECTION_STRING"

# Development branch
NEON_DEV_DATABASE_URL="$DEV_CONNECTION_STRING"

# Project ID
NEON_PROJECT_ID="$PROJECT_ID"
NEON_DEV_BRANCH_ID="$DEV_BRANCH_ID"
EOF

echo "✅ Connection strings saved to .env.neon"

# Summary
echo ""
echo "======================================"
echo "✅ Neon Setup Complete!"
echo "======================================"
echo ""
echo "📋 Next Steps:"
echo "1. Copy connection strings from .env.neon to .env"
echo "2. Update backend/config/database.ts to use Neon"
echo "3. Run Prisma migrations: npm run db:migrate"
echo ""
echo "🔗 Project Dashboard:"
echo "   https://console.neon.tech/app/projects/$PROJECT_ID"
echo ""
echo "📖 Documentation:"
echo "   docs/setup/neon-setup.md"
echo ""
```

### 3. Update `.env.example`

Add Neon-specific variables:

```bash
# Add to existing .env.example

# Neon PostgreSQL (Production/Staging)
NEON_DATABASE_URL="postgresql://user:password@ep-xxx.region.aws.neon.tech/neondb?sslmode=require"
NEON_PROJECT_ID="your_project_id"

# Database switching (use 'local' or 'neon')
DATABASE_PROVIDER=local
```

### 4. `.gitignore` update

Ensure Neon files are ignored:

```gitignore
# Add to existing .gitignore

# Neon
.env.neon
```

---

## Installation Commands

```bash
# Make script executable
chmod +x scripts/create-neon-project.sh

# Optional: Install Neon CLI
npm install -g neonctl

# Run automated setup (if using CLI)
./scripts/create-neon-project.sh

# Or follow manual setup guide
open docs/setup/neon-setup.md
```

---

## Testing Checklist

- [ ] Documentation guide is complete and accurate
- [ ] Automation script has execute permissions
- [ ] Script checks for neonctl installation
- [ ] Connection strings saved securely
- [ ] .env.neon added to .gitignore
- [ ] Manual setup steps verified in Neon Console

---

## Validation Commands

```bash
# If using CLI automation
./scripts/create-neon-project.sh

# Verify connection string format
cat .env.neon | grep NEON_DATABASE_URL
# Should start with postgresql:// and end with ?sslmode=require

# Test connection (requires database.ts from Step 2)
# node -e "require('./src/backend/config/database').testNeonConnection()"
```

---

## Next Step

Proceed to Step 2: Database Connection Configuration

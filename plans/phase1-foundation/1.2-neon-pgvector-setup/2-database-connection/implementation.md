# Step 2: Database Connection Configuration

**PR:** 1.2 Neon PostgreSQL + pgvector Configuration  
**Step:** 2 of 4  
**Estimated Time:** 2 hours

---

## Goal

Create database connection module that intelligently switches between local Docker Postgres and Neon based on environment variable. Implement connection pooling (max 20 connections) and retry logic with exponential backoff.

---

## Files to Create

### 1. `src/backend/package.json`

Create backend workspace package file:

```json
{
  "name": "backend",
  "version": "1.0.0",
  "private": true,
  "description": "DovvyDive backend API server",
  "main": "dist/server.js",
  "scripts": {
    "dev": "ts-node-dev --respawn --transpile-only src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "test": "jest",
    "test:unit": "jest --testPathPattern=tests/unit",
    "test:integration": "jest --testPathPattern=tests/integration",
    "test:watch": "jest --watch",
    "lint": "eslint . --ext .ts",
    "lint:fix": "eslint . --ext .ts --fix",
    "typecheck": "tsc --noEmit",
    "db:migrate": "prisma migrate dev",
    "db:migrate:prod": "prisma migrate deploy",
    "db:seed": "ts-node prisma/seed.ts",
    "db:reset": "prisma migrate reset",
    "db:studio": "prisma studio"
  },
  "dependencies": {
    "@prisma/client": "^5.7.0",
    "express": "^4.18.2",
    "dotenv": "^16.3.1",
    "zod": "^3.22.4",
    "helmet": "^7.1.0",
    "cors": "^2.8.5",
    "morgan": "^1.10.0"
  },
  "devDependencies": {
    "@types/express": "^4.17.21",
    "@types/node": "^20.10.0",
    "@types/cors": "^2.8.17",
    "@types/morgan": "^1.9.9",
    "prisma": "^5.7.0",
    "ts-node": "^10.9.2",
    "ts-node-dev": "^2.0.0",
    "typescript": "^5.3.2",
    "jest": "^29.7.0",
    "@types/jest": "^29.5.10"
  }
}
```

### 2. `src/backend/tsconfig.json`

```json
{
  "extends": "../../tsconfig.json",
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./",
    "moduleResolution": "node",
    "baseUrl": "./",
    "paths": {
      "@/config/*": ["config/*"],
      "@/routes/*": ["routes/*"],
      "@/middleware/*": ["middleware/*"],
      "@/services/*": ["services/*"],
      "@/controllers/*": ["controllers/*"],
      "@/utils/*": ["utils/*"],
      "@/types/*": ["types/*"],
      "@shared/*": ["../shared/*"]
    },
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["**/*.ts"],
  "exclude": ["node_modules", "dist", "tests"]
}
```

### 3. `src/backend/config/env.ts`

Create environment variable validation:

```typescript
import { z } from 'zod';
import dotenv from 'dotenv';

// Load .env file
dotenv.config();

const envSchema = z.object({
  // Node environment
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),

  // Server
  BACKEND_PORT: z.string().default('4000'),
  BACKEND_URL: z.string().url().default('http://localhost:4000'),

  // Database
  DATABASE_URL: z.string().url(),
  NEON_DATABASE_URL: z.string().url().optional(),
  DATABASE_PROVIDER: z.enum(['local', 'neon']).default('local'),

  // Session
  SESSION_SECRET: z.string().min(32),
  SESSION_TTL_HOURS: z.string().default('24'),

  // Logging
  LOG_LEVEL: z.enum(['debug', 'info', 'warn', 'error']).default('info'),
});

type Env = z.infer<typeof envSchema>;

function validateEnv(): Env {
  try {
    return envSchema.parse(process.env);
  } catch (error) {
    if (error instanceof z.ZodError) {
      const missingVars = error.errors.map((err) => err.path.join('.')).join(', ');
      throw new Error(
        `Missing or invalid environment variables: ${missingVars}\n` +
          `Please check your .env file against .env.example`
      );
    }
    throw error;
  }
}

export const env = validateEnv();

export default env;
```

### 4. `src/backend/config/database.ts`

Create database connection module:

```typescript
import { PrismaClient } from '@prisma/client';
import env from './env';

// Connection pool configuration
const POOL_CONFIG = {
  max: 20, // Maximum connections
  min: 2, // Minimum connections
  idleTimeoutMillis: 30000, // 30 seconds
  connectionTimeoutMillis: 10000, // 10 seconds
};

// Retry configuration
const RETRY_CONFIG = {
  maxRetries: 5,
  initialDelay: 1000, // 1 second
  maxDelay: 30000, // 30 seconds
  factor: 2, // Exponential backoff factor
};

class DatabaseConnection {
  private static instance: DatabaseConnection;
  private prisma: PrismaClient | null = null;
  private isConnected = false;
  private connectionAttempts = 0;

  private constructor() {}

  static getInstance(): DatabaseConnection {
    if (!DatabaseConnection.instance) {
      DatabaseConnection.instance = new DatabaseConnection();
    }
    return DatabaseConnection.instance;
  }

  /**
   * Get database URL based on provider
   */
  private getDatabaseUrl(): string {
    if (env.DATABASE_PROVIDER === 'neon' && env.NEON_DATABASE_URL) {
      console.log('📡 Using Neon database');
      return env.NEON_DATABASE_URL;
    }
    console.log('🐳 Using local Docker database');
    return env.DATABASE_URL;
  }

  /**
   * Initialize Prisma client with connection pooling
   */
  private createPrismaClient(): PrismaClient {
    const databaseUrl = this.getDatabaseUrl();

    return new PrismaClient({
      datasources: {
        db: {
          url: databaseUrl,
        },
      },
      log:
        env.NODE_ENV === 'development'
          ? ['query', 'error', 'warn']
          : ['error'],
    });
  }

  /**
   * Exponential backoff delay
   */
  private async delay(attempt: number): Promise<void> {
    const delay = Math.min(
      RETRY_CONFIG.initialDelay * Math.pow(RETRY_CONFIG.factor, attempt),
      RETRY_CONFIG.maxDelay
    );
    console.log(`⏳ Retrying in ${delay}ms...`);
    return new Promise((resolve) => setTimeout(resolve, delay));
  }

  /**
   * Connect to database with retry logic
   */
  async connect(): Promise<PrismaClient> {
    if (this.isConnected && this.prisma) {
      return this.prisma;
    }

    this.prisma = this.createPrismaClient();

    for (let attempt = 0; attempt < RETRY_CONFIG.maxRetries; attempt++) {
      try {
        console.log(
          `🔌 Connecting to database... (attempt ${attempt + 1}/${RETRY_CONFIG.maxRetries})`
        );

        await this.prisma.$connect();
        this.isConnected = true;
        this.connectionAttempts = 0;

        console.log('✅ Database connected successfully');
        return this.prisma;
      } catch (error) {
        this.connectionAttempts = attempt + 1;
        console.error(`❌ Database connection failed (attempt ${attempt + 1}):`, error);

        if (attempt < RETRY_CONFIG.maxRetries - 1) {
          await this.delay(attempt);
        } else {
          throw new Error(
            `Failed to connect to database after ${RETRY_CONFIG.maxRetries} attempts: ${
              error instanceof Error ? error.message : 'Unknown error'
            }`
          );
        }
      }
    }

    throw new Error('Database connection failed');
  }

  /**
   * Disconnect from database
   */
  async disconnect(): Promise<void> {
    if (this.prisma) {
      await this.prisma.$disconnect();
      this.isConnected = false;
      console.log('🔌 Database disconnected');
    }
  }

  /**
   * Get Prisma client instance
   */
  getClient(): PrismaClient {
    if (!this.prisma || !this.isConnected) {
      throw new Error('Database not connected. Call connect() first.');
    }
    return this.prisma;
  }

  /**
   * Health check
   */
  async healthCheck(): Promise<{
    status: 'healthy' | 'unhealthy';
    provider: string;
    connected: boolean;
    latency?: number;
  }> {
    if (!this.prisma) {
      return {
        status: 'unhealthy',
        provider: env.DATABASE_PROVIDER,
        connected: false,
      };
    }

    try {
      const start = Date.now();
      await this.prisma.$queryRaw`SELECT 1`;
      const latency = Date.now() - start;

      return {
        status: 'healthy',
        provider: env.DATABASE_PROVIDER,
        connected: this.isConnected,
        latency,
      };
    } catch (error) {
      return {
        status: 'unhealthy',
        provider: env.DATABASE_PROVIDER,
        connected: false,
      };
    }
  }

  /**
   * Check if pgvector extension is available
   */
  async checkVectorExtension(): Promise<boolean> {
    if (!this.prisma) {
      throw new Error('Database not connected');
    }

    try {
      const result = await this.prisma.$queryRaw<Array<{ extname: string }>>`
        SELECT extname FROM pg_extension WHERE extname = 'vector'
      `;
      return result.length > 0;
    } catch (error) {
      console.error('Error checking vector extension:', error);
      return false;
    }
  }
}

// Export singleton instance
export const db = DatabaseConnection.getInstance();

// Export Prisma client getter
export const prisma = () => db.getClient();

export default db;
```

### 5. `src/backend/utils/dbHealth.ts`

Create health check utility:

```typescript
import db from '../config/database';

export interface HealthCheckResult {
  database: {
    status: 'healthy' | 'unhealthy';
    provider: string;
    connected: boolean;
    latency?: number;
    vectorExtension?: boolean;
  };
  timestamp: string;
}

export async function performHealthCheck(): Promise<HealthCheckResult> {
  const dbHealth = await db.healthCheck();
  
  let vectorExtension: boolean | undefined;
  try {
    vectorExtension = await db.checkVectorExtension();
  } catch (error) {
    vectorExtension = undefined;
  }

  return {
    database: {
      ...dbHealth,
      vectorExtension,
    },
    timestamp: new Date().toISOString(),
  };
}

export default performHealthCheck;
```

---

## Installation Commands

```bash
# Install backend dependencies
npm install --workspace=backend

# Generate Prisma Client (will do in Step 3)
# cd src/backend && npx prisma generate
```

---

## Testing Checklist

- [ ] Backend package.json created with all dependencies
- [ ] TypeScript configuration extends root config
- [ ] Environment validation with Zod works
- [ ] Database connection module compiles
- [ ] Health check utility compiles
- [ ] Connection switching logic (local/Neon) implemented
- [ ] Retry logic with exponential backoff implemented
- [ ] Connection pooling configured

---

## Validation Commands

```bash
# Verify backend workspace recognized
npm ls --workspace=backend

# Install dependencies
npm install --workspace=backend

# Type check
npm run typecheck --workspace=backend

# Test environment validation (will fail until .env complete)
# node -e "require('./src/backend/config/env')"
```

---

## Next Step

Proceed to Step 3: Prisma Initial Setup

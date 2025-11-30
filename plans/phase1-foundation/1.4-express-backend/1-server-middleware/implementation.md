# Step 1: Express Server & Middleware Setup

**PR:** 1.4 Express Backend API Structure  
**Step:** 1 of 4  
**Estimated Time:** 2 hours

---

## Goal

Initialize Express server with TypeScript, install core middleware (helmet, cors, morgan), and create basic server configuration with health check endpoint.

---

## Files to Create

### 1. Update `src/backend/package.json`

Add Express dependencies:

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
    "lint": "eslint . --ext .ts",
    "lint:fix": "eslint . --ext .ts --fix",
    "typecheck": "tsc --noEmit",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:studio": "prisma studio"
  },
  "dependencies": {
    "express": "^4.18.2",
    "helmet": "^7.1.0",
    "cors": "^2.8.5",
    "morgan": "^1.10.0",
    "dotenv": "^16.3.1",
    "zod": "^3.22.4",
    "@prisma/client": "^5.7.0",
    "compression": "^1.7.4",
    "express-rate-limit": "^7.1.5"
  },
  "devDependencies": {
    "@types/express": "^4.17.21",
    "@types/node": "^20.10.0",
    "@types/cors": "^2.8.17",
    "@types/morgan": "^1.9.9",
    "@types/compression": "^1.7.5",
    "@typescript-eslint/eslint-plugin": "^6.13.0",
    "@typescript-eslint/parser": "^6.13.0",
    "eslint": "^8.55.0",
    "typescript": "^5.3.2",
    "ts-node-dev": "^2.0.0",
    "prisma": "^5.7.0",
    "jest": "^29.7.0",
    "@types/jest": "^29.5.11",
    "ts-jest": "^29.1.1"
  }
}
```

### 2. `src/backend/src/server.ts`

Main server entry point:

```typescript
import express, { Express, Request, Response, NextFunction } from 'express';
import helmet from 'helmet';
import cors from 'cors';
import morgan from 'morgan';
import compression from 'compression';
import rateLimit from 'express-rate-limit';
import { config } from './config/env';
import { DatabaseConnection } from './config/database';
import { errorHandler } from './middleware/errorHandler';
import { notFoundHandler } from './middleware/notFoundHandler';
import { logger } from './utils/logger';

const app: Express = express();
const PORT = config.PORT;

// ===========================
// Middleware Setup
// ===========================

// Security middleware
app.use(helmet());

// CORS configuration
app.use(
  cors({
    origin: config.CORS_ORIGIN.split(','),
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
    allowedHeaders: ['Content-Type', 'Authorization'],
  })
);

// Request logging
if (config.NODE_ENV === 'development') {
  app.use(morgan('dev'));
} else {
  app.use(morgan('combined'));
}

// Body parsing
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true, limit: '10mb' }));

// Compression
app.use(compression());

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP, please try again later.',
  standardHeaders: true,
  legacyHeaders: false,
});

if (config.NODE_ENV === 'production') {
  app.use('/api', limiter);
}

// ===========================
// Routes
// ===========================

// Health check endpoint
app.get('/health', async (req: Request, res: Response) => {
  try {
    const dbHealth = await DatabaseConnection.getInstance().healthCheck();
    
    res.status(200).json({
      status: 'ok',
      timestamp: new Date().toISOString(),
      environment: config.NODE_ENV,
      database: {
        connected: dbHealth.connected,
        latency: dbHealth.latency,
      },
      uptime: process.uptime(),
    });
  } catch (error) {
    res.status(503).json({
      status: 'error',
      timestamp: new Date().toISOString(),
      message: 'Service unavailable',
    });
  }
});

// API version info
app.get('/api/v1', (req: Request, res: Response) => {
  res.json({
    name: 'DovvyDive API',
    version: '1.0.0',
    description: 'AI-powered dive site discovery API',
  });
});

// ===========================
// Error Handling
// ===========================

// 404 handler (must be after all routes)
app.use(notFoundHandler);

// Global error handler (must be last)
app.use(errorHandler);

// ===========================
// Server Startup
// ===========================

async function startServer() {
  try {
    // Initialize database connection
    logger.info('Connecting to database...');
    await DatabaseConnection.getInstance().connect();
    logger.info('Database connected successfully');

    // Start HTTP server
    app.listen(PORT, () => {
      logger.info(`🚀 Server running on http://localhost:${PORT}`);
      logger.info(`Environment: ${config.NODE_ENV}`);
      logger.info(`Database: ${config.DATABASE_PROVIDER}`);
    });
  } catch (error) {
    logger.error('Failed to start server:', error);
    process.exit(1);
  }
}

// Graceful shutdown
process.on('SIGTERM', async () => {
  logger.info('SIGTERM signal received: closing HTTP server');
  await DatabaseConnection.getInstance().disconnect();
  process.exit(0);
});

process.on('SIGINT', async () => {
  logger.info('SIGINT signal received: closing HTTP server');
  await DatabaseConnection.getInstance().disconnect();
  process.exit(0);
});

// Start the server
startServer();

export { app };
```

### 3. `src/backend/src/middleware/errorHandler.ts`

Global error handling:

```typescript
import { Request, Response, NextFunction } from 'express';
import { logger } from '../utils/logger';

export interface AppError extends Error {
  statusCode?: number;
  code?: string;
  details?: unknown;
}

export function errorHandler(
  err: AppError,
  req: Request,
  res: Response,
  next: NextFunction
): void {
  const statusCode = err.statusCode || 500;
  const message = err.message || 'Internal server error';

  // Log error details
  logger.error({
    message: err.message,
    stack: err.stack,
    statusCode,
    path: req.path,
    method: req.method,
    code: err.code,
  });

  // Don't expose stack trace in production
  const response: {
    error: string;
    message: string;
    code?: string;
    stack?: string;
  } = {
    error: statusCode >= 500 ? 'Internal Server Error' : message,
    message,
    code: err.code,
  };

  if (process.env.NODE_ENV === 'development') {
    response.stack = err.stack;
  }

  res.status(statusCode).json(response);
}

export class BadRequestError extends Error implements AppError {
  statusCode = 400;
  code = 'BAD_REQUEST';
  
  constructor(message: string) {
    super(message);
    this.name = 'BadRequestError';
  }
}

export class UnauthorizedError extends Error implements AppError {
  statusCode = 401;
  code = 'UNAUTHORIZED';
  
  constructor(message: string = 'Unauthorized') {
    super(message);
    this.name = 'UnauthorizedError';
  }
}

export class NotFoundError extends Error implements AppError {
  statusCode = 404;
  code = 'NOT_FOUND';
  
  constructor(message: string = 'Resource not found') {
    super(message);
    this.name = 'NotFoundError';
  }
}

export class InternalServerError extends Error implements AppError {
  statusCode = 500;
  code = 'INTERNAL_SERVER_ERROR';
  
  constructor(message: string = 'Internal server error') {
    super(message);
    this.name = 'InternalServerError';
  }
}
```

### 4. `src/backend/src/middleware/notFoundHandler.ts`

404 handler:

```typescript
import { Request, Response } from 'express';

export function notFoundHandler(req: Request, res: Response): void {
  res.status(404).json({
    error: 'Not Found',
    message: `Cannot ${req.method} ${req.path}`,
    path: req.path,
    timestamp: new Date().toISOString(),
  });
}
```

### 5. `src/backend/src/utils/logger.ts`

Simple logger utility:

```typescript
type LogLevel = 'info' | 'warn' | 'error' | 'debug';

interface LogData {
  [key: string]: unknown;
}

class Logger {
  private formatMessage(level: LogLevel, message: string, data?: LogData): string {
    const timestamp = new Date().toISOString();
    const logObject = {
      timestamp,
      level: level.toUpperCase(),
      message,
      ...data,
    };
    return JSON.stringify(logObject);
  }

  info(message: string, data?: LogData): void {
    console.log(this.formatMessage('info', message, data));
  }

  warn(message: string, data?: LogData): void {
    console.warn(this.formatMessage('warn', message, data));
  }

  error(message: string, data?: LogData): void {
    console.error(this.formatMessage('error', message, data));
  }

  debug(message: string, data?: LogData): void {
    if (process.env.NODE_ENV === 'development') {
      console.debug(this.formatMessage('debug', message, data));
    }
  }
}

export const logger = new Logger();
```

### 6. Update `src/backend/config/env.ts`

Add server configuration:

```typescript
import { z } from 'zod';
import * as dotenv from 'dotenv';

dotenv.config();

const envSchema = z.object({
  // Server
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
  PORT: z.string().default('4000').transform(Number),
  CORS_ORIGIN: z.string().default('http://localhost:3000'),
  
  // Database (from previous PR)
  DATABASE_PROVIDER: z.enum(['local', 'neon']).default('local'),
  DATABASE_URL: z.string(),
  NEON_DATABASE_URL: z.string().optional(),
  
  // Logging
  LOG_LEVEL: z.enum(['info', 'warn', 'error', 'debug']).default('info'),
});

export const config = envSchema.parse(process.env);

export type Config = z.infer<typeof envSchema>;
```

---

## Installation Commands

```bash
# Install backend dependencies
npm install --workspace=backend

# Create missing directories
mkdir -p src/backend/src/{middleware,routes,controllers,services}
```

---

## Testing Checklist

- [ ] Express server dependencies installed
- [ ] Server.ts created with middleware setup
- [ ] Error handlers created
- [ ] Logger utility created
- [ ] Config updated with server settings
- [ ] Health check endpoint works

---

## Validation Commands

```bash
# Install dependencies
npm install --workspace=backend

# Type check
npm run typecheck --workspace=backend

# Start dev server
npm run dev --workspace=backend
# Should show: 🚀 Server running on http://localhost:4000

# Test health endpoint
curl http://localhost:4000/health
# Should return JSON with status: ok

# Test API version endpoint
curl http://localhost:4000/api/v1
# Should return API info

# Test 404 handler
curl http://localhost:4000/nonexistent
# Should return 404 error

# View logs (should be JSON formatted)
```

---

## Common Issues

**Issue:** Port 4000 already in use  
**Solution:** Change PORT in .env or stop process using port: `lsof -ti:4000 | xargs kill`

**Issue:** Database connection fails  
**Solution:** Ensure PR 1.2 database setup completed and Docker running

**Issue:** CORS errors from frontend  
**Solution:** Verify CORS_ORIGIN in .env matches frontend URL

---

## Next Step

Proceed to Step 2: API Routes Structure & Versioning

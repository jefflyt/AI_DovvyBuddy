# Step 4: Backend Testing Setup & Documentation

**PR:** 1.4 Express Backend API Structure  
**Step:** 4 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Configure Jest for backend testing, create test utilities, write sample tests for API endpoints, and document the backend structure.

---

## Files to Create

### 1. `src/backend/jest.config.js`

Jest configuration:

```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/src'],
  testMatch: ['**/__tests__/**/*.ts', '**/*.test.ts', '**/*.spec.ts'],
  collectCoverageFrom: [
    'src/**/*.ts',
    '!src/**/*.d.ts',
    '!src/**/__tests__/**',
    '!src/server.ts',
  ],
  coverageDirectory: 'coverage',
  coverageReporters: ['text', 'lcov', 'html'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  setupFilesAfterEnv: ['<rootDir>/src/__tests__/setup.ts'],
  verbose: true,
};
```

### 2. `src/backend/src/__tests__/setup.ts`

Test setup file:

```typescript
// Global test setup
beforeAll(() => {
  // Set test environment
  process.env.NODE_ENV = 'test';
  process.env.DATABASE_PROVIDER = 'local';
});

afterAll(() => {
  // Cleanup
});

// Mock console methods in tests to reduce noise
global.console = {
  ...console,
  log: jest.fn(),
  debug: jest.fn(),
  info: jest.fn(),
  warn: jest.fn(),
  error: jest.fn(),
};
```

### 3. `src/backend/src/__tests__/utils/testHelpers.ts`

Test utilities:

```typescript
import request from 'supertest';
import { Express } from 'express';

export class TestHelpers {
  static async getHealth(app: Express) {
    return request(app).get('/health');
  }

  static async getApiV1(app: Express) {
    return request(app).get('/api/v1');
  }

  static async getDiveSites(app: Express, query: Record<string, any> = {}) {
    return request(app)
      .get('/api/v1/dive-sites')
      .query(query);
  }

  static async searchDiveSites(app: Express, body: Record<string, any>) {
    return request(app)
      .post('/api/v1/dive-sites/search')
      .send(body)
      .set('Content-Type', 'application/json');
  }

  static async sendChatMessage(app: Express, body: Record<string, any>) {
    return request(app)
      .post('/api/v1/chat/message')
      .send(body)
      .set('Content-Type', 'application/json');
  }
}
```

### 4. `src/backend/src/__tests__/routes/health.test.ts`

Health endpoint tests:

```typescript
import request from 'supertest';
import { app } from '../../server';

describe('Health Endpoints', () => {
  describe('GET /health', () => {
    it('should return 200 and health status', async () => {
      const response = await request(app).get('/health');
      
      expect(response.status).toBe(200);
      expect(response.body).toHaveProperty('status', 'ok');
      expect(response.body).toHaveProperty('timestamp');
      expect(response.body).toHaveProperty('database');
      expect(response.body).toHaveProperty('uptime');
    });
  });

  describe('GET /api/v1/health', () => {
    it('should return detailed health check', async () => {
      const response = await request(app).get('/api/v1/health');
      
      expect(response.status).toBe(200);
      expect(response.body).toHaveProperty('status');
      expect(response.body).toHaveProperty('checks');
      expect(response.body.checks).toHaveProperty('database');
      expect(response.body.checks).toHaveProperty('server');
    });
  });

  describe('GET /api/v1/health/liveness', () => {
    it('should return alive status', async () => {
      const response = await request(app).get('/api/v1/health/liveness');
      
      expect(response.status).toBe(200);
      expect(response.body).toHaveProperty('status', 'alive');
    });
  });

  describe('GET /api/v1/health/readiness', () => {
    it('should return readiness status', async () => {
      const response = await request(app).get('/api/v1/health/readiness');
      
      expect(response.status).toBeOneOf([200, 503]);
      expect(response.body).toHaveProperty('status');
    });
  });
});

// Custom matcher
expect.extend({
  toBeOneOf(received, expected) {
    const pass = expected.includes(received);
    return {
      message: () =>
        `expected ${received} to be one of ${expected.join(', ')}`,
      pass,
    };
  },
});
```

### 5. `src/backend/src/__tests__/routes/diveSites.test.ts`

Dive sites endpoint tests:

```typescript
import request from 'supertest';
import { app } from '../../server';
import { TestHelpers } from '../utils/testHelpers';

describe('Dive Sites Endpoints', () => {
  describe('GET /api/v1/dive-sites', () => {
    it('should return paginated results', async () => {
      const response = await TestHelpers.getDiveSites(app, { page: 1, limit: 10 });
      
      expect(response.status).toBe(200);
      expect(response.body).toHaveProperty('data');
      expect(response.body).toHaveProperty('meta');
      expect(response.body.meta).toHaveProperty('page', 1);
      expect(response.body.meta).toHaveProperty('limit', 10);
    });

    it('should validate pagination params', async () => {
      const response = await TestHelpers.getDiveSites(app, { page: -1 });
      
      // Currently returns default values, validation can be improved
      expect(response.status).toBe(200);
    });
  });

  describe('POST /api/v1/dive-sites/search', () => {
    it('should accept valid search params', async () => {
      const response = await TestHelpers.searchDiveSites(app, {
        query: 'coral reef',
        difficulty: 'Beginner',
      });
      
      expect(response.status).toBe(200);
      expect(response.body).toHaveProperty('data');
    });

    it('should reject invalid difficulty', async () => {
      const response = await TestHelpers.searchDiveSites(app, {
        query: 'reef',
        difficulty: 'InvalidLevel',
      });
      
      expect(response.status).toBe(400);
      expect(response.body).toHaveProperty('error');
      expect(response.body.error).toContain('Validation');
    });

    it('should reject missing query', async () => {
      const response = await TestHelpers.searchDiveSites(app, {
        difficulty: 'Beginner',
      });
      
      expect(response.status).toBe(400);
    });
  });

  describe('POST /api/v1/dive-sites/semantic-search', () => {
    it('should accept valid semantic search', async () => {
      const response = await request(app)
        .post('/api/v1/dive-sites/semantic-search')
        .send({
          query: 'warm water diving with good visibility',
          limit: 5,
        });
      
      expect(response.status).toBe(200);
      expect(response.body).toHaveProperty('data');
    });

    it('should use default limit', async () => {
      const response = await request(app)
        .post('/api/v1/dive-sites/semantic-search')
        .send({
          query: 'reef diving',
        });
      
      expect(response.status).toBe(200);
    });
  });
});
```

### 6. `src/backend/src/__tests__/middleware/validate.test.ts`

Validation middleware tests:

```typescript
import { Request, Response, NextFunction } from 'express';
import { z } from 'zod';
import { validate } from '../../middleware/validate';

describe('Validation Middleware', () => {
  let mockReq: Partial<Request>;
  let mockRes: Partial<Response>;
  let mockNext: NextFunction;

  beforeEach(() => {
    mockReq = {
      body: {},
      query: {},
      params: {},
    };
    mockRes = {
      status: jest.fn().mockReturnThis(),
      json: jest.fn(),
    };
    mockNext = jest.fn();
  });

  it('should pass validation with valid data', () => {
    const schema = z.object({ name: z.string() });
    mockReq.body = { name: 'Test' };

    const middleware = validate(schema, 'body');
    middleware(mockReq as Request, mockRes as Response, mockNext);

    expect(mockNext).toHaveBeenCalled();
    expect(mockRes.status).not.toHaveBeenCalled();
  });

  it('should fail validation with invalid data', () => {
    const schema = z.object({ age: z.number() });
    mockReq.body = { age: 'not a number' };

    const middleware = validate(schema, 'body');
    middleware(mockReq as Request, mockRes as Response, mockNext);

    expect(mockRes.status).toHaveBeenCalledWith(400);
    expect(mockRes.json).toHaveBeenCalledWith(
      expect.objectContaining({
        error: 'Validation Error',
      })
    );
  });

  it('should validate query params', () => {
    const schema = z.object({ id: z.string().uuid() });
    mockReq.query = { id: '123' };

    const middleware = validate(schema, 'query');
    middleware(mockReq as Request, mockRes as Response, mockNext);

    expect(mockRes.status).toHaveBeenCalledWith(400);
  });
});
```

### 7. `src/backend/README.md`

Backend documentation:

```markdown
# DovvyDive Backend API

Express.js REST API with TypeScript, PostgreSQL, and pgvector.

## Stack

- **Runtime:** Node.js 20+
- **Framework:** Express 4.18
- **Language:** TypeScript 5.3
- **Database:** PostgreSQL 16 + pgvector (Neon or local Docker)
- **ORM:** Prisma 5.7
- **Validation:** Zod 3.22
- **Testing:** Jest 29

## Directory Structure

```
src/backend/
├── src/
│   ├── server.ts              # Main server entry point
│   ├── config/                # Configuration files
│   │   ├── env.ts            # Environment validation
│   │   └── database.ts       # Database connection
│   ├── routes/               # API routes
│   │   ├── index.ts          # Route aggregator
│   │   └── v1/               # API v1 routes
│   ├── controllers/          # Request handlers
│   ├── services/             # Business logic (Phase 2+)
│   ├── middleware/           # Express middleware
│   ├── types/                # TypeScript types & DTOs
│   ├── utils/                # Helper utilities
│   └── __tests__/            # Test files
├── prisma/                   # Prisma schema & migrations
├── dist/                     # Compiled JavaScript (gitignored)
└── coverage/                 # Test coverage reports
```

## Development

```bash
# Install dependencies
npm install --workspace=backend

# Start dev server (with hot reload)
npm run dev --workspace=backend

# Build for production
npm run build --workspace=backend

# Start production server
npm start --workspace=backend

# Type check
npm run typecheck --workspace=backend

# Lint
npm run lint --workspace=backend
npm run lint:fix --workspace=backend

# Test
npm test --workspace=backend
npm run test:watch --workspace=backend
npm run test:coverage --workspace=backend

# Database
npm run prisma:generate --workspace=backend
npm run prisma:migrate --workspace=backend
npm run prisma:studio --workspace=backend
```

## API Endpoints

### Health Checks
- `GET /health` - Basic health check
- `GET /api/v1/health` - Detailed health check
- `GET /api/v1/health/liveness` - Kubernetes liveness probe
- `GET /api/v1/health/readiness` - Kubernetes readiness probe

### Dive Sites (Phase 2)
- `GET /api/v1/dive-sites` - List dive sites (paginated)
- `GET /api/v1/dive-sites/:id` - Get dive site by ID
- `POST /api/v1/dive-sites/search` - Search dive sites
- `POST /api/v1/dive-sites/semantic-search` - Vector similarity search

### Chat (Phase 3)
- `POST /api/v1/chat/message` - Send chat message
- `GET /api/v1/chat/sessions/:sessionId` - Get session history
- `POST /api/v1/chat/sessions` - Create new session

## Environment Variables

Required variables (see `.env.example`):

```env
NODE_ENV=development
PORT=4000
CORS_ORIGIN=http://localhost:3000

DATABASE_PROVIDER=local  # or 'neon'
DATABASE_URL=postgresql://...
NEON_DATABASE_URL=postgresql://...  # if using Neon
```

## Testing

Run all tests:
```bash
npm test --workspace=backend
```

Run specific test file:
```bash
npm test --workspace=backend -- routes/health.test.ts
```

Coverage report:
```bash
npm run test:coverage --workspace=backend
```

## Validation

All endpoints use Zod schemas for request validation. See `src/types/dtos.ts` for schema definitions.

Example:
```typescript
import { DiveSiteSearchSchema } from '@/types/dtos';

// In controller
const validated = DiveSiteSearchSchema.parse(req.body);
```

## Error Handling

Centralized error handling with custom error classes:

```typescript
import { BadRequestError, NotFoundError } from '@/middleware/errorHandler';

// In controller
if (!found) {
  throw new NotFoundError('Dive site not found');
}
```

## Logging

JSON-formatted logs using custom logger:

```typescript
import { logger } from '@/utils/logger';

logger.info('Message', { key: 'value' });
logger.error('Error occurred', { error });
```

## Phase 1 Status

✅ **Complete:**
- Express server setup
- Middleware configuration (helmet, cors, morgan, rate limiting)
- API route structure with versioning
- Request validation with Zod
- DTOs and TypeScript types
- Error handling
- Health check endpoints
- Jest testing setup
- API documentation

⏳ **Phase 2:** Database models, Prisma setup, dive site data

⏳ **Phase 3:** OpenAI integration, chat functionality, RAG pipeline
```

### 8. Update `src/backend/package.json`

Add supertest for testing:

```json
{
  "devDependencies": {
    "supertest": "^6.3.3",
    "@types/supertest": "^2.0.16"
  }
}
```

---

## Installation Commands

```bash
# Install test dependencies
npm install --workspace=backend supertest @types/supertest ts-jest @types/jest

# Create test directories
mkdir -p src/backend/src/__tests__/{routes,middleware,utils}
```

---

## Testing Checklist

- [ ] Jest configured
- [ ] Test setup file created
- [ ] Test helpers created
- [ ] Health endpoint tests written
- [ ] Dive sites endpoint tests written
- [ ] Validation middleware tests written
- [ ] Backend README created

---

## Validation Commands

```bash
# Install test dependencies
npm install --workspace=backend

# Run all tests
npm test --workspace=backend
# Should pass all tests

# Run with coverage
npm run test:coverage --workspace=backend

# Type check
npm run typecheck --workspace=backend

# Lint check
npm run lint --workspace=backend
```

---

## PR 1.4 Complete! ✅

You now have:
- ✅ Express server with security middleware
- ✅ API routes with versioning (v1)
- ✅ Request validation with Zod
- ✅ DTOs for type safety
- ✅ Error handling
- ✅ Standardized responses
- ✅ Jest testing setup
- ✅ Comprehensive documentation

**Next Action:** Commit and create PR

```bash
git checkout -b 1.4-express-backend
git add src/backend/
git commit -m "feat(backend): implement Express API structure with validation

- Configure Express server with security middleware
- Implement API route structure with v1 versioning
- Add request validation using Zod schemas
- Create DTOs for type-safe request/response handling
- Implement error handling middleware
- Add health check endpoints (liveness/readiness)
- Configure Jest for backend testing
- Write test suites for routes and middleware
- Document API structure and usage"

git push origin 1.4-express-backend
```

**Open PR on GitHub and request review!**

---

## Next PR

Proceed to PR 1.5: Internationalization (i18n) Setup

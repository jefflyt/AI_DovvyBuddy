# Step 2: API Routes Structure & Versioning

**PR:** 1.4 Express Backend API Structure  
**Step:** 2 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Create RESTful API route structure with versioning (v1), implement router organization, and define placeholder endpoints for dive sites and chat.

---

## Files to Create

### 1. `src/backend/src/routes/index.ts`

Main router with versioning:

```typescript
import { Router } from 'express';
import { v1Router } from './v1';

const router = Router();

// API version 1
router.use('/v1', v1Router);

// Future versions
// router.use('/v2', v2Router);

export { router as apiRouter };
```

### 2. `src/backend/src/routes/v1/index.ts`

V1 route aggregator:

```typescript
import { Router } from 'express';
import { diveSitesRouter } from './diveSites';
import { chatRouter } from './chat';
import { healthRouter } from './health';

const router = Router();

// Mount route modules
router.use('/dive-sites', diveSitesRouter);
router.use('/chat', chatRouter);
router.use('/health', healthRouter);

// V1 API info
router.get('/', (req, res) => {
  res.json({
    version: '1.0.0',
    name: 'DovvyDive API v1',
    endpoints: {
      diveSites: '/api/v1/dive-sites',
      chat: '/api/v1/chat',
      health: '/api/v1/health',
    },
  });
});

export { router as v1Router };
```

### 3. `src/backend/src/routes/v1/diveSites.ts`

Dive sites routes (placeholder):

```typescript
import { Router } from 'express';
import { DiveSitesController } from '../../controllers/diveSitesController';

const router = Router();

/**
 * @route   GET /api/v1/dive-sites
 * @desc    Get all dive sites (with pagination)
 * @access  Public
 */
router.get('/', DiveSitesController.getAll);

/**
 * @route   GET /api/v1/dive-sites/:id
 * @desc    Get dive site by ID
 * @access  Public
 */
router.get('/:id', DiveSitesController.getById);

/**
 * @route   POST /api/v1/dive-sites/search
 * @desc    Search dive sites by criteria
 * @access  Public
 */
router.post('/search', DiveSitesController.search);

/**
 * @route   POST /api/v1/dive-sites/semantic-search
 * @desc    Semantic search using vector embeddings
 * @access  Public
 */
router.post('/semantic-search', DiveSitesController.semanticSearch);

export { router as diveSitesRouter };
```

### 4. `src/backend/src/routes/v1/chat.ts`

Chat routes (placeholder):

```typescript
import { Router } from 'express';
import { ChatController } from '../../controllers/chatController';

const router = Router();

/**
 * @route   POST /api/v1/chat/message
 * @desc    Send a chat message and get AI response
 * @access  Public
 */
router.post('/message', ChatController.sendMessage);

/**
 * @route   GET /api/v1/chat/sessions/:sessionId
 * @desc    Get chat session history
 * @access  Public
 */
router.get('/sessions/:sessionId', ChatController.getSession);

/**
 * @route   POST /api/v1/chat/sessions
 * @desc    Create a new chat session
 * @access  Public
 */
router.post('/sessions', ChatController.createSession);

export { router as chatRouter };
```

### 5. `src/backend/src/routes/v1/health.ts`

Health check routes:

```typescript
import { Router } from 'express';
import { DatabaseConnection } from '../../config/database';

const router = Router();

/**
 * @route   GET /api/v1/health
 * @desc    Detailed health check
 * @access  Public
 */
router.get('/', async (req, res) => {
  try {
    const dbHealth = await DatabaseConnection.getInstance().healthCheck();
    
    res.json({
      status: 'healthy',
      timestamp: new Date().toISOString(),
      checks: {
        database: {
          status: dbHealth.connected ? 'up' : 'down',
          latency: `${dbHealth.latency}ms`,
          provider: process.env.DATABASE_PROVIDER || 'unknown',
        },
        server: {
          uptime: `${Math.floor(process.uptime())}s`,
          memoryUsage: process.memoryUsage(),
          nodeVersion: process.version,
        },
      },
    });
  } catch (error) {
    res.status(503).json({
      status: 'unhealthy',
      timestamp: new Date().toISOString(),
      error: error instanceof Error ? error.message : 'Unknown error',
    });
  }
});

/**
 * @route   GET /api/v1/health/liveness
 * @desc    Kubernetes liveness probe
 * @access  Public
 */
router.get('/liveness', (req, res) => {
  res.status(200).json({ status: 'alive' });
});

/**
 * @route   GET /api/v1/health/readiness
 * @desc    Kubernetes readiness probe
 * @access  Public
 */
router.get('/readiness', async (req, res) => {
  try {
    const dbHealth = await DatabaseConnection.getInstance().healthCheck();
    if (dbHealth.connected) {
      res.status(200).json({ status: 'ready' });
    } else {
      res.status(503).json({ status: 'not ready' });
    }
  } catch (error) {
    res.status(503).json({ status: 'not ready' });
  }
});

export { router as healthRouter };
```

### 6. `src/backend/src/controllers/diveSitesController.ts`

Dive sites controller (stub):

```typescript
import { Request, Response, NextFunction } from 'express';
import { logger } from '../utils/logger';

export class DiveSitesController {
  static async getAll(req: Request, res: Response, next: NextFunction) {
    try {
      // TODO: Implement in Phase 2
      logger.info('GET /dive-sites - returning mock data');
      
      res.json({
        data: [],
        meta: {
          total: 0,
          page: 1,
          limit: 10,
        },
        message: 'Dive sites endpoint - implementation pending Phase 2',
      });
    } catch (error) {
      next(error);
    }
  }

  static async getById(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      logger.info(`GET /dive-sites/${id} - returning mock data`);
      
      res.json({
        data: null,
        message: `Dive site ${id} - implementation pending Phase 2`,
      });
    } catch (error) {
      next(error);
    }
  }

  static async search(req: Request, res: Response, next: NextFunction) {
    try {
      logger.info('POST /dive-sites/search - returning mock data');
      
      res.json({
        data: [],
        message: 'Search endpoint - implementation pending Phase 2',
      });
    } catch (error) {
      next(error);
    }
  }

  static async semanticSearch(req: Request, res: Response, next: NextFunction) {
    try {
      logger.info('POST /dive-sites/semantic-search - returning mock data');
      
      res.json({
        data: [],
        message: 'Semantic search endpoint - implementation pending Phase 2',
      });
    } catch (error) {
      next(error);
    }
  }
}
```

### 7. `src/backend/src/controllers/chatController.ts`

Chat controller (stub):

```typescript
import { Request, Response, NextFunction } from 'express';
import { logger } from '../utils/logger';

export class ChatController {
  static async sendMessage(req: Request, res: Response, next: NextFunction) {
    try {
      const { message } = req.body;
      logger.info('POST /chat/message - returning mock response');
      
      res.json({
        data: {
          message: 'Chat functionality coming in Phase 3!',
          userMessage: message,
        },
        message: 'Chat endpoint - implementation pending Phase 3',
      });
    } catch (error) {
      next(error);
    }
  }

  static async getSession(req: Request, res: Response, next: NextFunction) {
    try {
      const { sessionId } = req.params;
      logger.info(`GET /chat/sessions/${sessionId} - returning mock data`);
      
      res.json({
        data: {
          sessionId,
          messages: [],
        },
        message: 'Session retrieval - implementation pending Phase 3',
      });
    } catch (error) {
      next(error);
    }
  }

  static async createSession(req: Request, res: Response, next: NextFunction) {
    try {
      logger.info('POST /chat/sessions - creating mock session');
      
      res.status(201).json({
        data: {
          sessionId: `session_${Date.now()}`,
          createdAt: new Date().toISOString(),
        },
        message: 'Session creation - implementation pending Phase 3',
      });
    } catch (error) {
      next(error);
    }
  }
}
```

### 8. Update `src/backend/src/server.ts`

Mount API router:

```typescript
// Add after middleware setup, before health check:
import { apiRouter } from './routes';

// Mount API routes
app.use('/api', apiRouter);
```

---

## Installation Commands

```bash
# No new dependencies needed
# Create controller directories
mkdir -p src/backend/src/controllers
mkdir -p src/backend/src/routes/v1
```

---

## Testing Checklist

- [ ] Route structure created with versioning
- [ ] Dive sites routes defined
- [ ] Chat routes defined
- [ ] Health check routes created
- [ ] Controllers created with stub implementations
- [ ] API router mounted in server.ts

---

## Validation Commands

```bash
# Type check
npm run typecheck --workspace=backend

# Start server
npm run dev --workspace=backend

# Test API root
curl http://localhost:4000/api/v1
# Should return API info with endpoints

# Test dive sites endpoint
curl http://localhost:4000/api/v1/dive-sites
# Should return mock response

# Test chat endpoint
curl -X POST http://localhost:4000/api/v1/chat/message \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello"}'
# Should return mock response

# Test health check
curl http://localhost:4000/api/v1/health
# Should return detailed health info

# Test liveness probe
curl http://localhost:4000/api/v1/health/liveness

# Test readiness probe
curl http://localhost:4000/api/v1/health/readiness
```

---

## Next Step

Proceed to Step 3: Request Validation & DTOs

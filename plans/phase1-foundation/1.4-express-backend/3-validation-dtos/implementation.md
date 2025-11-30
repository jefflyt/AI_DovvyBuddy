# Step 3: Request Validation & DTOs

**PR:** 1.4 Express Backend API Structure  
**Step:** 3 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Implement request validation middleware using Zod, create Data Transfer Objects (DTOs) for type safety, and add validation to API endpoints.

---

## Files to Create

### 1. `src/backend/src/middleware/validate.ts`

Validation middleware factory:

```typescript
import { Request, Response, NextFunction } from 'express';
import { ZodSchema, ZodError } from 'zod';
import { BadRequestError } from './errorHandler';

export type ValidateSource = 'body' | 'query' | 'params';

/**
 * Create validation middleware for request data
 */
export function validate(schema: ZodSchema, source: ValidateSource = 'body') {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      const dataToValidate = req[source];
      const validated = schema.parse(dataToValidate);
      
      // Replace request data with validated data
      req[source] = validated;
      
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        const formattedErrors = error.errors.map((err) => ({
          field: err.path.join('.'),
          message: err.message,
          code: err.code,
        }));
        
        res.status(400).json({
          error: 'Validation Error',
          message: 'Invalid request data',
          details: formattedErrors,
        });
      } else {
        next(error);
      }
    }
  };
}
```

### 2. `src/backend/src/types/dtos.ts`

Data Transfer Objects:

```typescript
import { z } from 'zod';

// ===========================
// Common DTOs
// ===========================

export const PaginationQuerySchema = z.object({
  page: z.string().optional().default('1').transform(Number),
  limit: z.string().optional().default('10').transform(Number),
  sortBy: z.string().optional(),
  sortOrder: z.enum(['asc', 'desc']).optional().default('asc'),
});

export type PaginationQuery = z.infer<typeof PaginationQuerySchema>;

// ===========================
// Dive Site DTOs
// ===========================

export const DiveSiteSearchSchema = z.object({
  query: z.string().min(1).max(500),
  location: z.string().optional(),
  country: z.string().optional(),
  difficulty: z.enum(['Beginner', 'Intermediate', 'Advanced', 'Expert']).optional(),
  maxDepth: z.number().positive().optional(),
  minDepth: z.number().positive().optional(),
  diveTypes: z.array(z.string()).optional(),
});

export type DiveSiteSearch = z.infer<typeof DiveSiteSearchSchema>;

export const SemanticSearchSchema = z.object({
  query: z.string().min(1).max(500),
  limit: z.number().int().positive().max(50).optional().default(10),
  threshold: z.number().min(0).max(1).optional().default(0.7),
});

export type SemanticSearch = z.infer<typeof SemanticSearchSchema>;

export const DiveSiteIdSchema = z.object({
  id: z.string().uuid(),
});

export type DiveSiteId = z.infer<typeof DiveSiteIdSchema>;

// ===========================
// Chat DTOs
// ===========================

export const ChatMessageSchema = z.object({
  message: z.string().min(1).max(500),
  sessionId: z.string().optional(),
  language: z.enum(['en', 'es', 'fr', 'de', 'pt', 'it', 'ja', 'zh']).optional().default('en'),
});

export type ChatMessage = z.infer<typeof ChatMessageSchema>;

export const CreateSessionSchema = z.object({
  language: z.enum(['en', 'es', 'fr', 'de', 'pt', 'it', 'ja', 'zh']).optional().default('en'),
  metadata: z.record(z.unknown()).optional(),
});

export type CreateSession = z.infer<typeof CreateSessionSchema>;

export const SessionIdSchema = z.object({
  sessionId: z.string().uuid(),
});

export type SessionId = z.infer<typeof SessionIdSchema>;

// ===========================
// Response DTOs
// ===========================

export interface ApiResponse<T> {
  data?: T;
  error?: string;
  message?: string;
  meta?: {
    page?: number;
    limit?: number;
    total?: number;
  };
}

export interface DiveSiteResponse {
  id: string;
  name: string;
  location: string;
  country: string;
  coordinates: {
    lat: number;
    lng: number;
  };
  difficulty: string;
  maxDepth: number;
  description: string;
  imageUrl?: string;
  rating?: number;
  createdAt: string;
  updatedAt: string;
}

export interface ChatMessageResponse {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  timestamp: string;
  diveSites?: DiveSiteResponse[];
}

export interface ChatSessionResponse {
  id: string;
  messages: ChatMessageResponse[];
  language: string;
  createdAt: string;
  updatedAt: string;
}
```

### 3. Update `src/backend/src/routes/v1/diveSites.ts`

Add validation:

```typescript
import { Router } from 'express';
import { DiveSitesController } from '../../controllers/diveSitesController';
import { validate } from '../../middleware/validate';
import {
  PaginationQuerySchema,
  DiveSiteSearchSchema,
  SemanticSearchSchema,
  DiveSiteIdSchema,
} from '../../types/dtos';

const router = Router();

router.get(
  '/',
  validate(PaginationQuerySchema, 'query'),
  DiveSitesController.getAll
);

router.get(
  '/:id',
  validate(DiveSiteIdSchema, 'params'),
  DiveSitesController.getById
);

router.post(
  '/search',
  validate(DiveSiteSearchSchema, 'body'),
  DiveSitesController.search
);

router.post(
  '/semantic-search',
  validate(SemanticSearchSchema, 'body'),
  DiveSitesController.semanticSearch
);

export { router as diveSitesRouter };
```

### 4. Update `src/backend/src/routes/v1/chat.ts`

Add validation:

```typescript
import { Router } from 'express';
import { ChatController } from '../../controllers/chatController';
import { validate } from '../../middleware/validate';
import {
  ChatMessageSchema,
  CreateSessionSchema,
  SessionIdSchema,
} from '../../types/dtos';

const router = Router();

router.post(
  '/message',
  validate(ChatMessageSchema, 'body'),
  ChatController.sendMessage
);

router.get(
  '/sessions/:sessionId',
  validate(SessionIdSchema, 'params'),
  ChatController.getSession
);

router.post(
  '/sessions',
  validate(CreateSessionSchema, 'body'),
  ChatController.createSession
);

export { router as chatRouter };
```

### 5. `src/backend/src/utils/apiResponse.ts`

Standardized response helper:

```typescript
import { Response } from 'express';
import { ApiResponse } from '../types/dtos';

export class ApiResponseUtil {
  static success<T>(
    res: Response,
    data: T,
    message?: string,
    statusCode: number = 200
  ): void {
    const response: ApiResponse<T> = {
      data,
      message,
    };
    res.status(statusCode).json(response);
  }

  static successWithMeta<T>(
    res: Response,
    data: T,
    meta: { page?: number; limit?: number; total?: number },
    message?: string
  ): void {
    const response: ApiResponse<T> = {
      data,
      meta,
      message,
    };
    res.status(200).json(response);
  }

  static created<T>(res: Response, data: T, message?: string): void {
    const response: ApiResponse<T> = {
      data,
      message: message || 'Resource created successfully',
    };
    res.status(201).json(response);
  }

  static noContent(res: Response): void {
    res.status(204).send();
  }

  static error(
    res: Response,
    message: string,
    statusCode: number = 500
  ): void {
    const response: ApiResponse<null> = {
      error: message,
      message,
    };
    res.status(statusCode).json(response);
  }
}
```

### 6. Update `src/backend/src/controllers/diveSitesController.ts`

Use DTOs and response helper:

```typescript
import { Request, Response, NextFunction } from 'express';
import { logger } from '../utils/logger';
import { ApiResponseUtil } from '../utils/apiResponse';
import { PaginationQuery, DiveSiteSearch, SemanticSearch } from '../types/dtos';

export class DiveSitesController {
  static async getAll(req: Request, res: Response, next: NextFunction) {
    try {
      const { page, limit, sortBy, sortOrder } = req.query as unknown as PaginationQuery;
      
      logger.info('GET /dive-sites', { page, limit, sortBy, sortOrder });
      
      ApiResponseUtil.successWithMeta(
        res,
        [],
        { page, limit, total: 0 },
        'Dive sites endpoint - implementation pending Phase 2'
      );
    } catch (error) {
      next(error);
    }
  }

  static async getById(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      logger.info(`GET /dive-sites/${id}`);
      
      ApiResponseUtil.success(
        res,
        null,
        `Dive site ${id} - implementation pending Phase 2`
      );
    } catch (error) {
      next(error);
    }
  }

  static async search(req: Request, res: Response, next: NextFunction) {
    try {
      const searchParams = req.body as DiveSiteSearch;
      logger.info('POST /dive-sites/search', searchParams);
      
      ApiResponseUtil.success(
        res,
        [],
        'Search endpoint - implementation pending Phase 2'
      );
    } catch (error) {
      next(error);
    }
  }

  static async semanticSearch(req: Request, res: Response, next: NextFunction) {
    try {
      const searchParams = req.body as SemanticSearch;
      logger.info('POST /dive-sites/semantic-search', searchParams);
      
      ApiResponseUtil.success(
        res,
        [],
        'Semantic search endpoint - implementation pending Phase 2'
      );
    } catch (error) {
      next(error);
    }
  }
}
```

---

## Testing Checklist

- [ ] Validation middleware created
- [ ] DTOs defined with Zod schemas
- [ ] Validation applied to routes
- [ ] Response helper created
- [ ] Controllers updated to use DTOs

---

## Validation Commands

```bash
# Type check
npm run typecheck --workspace=backend

# Start server
npm run dev --workspace=backend

# Test valid request
curl -X POST http://localhost:4000/api/v1/dive-sites/search \
  -H "Content-Type: application/json" \
  -d '{"query": "coral reef", "difficulty": "Beginner"}'
# Should succeed

# Test invalid difficulty
curl -X POST http://localhost:4000/api/v1/dive-sites/search \
  -H "Content-Type: application/json" \
  -d '{"query": "reef", "difficulty": "Invalid"}'
# Should return 400 validation error

# Test missing required field
curl -X POST http://localhost:4000/api/v1/chat/message \
  -H "Content-Type: application/json" \
  -d '{}'
# Should return 400 with field error

# Test pagination query params
curl "http://localhost:4000/api/v1/dive-sites?page=2&limit=20"
# Should succeed with validated params
```

---

## Next Step

Proceed to Step 4: Backend Testing Setup & Documentation

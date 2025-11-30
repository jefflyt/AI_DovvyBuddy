# Step 4: Backend i18n Integration & Testing

**PR:** 1.5 Internationalization Setup  
**Step:** 4 of 4  
**Estimated Time:** 1 hour

---

## Goal

Integrate i18n into backend error responses, create translation helper utilities, write tests for language detection, and document i18n usage.

---

## Files to Create

### 1. `src/backend/src/utils/i18nHelper.ts`

Translation helper for controllers:

```typescript
import { Request } from 'express';
import i18next from '../config/i18n';

export class I18nHelper {
  /**
   * Get translated message based on request language
   */
  static translate(
    req: Request,
    key: string,
    options?: Record<string, unknown>
  ): string {
    const language = req.language || 'en';
    return i18next.t(key, { lng: language, ...options });
  }

  /**
   * Get current language from request
   */
  static getLanguage(req: Request): string {
    return req.language || 'en';
  }

  /**
   * Translate error message
   */
  static translateError(
    req: Request,
    errorKey: string,
    params?: Record<string, unknown>
  ): string {
    return this.translate(req, `errors:${errorKey}`, params);
  }

  /**
   * Translate validation error
   */
  static translateValidation(
    req: Request,
    field: string,
    type: string,
    params?: Record<string, unknown>
  ): string {
    return this.translate(req, `errors:validation.${type}`, {
      field,
      ...params,
    });
  }
}
```

### 2. Update `src/backend/src/middleware/errorHandler.ts`

Add i18n to error messages:

```typescript
import { Request, Response, NextFunction } from 'express';
import { logger } from '../utils/logger';
import { I18nHelper } from '../utils/i18nHelper';

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
  
  // Translate error message if possible
  let message = err.message;
  try {
    // Try to translate error message
    if (err.code) {
      message = I18nHelper.translateError(req, `http.${statusCode}`) || err.message;
    }
  } catch (translationError) {
    // Fall back to original message
    message = err.message;
  }

  // Log error details
  logger.error({
    message: err.message,
    stack: err.stack,
    statusCode,
    path: req.path,
    method: req.method,
    code: err.code,
    language: req.language,
  });

  // Build response
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

// Error classes remain the same
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

### 3. Update `src/backend/src/middleware/validate.ts`

Add i18n to validation errors:

```typescript
import { Request, Response, NextFunction } from 'express';
import { ZodSchema, ZodError } from 'zod';
import { I18nHelper } from '../utils/i18nHelper';

export type ValidateSource = 'body' | 'query' | 'params';

export function validate(schema: ZodSchema, source: ValidateSource = 'body') {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      const dataToValidate = req[source];
      const validated = schema.parse(dataToValidate);
      
      req[source] = validated;
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        const formattedErrors = error.errors.map((err) => ({
          field: err.path.join('.'),
          message: I18nHelper.translateValidation(
            req,
            err.path.join('.'),
            err.code === 'invalid_type' ? 'invalid' : 'required'
          ),
          code: err.code,
        }));
        
        res.status(400).json({
          error: I18nHelper.translateError(req, 'validation.invalid'),
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

### 4. `src/backend/src/__tests__/i18n.test.ts`

i18n tests:

```typescript
import i18next from '../config/i18n';
import { I18nHelper } from '../utils/i18nHelper';
import { Request } from 'express';

describe('i18n Configuration', () => {
  it('should initialize with English as default', () => {
    expect(i18next.language).toBe('en');
  });

  it('should support all configured languages', () => {
    const supportedLanguages = ['en', 'es', 'fr', 'de', 'pt', 'it', 'ja', 'zh'];
    
    supportedLanguages.forEach((lang) => {
      expect(i18next.options.supportedLngs).toContain(lang);
    });
  });

  it('should translate error messages in English', async () => {
    const translated = await i18next.t('errors:http.404', { lng: 'en' });
    expect(translated).toBe('Resource not found');
  });

  it('should translate error messages in Spanish', async () => {
    const translated = await i18next.t('errors:http.404', { lng: 'es' });
    expect(translated).toBe('Recurso no encontrado');
  });

  it('should fall back to English for missing translations', async () => {
    const translated = await i18next.t('errors:nonexistent.key', {
      lng: 'de',
      defaultValue: 'Default message',
    });
    expect(translated).toBe('Default message');
  });
});

describe('I18nHelper', () => {
  const mockRequest = (language: string) =>
    ({ language } as Request);

  it('should get language from request', () => {
    const req = mockRequest('es');
    expect(I18nHelper.getLanguage(req)).toBe('es');
  });

  it('should default to English when language not set', () => {
    const req = {} as Request;
    expect(I18nHelper.getLanguage(req)).toBe('en');
  });

  it('should translate validation error with field name', async () => {
    const req = mockRequest('en');
    const message = I18nHelper.translateValidation(req, 'email', 'required');
    expect(message).toContain('email');
  });
});
```

### 5. `docs/i18n-guide.md`

i18n documentation:

```markdown
# Internationalization (i18n) Guide

DovvyDive supports 8 languages across frontend and backend.

## Supported Languages

| Code | Language | Native Name |
|------|----------|-------------|
| en   | English  | English     |
| es   | Spanish  | Español     |
| fr   | French   | Français    |
| de   | German   | Deutsch     |
| pt   | Portuguese | Português |
| it   | Italian  | Italiano    |
| ja   | Japanese | 日本語      |
| zh   | Chinese  | 中文        |

## Frontend Usage

### Using Translations in Components

```tsx
import { useTranslation } from 'react-i18next';

export function MyComponent() {
  const { t } = useTranslation('common');
  
  return <h1>{t('app.name')}</h1>;
}
```

### Multiple Namespaces

```tsx
const { t: tCommon } = useTranslation('common');
const { t: tChat } = useTranslation('chat');

<div>
  <h1>{tCommon('app.name')}</h1>
  <p>{tChat('placeholder')}</p>
</div>
```

### With Interpolation

```tsx
{t('footer.copyright', { year: 2024 })}
// Output: © 2024 DovvyDive. All rights reserved.
```

### Changing Language

```tsx
import { useLanguage } from '@/hooks/useLanguage';

const { changeLanguage, currentLanguage } = useLanguage();

<button onClick={() => changeLanguage('es')}>
  Español
</button>
```

## Backend Usage

### In Controllers

```typescript
import { I18nHelper } from '@/utils/i18nHelper';

export class MyController {
  static async myMethod(req: Request, res: Response) {
    const message = I18nHelper.translate(req, 'chat.welcome');
    res.json({ message });
  }
}
```

### Error Messages

```typescript
const errorMsg = I18nHelper.translateError(req, 'http.404');
throw new NotFoundError(errorMsg);
```

### Validation Errors

```typescript
const validationMsg = I18nHelper.translateValidation(
  req,
  'email',
  'invalid'
);
```

## Adding New Translations

### 1. Add to Frontend

Create or update file in `public/locales/{language}/{namespace}.json`:

```json
{
  "newKey": "Translated value"
}
```

### 2. Add to Backend

Create or update file in `src/backend/locales/{language}/{namespace}.json`:

```json
{
  "newError": "Error message"
}
```

### 3. Use in Code

Frontend:
```tsx
{t('newKey')}
```

Backend:
```typescript
I18nHelper.translate(req, 'namespace:newError')
```

## Language Detection

### Frontend
1. localStorage (i18nextLng)
2. Browser language
3. HTML lang attribute

### Backend
1. Query parameter (?lng=es)
2. Accept-Language header
3. Cookie (i18next)

## Testing Translations

```bash
# Frontend
npm run dev --workspace=frontend
# Test language switcher in browser

# Backend
curl -H "Accept-Language: es" http://localhost:4000/api/v1/health

# Or with query param
curl http://localhost:4000/api/v1/health?lng=fr
```

## Translation Files Structure

```
public/locales/
├── en/
│   ├── common.json      # App-wide translations
│   ├── chat.json        # Chat interface
│   └── diveSites.json   # Dive site content
├── es/
│   ├── common.json
│   ├── chat.json
│   └── diveSites.json
└── ...

src/backend/locales/
├── en/
│   └── errors.json      # Error messages
├── es/
│   └── errors.json
└── ...
```

## Best Practices

1. **Use keys, not English text** in code
2. **Keep keys consistent** across languages
3. **Use namespaces** to organize translations
4. **Provide context** in key names (e.g., `chat.send` not `send`)
5. **Test all languages** before deployment
6. **Use professional translators** for production
7. **Keep translations in sync** when adding features

## Common Issues

**Issue:** Translation not showing  
**Solution:** Check namespace is loaded and key exists

**Issue:** Language not persisting  
**Solution:** Check localStorage permissions

**Issue:** Wrong language detected  
**Solution:** Clear localStorage: `localStorage.removeItem('i18nextLng')`
```

---

## Testing Checklist

- [ ] i18nHelper utility created
- [ ] Error handler uses translations
- [ ] Validation middleware uses translations
- [ ] i18n tests written and passing
- [ ] i18n documentation created

---

## Validation Commands

```bash
# Run i18n tests
npm test --workspace=backend -- i18n.test.ts

# Test backend language detection
curl -H "Accept-Language: es" http://localhost:4000/api/v1/health

# Test with query parameter
curl "http://localhost:4000/api/v1/health?lng=fr"

# Test validation error in Spanish
curl -X POST "http://localhost:4000/api/v1/chat/message?lng=es" \
  -H "Content-Type: application/json" \
  -d '{}'
# Should return Spanish error message

# All tests
npm test --workspace=backend
```

---

## PR 1.5 Complete! ✅

You now have:
- ✅ i18next configured for frontend and backend
- ✅ 8 supported languages (en, es, fr, de, pt, it, ja, zh)
- ✅ Translation files for common, chat, diveSites namespaces
- ✅ Language switcher UI component
- ✅ Translated frontend components
- ✅ Backend translation helpers
- ✅ Localized error messages
- ✅ Language detection and persistence
- ✅ Comprehensive i18n documentation

**Next Action:** Commit and create PR

```bash
git checkout -b 1.5-i18n-setup
git add .
git commit -m "feat(i18n): implement multilingual support with i18next

- Install and configure i18next for frontend and backend
- Support 8 languages: en, es, fr, de, pt, it, ja, zh
- Create translation files for common, chat, diveSites namespaces
- Implement LanguageSwitcher component with dropdown UI
- Translate Header, Footer, and Homepage components
- Add i18n helpers for backend error messages
- Implement language detection (localStorage, headers, query params)
- Write i18n tests and documentation
- Add useLanguage custom hook"

git push origin 1.5-i18n-setup
```

**Open PR on GitHub and request review!**

---

## Phase 1 Complete! 🎉

All 5 PRs are now ready:
- ✅ PR 1.1: Repository Setup & Docker
- ✅ PR 1.2: Neon PostgreSQL + pgvector
- ✅ PR 1.3: Next.js Frontend Scaffolding
- ✅ PR 1.4: Express Backend API
- ✅ PR 1.5: Internationalization

**Next Phase:** Phase 2 - Data Layer & RAG Pipeline (Weeks 3-4)

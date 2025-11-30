# Step 1: i18next Installation & Configuration

**PR:** 1.5 Internationalization Setup  
**Step:** 1 of 4  
**Estimated Time:** 1 hour

---

## Goal

Install i18next for both frontend (Next.js) and backend (Express), configure supported languages (en, es, fr, de, pt, it, ja, zh), and set up language detection.

---

## Files to Create

### 1. Install Frontend i18n Dependencies

Update `src/frontend/package.json`:

```json
{
  "dependencies": {
    "i18next": "^23.7.6",
    "react-i18next": "^13.5.0",
    "i18next-browser-languagedetector": "^7.2.0",
    "i18next-http-backend": "^2.4.2"
  }
}
```

### 2. `src/frontend/lib/i18n.ts`

Frontend i18n configuration:

```typescript
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import LanguageDetector from 'i18next-browser-languagedetector';
import Backend from 'i18next-http-backend';

export const SUPPORTED_LANGUAGES = ['en', 'es', 'fr', 'de', 'pt', 'it', 'ja', 'zh'] as const;
export type SupportedLanguage = typeof SUPPORTED_LANGUAGES[number];

export const LANGUAGE_NAMES: Record<SupportedLanguage, string> = {
  en: 'English',
  es: 'Español',
  fr: 'Français',
  de: 'Deutsch',
  pt: 'Português',
  it: 'Italiano',
  ja: '日本語',
  zh: '中文',
};

i18n
  .use(Backend)
  .use(LanguageDetector)
  .use(initReactI18next)
  .init({
    fallbackLng: 'en',
    supportedLngs: [...SUPPORTED_LANGUAGES],
    
    detection: {
      order: ['localStorage', 'navigator', 'htmlTag'],
      caches: ['localStorage'],
      lookupLocalStorage: 'i18nextLng',
    },

    backend: {
      loadPath: '/locales/{{lng}}/{{ns}}.json',
    },

    ns: ['common', 'chat', 'diveSites'],
    defaultNS: 'common',

    interpolation: {
      escapeValue: false, // React already escapes
    },

    react: {
      useSuspense: false,
    },

    debug: process.env.NODE_ENV === 'development',
  });

export default i18n;
```

### 3. Install Backend i18n Dependencies

Update `src/backend/package.json`:

```json
{
  "dependencies": {
    "i18next": "^23.7.6",
    "i18next-fs-backend": "^2.3.1",
    "i18next-http-middleware": "^3.5.0"
  }
}
```

### 4. `src/backend/src/config/i18n.ts`

Backend i18n configuration:

```typescript
import i18next from 'i18next';
import Backend from 'i18next-fs-backend';
import middleware from 'i18next-http-middleware';
import path from 'path';

export const SUPPORTED_LANGUAGES = ['en', 'es', 'fr', 'de', 'pt', 'it', 'ja', 'zh'] as const;
export type SupportedLanguage = typeof SUPPORTED_LANGUAGES[number];

i18next
  .use(Backend)
  .use(middleware.LanguageDetector)
  .init({
    fallbackLng: 'en',
    supportedLngs: [...SUPPORTED_LANGUAGES],
    
    preload: [...SUPPORTED_LANGUAGES],
    
    backend: {
      loadPath: path.join(__dirname, '../../locales/{{lng}}/{{ns}}.json'),
    },

    ns: ['common', 'chat', 'diveSites', 'errors'],
    defaultNS: 'common',

    detection: {
      order: ['querystring', 'header', 'cookie'],
      lookupQuerystring: 'lng',
      lookupCookie: 'i18next',
      lookupHeader: 'accept-language',
      caches: ['cookie'],
    },

    interpolation: {
      escapeValue: true,
    },

    debug: process.env.NODE_ENV === 'development',
  });

export const i18nMiddleware = middleware.handle(i18next);

export default i18next;
```

### 5. Update `src/backend/src/server.ts`

Add i18n middleware:

```typescript
// Add import at top
import { i18nMiddleware } from './config/i18n';

// Add after body parsing middleware
app.use(i18nMiddleware);
```

---

## Installation Commands

```bash
# Install frontend i18n
npm install --workspace=frontend i18next react-i18next i18next-browser-languagedetector i18next-http-backend

# Install backend i18n
npm install --workspace=backend i18next i18next-fs-backend i18next-http-middleware

# Create locale directories (done in next step)
```

---

## Testing Checklist

- [ ] Frontend i18n dependencies installed
- [ ] Backend i18n dependencies installed
- [ ] Frontend i18n config created
- [ ] Backend i18n config created
- [ ] Supported languages defined (8 languages)
- [ ] Language detection configured

---

## Validation Commands

```bash
# Install dependencies
npm install --workspace=frontend
npm install --workspace=backend

# Type check frontend
npm run typecheck --workspace=frontend

# Type check backend
npm run typecheck --workspace=backend

# Verify imports work (after creating translation files in next step)
```

---

## Next Step

Proceed to Step 2: Translation Files & Namespaces

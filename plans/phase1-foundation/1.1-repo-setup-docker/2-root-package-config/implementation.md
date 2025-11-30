# Step 2: Root Package Configuration

**PR:** 1.1 Repository Setup & Docker Environment  
**Step:** 2 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Configure the monorepo workspace using npm workspaces to manage frontend and backend as separate packages. Set up shared TypeScript, ESLint, and Prettier configurations.

---

## Files to Create

### 1. Root `package.json`

Create at project root:

```json
{
  "name": "dovvydive-mvp",
  "version": "1.0.0",
  "private": true,
  "description": "AI-powered dive trip planning chatbot for Malaysia",
  "workspaces": [
    "src/backend",
    "src/frontend"
  ],
  "scripts": {
    "dev": "docker-compose up",
    "dev:backend": "npm run dev --workspace=backend",
    "dev:frontend": "npm run dev --workspace=frontend",
    "build": "npm run build --workspaces",
    "build:backend": "npm run build --workspace=backend",
    "build:frontend": "npm run build --workspace=frontend",
    "test": "npm run test --workspaces",
    "test:unit": "npm run test:unit --workspaces",
    "test:integration": "npm run test:integration --workspaces",
    "test:e2e": "playwright test",
    "lint": "npm run lint --workspaces",
    "lint:fix": "npm run lint:fix --workspaces",
    "format": "prettier --write \"**/*.{js,jsx,ts,tsx,json,md}\"",
    "format:check": "prettier --check \"**/*.{js,jsx,ts,tsx,json,md}\"",
    "typecheck": "npm run typecheck --workspaces",
    "db:migrate": "npm run db:migrate --workspace=backend",
    "db:seed": "npm run db:seed --workspace=backend",
    "db:reset": "npm run db:reset --workspace=backend",
    "docker:up": "docker-compose up -d",
    "docker:down": "docker-compose down",
    "docker:logs": "docker-compose logs -f",
    "docker:clean": "docker-compose down -v --remove-orphans"
  },
  "devDependencies": {
    "@playwright/test": "^1.40.0",
    "@typescript-eslint/eslint-plugin": "^6.13.0",
    "@typescript-eslint/parser": "^6.13.0",
    "eslint": "^8.54.0",
    "eslint-config-prettier": "^9.0.0",
    "prettier": "^3.1.0",
    "typescript": "^5.3.2"
  },
  "engines": {
    "node": ">=20.10.0",
    "npm": ">=10.0.0"
  }
}
```

### 2. Root `tsconfig.json`

Create base TypeScript configuration:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022"],
    "module": "commonjs",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "allowJs": true,
    "checkJs": false,
    "outDir": "./dist",
    "rootDir": "./",
    "removeComments": true,
    "noEmit": false,
    "incremental": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "skipLibCheck": true
  },
  "exclude": ["node_modules", "dist", "build", ".next"]
}
```

### 3. `.eslintrc.json`

Create ESLint configuration:

```json
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": 2022,
    "sourceType": "module",
    "project": "./tsconfig.json"
  },
  "plugins": ["@typescript-eslint"],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/recommended-requiring-type-checking",
    "prettier"
  ],
  "rules": {
    "@typescript-eslint/no-unused-vars": [
      "error",
      {
        "argsIgnorePattern": "^_",
        "varsIgnorePattern": "^_"
      }
    ],
    "@typescript-eslint/no-explicit-any": "warn",
    "@typescript-eslint/explicit-function-return-type": "off",
    "@typescript-eslint/explicit-module-boundary-types": "off",
    "@typescript-eslint/no-floating-promises": "error",
    "no-console": ["warn", { "allow": ["warn", "error"] }]
  },
  "ignorePatterns": [
    "node_modules/",
    "dist/",
    "build/",
    ".next/",
    "*.config.js",
    "*.config.ts"
  ]
}
```

### 4. `.prettierrc`

Create Prettier configuration:

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "arrowParens": "always",
  "endOfLine": "lf",
  "bracketSpacing": true,
  "jsxSingleQuote": false,
  "proseWrap": "preserve"
}
```

### 5. `.prettierignore`

Create Prettier ignore file:

```
node_modules/
dist/
build/
.next/
coverage/
*.min.js
*.min.css
package-lock.json
pnpm-lock.yaml
yarn.lock
```

### 6. `playwright.config.ts`

Create Playwright E2E test configuration:

```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './src/tests/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 12'] },
    },
  ],
  webServer: {
    command: 'docker-compose up',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

---

## Installation Commands

Run these commands after creating the files:

```bash
# Install root dependencies
npm install

# Verify workspace configuration
npm ls --workspaces

# Test linting (should pass with no files yet)
npm run lint

# Test formatting
npm run format:check
```

---

## Testing Checklist

- [ ] `npm install` runs successfully
- [ ] Workspace packages recognized (will show empty until backend/frontend packages created)
- [ ] ESLint configuration validates
- [ ] Prettier configuration validates
- [ ] TypeScript configuration compiles without errors
- [ ] All scripts defined in package.json

---

## Validation Commands

```bash
# Verify Node version
node --version
# Should show v20.10.0 or higher

# Verify npm version
npm --version
# Should show 10.0.0 or higher

# Check workspace configuration
npm ls --workspaces --depth=0
# Will show error until workspaces created in Step 3

# Test linting
npx eslint --version

# Test prettier
npx prettier --version
```

---

## Common Issues

**Issue:** npm workspaces not recognized  
**Solution:** Ensure npm version is 7.0.0+ (workspaces support), upgrade if needed: `npm install -g npm@latest`

**Issue:** TypeScript errors on tsconfig.json  
**Solution:** Install TypeScript globally or ensure devDependency installed: `npm install`

**Issue:** ESLint parser errors  
**Solution:** Ensure @typescript-eslint packages are installed in devDependencies

---

## Next Step

Proceed to Step 3: Docker Compose Configuration

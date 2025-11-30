# Step 4: Shared Components Library

**PR:** 1.3 Next.js Frontend Scaffolding  
**Step:** 4 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Create reusable UI components (Button, Card, Input, LoadingSpinner) with TypeScript variants and proper styling. Define frontend constants and types.

---

## Files to Create

### 1. `src/frontend/components/Button.tsx`

Polymorphic button component:

```typescript
import { ButtonHTMLAttributes, forwardRef } from 'react';
import { cn } from '@/lib/utils';

export interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'coral' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
}

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      className,
      variant = 'primary',
      size = 'md',
      isLoading = false,
      disabled,
      children,
      ...props
    },
    ref
  ) => {
    const variants = {
      primary: 'btn-primary',
      secondary: 'btn-secondary',
      coral: 'btn-coral',
      ghost: 'bg-transparent text-ocean-500 hover:bg-ocean-50',
    };

    const sizes = {
      sm: 'px-4 py-2 text-sm rounded-lg',
      md: 'px-6 py-3 text-base rounded-xl',
      lg: 'px-8 py-4 text-lg rounded-xl',
    };

    return (
      <button
        ref={ref}
        className={cn(
          'font-medium transition-colors duration-200 disabled:opacity-50 disabled:cursor-not-allowed inline-flex items-center justify-center gap-2',
          variants[variant],
          sizes[size],
          className
        )}
        disabled={disabled || isLoading}
        {...props}
      >
        {isLoading && <span className="spinner" />}
        {children}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

### 2. `src/frontend/components/Card.tsx`

Card container component:

```typescript
import { HTMLAttributes, forwardRef } from 'react';
import { cn } from '@/lib/utils';

export interface CardProps extends HTMLAttributes<HTMLDivElement> {
  variant?: 'default' | 'ocean' | 'flat';
  padding?: 'none' | 'sm' | 'md' | 'lg';
}

export const Card = forwardRef<HTMLDivElement, CardProps>(
  (
    { className, variant = 'default', padding = 'md', children, ...props },
    ref
  ) => {
    const variants = {
      default: 'card',
      ocean: 'bg-ocean-50 border-2 border-ocean-200 rounded-2xl p-6',
      flat: 'bg-sand-100 rounded-2xl p-6',
    };

    const paddings = {
      none: 'p-0',
      sm: 'p-4',
      md: 'p-6',
      lg: 'p-8',
    };

    return (
      <div
        ref={ref}
        className={cn(
          variants[variant],
          variant === 'default' && paddings[padding],
          className
        )}
        {...props}
      >
        {children}
      </div>
    );
  }
);

Card.displayName = 'Card';
```

### 3. `src/frontend/components/Input.tsx`

Form input component:

```typescript
import { InputHTMLAttributes, forwardRef } from 'react';
import { cn } from '@/lib/utils';

export interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
  helperText?: string;
}

export const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ className, label, error, helperText, id, ...props }, ref) => {
    const inputId = id || label?.toLowerCase().replace(/\s+/g, '-');

    return (
      <div className="w-full">
        {label && (
          <label
            htmlFor={inputId}
            className="block text-sm font-medium text-sand-700 mb-2"
          >
            {label}
          </label>
        )}
        <input
          ref={ref}
          id={inputId}
          className={cn(
            'input-primary',
            error && 'border-coral-500 focus:border-coral-500 focus:ring-coral-100',
            className
          )}
          {...props}
        />
        {error && <p className="mt-2 text-sm text-coral-500">{error}</p>}
        {helperText && !error && (
          <p className="mt-2 text-sm text-sand-500">{helperText}</p>
        )}
      </div>
    );
  }
);

Input.displayName = 'Input';
```

### 4. `src/frontend/components/LoadingSpinner.tsx`

Loading indicator:

```typescript
import { HTMLAttributes } from 'react';
import { cn } from '@/lib/utils';

export interface LoadingSpinnerProps extends HTMLAttributes<HTMLDivElement> {
  size?: 'sm' | 'md' | 'lg';
  text?: string;
}

export function LoadingSpinner({
  size = 'md',
  text,
  className,
  ...props
}: LoadingSpinnerProps) {
  const sizes = {
    sm: 'w-4 h-4 border-2',
    md: 'w-8 h-8 border-3',
    lg: 'w-12 h-12 border-4',
  };

  return (
    <div
      className={cn('flex flex-col items-center justify-center gap-3', className)}
      {...props}
    >
      <div
        className={cn(
          'spinner border-ocean-100 border-t-ocean-500',
          sizes[size]
        )}
      />
      {text && <p className="text-sm text-sand-600">{text}</p>}
    </div>
  );
}
```

### 5. `src/frontend/components/index.ts`

Export barrel:

```typescript
export { Button } from './Button';
export type { ButtonProps } from './Button';

export { Card } from './Card';
export type { CardProps } from './Card';

export { Input } from './Input';
export type { InputProps } from './Input';

export { LoadingSpinner } from './LoadingSpinner';
export type { LoadingSpinnerProps } from './LoadingSpinner';

export { Header } from './Header';
export { Footer } from './Footer';
```

### 6. `src/frontend/lib/constants.ts`

Frontend constants:

```typescript
export const APP_NAME = 'DovvyDive';
export const APP_DESCRIPTION = 'AI-Powered Dive Site Discovery';

export const API_BASE_URL =
  process.env.NEXT_PUBLIC_API_URL || 'http://localhost:4000/api/v1';

export const ROUTES = {
  HOME: '/',
  CHAT: '/chat',
  EXPLORE: '/explore',
  ABOUT: '/about',
  CONTACT: '/contact',
} as const;

export const BREAKPOINTS = {
  sm: 640,
  md: 768,
  lg: 1024,
  xl: 1280,
  '2xl': 1536,
} as const;

export const MAX_MESSAGE_LENGTH = 500;
export const DEBOUNCE_DELAY = 300; // milliseconds

export const DIFFICULTY_LEVELS = [
  'Beginner',
  'Intermediate',
  'Advanced',
  'Expert',
] as const;

export const DIVE_TYPES = [
  'Reef',
  'Wreck',
  'Cave',
  'Wall',
  'Drift',
  'Night',
  'Deep',
] as const;
```

### 7. `src/frontend/types/index.ts`

Frontend types:

```typescript
export interface DiveSite {
  id: string;
  name: string;
  location: string;
  country: string;
  coordinates: {
    lat: number;
    lng: number;
  };
  difficulty: 'Beginner' | 'Intermediate' | 'Advanced' | 'Expert';
  maxDepth: number;
  description: string;
  imageUrl?: string;
  rating?: number;
}

export interface ChatMessage {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  timestamp: Date;
  diveSites?: DiveSite[];
}

export interface ChatSession {
  id: string;
  messages: ChatMessage[];
  createdAt: Date;
  updatedAt: Date;
}

export interface ApiError {
  message: string;
  code?: string;
  statusCode?: number;
}

export interface ApiResponse<T> {
  data?: T;
  error?: ApiError;
  meta?: {
    page?: number;
    limit?: number;
    total?: number;
  };
}
```

### 8. `src/frontend/hooks/useLocalStorage.ts`

Custom hook for localStorage:

```typescript
'use client';

import { useState, useEffect } from 'react';

export function useLocalStorage<T>(
  key: string,
  initialValue: T
): [T, (value: T) => void] {
  // State to store our value
  const [storedValue, setStoredValue] = useState<T>(initialValue);

  // Initialize from localStorage on mount
  useEffect(() => {
    try {
      const item = window.localStorage.getItem(key);
      if (item) {
        setStoredValue(JSON.parse(item));
      }
    } catch (error) {
      console.error(`Error loading localStorage key "${key}":`, error);
    }
  }, [key]);

  // Return a wrapped version of useState's setter function that persists
  const setValue = (value: T) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(`Error setting localStorage key "${key}":`, error);
    }
  };

  return [storedValue, setValue];
}
```

### 9. `src/frontend/README.md`

Frontend documentation:

```markdown
# DovvyDive Frontend

Next.js 14 frontend application with App Router.

## Stack

- **Framework:** Next.js 14 (App Router)
- **Styling:** Tailwind CSS 3.3
- **Language:** TypeScript 5.3
- **UI Components:** Custom component library
- **State:** React hooks + localStorage

## Directory Structure

```
src/frontend/
├── app/                  # Next.js App Router pages
│   ├── layout.tsx       # Root layout
│   ├── page.tsx         # Homepage
│   ├── chat/            # Chat interface
│   └── globals.css      # Global styles
├── components/          # Reusable UI components
│   ├── Button.tsx
│   ├── Card.tsx
│   ├── Input.tsx
│   ├── Header.tsx
│   └── Footer.tsx
├── hooks/               # Custom React hooks
├── lib/                 # Utilities and helpers
│   ├── utils.ts
│   └── constants.ts
├── styles/              # Theme configuration
│   └── theme.ts
└── types/               # TypeScript types
    └── index.ts
```

## Development

```bash
# Install dependencies
npm install --workspace=frontend

# Start dev server
npm run dev --workspace=frontend
# Visit http://localhost:3000

# Build for production
npm run build --workspace=frontend

# Type check
npm run typecheck --workspace=frontend

# Lint
npm run lint --workspace=frontend
```

## Component Usage

### Button

```tsx
import { Button } from '@/components';

<Button variant="primary" size="lg">
  Click Me
</Button>
```

### Card

```tsx
import { Card } from '@/components';

<Card variant="ocean">
  <h3>Title</h3>
  <p>Content</p>
</Card>
```

### Input

```tsx
import { Input } from '@/components';

<Input 
  label="Email" 
  type="email"
  error={errors.email}
/>
```

## Theme Colors

- **Ocean Blue:** Primary brand color (#1890ff)
- **Coral:** Accent color (#f5222d)
- **Sand:** Neutral grays
- **Deep:** Dark mode colors

## Path Aliases

- `@/` - Root of frontend directory
- `@/components/*` - UI components
- `@/lib/*` - Utilities
- `@/hooks/*` - Custom hooks
- `@shared/*` - Shared types from monorepo
```

---

## Testing Checklist

- [ ] Button component with variants created
- [ ] Card component created
- [ ] Input component with error states created
- [ ] LoadingSpinner component created
- [ ] Components exported from index.ts
- [ ] Constants defined
- [ ] Types defined
- [ ] useLocalStorage hook created
- [ ] Frontend README created

---

## Validation Commands

```bash
# Type check all components
npm run typecheck --workspace=frontend

# Test component imports
cd src/frontend
node -e "console.log(require('./components/index.ts'))"

# Start dev server and test components
npm run dev --workspace=frontend
```

**Manual Testing:**

Create a test page `app/components-demo/page.tsx`:

```tsx
import { Button, Card, Input, LoadingSpinner } from '@/components';

export default function ComponentsDemo() {
  return (
    <div className="container-custom py-12 space-y-8">
      <section>
        <h2 className="text-2xl font-bold mb-4">Buttons</h2>
        <div className="flex gap-4">
          <Button variant="primary">Primary</Button>
          <Button variant="secondary">Secondary</Button>
          <Button variant="coral">Coral</Button>
          <Button variant="ghost">Ghost</Button>
        </div>
      </section>

      <section>
        <h2 className="text-2xl font-bold mb-4">Cards</h2>
        <div className="grid md:grid-cols-3 gap-4">
          <Card>Default Card</Card>
          <Card variant="ocean">Ocean Card</Card>
          <Card variant="flat">Flat Card</Card>
        </div>
      </section>

      <section>
        <h2 className="text-2xl font-bold mb-4">Inputs</h2>
        <div className="max-w-md space-y-4">
          <Input label="Email" type="email" />
          <Input label="Password" type="password" error="Invalid password" />
        </div>
      </section>

      <section>
        <h2 className="text-2xl font-bold mb-4">Loading</h2>
        <LoadingSpinner size="lg" text="Loading..." />
      </section>
    </div>
  );
}
```

Visit `http://localhost:3000/components-demo` to verify all components render correctly.

---

## Common Issues

**Issue:** Type errors with forwardRef  
**Solution:** Ensure React types are installed: `npm install --workspace=frontend @types/react@latest`

**Issue:** Tailwind classes not applying  
**Solution:** Verify `tailwind.config.js` content paths include all component directories

**Issue:** Path aliases not resolving  
**Solution:** Check `tsconfig.json` paths match import statements

---

## PR 1.3 Complete! ✅

You now have:
- ✅ Next.js 14 with App Router installed
- ✅ Tailwind CSS with custom ocean theme
- ✅ App structure with layouts and pages
- ✅ Reusable component library
- ✅ TypeScript types and constants
- ✅ Custom hooks for state management

**Next Action:** Commit and create PR

```bash
git checkout -b 1.3-nextjs-scaffolding
git add src/frontend/
git commit -m "feat(frontend): scaffold Next.js 14 application with Tailwind CSS

- Install Next.js 14 with App Router
- Configure Tailwind CSS with ocean/coral/sand color palette
- Create root layout with Header/Footer
- Implement Homepage and Chat page placeholders
- Build component library (Button, Card, Input, LoadingSpinner)
- Define TypeScript types and constants
- Add useLocalStorage custom hook"

git push origin 1.3-nextjs-scaffolding
```

**Open PR on GitHub and request review!**

---

## Next PR

Proceed to PR 1.4: Express Backend API Setup

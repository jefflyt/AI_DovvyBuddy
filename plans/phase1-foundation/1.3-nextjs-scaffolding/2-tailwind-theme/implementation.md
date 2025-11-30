# Step 2: Tailwind CSS & Theme Configuration

**PR:** 1.3 Next.js Frontend Scaffolding  
**Step:** 2 of 4  
**Estimated Time:** 1 hour

---

## Goal

Install Tailwind CSS and configure custom color palette inspired by diving (ocean blues, coral accents, sandy neutrals). Define typography scale optimized for chat messages.

---

## Files to Create

### 1. Install Tailwind Dependencies

Update `src/frontend/package.json`:

```json
{
  "dependencies": {
    "tailwindcss": "^3.3.6",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.32",
    "clsx": "^2.0.0",
    "tailwind-merge": "^2.1.0"
  }
}
```

### 2. `src/frontend/tailwind.config.js`

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './app/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './lib/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        // Ocean Blues (Primary)
        ocean: {
          50: '#e6f7ff',
          100: '#bae7ff',
          200: '#91d5ff',
          300: '#69c0ff',
          400: '#40a9ff',
          500: '#1890ff', // Primary ocean blue
          600: '#096dd9',
          700: '#0050b3',
          800: '#003a8c',
          900: '#002766',
        },
        // Coral (Accent)
        coral: {
          50: '#fff1f0',
          100: '#ffccc7',
          200: '#ffa39e',
          300: '#ff7875',
          400: '#ff4d4f',
          500: '#f5222d', // Primary coral
          600: '#cf1322',
          700: '#a8071a',
          800: '#820014',
          900: '#5c0011',
        },
        // Sand (Neutral)
        sand: {
          50: '#fafafa',
          100: '#f5f5f5',
          200: '#e8e8e8',
          300: '#d9d9d9',
          400: '#bfbfbf',
          500: '#8c8c8c',
          600: '#595959',
          700: '#434343',
          800: '#262626',
          900: '#1f1f1f',
        },
        // Deep Sea (Dark mode)
        deep: {
          50: '#1a1f2e',
          100: '#141824',
          200: '#0f1419',
          300: '#0a0d12',
          400: '#05070b',
          500: '#000000',
        },
      },
      fontFamily: {
        sans: ['var(--font-inter)', 'system-ui', 'sans-serif'],
        display: ['var(--font-poppins)', 'system-ui', 'sans-serif'],
        mono: ['var(--font-jetbrains-mono)', 'monospace'],
      },
      fontSize: {
        xs: ['0.75rem', { lineHeight: '1rem' }],
        sm: ['0.875rem', { lineHeight: '1.25rem' }],
        base: ['1rem', { lineHeight: '1.5rem' }],
        lg: ['1.125rem', { lineHeight: '1.75rem' }],
        xl: ['1.25rem', { lineHeight: '1.75rem' }],
        '2xl': ['1.5rem', { lineHeight: '2rem' }],
        '3xl': ['1.875rem', { lineHeight: '2.25rem' }],
        '4xl': ['2.25rem', { lineHeight: '2.5rem' }],
      },
      spacing: {
        18: '4.5rem',
        88: '22rem',
        112: '28rem',
        128: '32rem',
      },
      borderRadius: {
        '4xl': '2rem',
      },
      boxShadow: {
        'ocean': '0 4px 14px 0 rgba(24, 144, 255, 0.15)',
        'ocean-lg': '0 8px 24px 0 rgba(24, 144, 255, 0.2)',
        'chat': '0 2px 8px 0 rgba(0, 0, 0, 0.08)',
      },
      animation: {
        'fade-in': 'fadeIn 0.2s ease-in',
        'slide-up': 'slideUp 0.3s ease-out',
        'pulse-slow': 'pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { transform: 'translateY(10px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
};
```

### 3. `src/frontend/postcss.config.js`

```javascript
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

### 4. `src/frontend/app/globals.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --font-inter: 'Inter', sans-serif;
    --font-poppins: 'Poppins', sans-serif;
    --font-jetbrains-mono: 'JetBrains Mono', monospace;
  }

  * {
    @apply border-sand-200;
  }

  body {
    @apply bg-white text-sand-900 antialiased;
  }

  h1, h2, h3, h4, h5, h6 {
    @apply font-display font-semibold;
  }
}

@layer components {
  /* Chat message styles */
  .chat-message {
    @apply rounded-2xl px-4 py-3 max-w-3xl;
  }

  .chat-message-user {
    @apply bg-ocean-500 text-white ml-auto;
  }

  .chat-message-assistant {
    @apply bg-sand-100 text-sand-900;
  }

  /* Button variants */
  .btn-primary {
    @apply bg-ocean-500 text-white hover:bg-ocean-600 
           active:bg-ocean-700 disabled:bg-sand-300 
           disabled:text-sand-500 px-6 py-3 rounded-xl 
           font-medium transition-colors duration-200;
  }

  .btn-secondary {
    @apply bg-white text-ocean-500 border-2 border-ocean-500 
           hover:bg-ocean-50 active:bg-ocean-100 
           px-6 py-3 rounded-xl font-medium 
           transition-colors duration-200;
  }

  .btn-coral {
    @apply bg-coral-500 text-white hover:bg-coral-600 
           active:bg-coral-700 px-6 py-3 rounded-xl 
           font-medium transition-colors duration-200;
  }

  /* Input styles */
  .input-primary {
    @apply w-full px-4 py-3 rounded-xl border-2 border-sand-300 
           focus:border-ocean-500 focus:ring-2 focus:ring-ocean-100 
           outline-none transition-colors duration-200;
  }

  /* Card styles */
  .card {
    @apply bg-white rounded-2xl shadow-chat p-6 
           hover:shadow-ocean transition-shadow duration-200;
  }

  /* Container */
  .container-custom {
    @apply max-w-7xl mx-auto px-4 sm:px-6 lg:px-8;
  }
}

@layer utilities {
  .text-balance {
    text-wrap: balance;
  }

  .scrollbar-hide {
    -ms-overflow-style: none;
    scrollbar-width: none;
  }

  .scrollbar-hide::-webkit-scrollbar {
    display: none;
  }

  /* Gradient text */
  .gradient-ocean {
    @apply bg-gradient-to-r from-ocean-400 to-ocean-600 
           bg-clip-text text-transparent;
  }

  /* Ocean wave background animation */
  .wave-background {
    background: linear-gradient(135deg, #e6f7ff 0%, #91d5ff 100%);
    background-size: 400% 400%;
    animation: wave 15s ease infinite;
  }

  @keyframes wave {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }
}

/* Loading spinner */
.spinner {
  border: 3px solid rgba(24, 144, 255, 0.1);
  border-top-color: #1890ff;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
```

### 5. `src/frontend/lib/utils.ts`

Create className utility:

```typescript
import { type ClassValue, clsx } from 'clsx';
import { twMerge } from 'tailwind-merge';

/**
 * Merge Tailwind classes with clsx
 * Handles conflicts and duplicates
 */
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

/**
 * Format date for display
 */
export function formatDate(date: Date | string): string {
  const d = typeof date === 'string' ? new Date(date) : date;
  return new Intl.DateTimeFormat('en-US', {
    month: 'short',
    day: 'numeric',
    year: 'numeric',
  }).format(d);
}

/**
 * Format date with time
 */
export function formatDateTime(date: Date | string): string {
  const d = typeof date === 'string' ? new Date(date) : date;
  return new Intl.DateTimeFormat('en-US', {
    month: 'short',
    day: 'numeric',
    hour: 'numeric',
    minute: '2-digit',
  }).format(d);
}

/**
 * Truncate text with ellipsis
 */
export function truncate(text: string, length: number): string {
  if (text.length <= length) return text;
  return text.slice(0, length) + '...';
}
```

### 6. `src/frontend/styles/theme.ts`

TypeScript theme constants:

```typescript
export const theme = {
  colors: {
    ocean: {
      primary: '#1890ff',
      light: '#69c0ff',
      dark: '#096dd9',
    },
    coral: {
      primary: '#f5222d',
      light: '#ff7875',
      dark: '#cf1322',
    },
    sand: {
      light: '#f5f5f5',
      medium: '#8c8c8c',
      dark: '#262626',
    },
  },
  fonts: {
    sans: 'Inter, system-ui, sans-serif',
    display: 'Poppins, system-ui, sans-serif',
    mono: 'JetBrains Mono, monospace',
  },
  breakpoints: {
    sm: '640px',
    md: '768px',
    lg: '1024px',
    xl: '1280px',
    '2xl': '1536px',
  },
} as const;

export type Theme = typeof theme;
```

---

## Installation Commands

```bash
# Install Tailwind and plugins
npm install --workspace=frontend tailwindcss autoprefixer postcss @tailwindcss/forms @tailwindcss/typography

# Install utility dependencies
npm install --workspace=frontend clsx tailwind-merge

# Initialize Tailwind (already done with config files above)
# cd src/frontend && npx tailwindcss init -p
```

---

## Testing Checklist

- [ ] Tailwind CSS installed
- [ ] Custom ocean/coral/sand color palette configured
- [ ] Typography scale defined
- [ ] Global CSS with base styles created
- [ ] Utility functions created (cn, formatDate, etc.)
- [ ] Theme constants defined in TypeScript

---

## Validation Commands

```bash
# Install dependencies
npm install --workspace=frontend

# Verify Tailwind installed
cd src/frontend && npx tailwindcss --version

# Test CSS compilation (requires app layout from Step 3)
# npm run dev --workspace=frontend
```

---

## Next Step

Proceed to Step 3: App Router Structure & Root Layout

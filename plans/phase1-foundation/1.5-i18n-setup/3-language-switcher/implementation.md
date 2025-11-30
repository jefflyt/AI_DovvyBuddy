# Step 3: Language Switcher Components

**PR:** 1.5 Internationalization Setup  
**Step:** 3 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Create language switcher UI component for frontend, implement language toggle functionality, and update existing components to use translations.

---

## Files to Create

### 1. `src/frontend/components/LanguageSwitcher.tsx`

Language dropdown component:

```typescript
'use client';

import { useState, useRef, useEffect } from 'react';
import { useTranslation } from 'react-i18next';
import { cn } from '@/lib/utils';
import { SUPPORTED_LANGUAGES, LANGUAGE_NAMES, SupportedLanguage } from '@/lib/i18n';

export function LanguageSwitcher() {
  const { i18n } = useTranslation();
  const [isOpen, setIsOpen] = useState(false);
  const dropdownRef = useRef<HTMLDivElement>(null);

  const currentLanguage = i18n.language as SupportedLanguage;

  const changeLanguage = (lng: SupportedLanguage) => {
    i18n.changeLanguage(lng);
    setIsOpen(false);
  };

  // Close dropdown when clicking outside
  useEffect(() => {
    function handleClickOutside(event: MouseEvent) {
      if (dropdownRef.current && !dropdownRef.current.contains(event.target as Node)) {
        setIsOpen(false);
      }
    }

    document.addEventListener('mousedown', handleClickOutside);
    return () => document.removeEventListener('mousedown', handleClickOutside);
  }, []);

  return (
    <div className="relative" ref={dropdownRef}>
      {/* Current Language Button */}
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="flex items-center gap-2 px-3 py-2 rounded-lg hover:bg-sand-100 transition-colors"
        aria-label="Select language"
      >
        <span className="text-lg">🌍</span>
        <span className="hidden sm:inline text-sm font-medium">
          {LANGUAGE_NAMES[currentLanguage]}
        </span>
        <svg
          className={cn(
            'w-4 h-4 transition-transform',
            isOpen && 'rotate-180'
          )}
          fill="none"
          strokeLinecap="round"
          strokeLinejoin="round"
          strokeWidth="2"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path d="M19 9l-7 7-7-7" />
        </svg>
      </button>

      {/* Dropdown Menu */}
      {isOpen && (
        <div className="absolute right-0 mt-2 w-48 bg-white rounded-xl shadow-ocean border border-sand-200 py-2 z-50 animate-fade-in">
          {SUPPORTED_LANGUAGES.map((lang) => (
            <button
              key={lang}
              onClick={() => changeLanguage(lang)}
              className={cn(
                'w-full px-4 py-2 text-left text-sm hover:bg-ocean-50 transition-colors flex items-center justify-between',
                currentLanguage === lang && 'bg-ocean-100 text-ocean-700 font-medium'
              )}
            >
              <span>{LANGUAGE_NAMES[lang]}</span>
              {currentLanguage === lang && (
                <span className="text-ocean-500">✓</span>
              )}
            </button>
          ))}
        </div>
      )}
    </div>
  );
}
```

### 2. `src/frontend/app/layout.tsx`

Update root layout to initialize i18n:

```typescript
'use client';

import './globals.css';
import '../lib/i18n'; // Initialize i18n
import type { Metadata } from 'next';
import { Inter, Poppins, JetBrains_Mono } from 'next/font/google';
import { Header } from '@/components/Header';
import { Footer } from '@/components/Footer';
import { I18nextProvider } from 'react-i18next';
import i18n from '@/lib/i18n';

// ... font configurations (same as before)

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html
      lang={i18n.language}
      className={`${inter.variable} ${poppins.variable} ${jetbrainsMono.variable}`}
    >
      <body className="flex flex-col min-h-screen antialiased">
        <I18nextProvider i18n={i18n}>
          <Header />
          <main className="flex-grow">{children}</main>
          <Footer />
        </I18nextProvider>
      </body>
    </html>
  );
}
```

### 3. Update `src/frontend/components/Header.tsx`

Add language switcher and translate nav:

```typescript
'use client';

import Link from 'next/link';
import { useState } from 'react';
import { useTranslation } from 'react-i18next';
import { cn } from '@/lib/utils';
import { LanguageSwitcher } from './LanguageSwitcher';

export function Header() {
  const { t } = useTranslation('common');
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

  return (
    <header className="sticky top-0 z-50 bg-white border-b border-sand-200 shadow-sm">
      <nav className="container-custom">
        <div className="flex items-center justify-between h-16">
          {/* Logo */}
          <Link href="/" className="flex items-center space-x-2">
            <span className="text-2xl">🤿</span>
            <span className="text-xl font-display font-bold gradient-ocean">
              {t('app.name')}
            </span>
          </Link>

          {/* Desktop Navigation */}
          <div className="hidden md:flex items-center space-x-6">
            <Link
              href="/"
              className="text-sand-700 hover:text-ocean-500 transition-colors"
            >
              {t('nav.home')}
            </Link>
            <Link
              href="/chat"
              className="text-sand-700 hover:text-ocean-500 transition-colors"
            >
              {t('nav.chat')}
            </Link>
            <Link
              href="/explore"
              className="text-sand-700 hover:text-ocean-500 transition-colors"
            >
              {t('nav.explore')}
            </Link>
            <Link
              href="/about"
              className="text-sand-700 hover:text-ocean-500 transition-colors"
            >
              {t('nav.about')}
            </Link>
            
            {/* Language Switcher */}
            <LanguageSwitcher />
          </div>

          {/* Mobile Menu Button */}
          <div className="flex items-center gap-2 md:hidden">
            <LanguageSwitcher />
            <button
              className="p-2"
              onClick={() => setMobileMenuOpen(!mobileMenuOpen)}
              aria-label="Toggle menu"
            >
              <svg
                className="w-6 h-6"
                fill="none"
                strokeLinecap="round"
                strokeLinejoin="round"
                strokeWidth="2"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                {mobileMenuOpen ? (
                  <path d="M6 18L18 6M6 6l12 12" />
                ) : (
                  <path d="M4 6h16M4 12h16M4 18h16" />
                )}
              </svg>
            </button>
          </div>
        </div>

        {/* Mobile Menu */}
        <div
          className={cn(
            'md:hidden overflow-hidden transition-all duration-300',
            mobileMenuOpen ? 'max-h-64 pb-4' : 'max-h-0'
          )}
        >
          <div className="flex flex-col space-y-3">
            <Link
              href="/"
              className="text-sand-700 hover:text-ocean-500 transition-colors py-2"
              onClick={() => setMobileMenuOpen(false)}
            >
              {t('nav.home')}
            </Link>
            <Link
              href="/chat"
              className="text-sand-700 hover:text-ocean-500 transition-colors py-2"
              onClick={() => setMobileMenuOpen(false)}
            >
              {t('nav.chat')}
            </Link>
            <Link
              href="/explore"
              className="text-sand-700 hover:text-ocean-500 transition-colors py-2"
              onClick={() => setMobileMenuOpen(false)}
            >
              {t('nav.explore')}
            </Link>
            <Link
              href="/about"
              className="text-sand-700 hover:text-ocean-500 transition-colors py-2"
              onClick={() => setMobileMenuOpen(false)}
            >
              {t('nav.about')}
            </Link>
          </div>
        </div>
      </nav>
    </header>
  );
}
```

### 4. Update `src/frontend/app/page.tsx`

Translate homepage content:

```typescript
'use client';

import Link from 'next/link';
import { useTranslation } from 'react-i18next';
import { Button } from '@/components/Button';
import { Card } from '@/components/Card';

export default function HomePage() {
  const { t } = useTranslation('common');

  return (
    <div className="wave-background">
      {/* Hero Section */}
      <section className="container-custom py-20 md:py-32">
        <div className="text-center max-w-4xl mx-auto">
          <h1 className="text-4xl md:text-6xl font-display font-bold text-sand-900 mb-6">
            {t('app.tagline')}
          </h1>
          <p className="text-lg md:text-xl text-sand-700 mb-8 max-w-2xl mx-auto">
            {t('app.description')}
          </p>
          <div className="flex flex-col sm:flex-row gap-4 justify-center">
            <Link href="/chat">
              <Button size="lg" variant="primary">
                {t('actions.start')}
              </Button>
            </Link>
            <Link href="/about">
              <Button size="lg" variant="secondary">
                {t('actions.learnMore')}
              </Button>
            </Link>
          </div>
        </div>
      </section>

      {/* Features Section */}
      <section className="container-custom py-16">
        <h2 className="text-3xl font-display font-bold text-center mb-12 text-sand-900">
          Why {t('app.name')}?
        </h2>
        <div className="grid md:grid-cols-3 gap-8">
          <Card>
            <div className="text-center">
              <div className="w-16 h-16 bg-ocean-100 rounded-2xl flex items-center justify-center mx-auto mb-4">
                <span className="text-3xl">🤖</span>
              </div>
              <h3 className="text-xl font-semibold mb-2">AI-Powered</h3>
              <p className="text-sand-600">
                Conversational AI understands your diving preferences and suggests
                personalized sites.
              </p>
            </div>
          </Card>

          <Card>
            <div className="text-center">
              <div className="w-16 h-16 bg-ocean-100 rounded-2xl flex items-center justify-center mx-auto mb-4">
                <span className="text-3xl">🌍</span>
              </div>
              <h3 className="text-xl font-semibold mb-2">Global Coverage</h3>
              <p className="text-sand-600">
                Access dive sites from around the world with detailed information
                and reviews.
              </p>
            </div>
          </Card>

          <Card>
            <div className="text-center">
              <div className="w-16 h-16 bg-ocean-100 rounded-2xl flex items-center justify-center mx-auto mb-4">
                <span className="text-3xl">📍</span>
              </div>
              <h3 className="text-xl font-semibold mb-2">Smart Search</h3>
              <p className="text-sand-600">
                Vector-based semantic search finds sites matching your exact
                criteria.
              </p>
            </div>
          </Card>
        </div>
      </section>

      {/* CTA Section */}
      <section className="container-custom py-16">
        <div className="bg-ocean-500 rounded-3xl p-12 text-center text-white">
          <h2 className="text-3xl font-display font-bold mb-4">
            Ready to Dive In?
          </h2>
          <p className="text-xl mb-8 text-ocean-100">
            Start chatting with our AI assistant to find your perfect dive site.
          </p>
          <Link href="/chat">
            <Button size="lg" variant="secondary">
              {t('actions.start')}
            </Button>
          </Link>
        </div>
      </section>
    </div>
  );
}
```

### 5. `src/frontend/hooks/useLanguage.ts`

Custom hook for language management:

```typescript
'use client';

import { useTranslation } from 'react-i18next';
import { useEffect } from 'react';
import type { SupportedLanguage } from '@/lib/i18n';

export function useLanguage() {
  const { i18n } = useTranslation();

  const changeLanguage = (lng: SupportedLanguage) => {
    i18n.changeLanguage(lng);
    
    // Update HTML lang attribute
    if (typeof document !== 'undefined') {
      document.documentElement.lang = lng;
    }
  };

  const currentLanguage = i18n.language as SupportedLanguage;

  // Sync language with HTML attribute on mount
  useEffect(() => {
    if (typeof document !== 'undefined') {
      document.documentElement.lang = currentLanguage;
    }
  }, [currentLanguage]);

  return {
    currentLanguage,
    changeLanguage,
    isRTL: false, // None of our supported languages are RTL
  };
}
```

---

## Installation Commands

```bash
# No new dependencies needed
# All components use existing i18next setup
```

---

## Testing Checklist

- [ ] LanguageSwitcher component created
- [ ] Language dropdown UI works
- [ ] Language changes persist in localStorage
- [ ] Header component translated
- [ ] Homepage translated
- [ ] useLanguage hook created
- [ ] HTML lang attribute updates dynamically

---

## Validation Commands

```bash
# Type check
npm run typecheck --workspace=frontend

# Start frontend
npm run dev --workspace=frontend

# Visual Testing:
# 1. Visit http://localhost:3000
# 2. Click language dropdown (globe icon)
# 3. Select different language (e.g., Spanish)
# 4. Verify:
#    - Navigation items change to Spanish
#    - Homepage content translates
#    - Language persists on reload
#    - HTML lang attribute updates

# Test localStorage persistence
# In browser console:
localStorage.getItem('i18nextLng')
# Should show selected language code
```

---

## Next Step

Proceed to Step 4: Backend i18n Integration & Testing

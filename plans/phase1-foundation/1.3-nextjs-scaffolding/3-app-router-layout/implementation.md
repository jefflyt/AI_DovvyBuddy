# Step 3: App Router Structure & Root Layout

**PR:** 1.3 Next.js Frontend Scaffolding  
**Step:** 3 of 4  
**Estimated Time:** 1-2 hours

---

## Goal

Create Next.js 14 App Router structure with root layout, homepage, and chat page. Implement responsive navigation header and footer components.

---

## Files to Create

### 1. `src/frontend/app/layout.tsx`

Root layout with metadata and providers:

```typescript
import './globals.css';
import type { Metadata } from 'next';
import { Inter, Poppins, JetBrains_Mono } from 'next/font/google';
import { Header } from '@/components/Header';
import { Footer } from '@/components/Footer';

const inter = Inter({
  subsets: ['latin'],
  variable: '--font-inter',
  display: 'swap',
});

const poppins = Poppins({
  weight: ['400', '500', '600', '700'],
  subsets: ['latin'],
  variable: '--font-poppins',
  display: 'swap',
});

const jetbrainsMono = JetBrains_Mono({
  subsets: ['latin'],
  variable: '--font-jetbrains-mono',
  display: 'swap',
});

export const metadata: Metadata = {
  title: 'DovvyDive - AI-Powered Dive Site Discovery',
  description: 'Discover the perfect dive sites with conversational AI assistance',
  keywords: ['diving', 'scuba', 'dive sites', 'AI assistant', 'travel'],
  authors: [{ name: 'DovvyDive Team' }],
  viewport: 'width=device-width, initial-scale=1',
  themeColor: '#1890ff',
  icons: {
    icon: '/favicon.ico',
    apple: '/apple-icon.png',
  },
  openGraph: {
    type: 'website',
    locale: 'en_US',
    url: 'https://dovvydive.com',
    title: 'DovvyDive - AI-Powered Dive Site Discovery',
    description: 'Discover the perfect dive sites with conversational AI assistance',
    siteName: 'DovvyDive',
  },
  twitter: {
    card: 'summary_large_image',
    title: 'DovvyDive - AI-Powered Dive Site Discovery',
    description: 'Discover the perfect dive sites with conversational AI assistance',
  },
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html
      lang="en"
      className={`${inter.variable} ${poppins.variable} ${jetbrainsMono.variable}`}
    >
      <body className="flex flex-col min-h-screen antialiased">
        <Header />
        <main className="flex-grow">{children}</main>
        <Footer />
      </body>
    </html>
  );
}
```

### 2. `src/frontend/app/page.tsx`

Homepage with hero section:

```typescript
import Link from 'next/link';
import { Button } from '@/components/Button';
import { Card } from '@/components/Card';

export default function HomePage() {
  return (
    <div className="wave-background">
      {/* Hero Section */}
      <section className="container-custom py-20 md:py-32">
        <div className="text-center max-w-4xl mx-auto">
          <h1 className="text-4xl md:text-6xl font-display font-bold text-sand-900 mb-6">
            Discover Your Next
            <span className="gradient-ocean block mt-2">Dive Adventure</span>
          </h1>
          <p className="text-lg md:text-xl text-sand-700 mb-8 max-w-2xl mx-auto">
            Let our AI assistant guide you to the perfect dive sites based on your
            preferences, experience level, and travel plans.
          </p>
          <div className="flex flex-col sm:flex-row gap-4 justify-center">
            <Link href="/chat">
              <Button size="lg" variant="primary">
                Start Exploring
              </Button>
            </Link>
            <Link href="/about">
              <Button size="lg" variant="secondary">
                Learn More
              </Button>
            </Link>
          </div>
        </div>
      </section>

      {/* Features Section */}
      <section className="container-custom py-16">
        <h2 className="text-3xl font-display font-bold text-center mb-12 text-sand-900">
          Why DovvyDive?
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
              Start Conversation
            </Button>
          </Link>
        </div>
      </section>
    </div>
  );
}
```

### 3. `src/frontend/app/chat/page.tsx`

Chat interface placeholder:

```typescript
import { Card } from '@/components/Card';

export default function ChatPage() {
  return (
    <div className="min-h-screen bg-sand-50 py-8">
      <div className="container-custom">
        <div className="max-w-4xl mx-auto">
          <h1 className="text-3xl font-display font-bold mb-2">
            Chat with DovvyDive AI
          </h1>
          <p className="text-sand-600 mb-8">
            Ask me anything about dive sites, conditions, or recommendations!
          </p>

          <Card>
            <div className="text-center py-12">
              <div className="text-6xl mb-4">🤿</div>
              <h2 className="text-2xl font-semibold mb-2 text-sand-900">
                Chat Interface Coming Soon
              </h2>
              <p className="text-sand-600 max-w-md mx-auto">
                The interactive chat interface will be implemented in Phase 2.
                For now, this is a placeholder to demonstrate routing.
              </p>
            </div>
          </Card>

          {/* Example Messages Preview */}
          <div className="mt-8 space-y-4">
            <div className="chat-message chat-message-user">
              I'm looking for beginner-friendly dive sites in Southeast Asia
            </div>
            <div className="chat-message chat-message-assistant">
              Great! I'd recommend checking out the Similan Islands in Thailand or
              Tulamben in Bali. Both offer excellent visibility and calm conditions
              perfect for beginners.
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
```

### 4. `src/frontend/components/Header.tsx`

Navigation header:

```typescript
'use client';

import Link from 'next/link';
import { useState } from 'react';
import { cn } from '@/lib/utils';

export function Header() {
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

  return (
    <header className="sticky top-0 z-50 bg-white border-b border-sand-200 shadow-sm">
      <nav className="container-custom">
        <div className="flex items-center justify-between h-16">
          {/* Logo */}
          <Link href="/" className="flex items-center space-x-2">
            <span className="text-2xl">🤿</span>
            <span className="text-xl font-display font-bold gradient-ocean">
              DovvyDive
            </span>
          </Link>

          {/* Desktop Navigation */}
          <div className="hidden md:flex items-center space-x-8">
            <Link
              href="/"
              className="text-sand-700 hover:text-ocean-500 transition-colors"
            >
              Home
            </Link>
            <Link
              href="/chat"
              className="text-sand-700 hover:text-ocean-500 transition-colors"
            >
              Chat
            </Link>
            <Link
              href="/explore"
              className="text-sand-700 hover:text-ocean-500 transition-colors"
            >
              Explore
            </Link>
            <Link
              href="/about"
              className="text-sand-700 hover:text-ocean-500 transition-colors"
            >
              About
            </Link>
          </div>

          {/* Mobile Menu Button */}
          <button
            className="md:hidden p-2"
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
              Home
            </Link>
            <Link
              href="/chat"
              className="text-sand-700 hover:text-ocean-500 transition-colors py-2"
              onClick={() => setMobileMenuOpen(false)}
            >
              Chat
            </Link>
            <Link
              href="/explore"
              className="text-sand-700 hover:text-ocean-500 transition-colors py-2"
              onClick={() => setMobileMenuOpen(false)}
            >
              Explore
            </Link>
            <Link
              href="/about"
              className="text-sand-700 hover:text-ocean-500 transition-colors py-2"
              onClick={() => setMobileMenuOpen(false)}
            >
              About
            </Link>
          </div>
        </div>
      </nav>
    </header>
  );
}
```

### 5. `src/frontend/components/Footer.tsx`

Footer component:

```typescript
import Link from 'next/link';

export function Footer() {
  const currentYear = new Date().getFullYear();

  return (
    <footer className="bg-sand-900 text-white py-12">
      <div className="container-custom">
        <div className="grid md:grid-cols-4 gap-8 mb-8">
          {/* Brand */}
          <div>
            <div className="flex items-center space-x-2 mb-4">
              <span className="text-2xl">🤿</span>
              <span className="text-xl font-display font-bold">DovvyDive</span>
            </div>
            <p className="text-sand-400 text-sm">
              AI-powered dive site discovery for underwater explorers.
            </p>
          </div>

          {/* Product */}
          <div>
            <h3 className="font-semibold mb-4">Product</h3>
            <ul className="space-y-2 text-sm">
              <li>
                <Link href="/chat" className="text-sand-400 hover:text-white">
                  Chat Assistant
                </Link>
              </li>
              <li>
                <Link href="/explore" className="text-sand-400 hover:text-white">
                  Explore Sites
                </Link>
              </li>
              <li>
                <Link href="/features" className="text-sand-400 hover:text-white">
                  Features
                </Link>
              </li>
            </ul>
          </div>

          {/* Company */}
          <div>
            <h3 className="font-semibold mb-4">Company</h3>
            <ul className="space-y-2 text-sm">
              <li>
                <Link href="/about" className="text-sand-400 hover:text-white">
                  About Us
                </Link>
              </li>
              <li>
                <Link href="/contact" className="text-sand-400 hover:text-white">
                  Contact
                </Link>
              </li>
              <li>
                <Link href="/privacy" className="text-sand-400 hover:text-white">
                  Privacy Policy
                </Link>
              </li>
            </ul>
          </div>

          {/* Resources */}
          <div>
            <h3 className="font-semibold mb-4">Resources</h3>
            <ul className="space-y-2 text-sm">
              <li>
                <Link href="/docs" className="text-sand-400 hover:text-white">
                  Documentation
                </Link>
              </li>
              <li>
                <Link href="/blog" className="text-sand-400 hover:text-white">
                  Blog
                </Link>
              </li>
              <li>
                <Link href="/support" className="text-sand-400 hover:text-white">
                  Support
                </Link>
              </li>
            </ul>
          </div>
        </div>

        {/* Bottom Bar */}
        <div className="border-t border-sand-700 pt-8">
          <p className="text-sm text-sand-400 text-center">
            © {currentYear} DovvyDive. All rights reserved.
          </p>
        </div>
      </div>
    </footer>
  );
}
```

---

## Installation Commands

```bash
# No additional packages needed
# All dependencies were installed in previous steps
```

---

## Testing Checklist

- [ ] Root layout created with metadata
- [ ] Homepage with hero section created
- [ ] Chat page placeholder created
- [ ] Header component with responsive navigation
- [ ] Footer component with links
- [ ] Google Fonts loaded correctly
- [ ] Tailwind classes applied properly

---

## Validation Commands

```bash
# Start dev server
npm run dev --workspace=frontend

# Open browser to http://localhost:3000
# Should see:
# - Homepage with hero section
# - Working navigation to /chat
# - Responsive mobile menu
# - Footer with links

# Type check
npm run typecheck --workspace=frontend
# Should show no errors

# Lint check
npm run lint --workspace=frontend
```

**Visual Verification:**
- ✅ Homepage loads with gradient ocean text
- ✅ Three feature cards displayed
- ✅ Header sticky at top
- ✅ Mobile menu toggles on small screens
- ✅ Footer displays at bottom
- ✅ /chat route shows placeholder

---

## Next Step

Proceed to Step 4: Shared Components Library

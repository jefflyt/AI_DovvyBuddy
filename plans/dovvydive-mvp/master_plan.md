# DovvyDive MVP - Master Development Plan

**Document Version:** 1.0  
**Created:** 30 November 2025  
**Based on PSD Version:** 1.0  
**Target Completion:** Q2 2026 (9 weeks)

---

## Project Overview

DovvyDive is an AI-powered dive trip planning chatbot that helps scuba divers discover, plan, and organize dive trips across Malaysia through natural conversation. The MVP features bilingual support (English/Chinese), Google ADK multi-agent architecture with Gemini 2.5 Flash, and intelligent recommendations based on certification level, weather, and safety requirements.

**Key Differentiators:**
- Multi-agent RAG system with 5 specialized diving domain agents
- English-only embeddings with LLM-driven runtime translation for efficiency
- Guest-only sessions (no authentication) for frictionless user experience
- Safety-first validation of certification requirements

---

## Architecture & Technology Stack

### Recommended Approach

**3-tier architecture with AI service layer:**
- **Frontend**: Next.js 14+ with SSR for SEO-optimized dive site pages
- **Backend**: Node.js/Express API orchestrating AI agents and managing sessions
- **AI Layer**: Google ADK multi-agent system with router-solver pattern
- **Database**: Neon PostgreSQL with pgvector for vector search and relational data

**Translation Strategy (Key Decision):**
- Store embeddings in **English only** to reduce vector storage by 50%
- Use Gemini 2.5 Flash's bilingual capabilities to translate queries/responses at runtime
- UI translations via next-i18next for static content
- Single vector index with language-agnostic semantic search

### Key Technologies

- **Frontend**: Next.js 14+ (TypeScript), Tailwind CSS, next-i18next
- **Backend**: Node.js 20+, Express.js, Prisma ORM, Zod validation
- **AI/RAG**: Google ADK, Google Gemini 2.5 Flash, Google text-embedding-004
- **Database**: Neon (Serverless PostgreSQL) with pgvector extension
- **Infrastructure**: Docker, Vercel (frontend), Render (backend), Cloudflare CDN
- **External APIs**: Google Maps API, Weather API (OpenWeatherMap/WeatherAPI.com)

### High-Level Architecture

```
[Client Browser] 
    ↓ HTTPS
[Cloudflare CDN] → [Static Assets]
    ↓
[Load Balancer]
    ↓
[Next.js Frontend (SSR)] ←→ [Express Backend API]
                                ↓
                          [Google ADK Agents]
                                ↓
                          [Gemini 2.5 Flash]
                                ↓
                     [Neon Postgres + pgvector]
```

**Multi-Agent Flow:**
```
User Query → Router Agent → [DiveSite/Weather/Logistics/Safety/MarineLife Agent]
                                        ↓
                              RAG Retrieval (English embeddings)
                                        ↓
                              Context Assembly + Translation
                                        ↓
                              Gemini 2.5 Flash (bilingual response)
```

---

## Project Phases & PR Breakdown

### Phase 1: Foundation & Infrastructure Setup

**Goal:** Establish development environment, database, and project scaffolding with bilingual support foundation.

#### PR 1.1: Repository Setup & Docker Environment
**Branch:** `1.1-repo-setup-docker`  
**Description:** Initialize monorepo structure with Docker Compose for local development  
**Goal:** Enable developers to spin up full stack locally with one command  
**Key Components/Files:**
- `docker-compose.yml` (Postgres with pgvector, backend, frontend services)
- `README.md` (setup instructions)
- `.env.example` (environment variable template)
- `.gitignore` (Node.js, Docker, IDE files)
- `package.json` (root workspace config)

**Dependencies:** None  
**Tech Details:** Use `ankane/pgvector:latest` Docker image for PostgreSQL with vector extension

---

#### PR 1.2: Neon PostgreSQL + pgvector Configuration
**Branch:** `1.2-neon-pgvector-setup`  
**Description:** Configure Neon database with pgvector extension and connection pooling  
**Goal:** Establish production database with vector search capabilities  
**Key Components/Files:**
- Neon project creation (manual step documented)
- `prisma/schema.prisma` (initial schema with vector support)
- `src/backend/config/database.ts` (connection pool setup)
- Migration script for pgvector extension

**Dependencies:** PR 1.1  
**Tech Details:** 
- Enable pgvector extension in Neon console
- Configure connection pooling (max 20 connections)
- Test vector operations with sample embedding

---

#### PR 1.3: Next.js Frontend Scaffolding
**Branch:** `1.3-nextjs-scaffolding`  
**Description:** Initialize Next.js 14+ with TypeScript, Tailwind, and basic routing  
**Goal:** Create frontend foundation with SSR support and responsive layout  
**Key Components/Files:**
- `src/frontend/app/` (App Router structure)
- `src/frontend/app/layout.tsx` (root layout with Tailwind)
- `src/frontend/app/page.tsx` (landing page placeholder)
- `src/frontend/app/chat/page.tsx` (chat interface placeholder)
- `tailwind.config.js` (theme configuration)

**Dependencies:** PR 1.1  
**Tech Details:** Use App Router for SSR, configure Tailwind with custom dive-themed colors

---

#### PR 1.4: Express Backend Scaffolding
**Branch:** `1.4-express-scaffolding`  
**Description:** Set up Express.js backend with TypeScript, routing, and middleware  
**Goal:** Create API foundation with error handling and request validation  
**Key Components/Files:**
- `src/backend/server.ts` (Express app initialization)
- `src/backend/routes/` (route modules: chat, sites, health)
- `src/backend/middleware/errorHandler.ts`
- `src/backend/middleware/requestLogger.ts`
- `src/backend/middleware/rateLimiter.ts` (placeholder)

**Dependencies:** PR 1.2  
**Tech Details:** Use `express-validator` for request validation, `helmet` for security headers

---

#### PR 1.5: i18n Framework Setup (next-i18next)
**Branch:** `1.5-i18n-setup`  
**Description:** Configure next-i18next with English as source language and Chinese translations  
**Goal:** Enable bilingual UI with manual language toggle  
**Key Components/Files:**
- `next-i18next.config.js` (i18n configuration)
- `public/locales/en/common.json` (English UI strings)
- `public/locales/zh/common.json` (Chinese UI strings)
- `src/frontend/components/LanguageToggle.tsx` (manual toggle component)
- `src/frontend/app/layout.tsx` (i18n provider integration)

**Dependencies:** PR 1.3  
**Tech Details:** 
- English as fallback language
- Manual toggle (no auto-detection in MVP)
- Persist language preference in browser localStorage

---

### Phase 2: Data Layer & RAG Pipeline

**Goal:** Build knowledge base infrastructure with English-only embeddings and vector search capabilities.

#### PR 2.1: Prisma Schema Definition
**Branch:** `2.1-prisma-schema`  
**Description:** Define complete database schema with vector columns for English embeddings  
**Goal:** Establish data models for dive sites, operators, weather, conversations  
**Key Components/Files:**
- `prisma/schema.prisma` (complete schema from TDD)
  - `DiveSite` (with `embedding_en` vector column)
  - `DiveOperator`
  - `WeatherData`
  - `Conversation`, `Message`
  - `Session`
- `prisma/migrations/` (initial migration)

**Dependencies:** PR 1.2  
**Tech Details:** 
- Use `Unsupported("vector(1536)")` for embedding columns (text-embedding-004 dimension)
- Single `embedding_en` column per DiveSite (no `embedding_zh`)
- HNSW index on `embedding_en` for fast vector search

---

#### PR 2.2: Data Ingestion Script (English Embeddings)
**Branch:** `2.2-ingestion-script`  
**Description:** Create script to process dive site content and generate English embeddings  
**Goal:** Convert dive site markdown files into vector embeddings and store in database  
**Key Components/Files:**
- `scripts/ingest-dive-sites.ts` (main ingestion script)
- `scripts/utils/chunkText.ts` (500-token chunking logic)
- `scripts/utils/generateEmbedding.ts` (Google text-embedding-004 API call)
- `data/dive-sites/` (sample dive site markdown files)

**Dependencies:** PR 2.1  
**Tech Details:** 
- Chunk English descriptions into 500-token segments
- Generate embeddings using Google text-embedding-004
- Store only English embeddings; Chinese descriptions stored as text without embeddings
- Batch processing with rate limiting (10 requests/sec)

---

#### PR 2.3: Vector Search API
**Branch:** `2.3-vector-search-api`  
**Description:** Implement semantic search endpoint using pgvector cosine similarity  
**Goal:** Enable language-agnostic retrieval of relevant dive site content  
**Key Components/Files:**
- `src/backend/services/vectorSearch.ts` (search service)
- `src/backend/routes/search.ts` (POST /api/v1/search endpoint)
- `src/backend/utils/embedding.ts` (embedding generation helper)

**Dependencies:** PR 2.2  
**Tech Details:** 
- Query: user input (any language) → embed in English → search `embedding_en`
- SQL: `ORDER BY embedding_en <-> $query_embedding LIMIT 5`
- Return top 5 chunks with metadata (siteId, name, description in both languages)
- Translation happens in LLM layer, not database layer

---

#### PR 2.4: Manual Data Curation Workflow
**Branch:** `2.4-data-curation-workflow`  
**Description:** Document process and templates for manual dive site content creation  
**Goal:** Establish workflow for 50+ dive sites with bilingual content  
**Key Components/Files:**
- `docs/data-curation-guide.md` (curation process documentation)
- `data/templates/dive-site-template.md` (content template)
- `data/dive-sites/sipadan.md` (sample completed entry)
- `scripts/validate-dive-site.ts` (content validation script)

**Dependencies:** PR 2.2  
**Tech Details:** 
- Template requires: name (EN/ZH), description (500+ words in both languages), depth, difficulty, marine life, best season
- LLM-assisted translation with human review
- Validation script checks required fields and word count

---

### Phase 3: Multi-Agent AI System

**Goal:** Integrate Google ADK with Gemini 2.5 Flash and implement 5 specialized agents with bilingual capabilities.

#### PR 3.1: Google ADK Integration
**Branch:** `3.1-google-adk-integration`  
**Description:** Set up Google ADK framework and Gemini 2.5 Flash API connection  
**Goal:** Establish AI infrastructure foundation  
**Key Components/Files:**
- `src/backend/services/adk/adkClient.ts` (ADK initialization)
- `src/backend/services/adk/geminiConfig.ts` (Gemini 2.5 Flash configuration)
- `src/backend/config/ai.ts` (API key management)
- `.env` (GOOGLE_API_KEY)

**Dependencies:** PR 1.4  
**Tech Details:** 
- Use Google ADK SDK (npm package)
- Configure Gemini 2.5 Flash with temperature=0.7 for balanced creativity
- System prompt includes bilingual instruction: "Respond in user's preferred language (English or Chinese)"

---

#### PR 3.2: Router Agent Implementation
**Branch:** `3.2-router-agent`  
**Description:** Implement intent classification router to delegate queries to specialized agents  
**Goal:** Intelligently route user queries to appropriate domain expert  
**Key Components/Files:**
- `src/backend/services/adk/agents/routerAgent.ts`
- `src/backend/services/adk/prompts/routerPrompt.ts` (intent classification prompt)

**Dependencies:** PR 3.1  
**Tech Details:** 
- Classifies intents: DIVE_SITE_DISCOVERY, WEATHER_INQUIRY, LOGISTICS_PLANNING, SAFETY_VALIDATION, MARINE_LIFE_INQUIRY, CHITCHAT
- Routes to corresponding agent
- Handles multi-domain queries by orchestrating multiple agents

---

#### PR 3.3: DiveSite Expert Agent
**Branch:** `3.3-divesite-agent`  
**Description:** Implement RAG-powered dive site recommendation agent  
**Goal:** Provide personalized dive site recommendations based on user criteria  
**Key Components/Files:**
- `src/backend/services/adk/agents/diveSiteAgent.ts`
- `src/backend/services/adk/prompts/diveSitePrompt.ts`
- Integration with vector search API (PR 2.3)

**Dependencies:** PR 3.2, PR 2.3  
**Tech Details:** 
- Query → embed → vector search → retrieve top 5 chunks
- Assemble context with English chunks + Chinese descriptions
- Gemini prompt: "Use retrieved context to recommend sites. Respond in {language}."
- Cite sources in responses

---

#### PR 3.4: Weather, Logistics, Safety, MarineLife Agents
**Branch:** `3.4-specialized-agents`  
**Description:** Implement remaining 4 specialized agents  
**Goal:** Complete multi-agent system with full domain coverage  
**Key Components/Files:**
- `src/backend/services/adk/agents/weatherAgent.ts` (Weather API integration)
- `src/backend/services/adk/agents/logisticsAgent.ts` (Itinerary generation)
- `src/backend/services/adk/agents/safetyAgent.ts` (Certification validation)
- `src/backend/services/adk/agents/marineLifeAgent.ts` (Marine life info)

**Dependencies:** PR 3.3  
**Tech Details:** 
- **Weather Agent**: Integrates Weather API (OpenWeatherMap), provides seasonal insights
- **Logistics Agent**: Generates multi-day itineraries with safety constraints (max 3-4 dives/day, 18hr surface interval before flight)
- **Safety Agent**: Validates certification level against dive site requirements, issues warnings
- **MarineLife Agent**: Retrieves marine species data from knowledge base

---

#### PR 3.5: LLM-Driven Translation in Agent Prompts
**Branch:** `3.5-llm-translation`  
**Description:** Configure agents to handle EN↔ZH translation at query/response time  
**Goal:** Enable bilingual responses without multilingual embeddings  
**Key Components/Files:**
- Update all agent prompts to include language instruction
- `src/backend/services/adk/utils/languageDetection.ts` (detect user language from input)
- `src/backend/services/adk/utils/promptBuilder.ts` (inject language preference into system prompts)

**Dependencies:** PR 3.4  
**Tech Details:** 
- System prompt: "User prefers {language}. Translate retrieved English content to {language} if needed. Respond naturally in {language}."
- Language preference passed from frontend (manual toggle state)
- Gemini 2.5 Flash handles translation without separate translation API

---

### Phase 4: Backend APIs & Session Management

**Goal:** Build REST API endpoints and implement guest-only session management with browser storage.

#### PR 4.1: Chat API Endpoint
**Branch:** `4.1-chat-api`  
**Description:** Implement POST /api/v1/chat/message endpoint with agent orchestration  
**Goal:** Enable frontend to send messages and receive AI responses  
**Key Components/Files:**
- `src/backend/routes/chat.ts` (chat routes)
- `src/backend/controllers/chatController.ts` (message handling logic)
- `src/backend/services/conversationService.ts` (conversation state management)

**Dependencies:** PR 3.5, PR 2.1  
**Tech Details:** 
- Request: `{ sessionId, message, language }`
- Response: `{ messageId, content, agent, sources, timestamp }`
- Maintains last 5 turns in context for Gemini
- Stores messages in Postgres for session duration

---

#### PR 4.2: Guest Session Management
**Branch:** `4.2-guest-sessions`  
**Description:** Implement guest-only sessions with browser storage and 24hr TTL  
**Goal:** Enable frictionless chatbot usage without authentication  
**Key Components/Files:**
- `src/backend/middleware/sessionManager.ts` (session creation/validation)
- `src/backend/services/sessionService.ts` (session CRUD operations)
- `src/backend/jobs/sessionCleanup.ts` (cron job to delete expired sessions)

**Dependencies:** PR 4.1  
**Tech Details:** 
- Generate UUID v4 on first message
- Store in browser localStorage + backend database
- TTL: 24 hours from last activity
- Cron job runs every 6 hours to delete expired sessions

---

#### PR 4.3: Rate Limiting & Abuse Prevention
**Branch:** `4.3-rate-limiting`  
**Description:** Implement rate limiting (100 messages/hour/session) to prevent abuse  
**Goal:** Protect backend from spam and excessive API usage  
**Key Components/Files:**
- `src/backend/middleware/rateLimiter.ts` (rate limit logic using Redis or in-memory store)
- Error response: HTTP 429 with retry-after header

**Dependencies:** PR 4.2  
**Tech Details:** 
- Limit: 100 messages per session per hour
- Use `express-rate-limit` with Redis store (optional)
- Fallback to in-memory store for MVP

---

#### PR 4.4: Dive Sites & Export Endpoints
**Branch:** `4.4-sites-export-endpoints`  
**Description:** Implement GET /api/v1/sites and POST /api/v1/export endpoints  
**Goal:** Enable dive site browsing and trip plan export (PDF/JSON)  
**Key Components/Files:**
- `src/backend/routes/sites.ts` (GET /sites, GET /sites/:slug)
- `src/backend/routes/export.ts` (POST /export/pdf, POST /export/json)
- `src/backend/services/pdfGenerator.ts` (PDF generation using Puppeteer or PDFKit)

**Dependencies:** PR 2.1  
**Tech Details:** 
- `/sites` returns paginated list with filters (difficulty, certification)
- `/sites/:slug` returns full dive site details for SSR pages
- PDF export generates trip itinerary with branding

---

#### PR 4.5: Google Maps & Weather API Integration
**Branch:** `4.5-external-apis`  
**Description:** Integrate Google Maps API and Weather API  
**Goal:** Provide location visualization and real-time weather data  
**Key Components/Files:**
- `src/backend/services/googleMaps.ts` (Maps API client)
- `src/backend/services/weatherApi.ts` (Weather API client)
- `src/backend/routes/maps.ts` (GET /api/v1/maps/location)

**Dependencies:** PR 4.4  
**Tech Details:** 
- Google Maps: Geocoding, Distance Matrix for dive site locations
- Weather API: Current weather + 7-day forecast
- Cache weather data for 6 hours to reduce API calls

---

### Phase 5: Frontend Chat Interface

**Goal:** Build responsive chat UI with manual language toggle, SSR dive site pages, and export functionality.

#### PR 5.1: Chat Interface Component
**Branch:** `5.1-chat-interface`  
**Description:** Create chat UI with message history, typing indicators, and input field  
**Goal:** Enable users to interact with AI chatbot  
**Key Components/Files:**
- `src/frontend/app/chat/page.tsx` (chat page)
- `src/frontend/components/ChatMessage.tsx` (message bubble component)
- `src/frontend/components/ChatInput.tsx` (input field with send button)
- `src/frontend/components/TypingIndicator.tsx` (animated dots)
- `src/frontend/hooks/useChat.ts` (API integration hook)

**Dependencies:** PR 4.1  
**Tech Details:** 
- WebSocket or polling for real-time updates (use polling for MVP simplicity)
- Auto-scroll to latest message
- Display agent badges (DiveSite, Weather, etc.)
- Rich media support (images, links)

---

#### PR 5.2: Language Toggle & Localization
**Branch:** `5.2-language-toggle`  
**Description:** Implement manual language toggle with next-i18next integration  
**Goal:** Enable users to switch between English and Chinese  
**Key Components/Files:**
- `src/frontend/components/LanguageToggle.tsx` (updated with state management)
- `src/frontend/app/layout.tsx` (language preference provider)
- `src/frontend/hooks/useLanguage.ts` (language state hook)

**Dependencies:** PR 1.5, PR 5.1  
**Tech Details:** 
- Toggle button in header (EN | 中文)
- Persist preference in localStorage
- Pass language to chat API in message payload
- All UI elements localized via next-i18next

---

#### PR 5.3: SSR Dive Site Detail Pages
**Branch:** `5.3-ssr-dive-sites`  
**Description:** Create SEO-optimized dive site detail pages with server-side rendering  
**Goal:** Improve SEO and enable direct access to dive site information  
**Key Components/Files:**
- `src/frontend/app/sites/[slug]/page.tsx` (dynamic SSR page)
- `src/frontend/components/DiveSiteCard.tsx` (site detail component)
- `src/frontend/components/DiveSiteGallery.tsx` (image gallery)

**Dependencies:** PR 4.4  
**Tech Details:** 
- Fetch dive site data server-side using `generateStaticParams` (optional pre-rendering)
- OpenGraph meta tags for social sharing
- Structured data (JSON-LD) for Google rich results

---

#### PR 5.4: Map Visualization & Export
**Branch:** `5.4-maps-export`  
**Description:** Integrate Google Maps for location display and implement PDF/JSON export  
**Goal:** Enable users to visualize dive sites and save trip plans  
**Key Components/Files:**
- `src/frontend/components/DiveMap.tsx` (Google Maps component)
- `src/frontend/components/ExportButton.tsx` (export dropdown with PDF/JSON options)
- `src/frontend/utils/exportHelpers.ts` (client-side export logic)

**Dependencies:** PR 4.5, PR 5.1  
**Tech Details:** 
- Use `@googlemaps/react-wrapper` for Maps integration
- PDF: call backend API (POST /api/v1/export/pdf)
- JSON: client-side export of conversation state
- Prominent "Export Trip" button in chat sidebar

---

#### PR 5.5: Session Storage Warnings & UX Polish
**Branch:** `5.5-session-warnings-ux`  
**Description:** Add session expiry warnings and final UX polish  
**Goal:** Inform users about session limitations and improve overall experience  
**Key Components/Files:**
- `src/frontend/components/SessionWarning.tsx` (banner warning about 24hr expiry)
- `src/frontend/components/SuggestedQuestions.tsx` (suggested follow-up questions)
- `src/frontend/app/page.tsx` (landing page with example prompts)
- Mobile responsiveness improvements

**Dependencies:** PR 5.4  
**Tech Details:** 
- Display warning: "Your conversation is saved for 24 hours. Export to keep it longer."
- Suggested questions appear after AI responses
- Mobile: full-screen chat, collapsible sidebar
- Loading states, error handling, empty states

---

### Phase 6: Testing, SEO & Deployment

**Goal:** Validate functionality, optimize for search engines, and deploy to production.

#### PR 6.1: End-to-End Testing
**Branch:** `6.1-e2e-tests`  
**Description:** Write E2E tests for critical user flows  
**Goal:** Ensure core functionality works end-to-end  
**Key Components/Files:**
- `tests/e2e/chat-flow.spec.ts` (chat interaction tests)
- `tests/e2e/dive-site-pages.spec.ts` (SSR page tests)
- `tests/e2e/export.spec.ts` (PDF/JSON export tests)
- Playwright or Cypress configuration

**Dependencies:** PR 5.5  
**Tech Details:** 
- Test scenarios: first chat message, multi-turn conversation, language toggle, export trip, session expiry
- Mock AI responses for predictable tests
- Run in CI pipeline (GitHub Actions)

---

#### PR 6.2: Dynamic Sitemap & SEO Optimization
**Branch:** `6.2-sitemap-seo`  
**Description:** Generate dynamic sitemap and optimize SEO meta tags  
**Goal:** Improve search engine discoverability  
**Key Components/Files:**
- `src/frontend/app/sitemap.ts` (dynamic sitemap generation)
- `src/frontend/app/robots.txt` (robots file)
- `src/frontend/components/SEOHead.tsx` (meta tag component)

**Dependencies:** PR 5.3  
**Tech Details:** 
- Sitemap: include all dive site pages (`/sites/[slug]`)
- Meta tags: title, description, OpenGraph, Twitter Card
- Canonical URLs for duplicate content prevention

---

#### PR 6.3: Vercel & Render Deployment Configuration
**Branch:** `6.3-deployment-config`  
**Description:** Configure deployment to Vercel (frontend) and Render (backend)  
**Goal:** Deploy production-ready MVP  
**Key Components/Files:**
- `vercel.json` (Vercel configuration)
- `render.yaml` (Render configuration for backend)
- Environment variable documentation
- Deployment guide in `docs/deployment.md`

**Dependencies:** PR 6.2  
**Tech Details:** 
- Vercel: Auto-deploy from `main` branch, SSR enabled
- Render: Docker-based deployment with auto-scaling
- Environment variables set in platform dashboards
- Neon database connection string configured

---

#### PR 6.4: Bilingual UX Validation & Bug Fixes
**Branch:** `6.4-bilingual-validation`  
**Description:** Validate bilingual experience and fix language-related bugs  
**Goal:** Ensure seamless EN↔ZH experience  
**Key Components/Files:**
- Manual testing checklist in `docs/testing/bilingual-validation.md`
- Bug fixes based on testing results

**Dependencies:** PR 6.3  
**Tech Details:** 
- Test: English query → Chinese response, Chinese query → English response
- Validate UI translations (all buttons, labels, messages)
- Check PDF export in both languages
- Fix translation inconsistencies or missing strings

---

#### PR 6.5: Production Monitoring & Analytics Setup
**Branch:** `6.5-monitoring-analytics`  
**Description:** Set up error tracking, logging, and analytics  
**Goal:** Monitor production health and user behavior  
**Key Components/Files:**
- Sentry integration for error tracking
- Google Analytics 4 integration
- Backend logging (Winston or Pino)
- `docs/monitoring-guide.md`

**Dependencies:** PR 6.4  
**Tech Details:** 
- Sentry: track frontend and backend errors
- GA4: track page views, chat interactions, export actions
- Backend logs: structured JSON logs with request IDs
- Set up alerts for critical errors (5xx responses, LLM API failures)

---

## Implementation Sequence

**Sequential Phases:**
1. Phase 1 (Foundation) → Phase 2 (Data Layer) → Phase 3 (AI Agents) → Phase 4 (Backend APIs) → Phase 5 (Frontend) → Phase 6 (Testing & Deployment)

**Within each phase:**
- PRs should be completed in order where dependencies exist
- Some PRs can be parallelized (e.g., PR 1.3 and PR 1.4 can run concurrently)

**Recommended Timeline:**
- **Week 1-2**: Phase 1 (Foundation)
- **Week 3-4**: Phase 2 (Data Layer) + start manual data curation
- **Week 5-6**: Phase 3 (AI Agents)
- **Week 7**: Phase 4 (Backend APIs)
- **Week 8**: Phase 5 (Frontend)
- **Week 9**: Phase 6 (Testing & Deployment)

---

## Testing Strategy

### Unit Tests
- Backend services (vector search, session management, agents)
- Frontend components (ChatMessage, LanguageToggle)
- Utility functions (embedding, chunking, validation)

### Integration Tests
- API endpoints (chat, sites, export)
- Database operations (Prisma queries, vector search)
- External API integrations (Maps, Weather)

### End-to-End Tests
- Critical user flows (chat, language toggle, export)
- SSR dive site pages
- Session expiry and cleanup

### Manual Testing
- Bilingual UX validation (EN↔ZH)
- Mobile responsiveness
- Accessibility (keyboard navigation, screen readers)

---

## Success Criteria

**MVP is complete when:**
1. ✅ Users can chat with AI in English or Chinese via manual toggle
2. ✅ AI recommends dive sites based on certification level and preferences
3. ✅ System validates safety (certification vs. site requirements)
4. ✅ Multi-day itineraries generated with surface interval enforcement
5. ✅ Weather and seasonal insights provided
6. ✅ Trip plans exportable as PDF or JSON
7. ✅ 50+ Malaysian dive sites in knowledge base (bilingual content)
8. ✅ Guest sessions work without authentication (24hr persistence)
9. ✅ SSR dive site pages indexed by Google
10. ✅ Rate limiting prevents abuse (100 msg/hr/session)
11. ✅ Production deployed on Vercel + Render with monitoring
12. ✅ E2E tests pass for critical flows

**Performance Targets:**
- 90% of AI responses within 5 seconds
- Page load time < 2 seconds on 4G
- 99.5% uptime during peak hours
- Vector search < 1 second latency

---

## Known Constraints & Considerations

### Technical Constraints
1. **English-Only Embeddings:** Chinese queries will be translated to English for embedding; semantic search happens in English space. May have slight accuracy loss for Chinese idiomatic expressions.
2. **Guest-Only Sessions:** No persistent user accounts means no personalization across sessions. Users must export trips manually.
3. **Manual Language Toggle:** No auto-detection in MVP; users must manually switch languages.
4. **Google ADK Dependency:** Core functionality relies on Google ADK availability. No fallback LLM in MVP.

### Business Constraints
1. **Manual Data Curation:** 50+ dive sites require 3 months of manual content creation with human review of translations.
2. **No Booking/Payment:** Platform is recommendation-only; users contact operators directly.
3. **MVP Geography:** Malaysia only; expansion to Indonesia/Philippines deferred to V2.

### User Experience Considerations
1. **Session Warnings:** Prominent warnings needed about 24hr session expiry to avoid user frustration.
2. **Translation Quality:** Gemini 2.5 Flash translation quality should be validated with native speakers during Phase 6.
3. **Mobile-First:** Majority of users likely on mobile; responsive design critical.

### Risk Mitigation
1. **Google ADK Availability:** Monitor ADK release schedule; have backup plan to use raw Gemini API if ADK delayed.
2. **Data Quality:** Implement validation scripts (PR 2.4) to ensure all dive sites meet content requirements.
3. **Rate Limiting Tuning:** 100 msg/hr may need adjustment based on real-world usage patterns.

---

**End of Master Plan**

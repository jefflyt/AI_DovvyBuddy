# Technical Design Document (TDD)

## DovvyDive - AI-Powered Dive Trip Planner

**Document Version:** 1.0
**Last Updated:** 28 November 2025
**Status:** Draft
**Based on PSD Version:** 1.0

---

## 1. Introduction

### 1.1 Purpose

This document defines the technical architecture, system design, and implementation details for DovvyDive, an AI-powered dive trip planning platform. It serves as the blueprint for the engineering team to build the MVP.

### 1.2 Scope

The scope covers the MVP implementation including:

- Web-based Chat Interface (Frontend)
- Backend API & Orchestration Layer
- Multi-Agent RAG System using Google ADK & Google Gemini 2.5 Flash
- Neon (Serverless PostgreSQL) with pgvector
- Data Ingestion Pipeline for Dive Sites

### 1.3 Definitions

- **ADK**: Google Agent Development Kit
- **RAG**: Retrieval-Augmented Generation
- **SSR**: Server-Side Rendering
- **MVP**: Minimum Viable Product

---

## 2. System Architecture

### 2.1 High-Level Architecture

The system follows a standard 3-tier web architecture enhanced with an AI service layer.

```mermaid
graph TD
    Client[Client Browser] -->|HTTPS/JSON| CDN[CDN (Cloudflare)]
    CDN -->|Static Assets| Client
    CDN -->|API Requests| LB[Load Balancer]
    
    subgraph "Application Cluster"
        LB --> Frontend[Next.js Frontend (SSR)]
        Frontend -->|Internal API| Backend[Node.js Backend API]
    end
    
    subgraph "Data Layer"
        Backend -->|SQL/Vector| DB[(Neon Postgres + pgvector)]
        Backend -->|Session State| Redis[Redis (Optional for Session)]
    end
    
    subgraph "AI Service Layer"
        Backend -->|Agent Orchestration| ADK[Google ADK Service]
        ADK -->|Inference| LLM[Gemini 2.5 Flash API]
        ADK -->|Retrieval| DB
    end
    
    subgraph "External Services"
        Backend -->|Maps| GMap[Google Maps API]
        Backend -->|Weather| Weather[Weather API]
    end
```

### 2.2 Component Description

1. **Frontend (Next.js)**:
   - Handles UI rendering, chat interface, and state management.
   - Implements Server-Side Rendering (SSR) for SEO (Dive Site pages).
   - Manages browser session storage for guest persistence.

2. **Backend (Node.js/Express)**:
   - RESTful API for frontend communication.
   - Orchestrates calls to the AI service.
   - Manages session validation and rate limiting.
   - Handles data retrieval from Neon Postgres.

3. **AI Service (Google ADK)**:
   - Implements the Multi-Agent architecture.
   - Manages the RAG pipeline (query embedding -> vector search -> context assembly).
   - Interfaces with Google Gemini 2.5 Flash.

4. **Database (Neon Postgres)**:
   - Stores relational data (Dive Sites, Operators).
   - Stores vector embeddings (pgvector) for semantic search.

---

## 3. Technology Stack

### 3.1 Frontend

- **Framework**: Next.js 14+ (React)
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **State Management**: React Context / Zustand
- **HTTP Client**: Axios / Fetch
- **Internationalization**: next-i18next

### 3.2 Backend

- **Runtime**: Node.js 20+
- **Framework**: Express.js
- **Language**: TypeScript
- **Validation**: Zod
- **ORM**: Prisma (supports pgvector)

### 3.3 AI & Data

- **LLM**: Google Gemini 2.5 Flash (via Google AI Studio)
- **Framework**: Google ADK (Agent Development Kit)
- **Database**: Neon (Serverless PostgreSQL) with `vector` extension
- **Embeddings**: Google `text-embedding-004` (or similar high-performance model)

### 3.4 Infrastructure

- **Containerization**: Docker & Docker Compose
- **CDN**: Cloudflare
- **Hosting**: Vercel (Frontend) + Render (Backend)

---

## 4. Data Design

### 4.1 Database Schema (Prisma)

#### User & Session (Ephemeral)

*Note: No permanent user table for MVP, but session tracking is required.*

```prisma
model Session {
  id        String   @id @default(uuid())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  expiresAt DateTime
  metadata  Json?    // Stores temp preferences
  
  conversations Conversation[]
}

model Conversation {
  id        String   @id @default(uuid())
  sessionId String
  session   Session  @relation(fields: [sessionId], references: [id])
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  messages  Message[]
}

model Message {
  id             String   @id @default(uuid())
  conversationId String
  conversation   Conversation @relation(fields: [conversationId], references: [id])
  role           Role     // USER, ASSISTANT, SYSTEM
  content        String   @db.Text
  agentUsed      String?  // Which agent handled this
  createdAt      DateTime @default(now())
}

enum Role {
  USER
  ASSISTANT
  SYSTEM
}
```

#### Content Data

```prisma
model DiveSite {
  id                  String   @id @default(uuid())
  name                String
  slug                String   @unique // For SEO URLs
  country             String
  region              String
  description_en      String   @db.Text
  description_zh      String   @db.Text
  latitude            Float
  longitude           Float
  difficulty          DifficultyLevel
  minCertification    CertificationLevel
  depthMin            Int
  depthMax            Int
  
  // RAG Embeddings
  embedding_en        Unsupported("vector(1536)")?
  embedding_zh        Unsupported("vector(1536)")?
  
  operators           DiveOperator[]
  weatherData         WeatherData[]
  
  updatedAt           DateTime @updatedAt
}

model DiveOperator {
  id          String   @id @default(uuid())
  name        String
  type        OperatorType // RESORT, DIVE_CENTER, LIVEABOARD
  contactInfo Json
  diveSites   DiveSite[]
}

enum DifficultyLevel {
  BEGINNER
  INTERMEDIATE
  ADVANCED
  TECHNICAL
}

enum OperatorType {
  RESORT
  DIVE_CENTER
  LIVEABOARD
}
```

### 4.2 Vector Search Strategy

- **Index**: HNSW index on `embedding_en` and `embedding_zh` columns.
- **Query**:

```sql
SELECT id, name, description_en 
FROM "DiveSite" 
ORDER BY embedding_en <-> $query_embedding 
LIMIT 5;
```

---

## 5. API Design

### 5.1 REST Endpoints

#### Chat

- `POST /api/v1/chat/message`
  - **Body**: `{ sessionId: string, message: string, language: 'en' | 'zh' }`
  - **Response**: `{ messageId: string, content: string, agent: string, sources: [] }`
  - **Behavior**: Synchronous (Batch) response.

#### Dive Sites

- `GET /api/v1/sites` (Search/Filter)
- `GET /api/v1/sites/:slug` (Details for SEO pages)

#### Utility

- `POST /api/v1/export/pdf` (Generate Trip PDF)
- `GET /api/v1/health` (Health check)

---

## 6. AI Architecture

### 6.1 Multi-Agent System (Google ADK)

The system uses a "Router-Solver" pattern.

1. **Router Agent**:
   - Analyzes user input.
   - Classifies intent: `DISCOVERY`, `PLANNING`, `SAFETY`, `WEATHER`, `CHITCHAT`.
   - Routes to specific sub-agent.

2. **Specialized Agents**:
   - **DiveSiteAgent**: Uses RAG to find sites matching criteria.
   - **LogisticsAgent**: Handles itinerary construction.
   - **SafetyAgent**: Checks certification vs. site depth/difficulty.
   - **WeatherAgent**: Fetches weather API data.

### 6.2 RAG Pipeline

1. **Ingestion**:
   - Markdown files of dive sites -> Chunking (500 tokens) -> Embedding -> Neon Postgres.
2. **Retrieval**:
   - User Query -> Embedding -> Vector Search (Neon Postgres) -> Top K Chunks.
3. **Generation**:
   - System Prompt + Conversation History (Last 5 turns) + Retrieved Chunks + User Query -> Gemini 2.5 Flash -> Response.

### 6.3 Prompt Engineering Strategy

- **System Prompts**: Enforce "Helpful Divemaster" persona.
- **Safety Guardrails**: Inject instructions to *always* disclaim medical advice and verify certification.
- **Language**: Dynamic system prompt injection based on detected language (`en` or `zh`).

---

## 7. Security & Compliance

### 7.1 Authentication

- **Guest Access**: UUID v4 generated on client, stored in `HttpOnly` cookie or LocalStorage.
- **Session Expiry**: 24-hour TTL enforced by backend/database cleanup job.

### 7.2 Data Protection

- **Input Sanitization**: All user inputs sanitized to prevent XSS/Injection.
- **PII**: No PII stored permanently. Session data is ephemeral.

### 7.3 Rate Limiting

- **Limit**: 100 requests / hour / IP.
- **Implementation**: Redis-based or Memory-based rate limiter middleware.

---

## 8. Performance & Scalability

### 8.1 Caching

- **Static Content**: Cloudflare CDN for images, CSS, JS.
- **API Responses**: Cache `GET /api/v1/sites/:slug` responses for 1 hour.
- **Weather Data**: Cache external Weather API responses for 6 hours.

### 8.2 SEO Optimization

- **SSR**: Next.js renders dive site detail pages on the server.
- **Sitemap**: Dynamic `sitemap.xml` generation based on `DiveSite` table.
- **Metadata**: Dynamic OpenGraph tags for each dive site.

---

## 9. Deployment & DevOps

### 9.1 CI/CD Pipeline

- **Source**: GitHub
- **CI**: GitHub Actions (Lint, Test, Build)
- **CD**: Auto-deploy to staging on merge to `main`.

### 9.2 Environment Variables

- `DATABASE_URL`
- `GOOGLE_API_KEY`
- `GOOGLE_MAPS_KEY`
- `WEATHER_API_KEY`
- `JWT_SECRET` (for session signing)

### 9.3 Local Development

- `docker-compose.yml` spins up:
  - Postgres (with pgvector image)
  - Node.js Backend
  - Next.js Frontend (dev mode)

---

## 10. Implementation Roadmap (MVP)

1. **Phase 1: Foundation (Week 1-2)**
   - Setup Repo, Docker, Postgres+pgvector.
   - Basic Next.js + Express scaffolding.
2. **Phase 2: Data & RAG (Week 3-4)**
   - Define Dive Site Schema.
   - Implement Ingestion Script.
   - Build Vector Search API.
3. **Phase 3: AI Agents (Week 5-6)**
   - Integrate Google ADK.
   - Implement Router and DiveSite Agent.
   - Connect Google Gemini 2.5 Flash.
4. **Phase 4: Frontend & UI (Week 7-8)**
   - Chat Interface.
   - Dive Site Detail Pages.
   - Map Integration.
5. **Phase 5: Testing & Polish (Week 9)**
   - End-to-end testing.
   - SEO verification.
   - Deployment.

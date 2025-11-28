# Product Specification Document (PSD)

## DovvyDive - AI-Powered Dive Trip Planner

**Document Version:** 1.0  
**Last Updated:** 28 November 2025  
**Product Owner:** TBD  
**Target Launch:** Q2 2026  

---

## 1. Product Overview

### What the Product Is

DovvyDive is a web-based AI-powered dive trip planning platform featuring an intelligent chatbot that helps scuba divers discover, plan, and organize dive trips across the Asia Pacific region, starting with Malaysia.

### Who It Is For

- Recreational scuba divers (beginner to advanced)
- Dive enthusiasts planning their next adventure
- Travel agencies specializing in dive tourism
- Dive operators seeking to reach international customers

### Core Problems It Solves

1. **Information Overload**: Divers struggle to find reliable, consolidated information about dive sites across multiple sources
2. **Trip Planning Complexity**: Coordinating dive sites, accommodations, weather, certifications, and logistics is time-consuming
3. **Local Knowledge Gap**: International divers lack insider knowledge about hidden gems and local conditions
4. **Safety Concerns**: Difficulty assessing dive site suitability based on skill level and conditions

### Primary Value Proposition

An intelligent, conversational AI assistant that understands diving terminology, safety requirements, and local expertise to provide personalized, comprehensive dive trip recommendations in minutes instead of hours of research.

### Success Metrics

- **User Engagement**: 70%+ of users complete at least one full trip planning conversation
- **Response Accuracy**: 90%+ user satisfaction with AI recommendations
- **Operator Contact Rate**: 40%+ of users with trip plans contact dive operators or export trip details
- **Time Saved**: Average 80% reduction in trip planning time vs. manual research
- **Geographic Coverage**: 100+ dive sites catalogued within 6 months
- **Session Completion**: 60%+ of sessions result in exported trip plan or operator contact
- **Returning Visitor Rate**: 15%+ of users return within 3 months to plan another trip

---

## 2. User Personas

### Persona 1: Sarah - The Adventure Seeker

- **Role**: Experienced recreational diver (Advanced Open Water certified)
- **Age**: 28-35
- **Location**: Singapore, Hong Kong, or major Asia Pacific cities
- **Goals**:
  - Discover new and exciting dive sites
  - Plan weekend or week-long dive trips
  - Find dive buddies and group trips
  - Optimize travel budget while maximizing dive experiences
- **Pain Points**:
  - Limited time to research dive destinations
  - Uncertainty about dive site conditions and difficulty
  - Wants authentic local recommendations beyond tourist traps
  - Struggles to coordinate logistics (transport, accommodation, dive operators)

### Persona 2: Marcus - The Cautious Beginner

- **Role**: Newly certified diver (Open Water certified within last year)
- **Age**: 35-45
- **Location**: Major cities in Malaysia, Thailand, or Australia
- **Goals**:
  - Build confidence with beginner-friendly dive sites
  - Understand safety requirements and conditions
  - Learn about marine life and what to expect
  - Find reputable dive operators with good safety records
- **Pain Points**:
  - Intimidated by advanced dive sites
  - Needs reassurance about safety and skill requirements
  - Overwhelmed by technical diving terminology
  - Worried about equipment rental quality

### Persona 3: Lisa - The Dive Travel Organizer

- **Role**: Travel agency coordinator or dive club organizer
- **Age**: 30-50
- **Location**: Any Asia Pacific region
- **Goals**:
  - Plan group dive trips for 10-30 people
  - Compare multiple destinations efficiently
  - Get bulk pricing and availability information
  - Coordinate complex itineraries with multiple dive sites
- **Pain Points**:
  - Manual coordination with multiple dive operators
  - Difficulty finding sites suitable for mixed skill levels
  - Budget management across accommodation, diving, and transport
  - Last-minute weather or availability changes

---

## 3. Core Use Cases

1. **UC-1: First-Time Dive Trip Discovery** - New user asks chatbot for beginner-friendly dive site recommendations in Malaysia
2. **UC-2: Skill-Based Site Matching** - User provides certification level and experience, AI recommends appropriate dive sites
3. **UC-3: Weather-Aware Planning** - User plans trip for specific dates, AI provides seasonal weather and visibility insights
4. **UC-4: Budget-Conscious Planning** - User sets budget constraints, AI recommends cost-effective dive packages
5. **UC-5: Multi-Day Itinerary Creation** - User requests 3-5 day trip plan, AI generates daily dive schedule with logistics
6. **UC-6: Marine Life Interest Matching** - User expresses interest in specific marine life (e.g., turtles, sharks), AI suggests best locations
7. **UC-7: Equipment Rental Guidance** - User asks about equipment availability and rental costs at destination
8. **UC-8: Safety and Certification Validation** - User asks if they're qualified for a specific dive site, AI provides safety assessment
9. **UC-9: Alternative Recommendation** - User dislikes initial recommendation, AI provides alternatives based on feedback
10. **UC-10: Group Trip Planning** - User plans trip for mixed skill group, AI recommends sites suitable for all levels
11. **UC-11: Dive Operator Comparison** - User compares dive operators at same location based on reviews, pricing, safety
12. **UC-12: Trip Refinement** - User modifies existing trip plan (dates, location, activities), AI updates recommendations

---

## 4. User Stories

### Discovery & Exploration

- **US-1**: As Sarah, I want to ask the chatbot "What are the best wreck dives in Malaysia?" so that I can discover exciting advanced dive sites
- **US-2**: As Marcus, I want to filter dive sites by certification level so that I only see sites appropriate for my Open Water certification
- **US-3**: As Sarah, I want to see photos and videos of dive sites so that I can visualize the experience before booking
- **US-4**: As Marcus, I want to read about marine life I might encounter so that I know what to expect underwater

### Planning & Logistics

- **US-5**: As Lisa, I want to plan a 4-day dive trip for 15 people with mixed skill levels so that everyone has a suitable diving experience
- **US-6**: As Sarah, I want to know the best time of year to dive in Sipadan so that I can plan when visibility and weather are optimal
- **US-7**: As Marcus, I want recommendations for dive resorts that include accommodation so that I can book everything in one place
- **US-8**: As Lisa, I want to export trip itinerary as PDF so that I can share it with my group members

### Safety & Requirements

- **US-9**: As Marcus, I want to verify if my certification is sufficient for a specific dive site so that I can dive safely
- **US-10**: As Sarah, I want to know current water temperature and visibility so that I can pack appropriate gear
- **US-11**: As Marcus, I want to see safety records and reviews of dive operators so that I can choose reputable providers
- **US-12**: As Sarah, I want to understand emergency medical facilities nearby so that I feel confident about safety

### Conversation & Interaction

- **US-13**: As Sarah, I want to ask follow-up questions in natural language so that I can refine my trip plan conversationally
- **US-14**: As Marcus, I want the AI to explain diving terminology I don't understand so that I can make informed decisions
- **US-15**: As Lisa, I want to save my conversation history so that I can return to my trip plan later
- **US-16**: As Sarah, I want to provide feedback on recommendations so that the AI learns my preferences

### Language & Localization

- **US-17**: As a Chinese-speaking diver, I want to interact with the chatbot in Chinese so that I can plan my trip in my preferred language
- **US-18**: As a bilingual user, I want to switch between English and Chinese so that I can share information with friends in different languages

### Operator Contact & Information

- **US-19**: As Sarah, I want to access dive operator contact information directly from the chat so that I can reach out to make inquiries
- **US-20**: As Marcus, I want to see dive operator details and hours of operation so that I know when to contact them
- **US-21**: As Lisa, I want to export operator contact information so that I can request group quotes offline

---

## 5. Feature List

### F-1: Conversational AI Chatbot (P0)

- **Description**: Natural language chatbot powered by Google ADK multi-agent RAG system using Google Gemini 2.5 Flash
- **Priority**: P0 (Core feature)
- **Dependencies**: Google ADK framework, Google Gemini 2.5 Flash API access, RAG knowledge base

### F-2: Malaysia Dive Site Knowledge Base (P0)

- **Description**: Comprehensive database of Malaysian dive sites with details on depth, difficulty, marine life, accessibility
- **Priority**: P0 (Required for MVP)
- **Dependencies**: Data collection, RAG indexing

### F-3: User Profile & Authentication (Future)

- **Description**: User registration and login to save conversation history and preferences
- **Priority**: Future feature (not in MVP)
- **Dependencies**: Authentication system

### F-4: Certification Level Filtering (P0)

- **Description**: Filter and recommend dive sites based on PADI/SSI certification levels
- **Priority**: P0 (Critical for safety)
- **Dependencies**: Dive site database with difficulty ratings

### F-5: Weather & Seasonal Insights (P1)

- **Description**: AI provides weather patterns, visibility, and best diving seasons
- **Priority**: P1 (High value for planning)
- **Dependencies**: Historical weather data, external weather API integration

### F-6: Multi-Day Itinerary Generation (P1)

- **Description**: AI creates detailed day-by-day trip plans with dive schedules, logistics, accommodation
- **Priority**: P1 (Key differentiator)
- **Dependencies**: F-2, F-5, accommodation database

### F-7: Dive Operator Directory (P1)

- **Description**: Database of verified dive operators with pricing, reviews, contact information
- **Priority**: P1 (Conversion driver)
- **Dependencies**: Operator partnership or data scraping

### F-8: Equipment Rental Information (P2)

- **Description**: Details on equipment rental availability, costs, and quality at each location
- **Priority**: P2 (Nice to have)
- **Dependencies**: F-7

### F-9: Marine Life Encyclopedia (P2)

- **Description**: Searchable database of marine species found at each dive site with photos
- **Priority**: P2 (Enhances experience)
- **Dependencies**: Marine biology data collection

### F-10: Interactive Map Visualization (P1)

- **Description**: Visual map showing dive sites, accommodations, and travel routes
- **Priority**: P1 (UX enhancement)
- **Dependencies**: Mapping API (Google Maps or Mapbox)

### F-11: Trip Export & Sharing (P2)

- **Description**: Export trip itinerary as PDF or shareable link
- **Priority**: P2 (Group planning feature)
- **Dependencies**: F-6, PDF generation library

### F-12: Conversation History & Saved Trips (Future)

- **Description**: Save and resume past conversations and trip plans (requires user accounts)
- **Priority**: Future feature (not in MVP)
- **Dependencies**: F-3 (User Authentication), database storage

### F-13: Multi-Agent Orchestration (P0)

- **Description**: Specialized AI agents for dive sites, weather, logistics, safety, and marine life
- **Priority**: P0 (Core architecture)
- **Dependencies**: Google ADK multi-agent framework

### F-14: Safety Validation Agent (P0)

- **Description**: Dedicated agent that validates certification requirements and provides safety warnings
- **Priority**: P0 (Legal and safety requirement)
- **Dependencies**: F-13, certification standards database

### F-15: Budget Optimization (P2)

- **Description**: AI optimizes recommendations based on user budget constraints
- **Priority**: P2 (Value-add feature)
- **Dependencies**: Pricing database

### F-16: Bilingual Language Support (P0)

- **Description**: Support English and Chinese languages with auto-detection, manual switching, and multilingual content
- **Priority**: P0 (Core feature for target market)
- **Dependencies**: Translation database, LLM language capabilities, i18n framework

---

## 6. Functional Requirements

### Chat Interface

- **FR-1**: System SHALL display a prominent chat interface on homepage with welcome message and example prompts
- **FR-2**: System SHALL accept text input from users with minimum 500 character limit per message
- **FR-3**: System SHALL respond to user messages within 5 seconds for 90% of queries
- **FR-4**: System SHALL display typing indicators while AI is processing response
- **FR-5**: System SHALL support conversation threading with clear message history chronologically ordered
- **FR-6**: System SHALL allow users to edit or delete their last message before AI responds
- **FR-7**: System SHALL support rich media in responses (images, maps, embedded links)
- **FR-8**: System SHALL provide "suggested follow-up questions" after each AI response

### Multi-Agent RAG System

- **FR-9**: System SHALL implement Google ADK framework with minimum 5 specialized agents:
  - Dive Site Expert Agent
  - Weather & Seasonality Agent
  - Logistics & Planning Agent
  - Safety & Certification Agent
  - Marine Life Specialist Agent
- **FR-10**: System SHALL use Google Gemini 2.5 Flash as the underlying LLM for all agents
- **FR-11**: System SHALL route user queries to the most appropriate agent based on intent classification
- **FR-12**: System SHALL orchestrate multi-agent responses when query requires multiple domains of knowledge
- **FR-13**: System SHALL retrieve and cite sources from RAG knowledge base in agent responses
- **FR-14**: RAG knowledge base SHALL be updated at minimum monthly with new dive site information
- **FR-15**: System SHALL implement semantic search over knowledge base with minimum 80% retrieval accuracy
- **FR-16**: System SHALL maintain conversation context across agent handoffs within same session

### Dive Site Recommendations

- **FR-17**: System SHALL recommend dive sites based on user certification level (Open Water, Advanced, Rescue, Divemaster)
- **FR-18**: System SHALL NOT recommend dive sites that exceed user's stated certification level without explicit safety warning
- **FR-19**: System SHALL filter dive sites by depth range (0-18m for OW, 0-30m for AOW, 30m+ for tech divers)
- **FR-20**: System SHALL provide for each recommended dive site:
  - Name and location
  - Difficulty rating (Beginner/Intermediate/Advanced)
  - Depth range
  - Typical visibility range
  - Water temperature range
  - Required certification minimum
  - Marine life highlights
  - Best season to dive
  - Estimated cost range
- **FR-21**: System SHALL rank recommendations by relevance score based on user preferences and query
- **FR-22**: System SHALL provide minimum 3 alternative recommendations when user rejects initial suggestion

### Safety & Validation

- **FR-23**: System SHALL display safety disclaimer on first chat interaction requiring user acknowledgment
- **FR-23a**: Disclaimer SHALL state that all recommendations are informational only and users must confirm details, availability, and safety with dive operators before booking
- **FR-23b**: System SHALL require user to confirm they are 18 years or older before starting the chat
- **FR-24**: System SHALL warn users when they ask about dive sites beyond their certification level
- **FR-25**: System SHALL recommend DAN (Divers Alert Network) or equivalent insurance for all trips
- **FR-26**: System SHALL provide emergency contact information and nearest hyperbaric chambers for recommended dive sites
- **FR-27**: System SHALL validate user certification level against dive site requirements before generating itinerary
- **FR-28**: System SHALL include safety briefing information in all itinerary exports

### Weather & Seasonal Intelligence

- **FR-29**: System SHALL provide month-by-month weather patterns for each dive site
- **FR-30**: System SHALL indicate monsoon seasons and advise against diving during dangerous periods
- **FR-31**: System SHALL show average visibility by month for each dive site
- **FR-32**: System SHALL integrate real-time weather data when user specifies specific travel dates
- **FR-33**: System SHALL warn users about potential weather risks for trips within 14 days

### Itinerary Generation

- **FR-34**: System SHALL generate multi-day itineraries with day-by-day breakdown
- **FR-35**: Itineraries SHALL include:
  - Daily dive schedule (AM/PM dives)
  - Surface intervals between dives
  - Recommended accommodations
  - Transportation options
  - Meal suggestions
  - Free time activities
  - Estimated daily costs
- **FR-36**: System SHALL limit to maximum 3-4 dives per day per safety guidelines
- **FR-37**: System SHALL enforce minimum 18-24 hour surface interval before flights
- **FR-38**: System SHALL allow users to modify itinerary through conversational requests
- **FR-39**: Itineraries SHALL be exportable as PDF or JSON (no server-side saving in MVP)

### Geographic Coverage

- **FR-40**: MVP SHALL cover minimum 50 dive sites across Malaysia including:
  - Sipadan/Mabul/Kapalai (Sabah)
  - Perhentian Islands
  - Redang Island
  - Tioman Island
  - Layang-Layang
  - Lankayan Island
  - Mataking Island
- **FR-41**: Each dive site entry SHALL include minimum 500 words of descriptive content in both English and Chinese
- **FR-42**: System SHALL indicate when user asks about locations outside current coverage area
- **FR-43**: System SHALL collect user interest data for future geographic expansion

### Session Management (Guest Users Only)

- **FR-44**: All users SHALL interact with chatbot as guests without account creation in MVP
- **FR-45**: System SHALL maintain conversation state using browser session storage (24-hour persistence)
- **FR-46**: System SHALL allow users to export conversation or trip plans without requiring account
- **FR-47**: System SHALL NOT save conversation history across browser sessions in MVP (future feature)
- **FR-48**: System SHALL provide "Export Chat" and "Export Trip" options for users to save locally
- **FR-49**: System SHALL clear session data after 24 hours of inactivity for privacy

### Data Citations & Transparency

- **FR-50**: System SHALL cite sources for factual claims (e.g., "According to Sipadan Dive Resort...")
- **FR-51**: System SHALL indicate when information may be outdated (>6 months old)
- **FR-52**: System SHALL provide "Last Updated" timestamp for dive site information
- **FR-53**: System SHALL allow users to report incorrect or outdated information

### Error Handling

- **FR-54**: System SHALL gracefully handle ambiguous queries by asking clarifying questions
- **FR-55**: System SHALL provide helpful error messages when unable to answer query
- **FR-56**: System SHALL suggest alternative phrasings when query intent is unclear
- **FR-57**: System SHALL fallback to general dive trip planning when specific location unavailable
- **FR-58**: System SHALL display user-friendly error message if LLM API is unavailable
- **FR-59**: System SHALL implement retry logic (3 attempts) for transient API failures

### Edge Cases

- **FR-60**: System SHALL handle queries in English and Chinese languages
- **FR-61**: System SHALL politely decline queries about dive destinations outside Asia Pacific
- **FR-62**: System SHALL redirect medical/health questions to professional dive physicians
- **FR-63**: System SHALL not provide specific medical advice regarding fitness to dive
- **FR-64**: System SHALL handle users without certification by recommending certification courses
- **FR-65**: System SHALL handle group trips with mixed certification levels by finding common suitable sites

---

## 7. Non-Functional Requirements

### Performance

- **NFR-1**: Chatbot SHALL respond to 90% of queries within 5 seconds
- **NFR-2**: Chatbot SHALL respond to 99% of queries within 10 seconds
- **NFR-3**: Page load time SHALL be under 2 seconds on 4G connection
- **NFR-4**: System SHALL support minimum 100 concurrent users without degradation
- **NFR-5**: RAG semantic search SHALL return results within 1 second
- **NFR-6**: System SHALL maintain 99.5% uptime during peak hours (6 PM - 11 PM local time)

### Scalability

- **NFR-7**: Architecture SHALL support horizontal scaling to 10,000+ concurrent users
- **NFR-8**: Knowledge base SHALL scale to 1,000+ dive sites across Asia Pacific
- **NFR-9**: PostgreSQL database SHALL handle 1M+ conversation messages with <100ms query time
- **NFR-10**: System SHALL support expansion to additional countries without architectural changes

### Security & Privacy

- **NFR-11**: System SHALL encrypt all data in transit using TLS 1.3
- **NFR-12**: System SHALL encrypt sensitive user data at rest (passwords, personal info)
- **NFR-13**: System SHALL NOT process payments or store payment information (recommendation-only platform)
- **NFR-14**: System SHALL comply with GDPR and Asia Pacific data protection regulations
- **NFR-15**: System SHALL allow users to delete their account and all associated data
- **NFR-16**: API keys and secrets SHALL be stored in secure environment variables, not code
- **NFR-17**: System SHALL implement rate limiting (100 messages per user per hour) to prevent abuse
- **NFR-18**: System SHALL log and monitor for suspicious activity patterns

### Reliability

- **NFR-19**: System SHALL implement graceful degradation when external APIs fail
- **NFR-20**: System SHALL maintain conversation state during network interruptions
- **NFR-21**: System SHALL backup knowledge base daily with 30-day retention
- **NFR-22**: System SHALL implement health checks for all critical services
- **NFR-23**: System SHALL auto-recover from transient failures without user intervention

### Accessibility

- **NFR-24**: Website SHALL meet WCAG 2.1 Level AA standards
- **NFR-25**: Chat interface SHALL be keyboard navigable
- **NFR-26**: System SHALL support screen readers for visually impaired users
- **NFR-27**: Text SHALL maintain minimum 4.5:1 contrast ratio
- **NFR-28**: System SHALL provide alternative text for all images and visual content

### Usability

- **NFR-29**: New users SHALL be able to complete first query within 30 seconds of landing on site
- **NFR-30**: Chat interface SHALL be mobile-responsive (phone, tablet, desktop)
- **NFR-30a**: System SHALL support seamless language switching without loss of conversation context
- **NFR-30b**: UI elements SHALL be fully localized in both English and Chinese
- **NFR-31**: System SHALL provide contextual help and tooltips for diving terminology
- **NFR-32**: Error messages SHALL be written in plain language, not technical jargon
- **NFR-33**: System SHALL auto-save conversation progress to browser session storage every 30 seconds

### Monitoring & Observability

- **NFR-34**: System SHALL log all user queries and AI responses for quality monitoring
- **NFR-35**: System SHALL track key metrics (response time, user satisfaction, conversion rate)
- **NFR-36**: System SHALL implement error tracking and alerting for critical failures
- **NFR-37**: System SHALL provide analytics dashboard for product team
- **NFR-38**: System SHALL track token usage and API costs for LLM calls

### Offline Behavior

- **NFR-39**: System SHALL display offline message when internet connection lost
- **NFR-40**: System SHALL queue user messages while offline and send when reconnected
- **NFR-41**: System SHALL cache last conversation state for offline viewing

### SEO & Discovery

- **NFR-42**: Public pages (Landing, Dive Site Gallery) SHALL be server-side rendered (SSR) for SEO indexing
- **NFR-43**: System SHALL generate dynamic sitemap.xml for all dive site pages
- **NFR-44**: All public pages SHALL implement proper Open Graph and Twitter Card meta tags

---

## 8. Data Model

### Primary Entities

#### User

```json
{
  "userId": "uuid",
  "email": "string (unique)",
  "passwordHash": "string",
  "displayName": "string",
  "preferredLanguage": "enum: EN, ZH",
  "certificationLevel": "enum: NONE, OW, AOW, RESCUE, DM, INSTRUCTOR",
  "certificationAgency": "enum: PADI, SSI, NAUI, OTHER",
  "totalDives": "integer",
  "preferences": {
    "budgetRange": "enum: BUDGET, MODERATE, LUXURY",
    "marineLifeInterests": ["string array"],
    "travelStyle": "enum: SOLO, COUPLE, GROUP, FAMILY"
  },
  "createdAt": "timestamp",
  "lastActiveAt": "timestamp"
}
```

#### Conversation

```json
{
  "conversationId": "uuid",
  "sessionId": "string (browser session identifier)",
  "messages": [
    {
      "messageId": "uuid",
      "role": "enum: USER, ASSISTANT, SYSTEM",
      "content": "string",
      "timestamp": "timestamp",
      "agentType": "enum: DIVE_SITE, WEATHER, LOGISTICS, SAFETY, MARINE_LIFE (for assistant messages)",
      "citations": ["string array"]
    }
  ],
  "status": "enum: ACTIVE, ARCHIVED, DELETED",
  "createdAt": "timestamp",
  "updatedAt": "timestamp"
}
```

#### DiveSite

```json
{
  "siteId": "uuid",
  "name": "string",
  "nameTranslations": {
    "en": "string",
    "zh": "string"
  },
  "location": {
    "country": "string",
    "region": "string",
    "coordinates": {
      "latitude": "float",
      "longitude": "float"
    }
  },
  "description": "string (500+ words in English)",
  "descriptionTranslations": {
    "en": "string (500+ words)",
    "zh": "string (500+ words)"
  },
  "difficulty": "enum: BEGINNER, INTERMEDIATE, ADVANCED, TECHNICAL",
  "minimumCertification": "enum: OW, AOW, RESCUE, DM",
  "depthRange": {
    "min": "integer (meters)",
    "max": "integer (meters)"
  },
  "visibility": {
    "averageMin": "integer (meters)",
    "averageMax": "integer (meters)",
    "bestMonths": ["integer array (1-12)"]
  },
  "waterTemperature": {
    "min": "integer (celsius)",
    "max": "integer (celsius)"
  },
  "marineLife": ["string array"],
  "highlights": ["string array"],
  "bestSeasons": ["string array"],
  "accessInfo": {
    "boatRequired": "boolean",
    "boatTimeMins": "integer",
    "beachEntry": "boolean"
  },
  "costEstimate": {
    "currency": "string",
    "minPerDive": "integer",
    "maxPerDive": "integer"
  },
  "nearestHyperbaricChamber": {
    "name": "string",
    "distance": "string",
    "contact": "string"
  },
  "images": ["string array of URLs"],
  "videoUrl": "string (nullable)",
  "tags": ["string array"],
  "lastUpdated": "timestamp",
  "sources": ["string array of URLs"]
}
```

#### DiveOperator

```json
{
  "operatorId": "uuid",
  "name": "string",
  "operatorType": "enum: DIVE_CENTER, RESORT, LIVEABOARD",
  "location": "string",
  "servingDiveSites": ["uuid array of siteIds"],
  "contactInfo": {
    "phone": "string",
    "email": "string",
    "website": "string",
    "whatsapp": "string (nullable)"
  },
  "services": {
    "equipmentRental": "boolean",
    "accommodation": "boolean",
    "transport": "boolean",
    "certification": "boolean"
  },
  "pricing": {
    "funDiveSingle": "integer",
    "funDivePackage": "integer",
    "equipmentRentalFull": "integer"
  },
  "certifications": ["string array: PADI 5-Star, SSI Resort, etc."],
  "rating": "float (0-5)",
  "reviewCount": "integer",
  "safetyRecord": "string",
  "lastVerified": "timestamp"
}
```

#### SavedTrip

```json
{
  "tripId": "uuid",
  "sessionId": "string (for guest session association)",
  "tripName": "string",
  "conversationId": "uuid",
  "itinerary": {
    "startDate": "date (nullable)",
    "endDate": "date (nullable)",
    "days": [
      {
        "dayNumber": "integer",
        "date": "date (nullable)",
        "dives": [
          {
            "siteId": "uuid",
            "timeSlot": "enum: MORNING, AFTERNOON",
            "diveNumber": "integer"
          }
        ],
        "accommodation": {
          "name": "string",
          "location": "string",
          "estimatedCost": "integer"
        },
        "notes": "string"
      }
    ],
    "estimatedTotalCost": {
      "currency": "string",
      "amount": "integer"
    }
  },
  "status": "enum: DRAFT, FINALIZED",
  "createdAt": "timestamp",
  "updatedAt": "timestamp"
}
```

#### WeatherData

```json
{
  "dataId": "uuid",
  "siteId": "uuid",
  "month": "integer (1-12)",
  "avgVisibilityM": "float",
  "avgWaterTempC": "float",
  "avgAirTempC": "float",
  "rainfall": "string (low/medium/high)",
  "monsoonRisk": "boolean",
  "bestTimeToVisit": "boolean",
  "notes": "string",
  "source": "string",
  "lastUpdated": "timestamp"
}
```

### Relationships

- Conversation 1:1 SavedTrip (one conversation can become one saved trip)
- DiveSite N:M DiveOperator (many sites served by many operators)
- DiveSite 1:N WeatherData (one site has weather data for 12 months)
- SavedTrip N:M DiveSite (one trip can include multiple sites)

### Data Constraints

- DiveSite coordinates must be within Asia Pacific bounding box
- Conversation messages must be ordered chronologically
- WeatherData must have 12 entries per DiveSite (one per month)
- DiveSite depthRange.max must be >= depthRange.min
- SavedTrip itinerary days must enforce 18-24 hour surface interval before flight day

---

## 9. Integrations

### PostgreSQL with pgvector (Critical)

- **Purpose**: Primary database for relational data and vector embeddings for RAG
- **Schema Strategy**: Single vector table with metadata filtering (e.g., `language` column) for multilingual support (English/Chinese)
- **API Usage**: SQL queries, pgvector operations for semantic search
- **Authentication**: Database credentials (username/password)
- **Rate Limits**: None (self-hosted or managed database)
- **Required Permissions**: Read/write access to tables, vector operations
- **Error Handling**:
  - Implement connection pooling for reliability
  - Retry logic for transient connection failures
  - Database backups for disaster recovery
- **Dependency Risks**:
  - HIGH: Database unavailability breaks entire system
  - Mitigation: Use managed PostgreSQL service (AWS RDS, Google Cloud SQL) with automatic failover

### Google ADK Framework (Critical)

- **Purpose**: Multi-agent RAG orchestration and LLM inference
- **API Usage**: Google ADK API for agent coordination and Google Gemini 2.5 Flash model access
- **Authentication**: API key-based authentication
- **Rate Limits**: TBD based on Google ADK pricing tier (assume 1000 requests/min)
- **Required Permissions**: Read/write access to knowledge base, model inference
- **Error Handling**:
  - Implement exponential backoff for rate limit errors
  - Fallback to cached responses for service downtime
  - Display user-friendly error message if quota exceeded
- **Dependency Risks**:
  - HIGH: If Google ADK or Google Gemini 2.5 Flash becomes unavailable, core functionality breaks
  - Mitigation: Implement architecture to allow model swapping (e.g., fall back to GPT-4)

### Google Maps API (High Priority)

- **Purpose**: Display dive site locations, distance calculations, route planning
- **API Usage**: Maps JavaScript API, Places API, Geocoding API
- **Authentication**: API key with domain restrictions
- **Rate Limits**: 25,000 map loads per day (free tier), paid scaling available
- **Required Permissions**: None (public API)
- **Error Handling**:
  - Display static map image if API fails
  - Cache geocoding results to minimize API calls
- **Dependency Risks**:
  - MEDIUM: Maps enhance UX but aren't critical for core chatbot
  - Mitigation: Provide text-based location descriptions as fallback

### Weather API (Medium Priority)

**Options**: OpenWeatherMap, WeatherAPI.com, or NOAA
    - **Purpose**: Real-time weather and historical climate data for dive sites
    - **API Usage**: Current weather, 7-day forecast, historical averages
    - **Authentication**: API key
    - **Rate Limits**: 1000 calls/day (free tier), paid scaling available
    - **Required Permissions**: None
    - **Error Handling**:
        - Fall back to historical seasonal data if real-time unavailable
        - Cache weather data for 6 hours to reduce API calls
    - **Dependency Risks**:
        - LOW: Weather data enhances recommendations but not critical for MVP
        - Mitigation: Use pre-loaded seasonal data as baseline

### Dive Site Data Sources (Initial Setup)

**Sources**: Web scraping, public dive databases, operator partnerships
    - **Purpose**: Initial knowledge base population
    - **API Usage**: HTTP requests for web scraping (where legally permitted)
    - **Authentication**: None (public data)
    - **Rate Limits**: Respectful crawling (1 request per 2 seconds)
    - **Required Permissions**: None (public data only)
    - **Error Handling**: Manual data entry for inaccessible sources
    - **Dependency Risks**:
        - LOW: One-time data collection for MVP
        - Mitigation: Focus on manual curation from dive operator websites initially

### Analytics & Monitoring

**Tools**: Google Analytics, Mixpanel, Sentry (error tracking)
    - **Purpose**: Track user behavior, monitor errors, measure success metrics
    - **API Usage**: Event tracking, error reporting
    - **Authentication**: API key or SDK initialization
    - **Rate Limits**: Based on plan (generally high limits)
    - **Required Permissions**: None
    - **Error Handling**: Analytics failures should not impact user experience
    - **Dependency Risks**: NONE (nice-to-have)

### Internationalization (i18n) Framework

**Tools**: react-i18next, i18next, or next-i18next (for Next.js)
    - **Purpose**: Manage UI translations, language detection, and locale switching
    - **API Usage**: Client-side translation library
    - **Authentication**: None
    - **Rate Limits**: None (client-side library)
    - **Required Permissions**: None
    - **Error Handling**: Fallback to English if translation missing
    - **Dependency Risks**:
        - LOW: Library failures only affect UI translations, not core functionality
        - Mitigation: Implement English as fallback language for all strings

---

## 10. UX & UI Specifications

### Screen List

1. **Landing Page** - Homepage with hero section and chat entry point
2. **Chat Interface** - Main conversational UI
3. **Trip Detail View** - Full itinerary display (exportable)
4. **Dive Site Gallery** - Visual browse of dive sites with filters
5. **About/Help Page** - Product information and FAQs

### Wireframe-Level Descriptions

#### Landing Page

**Layout:**
    - Header: Logo (left), Navigation links (right): "About", "Help"
    - Language selector: Dropdown or toggle (EN/中文) in header
    - Hero Section (full-width):
        - Large headline: "Plan Your Perfect Dive Trip with AI" (English) / "用AI規劃您的完美潛水之旅" (Chinese)
        - Subheadline: "Discover Malaysia's best dive sites with personalized recommendations"
        - Prominent CTA button: "Start Planning" (opens chat)
        - Background: Beautiful underwater photo with overlay
    - Feature Section (3-column grid):
        - Column 1: "Smart Recommendations" with icon
        - Column 2: "Safety First" with icon
        - Column 3: "Local Expertise" with icon
    - Example Prompts Section:
        - "Try asking..." with 4-6 clickable example queries
    - Footer: Links, social media, contact

**Interactions:**
    - "Start Planning" button scrolls to or opens chat interface
    - Example prompts auto-populate chat when clicked
    - Smooth scroll animations

#### Chat Interface UI

**Layout:**
    - Fixed header: Logo, "New Chat" button, "Export Chat" button
    - Main chat area (60% width, centered):
        - Conversation messages (scrollable)
        - User messages: right-aligned, blue bubble
        - AI messages: left-aligned, gray bubble with agent icon
        - Typing indicator: animated dots
    - Input area (bottom, fixed):
        - Text input field (multi-line, auto-expanding)
        - Send button (right side)
        - Character count (below input)
    - Sidebar (right, 30% width, collapsible):
        - "Current Trip Summary" card (if trip being planned)
        - Suggested follow-up questions
        - Quick filters (certification level, budget)

**Interactions:**
    - Messages fade in with smooth animation
    - Suggested questions clickable to auto-fill input
    - Sidebar collapses on mobile to maximize chat space
    - Input field auto-focuses on page load
    - Enter key sends message, Shift+Enter for new line
    - Rich media (images, maps) display inline with lightbox on click

**AI Message Components:**
    - Agent badge (small icon indicating which agent responded)
    - Message text with markdown formatting
    - Inline images/photos (max 3 per message)
    - "Sources" expandable section (shows RAG citations)
    - Feedback buttons: Thumbs Up/Down icon
    - Action buttons: "Save Trip", "Show on Map", "Contact Operator"

#### Trip Detail View

**Layout:**
    - Header: Trip name (editable), status badge, action buttons (Export PDF, Share, Book)
    - Summary card:
        - Dates, duration, total cost estimate
        - Skill level required, number of dives
    - Day-by-day accordion:
        - Each day expandable
        - Shows dive sites, times, accommodation, notes
    - Map view (interactive):
        - All dive sites pinned on map
        - Route visualization
    - Recommendations footer:
        - What to pack, safety tips, contact info

**Interactions:**
    - Click day to expand/collapse details
    - Hover on dive site name to see preview popup
    - Click dive site to open detailed modal
    - Export PDF generates downloadable itinerary
    - Share creates shareable link (public or password-protected)

### Navigation Flow

1. User lands on homepage → clicks "Start Planning" → chat opens
2. User asks question → AI responds with recommendations → user can:
   - Ask follow-up
   - Export trip as PDF
   - View dive site details
   - Start new conversation
3. User responsible for saving exported files locally

### Interaction Rules

- **Auto-save**: Conversations auto-save to browser session storage every 30 seconds
- **Session persistence**: Conversations persist for 24 hours in browser session storage
- **Mobile responsiveness**:
  - Chat interface full-screen on mobile
  - Sidebar becomes bottom sheet
  - Simplified navigation with hamburger menu
- **Keyboard shortcuts**:
  - `Ctrl/Cmd + K`: Focus chat input
  - `Ctrl/Cmd + N`: New conversation
  - `Esc`: Close modals

### UI States

#### Empty States

- **No Conversations Yet**: "Start your first dive trip planning conversation!" with example prompts

#### Loading States

- **AI Response Loading**: Animated typing indicator with agent icon
- **Trip Export**: Progress bar for PDF generation

#### Error States

- **Network Error**: "Connection lost. Your messages are saved and will send when back online."
- **Invalid Input**: "I didn't quite understand that. Could you rephrase or ask something else?"

## 11. System Constraints & Assumptions

### Technical Assumptions

- **ASSUM-1**: Google ADK framework supports Google Gemini 2.5 Flash model natively (verified Nov 2025)
- **ASSUM-2**: Google Gemini 2.5 Flash has sufficient context window (min 32k tokens) for multi-turn conversations
- **ASSUM-2a**: Google Gemini 2.5 Flash supports high-quality responses in both English and Chinese languages
- **ASSUM-3**: Google ADK multi-agent mode allows minimum 5 concurrent specialized agents
- **ASSUM-4**: PostgreSQL with pgvector extension suitable for RAG vector storage and relational data
- **ASSUM-5**: Backend will be built with Node.js/Express or Python/FastAPI
- **ASSUM-6**: Frontend will be React-based SPA (React, Next.js, or similar)
- **ASSUM-7**: PostgreSQL suitable for conversation, user data, and vector storage (via pgvector extension)
- **ASSUM-8**: System can be deployed on cloud platform (AWS, GCP, or Azure)
- **ASSUM-9**: API response times from Google ADK are under 3 seconds for 90% of queries
- **ASSUM-10**: Standard REST API sufficient for batch responses (streaming/WebSockets not required for MVP)

### Business Assumptions

- **ASSUM-11**: MVP supports English and Chinese languages; architecture designed for future multi-language expansion
- **ASSUM-12**: Dive operators willing to provide data or be listed without formal partnerships initially
- **ASSUM-13**: Platform is recommendation and information-only; no booking/payment functionality (users contact operators directly)
- **ASSUM-13a**: Users must verify all details with dive operators before booking; platform not liable for accuracy of recommendations
- **ASSUM-14**: Revenue model is Affiliate Commissions (accommodation/transport) and Lead Generation (dive operators); no direct payments in MVP
- **ASSUM-15**: Legal liability disclaimer sufficient for AI-generated dive recommendations (consult legal team)
- **ASSUM-16**: Target audience has reliable internet access (not optimizing for offline-first)
- **ASSUM-17**: Marketing budget available for user acquisition post-launch
- **ASSUM-18**: Initial user base acquisition via Organic SEO (high-intent keywords) and dive community engagement

### User Assumptions

- **ASSUM-19**: Users are already certified divers or interested in getting certified (not teaching diving fundamentals)
- **ASSUM-20**: Users trust AI recommendations when properly cited and safety-validated
- **ASSUM-21**: Users comfortable with conversational interface (minimal learning curve)
- **ASSUM-22**: Users willing to export and save trip plans locally (no cloud storage in MVP)
- **ASSUM-23**: Users have basic diving knowledge (understand terms like "surface interval", "depth", "certification level")
- **ASSUM-24**: Primary device is desktop or mobile phone (tablet as secondary)

### Data Assumptions

- **ASSUM-25**: Sufficient public data available on Malaysian dive sites to build initial knowledge base
- **ASSUM-26**: Dive site data can be manually curated and verified within 3 months for MVP
- **ASSUM-26a**: Chinese translations for dive site content can be completed within MVP timeline (human translation or AI-assisted)
- **ASSUM-27**: Weather data can be approximated using historical patterns (exact real-time not critical for MVP)
- **ASSUM-28**: Marine life data can be sourced from dive operator websites and dive logs
- **ASSUM-29**: Dive operator pricing is relatively stable (annual updates sufficient)
- **ASSUM-30**: User-generated content (reviews, photos) can be incorporated post-MVP

### Regulatory Assumptions

- **ASSUM-31**: No special licensing required to provide dive trip recommendations (vs. actual dive guiding)
- **ASSUM-32**: Clear disclaimers protect from liability for AI-generated advice
- **ASSUM-33**: GDPR/data protection compliance achievable with standard practices
- **ASSUM-34**: No export restrictions on AI technology to target markets (Malaysia, Singapore, etc.)

### Integration Assumptions

- **ASSUM-35**: Google Gemini 2.5 Flash API costs estimated to be lower than mini; Google ADK framework is free.
- **ASSUM-36**: Google Maps API free tier sufficient for MVP (can upgrade if needed)
- **ASSUM-37**: Weather API free tier sufficient for MVP
- **ASSUM-38**: No blockchain or Web3 integration required (standard web2 architecture)
- **ASSUM-39**: System uses local currency (e.g., MYR for Malaysia) for all cost estimates; no real-time currency conversion provided

---

## 12. Open Questions (Internal Notes)

### Critical Questions Requiring Immediate Clarification

1. **Q-1**: Is Google Gemini 2.5 Flash available and compatible with Google ADK?
   - **Answer**: ✓ **Yes**. Google Gemini 2.5 Flash exists; ADK integration is native.
2. **Q-2**: What are the licensing and cost implications?
   - **Answer**: ✓ **Free ADK / Paid API**. Google ADK is free (open source). Google Gemini 2.5 Flash pricing: TBD (Expected lower than mini).
3. **Q-3**: Will the platform handle bookings or payments?
   - **Answer**: ✓ **No**. Platform is recommendation and information-only; no booking/payment functionality.
4. **Q-4**: Do we need user accounts for MVP?
   - **Answer**: ✓ **No**. No user accounts required for MVP; saved as future feature for personalization and trip history.
5. **Q-5**: Are recommendations guaranteed to be available?
   - **Answer**: ✓ **No**. Recommendations only; users must confirm details with dive shops before booking.

### Product Strategy Questions

1. **Q-6**: What is the primary monetization strategy?
   - **Answer**: ✓ **Affiliate & Lead Gen**. Monetize via affiliate links (accommodation/transport) and "Verified Partner" leads for dive operators.
2. **Q-7**: Is the focus on B2C (divers) or B2B (operators)?
   - **Answer**: ✓ **B2C Focus**. Prioritize individual divers to build traffic volume first; operator partnerships follow user growth.
3. **Q-8**: What is the user acquisition strategy?
   - **Answer**: ✓ **SEO & Community**. Focus on high-intent search keywords ("Sipadan diving guide") and dive community engagement.
4. **Q-9**: Should we implement email alerts for price drops?
   - **Answer**: ✓ **No for MVP**. Defer email alerts to V2 when user accounts are implemented.
5. **Q-10**: Should this be a web app or native mobile app?
   - **Answer**: ✓ **Web-only (Responsive/PWA)**. Lower barrier to entry; native apps deferred to future phases.

### Technical Architecture Questions

1. **Q-11**: What database should we use for RAG and vector storage?
   - **Answer**: ✓ **PostgreSQL + pgvector**. PostgreSQL with pgvector extension for RAG knowledge base and vector storage.
   - **Q-11a**: How do we handle multi-language indexing?
     - **Answer**: ✓ **Single Index with Metadata**. Use single vector table with `language` column for filtering (e.g., `WHERE language = 'en'`).
2. **Q-12**: Should we use streaming responses or batch?
   - **Answer**: ✓ **Batch Responses**. Simpler to implement for MVP; streaming can be added later if latency is an issue.
3. **Q-13**: Where should conversation history be stored?
   - **Answer**: ✓ **No Server Storage**. Conversations exist only in browser session. Users must export (PDF/JSON) to save records.
4. **Q-14**: Should we implement A/B testing for prompts?
   - **Answer**: ✓ **No for MVP**. Focus on qualitative feedback and basic analytics (Google Analytics) first. A/B testing adds unnecessary complexity at this stage.
5. **Q-15**: Do we need a CDN for images and assets?
   - **Answer**: ✓ **Yes**. Use a CDN (e.g., Cloudflare, AWS CloudFront) for dive site images and map tiles to ensure fast loading across Asia Pacific.

### Data & Content Questions

1. **Q-16**: Who will perform initial data collection and curation?
   - **Answer**: ✓ **Internal Team + Freelance**. Initial curation by product team to ensure quality, supplemented by freelance divers for specific local knowledge.
   - **Q-16a**: Who will handle Chinese translations?
     - **Answer**: ✓ **AI-Assisted**. Use LLMs for initial translation with human review for accuracy.
2. **Q-17**: What is the content update cadence?
   - **Answer**: ✓ **Monthly**. Dive site data is relatively static; monthly updates are sufficient for MVP.
3. **Q-18**: Should we allow dive operators to claim and update their listings?
   - **Answer**: ✓ **No for MVP**. Manual verification by internal team first; operator portal deferred to V2.
4. **Q-19**: Do we need user-generated content (reviews, photos) in MVP?
   - **Answer**: ✓ **No for MVP**. Focus on curated, high-quality static content first.
5. **Q-20**: Should we scrape existing dive review sites?
   - **Answer**: ✓ **No**. Avoid scraping to minimize legal risk and dependency on external DOM structures.

### UX & Feature Prioritization Questions

1. **Q-21**: Should we support voice interaction?
   - **Answer**: ✓ **No for MVP**. Text-based interaction only to keep scope manageable.
2. **Q-22**: Should we include social sharing features?
   - **Answer**: ✓ **No for MVP**. Social features deferred to V2.
3. **Q-23**: Should we add gamification elements?
   - **Answer**: ✓ **No for MVP**. Gamification requires user accounts (F-3), which is a future feature.
4. **Q-24**: Should we include a "Dive Buddy Finder"?
   - **Answer**: ✓ **No for MVP**. Buddy finder requires user accounts and critical mass.
5. **Q-25**: Should the AI have long-term memory across sessions?
   - **Answer**: ✓ **No for MVP**. Session-based memory only (FR-45); no cross-session persistence for guests.

### Safety & Compliance Questions

1. **Q-26**: Is a safety disclaimer required?
   - **Answer**: ✓ **Yes**. Mandatory disclaimer acknowledgment on first load (covered in FR-23).
2. **Q-27**: Should we verify certification levels via API?
   - **Answer**: ✓ **No for MVP**. Trust self-reported certification level; no API integration with PADI/SSI yet.
3. **Q-28**: Should we partner with DAN (Divers Alert Network)?
   - **Answer**: ✓ **No**. No formal partnership with DAN for MVP, though we can still recommend them as a general safety tip.
4. **Q-29**: How should the system handle emergency medical queries?
   - **Answer**: ✓ **Immediate Redirection**. AI detects emergency keywords and responds with static emergency contact list (DAN, 999, Hyperbaric chambers). AI explicitly states it cannot assist in real-time emergencies.
5. **Q-30**: Do we need an age gate?
   - **Answer**: ✓ **Yes**. Simple age gate ("I confirm I am 18+") on the landing page or first chat interaction.

### Geographic Expansion Questions

1. **Q-31**: What is the roadmap for Asia Pacific expansion beyond Malaysia?
   - **Answer**: ✓ **Indonesia & Philippines**. Indonesia (Raja Ampat/Komodo) and Philippines (Palawan/Cebu) are logical next steps due to proximity and popularity.
2. **Q-32**: What languages should be supported in MVP?
   - **Answer**: ✓ **English & Chinese**. MVP supports English and Chinese; architecture designed for future language expansion.
3. **Q-33**: How should currency conversion be handled?
   - **Answer**: ✓ **Local Currency Only**. System uses local currency (e.g., MYR) for all cost estimates; no currency conversion required.
4. **Q-34**: Should we consider liveaboard dive trips?
   - **Answer**: ✓ **Yes**. Include liveaboards as a distinct category (e.g., 'Liveaboard' vs 'Resort') in the data model, as they are critical for advanced diving in the region.

### Analytics & Optimization Questions

1. **Q-35**: What are the key metrics for the success dashboard?
   - **Answer**: ✓ **Engagement & Conversion**. Key metrics: Trip Plan Completion Rate, Session Duration (>2 mins), User Satisfaction (Thumbs Up/Down), and Returning Visitor Rate.
2. **Q-36**: Should we implement user feedback collection?
   - **Answer**: ✓ **Yes**. Simple Thumbs Up/Down on AI responses to train RAG relevance.
3. **Q-37**: Do we need session replay tools?
   - **Answer**: ✓ **No for MVP**. Use Google Analytics events first. Session replay has privacy implications.
4. **Q-38**: Should we track and optimize for SEO from day 1?
   - **Answer**: ✓ **Yes**. Critical for organic acquisition. Ensure landing pages for top dive sites are server-side rendered (SSR) or pre-rendered for indexing.

---

## 13. Acceptance Criteria

### AC: Core Chatbot Functionality

- **AC-1**: User can land on homepage and initiate chat within 30 seconds without account creation
- **AC-2**: AI responds to dive site query with minimum 3 relevant recommendations within 5 seconds
- **AC-3**: AI correctly filters recommendations by user's stated certification level (e.g., no 40m deep dives for Open Water divers)
- **AC-4**: AI provides safety warning when user asks about dive site beyond their certification level
- **AC-5**: Conversation maintains context across minimum 10 message exchanges
- **AC-6**: AI cites sources for factual claims (e.g., "According to [dive operator website]...")

### AC: Multi-Agent RAG System

- **AC-7**: System correctly routes dive site queries to Dive Site Expert Agent
- **AC-8**: System correctly routes weather queries to Weather & Seasonality Agent
- **AC-9**: System orchestrates multi-agent response when query requires multiple domains (e.g., "best time to dive Sipadan for whale sharks")
- **AC-10**: RAG retrieval returns relevant dive site information with minimum 80% accuracy (verified via test queries)
- **AC-11**: System handles ambiguous queries by asking clarifying questions before providing recommendations

### AC: Geographic Coverage

- **AC-12**: System provides detailed information for minimum 50 dive sites across Malaysia
- **AC-13**: Each dive site entry includes all required fields: name, location, depth range, difficulty, certification requirement, marine life, best season
- **AC-14**: System correctly indicates when user asks about locations outside coverage area (e.g., "I currently only cover Malaysia, but I'm expanding soon!")

### AC: Itinerary Generation

- **AC-15**: User can request multi-day itinerary (e.g., "plan me a 3-day trip to Sipadan") and receive day-by-day breakdown
- **AC-16**: Generated itinerary enforces safety rules: max 3-4 dives per day, 18-24 hour surface interval before flights
- **AC-17**: Itinerary includes estimated costs, accommodations, and transportation details
- **AC-18**: User can modify itinerary through conversational requests (e.g., "replace day 2 with a different dive site")

### AC: Safety & Validation

- **AC-19**: System displays safety disclaimer on first chat interaction and requires user acknowledgment
- **AC-20**: System provides emergency contact info and nearest hyperbaric chamber for all recommended dive sites
- **AC-21**: System refuses to provide medical advice and redirects health questions to dive physicians
- **AC-22**: System enforces certification-based filtering with zero false negatives (never recommends unsafe sites)

### AC: User Experience

- **AC-23**: Chat interface is fully responsive on mobile (320px width), tablet (768px), and desktop (1920px)
- **AC-24**: New users can successfully complete first query and receive satisfactory recommendations in user testing (90%+ success rate)
- **AC-25**: Page load time under 2 seconds on 4G connection
- **AC-26**: AI response time under 5 seconds for 90% of queries
- **AC-27**: Error messages are clear and actionable (no technical jargon or stack traces visible to users)

### AC: Session Management & Data Export

- **AC-28**: All users can use chatbot without account creation
- **AC-29**: Conversations persist in browser session for 24 hours
- **AC-30**: Users can export trip itinerary as PDF or JSON successfully
- **AC-31**: Users can export full conversation history as text or JSON
- **AC-32**: Session data clears after 24 hours of inactivity for privacy

### AC: Data Quality

- **AC-33**: RAG knowledge base is updated at least monthly with new dive site information
- **AC-34**: All dive site information includes "Last Updated" timestamp
- **AC-35**: System indicates when information may be outdated (>6 months old)
- **AC-36**: Weather and seasonal data is accurate for minimum 12 months of historical patterns per dive site

### AC: Performance & Reliability

- **AC-37**: System supports minimum 100 concurrent users without response time degradation
- **AC-38**: System maintains 99.5% uptime during peak hours (6 PM - 11 PM Malaysia time)
- **AC-39**: System gracefully handles Google ADK API failures with user-friendly error messages
- **AC-40**: Conversation state is preserved during network interruptions (messages don't disappear)

### AC: Integration Correctness

- **AC-41**: Google Maps integration displays all dive sites correctly on interactive map
- **AC-42**: Weather API integration provides accurate current weather and 7-day forecast for dive locations
- **AC-43**: PostgreSQL pgvector semantic search returns relevant results with <1s latency

### AC: Conversion & Engagement

- **AC-44**: Minimum 70% of users who start conversation complete at least one full trip planning exchange (5+ message turns)
- **AC-45**: Minimum 90% user satisfaction score when asked "Did this recommendation help you?" after trip plan
- **AC-46**: Minimum 40% of users who receive trip plan access operator contact information or export trip
- **AC-47**: Session-based analytics track unique visitors and conversation completion rates

### AC: Accessibility

- **AC-48**: Website meets WCAG 2.1 Level AA standards (validated via automated and manual testing)
- **AC-49**: Chat interface is fully keyboard navigable (no mouse required)
- **AC-50**: Screen reader announces new AI messages and provides alternative text for images

### AC: Language Support

- **AC-51**: System correctly detects user language from first message (English or Chinese) with 95%+ accuracy
- **AC-52**: Users can manually switch between English and Chinese via language toggle in UI
- **AC-53**: All UI elements, labels, and system messages display correctly in both English and Chinese
- **AC-54**: AI responses maintain consistent language throughout conversation unless user switches
- **AC-55**: Dive site content is available in both English and Chinese for all 50+ Malaysian dive sites
- **AC-56**: Language preference persists within browser session

### AC: Security & Privacy

- **AC-57**: All API keys and secrets stored securely in environment variables, not in code
- **AC-58**: All data transmitted over HTTPS/TLS 1.3
- **AC-59**: Rate limiting prevents abuse (max 100 messages per session per hour)
- **AC-60**: System logs do not contain sensitive user information
- **AC-61**: Session data automatically purges after 24 hours for privacy compliance

---

## 14. Future Improvements (Post-MVP Roadmap)

### User Accounts & Personalization

- **User Profiles**: Registration and login to save preferences, certification details, and trip history.
- **Cross-Session Memory**: AI remembers user context and preferences across different sessions.
- **Gamification**: Achievement badges for number of dives planned, sites visited, or contributions.

### Social & Community

- **Dive Buddy Finder**: Feature to connect solo divers with others planning trips to the same location.
- **Social Sharing**: Collaborative trip planning tools and ability to share itineraries with friends.
- **Community Reviews**: User-generated reviews and photos for dive sites and operators.

### Advanced Features

- **Certification Verification**: API integration with PADI/SSI to automatically verify diver certification levels.
- **Price Alerts**: Email notifications for price drops on tracked dive packages.
- **Native Mobile Apps**: Dedicated iOS and Android applications for offline access and better mobile experience.
- **Voice Interface**: Voice input/output for hands-free interaction with the chatbot.

### Geographic Expansion

- **Indonesia**: Expansion to Raja Ampat, Komodo, and Bali.
- **Philippines**: Expansion to Palawan, Cebu, and Bohol.
- **Thailand**: Expansion to Similan Islands and Koh Tao.

---

## END OF PRODUCT SPECIFICATION DOCUMENT

---

## Document Change Log

- **v1.0** (28 Nov 2025): Initial PSD creation based on user requirements

## Next Steps

1. Clarify all questions in Section 12 (Open Questions)
2. Validate Google ADK + Google Gemini 2.5 Flash technical feasibility
3. Define revenue model and go-to-market strategy
4. Begin data collection for Malaysian dive sites
5. Create technical architecture diagram
6. Develop MVP development plan with timeline and milestones
7. Consult legal team on liability and safety disclaimers

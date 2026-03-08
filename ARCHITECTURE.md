<div align="center">

# 🏗 System Architecture

### Language Center SaaS — Technical Architecture Deep-Dive

</div>

---

## Table of Contents

- [High-Level Architecture](#high-level-architecture)
- [Infrastructure Layer](#infrastructure-layer)
- [Frontend Architecture](#frontend-architecture)
- [Backend Architecture](#backend-architecture)
- [Database Architecture](#database-architecture)
- [Real-Time Communication](#real-time-communication)
- [Caching Strategy](#caching-strategy)
- [Security Architecture](#security-architecture)
- [Background Jobs](#background-jobs)
- [CI/CD Pipeline](#cicd-pipeline)
- [Monitoring & Logging](#monitoring--logging)

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        INTERNET                                 │
└────────────────────────────┬────────────────────────────────────┘
                             │
                    ┌────────▼────────┐
                    │   CLOUDFLARE    │
                    │  • DNS Routing  │
                    │  • CDN / Cache  │
                    │  • DDoS Shield  │
                    │  • Full Strict  │
                    │    SSL/TLS      │
                    └────────┬────────┘
                             │
              ┌──────────────▼──────────────┐
              │     DIGITALOCEAN VPS        │
              │  Ubuntu 22.04 │ 4GB │ 80GB  │
              │  Singapore (SGP1)           │
              │                             │
              │  ┌────────────────────────┐ │
              │  │       NGINX            │ │
              │  │  • Reverse Proxy       │ │
              │  │  • SSL Termination     │ │
              │  │  • Static Caching      │ │
              │  │  • Gzip Compression    │ │
              │  │  Ports: 80, 443        │ │
              │  └───┬──────────────┬─────┘ │
              │      │              │        │
              │  ┌───▼────┐  ┌─────▼──────┐ │
              │  │FRONTEND│  │  BACKEND   │ │
              │  │React   │  │  Express   │ │
              │  │SPA     │  │  API       │ │
              │  │(Nginx) │  │  Port:3000 │ │
              │  └────────┘  └──┬────┬────┘ │
              │                 │    │       │
              │  ┌──────────┐ ┌▼────▼─────┐ │
              │  │  REDIS   │ │ MONGODB 7 │ │
              │  │  Cache   │ │  Database  │ │
              │  │  (6379)  │ │  (27017)   │ │
              │  └──────────┘ └───────────┘ │
              │                             │
              └─────────────────────────────┘
                             │
         ┌───────────┬───────┼───────┬──────────┐
         │           │       │       │          │
    ┌────▼───┐ ┌─────▼──┐ ┌─▼────┐ ┌▼───────┐ ┌▼────────┐
    │ AWS S3 │ │ Azure  │ │Send- │ │ Google │ │ Oracle  │
    │Storage │ │ Email  │ │Grid  │ │ Vertex │ │ + GCS   │
    │        │ │Service │ │      │ │  AI    │ │ Backup  │
    └────────┘ └────────┘ └──────┘ └────────┘ └─────────┘
```

---

## Infrastructure Layer

### Docker Compose — Production Services

```
┌─────────────────────────────────────────────────────────┐
│                 Docker Compose Network                   │
│                   (Bridge Network)                       │
│                                                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐             │
│  │  nginx   │  │ frontend │  │ backend  │             │
│  │  :80/443 │──│ internal │  │ :3000    │             │
│  │  PUBLIC  │  │          │  │ internal │             │
│  └────┬─────┘  └──────────┘  └────┬─────┘             │
│       │                           │                     │
│       │    ┌─────────┐   ┌────────▼──┐  ┌───────────┐ │
│       │    │  redis  │   │ mongodb   │  │  backup   │ │
│       │    │  :6379  │   │  :27017   │  │ scheduled │ │
│       │    │internal │   │ internal  │  │ internal  │ │
│       │    └─────────┘   └───────────┘  └───────────┘ │
│       │                                                 │
│  Only nginx is exposed to the internet                  │
│  All other services communicate via internal network    │
└─────────────────────────────────────────────────────────┘
```

| Service | Image | Exposed | Health Check |
|---------|-------|---------|-------------|
| **nginx** | nginx:alpine | 80, 443 | TCP connect |
| **frontend** | node:20-alpine (multi-stage → nginx) | Internal only | HTTP /health |
| **backend** | node:20-alpine | Internal only | HTTP /api/health |
| **mongodb** | mongo:7.0 | Internal only | `mongosh --eval "db.runCommand('ping')"` |
| **redis** | redis:alpine | Internal only | `redis-cli ping` |
| **backup** | custom | Internal only | Scheduled execution |

---

## Frontend Architecture

### Layer Diagram

```
┌─────────────────────────────────────────────────────────┐
│                      App.tsx                             │
│  ┌───────────────────────────────────────────────────┐  │
│  │              Context Providers                     │  │
│  │  ┌─────────────┐  ┌──────────────────────────┐   │  │
│  │  │ AuthContext  │  │ UserPreferencesContext    │   │  │
│  │  │ • user      │  │ • theme settings          │   │  │
│  │  │ • token     │  │ • display preferences     │   │  │
│  │  │ • role      │  │ • language                 │   │  │
│  │  │ • branch    │  │                            │   │  │
│  │  └─────────────┘  └──────────────────────────┘   │  │
│  └───────────────────────────────────────────────────┘  │
│                          │                               │
│  ┌───────────────────────▼───────────────────────────┐  │
│  │              Router (React Router v6)              │  │
│  │         Lazy-loaded routes with code-splitting     │  │
│  │                                                    │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────────────┐  │  │
│  │  │ Public   │ │Protected │ │   Role-Specific  │  │  │
│  │  │ Routes   │ │ Routes   │ │   Routes         │  │  │
│  │  │ • Login  │ │ • Dash   │ │ • ownerRoutes    │  │  │
│  │  │ • Regist │ │ • Studs  │ │ • salaryRoutes   │  │  │
│  │  │ • Reset  │ │ • Class  │ │ • settingsRoutes │  │  │
│  │  └──────────┘ └──────────┘ └──────────────────┘  │  │
│  └───────────────────────────────────────────────────┘  │
│                          │                               │
│  ┌───────────────────────▼───────────────────────────┐  │
│  │                 Pages (80+)                        │  │
│  │  Each page follows the pattern:                    │  │
│  │                                                    │  │
│  │  Page.tsx                                          │  │
│  │    ├── hooks/usePageLogic.ts  (business logic)     │  │
│  │    ├── components/            (page-specific UI)   │  │
│  │    └── types/                 (TypeScript types)   │  │
│  └───────────────────────────────────────────────────┘  │
│                          │                               │
│  ┌───────────────────────▼───────────────────────────┐  │
│  │              Services Layer (42+)                  │  │
│  │  Centralized API wrappers with:                    │  │
│  │  • Axios interceptors (auth, error handling)       │  │
│  │  • Request/Response normalization                  │  │
│  │  • Type-safe API contracts                         │  │
│  └───────────────────────────────────────────────────┘  │
│                          │                               │
│  ┌───────────────────────▼───────────────────────────┐  │
│  │           State Management                         │  │
│  │  ┌─────────────────┐  ┌─────────────────────┐    │  │
│  │  │  TanStack Query  │  │     Zustand          │    │  │
│  │  │  (Server State)  │  │  (Client State)      │    │  │
│  │  │  • Caching       │  │  • UI State          │    │  │
│  │  │  • Background    │  │  • Filters           │    │  │
│  │  │    refetch       │  │  • Modals            │    │  │
│  │  │  • Optimistic    │  │  • Preferences       │    │  │
│  │  │    updates       │  │                      │    │  │
│  │  └─────────────────┘  └─────────────────────┘    │  │
│  └───────────────────────────────────────────────────┘  │
│                          │                               │
│  ┌───────────────────────▼───────────────────────────┐  │
│  │            Shared Components (200+)                │  │
│  │                                                    │  │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────────┐ │  │
│  │  │  UI    │ │ Forms  │ │ Charts │ │ Business   │ │  │
│  │  │Radix + │ │  RHF + │ │Recharts│ │ Attendance │ │  │
│  │  │Custom  │ │  Zod   │ │Chart.js│ │ Payment    │ │  │
│  │  │        │ │        │ │        │ │ Salary     │ │  │
│  │  └────────┘ └────────┘ └────────┘ └────────────┘ │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### Key Design Decisions — Frontend

| Decision | Rationale |
|----------|-----------|
| **Vite over CRA** | 10-50x faster HMR, native ESM, optimized builds |
| **TanStack Query over Redux** | Built-in caching, background refetch, optimistic UI — no boilerplate |
| **Zustand over Context** | Simpler API, no provider nesting, selective re-rendering |
| **Radix UI primitives** | Unstyled + accessible by default, full control over design |
| **Lazy routes** | ~80 pages — code-splitting essential for initial load performance |
| **TanStack Virtual** | Virtualized rendering for tables with 1000+ rows |
| **React Hook Form + Zod** | Zero re-renders on input, schema-based validation |

---

## Backend Architecture

### Request Pipeline

```
Incoming Request
       │
       ▼
┌──────────────────────────────────────────────────┐
│                MIDDLEWARE STACK                    │
│                                                   │
│  1. helmet()           → Security headers (15+)   │
│  2. cors()             → Origin validation         │
│  3. rateLimiter()      → 6-tier rate limiting      │
│  4. express.json()     → Body parsing (50kb limit) │
│  5. mongoSanitize()    → NoSQL injection prevent   │
│  6. xssClean()         → XSS input cleaning        │
│  7. hpp()              → Parameter pollution prev   │
│  8. cookieParser()     → Cookie reading             │
│  9. auth()             → JWT verification           │
│  10. roleCheck()       → RBAC authorization         │
│  11. validator()       → Input validation            │
│                                                   │
└──────────────────────┬───────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────┐
│                 ROUTE HANDLER                     │
│                                                   │
│   routes/  →  controllers/  →  services/  → DB    │
│                                                   │
│   42+ route     40+ ctrl      23+ services        │
│   modules       modules       (business logic)    │
│                                                   │
└──────────────────────┬───────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────┐
│              POST-PROCESSING                      │
│                                                   │
│  1. activityLogger()  → Audit trail (before/after │
│                          diffs on all mutations)   │
│  2. errorHandler()    → Centralized error format   │
│  3. Socket.IO emit    → Real-time notifications    │
│                                                   │
└──────────────────────────────────────────────────┘
```

### Service Layer Pattern

```
Controller (HTTP layer)
    │
    ├── Validates request params
    ├── Calls Service functions
    ├── Formats HTTP response
    │
    ▼
Service (Business logic)
    │
    ├── Pure business computations
    ├── Cross-model operations
    ├── External API integrations
    │
    ▼
Model (Data access)
    │
    ├── Mongoose schema + validation
    ├── Virtual fields
    ├── Pre/post hooks
    └── Indexed queries
```

### Key Backend Services

| Service | Purpose |
|---------|---------|
| **PaymentCalculator** | Computes tuition amounts based on payment cycles, prorations, and discounts |
| **SalaryCalculator** | Calculates teacher/admin salary from attendance records and salary codes |
| **PDFService** | Generates professional receipts and invoices using Puppeteer |
| **AnalyticsService** | Aggregates revenue, attendance, and enrollment data for dashboards |
| **EmailService** | Multi-provider email (Nodemailer, SendGrid, Azure) with templates |
| **cacheService** | Redis-backed caching layer with intelligent invalidation |
| **aiService** | Google Vertex AI integration for suggestions and analysis |
| **storageService** | AWS S3 file management with presigned URLs |
| **threatDetector** | Monitors and flags suspicious request patterns |

---

## Database Architecture

### Collection Relationships

```
                    ┌──────────┐
                    │   User   │
                    │  (roles) │
                    └┬────┬──┬─┘
                     │    │  │
        ┌────────────┘    │  └────────────┐
        │                 │               │
   ┌────▼────┐    ┌──────▼─────┐   ┌─────▼──────┐
   │LoginHist│    │  Student   │   │   Branch    │
   │TrustDev │    │  (A1-C2)   │   │             │
   └─────────┘    └──┬───┬──┬──┘   └─────────────┘
                     │   │  │
          ┌──────────┘   │  └──────────┐
          │              │             │
     ┌────▼────┐  ┌──────▼─────┐ ┌────▼─────┐
     │Enrollmt │  │   Class    │ │ Attendance│
     │         │◄─┤ (schedule) │ │ (session) │
     └────┬────┘  └──┬────┬───┘ └─────┬─────┘
          │          │    │            │
     ┌────▼────┐     │  ┌─▼──────┐ ┌──▼──────────┐
     │ Payment │     │  │Holiday │ │TeacherSalary │
     │         │     │  │TimeSlot│ │AdminSalary   │
     └────┬────┘     │  └────────┘ └──────┬───────┘
          │          │                    │
     ┌────▼────┐  ┌──▼────────┐   ┌──────▼───────┐
     │ Invoice │  │TuitionTbl │   │ SalaryPeriod │
     │ Receipt │  │ PriceTable│   │ SalaryCode   │
     └─────────┘  └───────────┘   └──────────────┘
```

### Index Strategy

- **Compound indexes** on frequently queried combinations (e.g., `{ classId, date }` for attendance)
- **Unique indexes** on business keys (student codes, invoice numbers)
- **TTL indexes** on session tokens for automatic expiry
- **Text indexes** on searchable fields (student name, class name)

> 📄 For full schema documentation, see [DATABASE-SCHEMA.md](DATABASE-SCHEMA.md)

---

## Real-Time Communication

### Socket.IO Architecture

```
┌──────────┐     WebSocket      ┌──────────────┐
│  Client  │◄──────────────────►│  Socket.IO   │
│  Browser │     (persistent)   │   Server     │
└──────────┘                    └──────┬───────┘
                                       │
                                ┌──────▼───────┐
                                │   Events     │
                                │              │
                                │ • attendance │
                                │   :update    │
                                │ • payment    │
                                │   :status    │
                                │ • salary     │
                                │   :approved  │
                                │ • notify     │
                                │   :new       │
                                └──────────────┘
```

### Event Flow Example — Attendance Update

```
Teacher marks attendance
       │
       ▼
POST /api/attendance
       │
       ▼
Controller saves to MongoDB
       │
       ├──► ActivityLog created (audit)
       │
       └──► Socket.IO emits "attendance:update"
                    │
                    ├──► Admin dashboard refreshes
                    ├──► Owner analytics update
                    └──► Teacher salary recalculated
```

---

## Caching Strategy

```
Request Flow with Caching:

Client Request
     │
     ▼
┌────────────┐     Cache Hit?     ┌─────────┐
│   Backend  │────────────────────► │  Redis  │
│   API      │◄────────────────────│  Cache  │
│            │     Return cached    │         │
│            │                      └─────────┘
│            │     Cache Miss ↓
│            │──────────────────────► MongoDB 7
│            │◄────────────────────── Query
│            │
│            │──── Store in Redis ──► Redis
│            │     (with TTL)
└────────────┘
```

### Cache Invalidation

| Trigger | Action |
|---------|--------|
| Data mutation (POST/PUT/DELETE) | Invalidate related cache keys |
| Salary period close | Clear salary-related caches |
| Enrollment change | Invalidate student + class caches |
| Payment update | Invalidate financial + analytics caches |

---

## Security Architecture

### Defense in Depth

```
Layer 1: Network
  └─ Cloudflare DDoS protection + WAF

Layer 2: Transport
  └─ Full Strict SSL/TLS (end-to-end encryption)

Layer 3: Reverse Proxy
  └─ Nginx: request size limits, security headers

Layer 4: Application
  ├─ Helmet (15+ HTTP security headers)
  ├─ CORS (strict origin whitelist)
  ├─ Rate limiting (6 tiers)
  ├─ Input validation (express-validator)
  ├─ Mongo sanitization
  ├─ XSS cleaning
  └─ CSRF tokens

Layer 5: Authentication
  ├─ JWT with refresh token rotation
  ├─ Google OAuth 2.0
  ├─ TOTP 2FA with backup codes
  ├─ bcrypt password hashing
  └─ Device trust management

Layer 6: Authorization
  └─ RBAC (Owner > Admin > Teacher/Accountant)

Layer 7: Data
  ├─ MongoDB network isolation (Docker)
  ├─ Mongoose schema validation
  └─ Activity logging on all mutations

Layer 8: Monitoring
  ├─ Winston structured logging
  ├─ Login history tracking
  ├─ Threat detection service
  └─ Activity audit trail with diffs
```

---

## Background Jobs

### Scheduled Tasks (Node-Cron)

| Job | Schedule | Purpose |
|-----|----------|---------|
| **Enrollment Activation** | Daily | Auto-activate enrollments on start date |
| **Payment Due Adjustment** | Daily | Update payment deadlines and status |
| **Auto Reminders** | Daily | Send payment/attendance email reminders |
| **Account Auto-Unlock** | Hourly | Unlock temporarily locked accounts |
| **Registration Deadline** | Daily | Check and process expired registrations |
| **End-of-Cycle Detection** | Daily | Detect and flag ending enrollment cycles |
| **Email Batch Processing** | Every 5 min | Process queued emails in batches |
| **Health Monitoring** | Every 10 min | Check service health and alert on issues |

---

## CI/CD Pipeline

```
Developer pushes to main
          │
          ▼
┌─────────────────────┐
│   GitHub Actions     │
│                      │
│  ┌────────────────┐  │
│  │ Backend Tests  │  │
│  │ • Jest unit    │  │
│  │ • Integration  │  │
│  │ • Coverage     │  │
│  └────────────────┘  │
│  ┌────────────────┐  │
│  │ Frontend Tests │  │
│  │ • Vitest unit  │  │
│  │ • Coverage     │  │
│  └────────────────┘  │
│  ┌────────────────┐  │
│  │  E2E Tests     │  │
│  │ • Playwright   │  │
│  └────────────────┘  │
│  ┌────────────────┐  │
│  │  Type Check    │  │
│  │ • tsc --noEmit │  │
│  └────────────────┘  │
│  ┌────────────────┐  │
│  │  Lint Check    │  │
│  │ • ESLint       │  │
│  └────────────────┘  │
│                      │
│  All pass? ──────────┤
│         │            │
└─────────▼────────────┘
          │
    ┌─────▼──────┐
    │ SSH Deploy │
    │ to VPS     │
    │            │
    │ docker-    │
    │ compose    │
    │ up --build │
    └────────────┘
```

---

## Monitoring & Logging

### Logging Architecture

```
Application Events
       │
       ├──► Winston Logger
       │      ├── Console (development)
       │      ├── File: combined.log (all levels)
       │      ├── File: error.log (errors only)
       │      └── Daily rotation (14-day retention)
       │
       ├──► Activity Logger (Middleware)
       │      ├── Module identification
       │      ├── Action classification
       │      ├── Before/After state diffs
       │      ├── User attribution
       │      ├── IP address tracking
       │      └── Stored in ActivityLog collection
       │
       └──► Health Endpoints
              ├── /api/health (API health)
              ├── MongoDB connectivity
              ├── Redis connectivity
              └── External service status
```

---

<div align="center">

**[← Back to README](README.md)** · **[Features →](FEATURES.md)** · **[Database Schema →](DATABASE-SCHEMA.md)**

</div>

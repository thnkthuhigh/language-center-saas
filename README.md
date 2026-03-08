<div align="center">

# Language Center SaaS — Management Platform

### Enterprise-grade management system for English language centers

[![Live Demo](https://img.shields.io/badge/Live_Demo-englishforkids.me-blue?style=for-the-badge&logo=google-chrome&logoColor=white)](https://www.englishforkids.me/)
[![Stack](https://img.shields.io/badge/Stack-MERN-green?style=for-the-badge&logo=mongodb&logoColor=white)](https://www.englishforkids.me/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.9-blue?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Tests](https://img.shields.io/badge/Tests-822_passing-brightgreen?style=for-the-badge&logo=jest&logoColor=white)](#testing)
[![Coverage](https://img.shields.io/badge/Coverage-90%25+-brightgreen?style=for-the-badge&logo=codecov&logoColor=white)](#testing)

<br/>

**A full-stack SaaS platform built to manage every aspect of running an English language center — from student enrollment and class scheduling to financial tracking, payroll automation, and real-time analytics.**

*Nền tảng SaaS full-stack quản lý toàn bộ hoạt động trung tâm tiếng Anh — từ ghi danh học viên, xếp lịch lớp học, đến quản lý tài chính, tính lương tự động, và phân tích dữ liệu thời gian thực.*

<br/>

[Live Demo](https://www.englishforkids.me/) · [Architecture](ARCHITECTURE.md) · [Features](FEATURES.md) · [Database Schema](DATABASE-SCHEMA.md) · [API Reference](API-EXAMPLES.md)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Live Demo](#live-demo)
- [Technical Highlights](#technical-highlights)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [System Architecture](#system-architecture)
- [Project Scale](#project-scale)
- [Role-Based Access](#role-based-access)
- [Security](#security)
- [Performance & Optimization](#performance--optimization)
- [Testing & Quality](#testing--quality)
- [DevOps & Deployment](#devops--deployment)
- [API Reference](API-EXAMPLES.md)
- [Screenshots](#screenshots)
- [About & Challenges](#about--engineering-challenges)
- [Source Code](#source-code)
- [Contact](#contact)

---

## Overview

**Language Center SaaS** is a proprietary, production-deployed management platform designed to handle the complete operational lifecycle of English language centers. The system serves **4 distinct user roles** (Owner, Admin, Teacher, Accountant), each with dedicated dashboards and permissions.

This is not a tutorial project — it is a **real-world production system** serving actual users at [englishforkids.me](https://www.englishforkids.me/), built over months of iterative development with:

- **50,000+ lines** of production code
- **822 automated tests** with 90%+ coverage
- **200+ API endpoints** across 42+ route modules
- **40+ MongoDB data models** with complex relationships
- **80+ frontend pages** with lazy-loading and code-splitting
- **50+ pages** of technical documentation
- **121 improvement tasks** completed (100%)

---

## Technical Highlights

What sets this project apart from typical portfolio projects:

| Area | Highlight |
|------|----------|
| **Production-Deployed** | Not a localhost demo — live at [englishforkids.me](https://www.englishforkids.me/) serving real users |
| **4-Role RBAC System** | Owner, Admin, Teacher, Accountant — each with dedicated dashboards and fine-grained permissions |
| **Real-Time Sync** | Socket.IO for live attendance marking, payment updates, and salary approval notifications |
| **Multi-Tier Salary Engine** | Attendance-based teacher salary + shift-based admin salary + multi-step approval workflow |
| **Financial Pipeline** | End-to-end: Tuition pricing → Payment tracking → Invoice auto-gen → Receipt with QR → Email delivery |
| **Security-First** | JWT + 2FA (TOTP) + Device trust + 6-tier rate limiting + ReDoS prevention + CSRF + Mongo sanitization |
| **Cloud-Native** | Docker → Nginx → Cloudflare Full Strict SSL, with AWS S3 storage and multi-cloud backup |
| **AI-Integrated** | Google Vertex AI for intelligent suggestions and data analysis |
| **Enterprise Testing** | 822+ tests (Jest + Vitest + Playwright E2E), 90%+ coverage, CI/CD with GitHub Actions |
| **Accessibility** | WCAG 2.1 AA: focus trap, skip-to-content, screen reader hooks, keyboard table navigation |
| **Complete Documentation** | 50+ pages: feature contracts, logic flows, API docs, user manuals, security guidelines |
| **Zero Tech Debt** | 121/121 improvement tasks completed, all 4 priority phases at 100% |

---

## Live Demo

> **🔗 [https://www.englishforkids.me/](https://www.englishforkids.me/)**

The application is **live in production**, deployed on a DigitalOcean VPS with:
- **Nginx** reverse proxy with SSL termination
- **Cloudflare** Full (Strict) SSL/TLS encryption
- **Docker** containerized services
- **Automated CI/CD** via GitHub Actions

---

## Key Features

### Academic Management
| Feature | Description |
|---------|-------------|
| **Student Lifecycle** | Complete flow: Registration → Enrollment → Class Assignment → Transfer → Graduation |
| **CEFR Progression** | Track student levels from A1 through C2 with automated progression |
| **Class Scheduling** | Flexible multi-session weekly schedules with timezone support (Asia/Ho_Chi_Minh) |
| **Attendance Tracking** | Session-based attendance with present/absent/excused statuses |
| **Makeup Sessions** | Automated credit system for missed classes |
| **Class Cancellation** | Holiday-aware scheduling with cancellation handling |
| **Teacher Assignment** | Teacher allocation with substitution management |
| **Teaching Schedule** | Visual calendar view of all teaching sessions |

### Financial System
| Feature | Description |
|---------|-------------|
| **Payment Cycles** | Support for 1/4/8/12-week payment periods |
| **Invoice Generation** | Auto-generated invoices with sequential numbering |
| **Receipt System** | Professional receipts with QR codes and email delivery |
| **Payment Tracking** | Status flow: Pending → Partial → Paid → Overdue |
| **Tuition Price Tables** | Configurable pricing tiers with date ranges |
| **Cross-Class Payments** | Handle payments spanning multiple class enrollments |
| **Extension Proposals** | Payment deadline extension workflow |
| **Financial Reports** | Revenue analysis with Excel/PDF export |

### Salary & Payroll
| Feature | Description |
|---------|-------------|
| **Teacher Salary** | Attendance-based compensation with salary codes (K1, K2, K3 tiers) |
| **Admin Salary** | Shift-based tracking for office & assistant staff |
| **Approval Workflow** | Multi-tier approval: Draft → Reviewed → Approved → Paid |
| **Salary Periods** | Monthly period management with batch processing |
| **Detailed Breakdown** | Per-session salary calculation with full audit trail |

### Analytics & Reporting
| Feature | Description |
|---------|-------------|
| **Owner Dashboard** | Revenue metrics, enrollment trends, class performance at a glance |
| **Revenue Analytics** | Period-over-period revenue comparison with forecasting |
| **Attendance Statistics** | Student and class attendance rates with trend analysis |
| **Student Analytics** | Enrollment funnels, retention rates, level distribution |
| **Custom Reports** | Generate PDF/Excel reports with configurable filters |

### Real-Time & Communication
| Feature | Description |
|---------|-------------|
| **Socket.IO Integration** | Live attendance updates, payment notifications, salary approvals |
| **Email Notifications** | Automated reminders via SendGrid + Azure Communication Services |
| **In-App Notifications** | Real-time notification bell with read/unread management |
| **Automated Reminders** | Cron-based payment due dates, birthday alerts, enrollment activations |

### AI Integration
| Feature | Description |
|---------|-------------|
| **Google Vertex AI** | AI-powered suggestions for class management and data analysis |
| **Smart Formatting** | AI document formatting and content structuring |

### Cloud & Storage
| Feature | Description |
|---------|-------------|
| **AWS S3** | File storage with presigned URLs for secure access |
| **Multi-Cloud Backup** | Automated backups to Oracle Cloud and Google Cloud |
| **File Management** | In-app file browser with folder organization |

> 📄 For the complete feature breakdown, see [FEATURES.md](FEATURES.md)

---

## Tech Stack

### Frontend

| Technology | Version | Purpose |
|-----------|---------|---------|
| **React** | 18.2 | UI framework with hooks and concurrent features |
| **TypeScript** | 5.9 | Type-safe development across the entire frontend |
| **Vite** | 4.5 | Lightning-fast HMR and optimized production builds |
| **TailwindCSS** | 3.3 | Utility-first styling with custom design system |
| **TanStack Query** | 5.90 | Server state management with caching and background refetch |
| **TanStack Table** | 8.21 | Headless table with sorting, filtering, pagination |
| **TanStack Virtual** | 3.13 | Virtualized lists for 1000+ row rendering |
| **Zustand** | 5.0 | Lightweight client state management |
| **React Router** | 6.8 | File-based routing with lazy-loaded code splitting |
| **React Hook Form** | 7.59 | Performant form handling with controlled/uncontrolled support |
| **Zod** | 4.2 | Schema validation for forms and API responses |
| **Radix UI** | Latest | 14 accessible headless UI primitives |
| **Socket.IO Client** | 4.8 | Real-time bidirectional communication |
| **Recharts + Chart.js** | Latest | Data visualization dashboards |
| **Framer Motion** | 12.23 | Fluid animations and page transitions |
| **jsPDF + XLSX** | Latest | Client-side PDF and Excel export |
| **Playwright** | 1.57 | End-to-end browser testing |
| **Vitest** | 4.0 | Unit and integration testing |

### Backend

| Technology | Version | Purpose |
|-----------|---------|---------|
| **Node.js** | 20 LTS | Server runtime |
| **Express** | 4.18 | HTTP framework with middleware pipeline |
| **MongoDB** | 7.0 | Document database with Mongoose 8.0 ODM |
| **Redis** | Latest | Distributed caching, sessions, and rate limiting state |
| **Socket.IO** | 4.7 | WebSocket server for real-time features |
| **JWT** | Latest | Stateless authentication with refresh tokens |
| **Google Auth** | Latest | OAuth 2.0 social login |
| **Speakeasy** | Latest | TOTP-based 2FA with backup codes |
| **Helmet** | 8.1 | HTTP security headers |
| **Winston** | 3.19 | Structured logging with daily rotation |
| **Node-Cron** | 3.0 | 8 scheduled background jobs |
| **Puppeteer** | 24.34 | Server-side PDF generation |
| **Swagger** | 6.2 | OpenAPI 3.0 interactive documentation |
| **Jest** | 30.2 | Backend unit and integration testing |
| **Supertest** | 7.1 | HTTP assertion testing |

### Cloud & Infrastructure

| Technology | Purpose |
|-----------|---------|
| **Docker + Docker Compose** | Containerized multi-service deployment |
| **Nginx** | Reverse proxy, SSL termination, static file serving |
| **Cloudflare** | DNS, CDN, DDoS protection, Full Strict SSL |
| **DigitalOcean** | VPS hosting (4GB RAM, 80GB SSD) |
| **GitHub Actions** | CI/CD pipeline with automated testing and deployment |
| **AWS S3** | Object storage for uploads and documents |
| **Azure Communication** | Enterprise email delivery service |
| **SendGrid** | Transactional email service |
| **Google Vertex AI** | AI/ML model inference |

---

## System Architecture

```
                        ┌──────────────────┐
                        │   Cloudflare CDN  │
                        │  Full Strict SSL  │
                        │  DDoS Protection  │
                        └────────┬─────────┘
                                 │
                        ┌────────▼─────────┐
                        │      Nginx       │
                        │  Reverse Proxy   │
                        │  SSL Termination │
                        │  Rate Limiting   │
                        └───┬──────────┬───┘
                            │          │
               ┌────────────▼┐    ┌────▼────────────┐
               │   Frontend  │    │    Backend API   │
               │  React SPA  │    │   Express.js     │
               │  Port: 80   │    │   Port: 3000     │
               │             │    │                  │
               │ • TypeScript│    │ • 42+ Routes    │
               │ • TailwindCS│    │ • 40+ Controllers│
               │ • TanStack  │    │ • 23+ Services   │
               │ • Zustand   │    │ • 13 Middleware   │
               │ • Socket.IO │    │ • Socket.IO      │
               └─────────────┘    └──┬───────┬───┬───┘
                                     │       │   │
                         ┌───────────▼┐  ┌───▼───▼──────┐
                         │  MongoDB 7  │  │    Redis     │
                         │  40+ Models │  │   Cache &    │
                         │  Indexed    │  │  Rate Limit  │
                         └─────────────┘  └──────────────┘
                                     │
              ┌──────────┬───────────┼───────────┬──────────┐
              │          │           │           │          │
        ┌─────▼───┐ ┌────▼────┐ ┌────▼────┐ ┌────▼───┐ ┌───▼────┐
        │  AWS S3  │ │ Azure   │ │ SendGrid│ │ Google │ │  Cron  │
        │ Storage  │ │ Email   │ │  Email  │ │ Vertex │ │  Jobs  │
        │          │ │ Service │ │         │ │   AI   │ │ (8 bg) │
        └──────────┘ └─────────┘ └─────────┘ └────────┘ └────────┘
```

### Data Flow

```
User Request → Cloudflare → Nginx → Express Middleware Pipeline:
   │
   ├─ 1. Helmet (Security Headers)
   ├─ 2. CORS (Origin Validation)
   ├─ 3. Rate Limiter (6 different limits)
   ├─ 4. Body Parser + Mongo Sanitize
   ├─ 5. HPP (Parameter Pollution Prevention)
   ├─ 6. JWT Authentication
   ├─ 7. Role-Based Authorization
   ├─ 8. Input Validation (express-validator)
   ├─ 9. Controller → Service → Model
   ├─ 10. Activity Logger (audit trail with diffs)
   └─ 11. Response → Client
```

### Frontend Architecture

```
App.tsx
  ├─ AuthContext (user, token, role, branch)
  ├─ UserPreferencesContext (theme, settings)
  └─ Router (lazy-loaded pages)
       │
       ├─ Pages (80+ pages with code-splitting)
       │    ├─ Custom Hooks (business logic)
       │    │    └─ Services (API calls via Axios)
       │    └─ Components (200+ reusable UI)
       │         ├─ Radix UI Primitives
       │         ├─ Custom Design System
       │         └─ Chart Components
       │
       └─ Real-time Layer (Socket.IO)
            ├─ Attendance updates
            ├─ Payment notifications
            └─ Salary approvals
```

> 📄 For detailed architecture diagrams, see [ARCHITECTURE.md](ARCHITECTURE.md)

---

## Project Scale

| Category | Count |
|----------|-------|
| **Frontend Pages** | 80+ (lazy-loaded) |
| **Reusable Components** | 200+ |
| **Custom React Hooks** | 50+ |
| **API Service Modules** | 42+ |
| **Backend Route Modules** | 42+ |
| **REST API Endpoints** | 200+ |
| **MongoDB Models** | 40+ (39 schemas) |
| **Backend Controllers** | 40+ |
| **Backend Services** | 23+ specialized |
| **Middleware Layers** | 13 |
| **Background Cron Jobs** | 8 |
| **Automated Tests** | 822+ passing |
| **Test Coverage** | 90%+ (backend & frontend) |
| **Documentation Pages** | 50+ |
| **Lines of Code** | 50,000+ |
| **Improvement Tasks** | 121/121 completed (100%) |

---

## Role-Based Access

The system supports **4 distinct user roles**, each with a dedicated dashboard and tailored permissions:

| Role | Dashboard | Key Capabilities |
|------|-----------|-----------------|
| **🏢 Owner** | Executive overview with revenue, enrollment, and performance metrics | Full system access, user management, branch management, analytics, settings |
| **👨‍💼 Admin** | Operational dashboard for daily management tasks | Student & class management, enrollment, attendance, scheduling, salary processing |
| **👨‍🏫 Teacher** | Teaching-focused view with schedule and salary | View own schedule, mark attendance, view salary reports, teaching history |
| **💼 Accountant** | Financial dashboard with payment and salary overviews | Payment processing, invoice/receipt generation, salary approval, financial reports |

### Permission Matrix

```
Feature                    Owner  Admin  Teacher  Accountant
─────────────────────────  ─────  ─────  ───────  ──────────
User Management             ✅     ❌      ❌        ❌
Branch Management           ✅     ❌      ❌        ❌
System Settings             ✅     ❌      ❌        ❌
Student Management          ✅     ✅      ❌        ❌
Class Management            ✅     ✅      ❌        ❌
Enrollment Management       ✅     ✅      ❌        ❌
Attendance (all classes)    ✅     ✅      ❌        ❌
Attendance (own classes)    ✅     ✅      ✅        ❌
Payment Processing          ✅     ✅      ❌        ✅
Invoice / Receipt           ✅     ✅      ❌        ✅
Salary Processing           ✅     ✅      ❌        ✅
Salary Approval             ✅     ❌      ❌        ✅
Teaching Schedule           ✅     ✅      ✅        ❌
View Own Salary             ✅     ✅      ✅        ✅
Analytics / Reports         ✅     ✅      ❌        ✅
Activity Logs               ✅     ✅      ❌        ❌
File Storage                ✅     ✅      ❌        ❌
```

---

## Security

### Authentication & Authorization
- **JWT tokens** with HTTP-only cookies and refresh token rotation
- **Google OAuth 2.0** social login integration
- **TOTP-based 2FA** (Time-based One-Time Password) with backup codes
- **Device trust management** — track and manage trusted devices per user
- **Login history** — full audit trail of login sessions with IP and user-agent
- **Role-based access control (RBAC)** with 4 permission tiers
- **Password hashing** with bcrypt (adaptive cost factor)

### API Security
- **Helmet.js** — 15+ HTTP security headers (CSP, HSTS, X-Frame-Options, etc.)
- **6-tier rate limiting:**
  - General: 500 req/15 min
  - Authentication: 20 req/15 min
  - Password Reset: 5 req/hour
  - File Upload: 50 req/hour
  - AI Endpoints: 15 req/min
  - Payment: 20 req/hour
- **MongoDB injection prevention** — express-mongo-sanitize
- **XSS protection** — xss-clean middleware
- **Parameter pollution prevention** — HPP middleware
- **CORS** — strict origin validation
- **CSRF protection** — token-based cross-site request forgery prevention
- **ReDoS prevention** — regex input sanitization (all user inputs escaped before RegExp)

### Infrastructure Security
- **Cloudflare Full (Strict) SSL/TLS** — end-to-end encryption
- **Nginx hardened config** — security headers, request size limits
- **Docker network isolation** — only Nginx exposed publicly, DB hidden
- **sameSite cookie policy** — consistent `Lax` across all endpoints
- **Input validation** — express-validator on all API endpoints
- **Structured logging** — Winston with daily rotation, no sensitive data in logs

---

## Performance & Optimization

### Frontend
- **Code splitting** — Vite lazy-loading with `React.lazy()` for 80+ pages
- **Virtual scrolling** — TanStack Virtual for rendering 1000+ rows efficiently
- **Optimistic updates** — TanStack Query mutations with rollback
- **Bundle optimization** — Terser minification, tree-shaking, chunk splitting
- **Image optimization** — compressed assets via Vite build pipeline
- **Memoization** — `useMemo`, `useCallback` for expensive computations

### Backend
- **Redis caching** — hot data caching with intelligent invalidation
- **Database indexing** — compound indexes on frequently queried fields
- **Connection pooling** — Mongoose connection management
- **Response compression** — gzip compression for API responses
- **Query optimization** — `.lean()`, `.select()`, pagination on all list endpoints

### Infrastructure
- **Nginx caching** — static asset caching with `immutable` headers (1 year TTL)
- **Cloudflare CDN** — edge caching and DDoS mitigation
- **Docker multi-stage builds** — minimal production images (Node Alpine)
- **Health checks** — liveness probes on all services

---

## Testing & Quality

### Test Infrastructure

| Layer | Framework | Tests | Coverage |
|-------|-----------|-------|----------|
| **Backend Unit** | Jest 30.2 + Supertest | 400+ | ~85-90% |
| **Frontend Unit** | Vitest 4.0 + React Testing Library | 350+ | ~95% |
| **Frontend Hooks** | Vitest + custom render | 140+ | ~90% |
| **E2E** | Playwright 1.57 | 68+ | Key flows |
| **Total** | — | **822+** | **90%+** |

### Test Categories
- **47 test files** created with 800+ test cases
- **Component tests** — FormInput, Table, Modal, Navigation, Dashboard widgets
- **Hook tests** — usePagination, useAttendanceManagement, custom form hooks
- **Page tests** — Student management, Payment flows, Attendance tracking
- **E2E scenarios** — Auth flow, Payment processing, Enrollment workflow
- **API integration** — Controller testing with mocked database
- **MSW (Mock Service Worker)** — API mocking for frontend tests

### Code Quality
- **ESLint** — enforced coding standards with React + Hooks rules
- **TypeScript strict mode** — full type safety across frontend
- **Conventional commits** — `feat:`, `fix:`, `refactor:`, `perf:`, `style:` prefixes
- **File size limits** — <300 lines preferred, >800 lines mandatory split
- **121 improvement tasks** — all 121 completed (100% across 4 priority phases)

### Accessibility
- **WCAG 2.1 Level AA** compliance
- Focus trap management in modals (`useFocusTrap` hook)
- Skip-to-content navigation for keyboard users
- Screen reader announcements (`useAnnounce` hook)
- Full keyboard navigation for data tables (`useTableKeyboardNav` hook)
- ARIA attributes on all interactive components

---

## DevOps & Deployment

### Production Architecture

```
GitHub (push to main)
     │
     ▼
GitHub Actions CI/CD
     │
     ├─ Run backend tests (Jest)
     ├─ Run frontend tests (Vitest)
     ├─ Run E2E tests (Playwright)
     ├─ TypeScript type check
     ├─ ESLint validation
     │
     ▼
Deploy to DigitalOcean VPS
     │
     ▼
Docker Compose (Production)
     │
     ├─ Nginx (ports 80/443) ─── Only public-facing service
     ├─ Frontend (internal) ──── React SPA served by Nginx
     ├─ Backend (internal) ───── Express API
     ├─ MongoDB (internal) ───── Database (no external access)
     ├─ Redis (internal) ─────── Cache layer
     └─ Backup (scheduled) ──── Multi-cloud backup
```

### Infrastructure Details

| Component | Specification |
|-----------|--------------|
| **Server** | DigitalOcean Droplet — 4GB RAM, 80GB SSD, Ubuntu 22.04 |
| **Region** | Singapore (SGP1) — optimized for Southeast Asia |
| **SSL** | Cloudflare Full (Strict) — end-to-end encryption |
| **DNS** | Cloudflare — DDoS protection, edge caching |
| **Containers** | Docker Compose — 5 services with health checks |
| **Process Manager** | Docker restart policies (always) |
| **Backups** | Automated to Oracle Cloud + Google Cloud Storage |
| **Monitoring** | Winston logs with daily rotation + health endpoints |
| **CI/CD** | GitHub Actions — test → build → deploy on push |

---

## API Reference

The system exposes **200+ RESTful endpoints** across 44 route modules with consistent response formats, role-based authorization, and Redis caching.

**Quick examples:**

```
POST  /api/auth/login              → JWT token + user profile
GET   /api/students?page=1&limit=10 → Paginated student list with enrollments
POST  /api/attendance              → Record attendance + auto salary calculation
POST  /api/payments/:id/record     → Record payment with multi-cycle support
GET   /api/analytics/dashboard     → Revenue, students, occupancy, churn metrics
```

Every endpoint returns standardized JSON:
```json
{ "success": true, "data": { }, "pagination": { }, "message": "..." }
```

**📖 [View Full API Reference →](API-EXAMPLES.md)**  
*Includes request/response examples for all major modules: Auth, Students, Classes, Attendance, Payments, Enrollments, Salary, Analytics, Receipts & Invoices.*

---

## Screenshots

> 📷 Screenshots are being prepared. In the meantime, you can explore the live application:
>
> **🔗 [https://www.englishforkids.me/](https://www.englishforkids.me/)**

### Key Screens

| # | Screen | Description |
|---|--------|-------------|
| 1 | **Owner Dashboard** | Executive overview with revenue charts, enrollment stats, and real-time performance KPIs |
| 2 | **Teacher Dashboard** | Teaching-focused view with personal schedule, class list, and salary summary |
| 3 | **Accountant Dashboard** | Financial overview with pending payments, overdue invoices, and salary approval queue |
| 4 | **Student Management** | Searchable student list with CEFR level badges, enrollment status, and bulk actions |
| 5 | **Class Management** | Class list with schedule visualization, capacity indicators, and teacher assignments |
| 6 | **Attendance Tracking** | Session-based attendance interface with real-time Socket.IO sync |
| 7 | **Payment System** | Payment processing with invoice auto-generation, receipt with QR code, and email delivery |
| 8 | **Salary Management** | Payroll dashboard with per-session breakdown and multi-tier approval workflow |
| 9 | **Analytics & Charts** | Interactive Recharts/Chart.js dashboards for revenue, attendance, and enrollment trends |
| 10 | **Activity Logs** | Comprehensive audit trail with before/after diffs and user attribution |

<!-- 
When screenshots are ready, replace this section with:

<div align="center">
<img src="screenshots/dashboard-owner.png" alt="Owner Dashboard" width="800"/>
<p><em>Owner Dashboard — Revenue metrics, enrollment trends, and real-time KPIs</em></p>
<br/>
<img src="screenshots/student-management.png" alt="Student Management" width="800"/>
<p><em>Student Management — Search, filters, CEFR levels, and enrollment tracking</em></p>
<br/>
<img src="screenshots/attendance-tracking.png" alt="Attendance" width="800"/>
<p><em>Attendance Tracking — Real-time session-based marking with Socket.IO</em></p>
<br/>
<img src="screenshots/payment-system.png" alt="Payment System" width="800"/>
<p><em>Payment System — Invoice, receipt with QR code, and email delivery</em></p>
<br/>
<img src="screenshots/salary-management.png" alt="Salary" width="800"/>
<p><em>Salary Management — Per-session breakdown with approval workflow</em></p>
<br/>
<img src="screenshots/analytics.png" alt="Analytics" width="800"/>
<p><em>Analytics — Revenue charts, enrollment trends, attendance statistics</em></p>
</div>
-->

---

## Repository Structure

```
language-center-saas/
│
├── README.md                  ← You are here
├── ARCHITECTURE.md            ← System architecture deep-dive
├── FEATURES.md                ← Complete feature breakdown
├── DATABASE-SCHEMA.md         ← MongoDB schema documentation
├── API-EXAMPLES.md            ← API reference with request/response examples
│
├── screenshots/               ← Application screenshots
│   └── (coming soon)
│
├── diagrams/                  ← Architecture diagrams
│   └── (coming soon)
│
└── demo/                      ← Demo materials
    └── (coming soon)
```

---

## Demo Video

> 🎥 A full walkthrough demo video is being recorded and will be linked here.
>
> In the meantime, you can explore all features on the **[live application](https://www.englishforkids.me/)**.

---

## About & Engineering Challenges

### Who Built This

I'm a full-stack developer who built this entire platform **solo** — from database design to production deployment. This project represents months of iterative development, solving real-world problems for a real business, not a tutorial follow-along.

### Hardest Problems I Solved

| # | Challenge | What Made It Hard | How I Solved It |
|---|-----------|-------------------|------------------|
| 1 | **Multi-cycle payment system** | Students pay in 1/4/8/12-week cycles, each with partial payments, extensions, cross-class transfers, and prorated refunds. The state machine has 6 statuses with complex transitions. | Designed a payment period model that tracks per-cycle amounts, session grants, and payment history. Built a cross-payment engine that calculates prorated refunds when transferring between classes mid-cycle. |
| 2 | **Attendance-based salary engine** | Teacher salary depends on actual sessions taught, with 3 tier rates (K1/K2/K3) per class, substitute teacher handling, makeup sessions, and a multi-step approval workflow (Draft → Reviewed → Approved → Paid). | Created a salary code system where each class maps to a code with sub-tiers. Attendance records auto-calculate salary per session. Built a period-based batch system with finalize/approve/disburse pipeline. |
| 3 | **Real-time sync across roles** | When a teacher marks attendance, the admin dashboard, accountant's payment view, and owner's analytics must all update instantly — without page refresh. | Implemented Socket.IO rooms per role and branch. Each write operation emits targeted events. Frontend uses TanStack Query cache invalidation triggered by socket events for optimistic updates. |
| 4 | **Race conditions in concurrent payments** | Two accountants recording payments for the same student simultaneously caused double-counting and incorrect balances. | Added optimistic locking with version fields on payment documents. Used MongoDB's `findOneAndUpdate` with version checks. Frontend shows real-time lock indicators via Socket.IO. |
| 5 | **Holiday-aware scheduling** | Class sessions must skip holidays (including lunar calendar dates like Tết), auto-extend enrollment periods, and recalculate payment due dates accordingly. | Built a holiday service that supports both fixed and lunar calendar holidays. Enrollment creation pre-calculates all session dates, skipping holidays, and adjusts payment cycles automatically. |
| 6 | **Security hardening for production** | Handling real user data and financial transactions demanded enterprise-level security — not just "add Helmet and call it done". | Implemented 6-tier rate limiting, ReDoS prevention on all regex inputs, MongoDB injection sanitization, CSRF tokens, device trust management, 2FA with backup codes, and account lockout after failed attempts. |

### Key Takeaways

- **"It works" is not enough** — Production means handling edge cases, race conditions, timezone issues, and malicious input that tutorials never cover.
- **Testing saves time** — The 822 tests caught dozens of regressions during the 121-task improvement sprint. Without them, I would have broken production multiple times.
- **Documentation is a feature** — Writing 50+ pages of docs forced me to think clearly about every design decision. Future me (and any team) can onboard in hours, not weeks.

---

## Source Code

> **This repository is a showcase of the project.** The full source code is in a private repository due to proprietary business logic, API keys, and client data protection.

### Access Policy
- **Source code is available upon request** for potential employers and recruiters
- Contact me directly and I will grant collaborator access to the private repository
- Happy to do a **live code walkthrough** or **technical deep-dive** in an interview setting

### What the Private Repository Contains
- 50,000+ lines of production TypeScript/JavaScript
- Complete frontend (React + TypeScript) and backend (Node.js + Express) source
- 822+ automated tests with 90%+ coverage
- 50+ pages of internal technical documentation
- Docker configurations for development and production
- CI/CD pipeline configurations
- Database migration and seeding scripts

---

## Contact

If you'd like to discuss this project, request source code access, or schedule a technical walkthrough:

- **Email:** a01204496068@gmail.com
- **LinkedIn:** [linkedin.com/in/thnkthuhigh](https://www.linkedin.com/in/thnkthuhigh/)
- **GitHub:** [github.com/thnkthuhigh](https://github.com/thnkthuhigh)

> *Feel free to open an [issue](../../issues) in this repository for questions about the project.*

---

<div align="center">

### Built with modern technologies and production-grade engineering practices

**React** · **TypeScript** · **Node.js** · **Express** · **MongoDB** · **Redis** · **Socket.IO** · **Docker** · **AWS S3** · **Cloudflare** · **Google Vertex AI**

---

⭐ If you find this project interesting, consider giving it a star!

*Last updated: March 2026*

</div>

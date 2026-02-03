---
stepsCompleted: [1, 2, 3, 4]
inputDocuments:
  - 'PRD/master-index.md'
  - 'PRD/breakdown/06-technical-architecture/requirements.md'
  - 'PRD/breakdown/06-technical-architecture/questions-answers.md'
  - 'PRD/breakdown/07-database-schema/requirements.md'
  - 'PRD/breakdown/08-api-specifications/requirements.md'
workflowType: 'architecture'
project_name: 'Livvly-Req'
user_name: 'Nandhu'
date: '2026-02-02'
---
# Architecture Decision Document: LIVVLY Voice Dating App

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Document Context

**Project:** LIVVLY Voice Dating App - India's first voice-first dating platform
**Created:** 2026-02-02
**Status:** In Progress
**Facilitator:** Claude (BMad Architecture Workflow)

## Input Documents Loaded

This architecture builds upon comprehensive PRD breakdown documentation:

1. **Master Index** (PRD/master-index.md)

   - 15/15 sections complete
   - 203 total requirements
   - 107 design decisions
   - 129 questions answered
2. **Section 6: Technical Architecture** (requirements + Q&A)

   - 18 requirements (P0: 10, P1: 6, P2: 2)
   - Recently updated with scalability review (2026-02-02)
   - Key decisions: Flutter + FastAPI, right-sized auto-scaling, deferred sharding
   - Infrastructure cost optimization: $3,480/year savings
3. **Section 7: Database Schema**

   - 15 requirements covering 22 database tables
   - 8-shard logical partitioning architecture
   - 50+ indexes for <200ms SLA
   - Comprehensive i18n strategy (5 languages)
4. **Section 8: API Specifications**

   - 20 requirements covering 40+ RESTful endpoints
   - Complete API coverage for all database tables
   - Tiered creator revenue model (75-85%)

## Key Context from PRD

**Scale Targets:**

- MVP: 10K MAU with 500-800 concurrent users
- Month 12: 200K MAU with 20K-24K concurrent
- Year 2: 1M MAU with 120K-150K concurrent

**Technology Stack (Validated):**

- Mobile: Flutter 3.x (cross-platform)
- Backend: FastAPI + Python 3.11+
- Database: PostgreSQL 15 (AWS RDS managed)
- Cache: Redis 7 (Cluster with replication)
- Real-time: Agora SDK (Mumbai + Bangalore edges)

**Performance Targets:**

- API Response: <200ms p95
- Call Capacity: 1,000 simultaneous (MVP) → 25,000 (Year 2)
- Concurrent Users: Phased 5-8% → 12-15%

---

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
The LIVVLY PRD defines 203 requirements across 15 sections with the following architectural implications:

| Category                                 | Requirements | Key Architectural Concerns                            |
| ---------------------------------------- | ------------ | ----------------------------------------------------- |
| Foundation (Sections 1-4)                | 47           | Market positioning, revenue model, 5-language support |
| Core Features (Section 5)                | 25           | Voice calls, live rooms, gifts, onboarding, profiles  |
| Technical Core (Sections 6-8)            | 53           | Infrastructure, database schema, API design           |
| Economy (Sections 9-10)                  | 24           | Coin system, creator payouts, financial compliance    |
| Automation & Compliance (Sections 11-12) | 24           | N8N workflows, legal requirements, moderation         |
| Launch (Sections 13-15)                  | 30           | Timeline, costs, success metrics                      |

**Critical Path (86 P0 Requirements):**

- User authentication with 18+ age verification (Karza KYC)
- Voice/video call system with coin-based billing
- Creator payout system with T+1 processing
- Virtual gift economy with dual revenue model (75-85% calls, 45% gifts)
- Multi-language support (English, Hindi, Tamil, Telugu, Bengali)

**Non-Functional Requirements:**

| NFR Category      | Target                         | Architectural Impact                                   |
| ----------------- | ------------------------------ | ------------------------------------------------------ |
| API Response Time | <200ms p95                     | Full observability stack, APM, aggressive auto-scaling |
| Concurrent Users  | 500-800 MVP → 150K Year 2     | Right-sized auto-scaling with warm pools               |
| Call Capacity     | 1,000 → 25,000 simultaneous   | Agora SDK with Mumbai/Bangalore edges                  |
| Call Quality      | <100ms jitter, <2% packet loss | Edge server optimization, bandwidth adaptation         |
| Data Recovery     | RTO 4h, RPO 1h                 | Hourly backups, warm standby, cross-region replication |
| Security          | Enterprise-grade               | WAF, JWT, MFA, E2E encryption, PCI DSS                 |

### Scale & Complexity Assessment

**Complexity Level: ENTERPRISE**

| Indicator                   | Assessment | Rationale                                                           |
| --------------------------- | ---------- | ------------------------------------------------------------------- |
| Real-time Features          | HIGH       | Voice/video calls, live audio rooms, real-time gifting              |
| Multi-tenancy               | MEDIUM     | Creator vs consumer roles, tiered monetization                      |
| Regulatory Compliance       | HIGH       | Age verification (18+), PCI DSS, TDS compliance, content moderation |
| Integration Complexity      | HIGH       | Agora, Razorpay, Firebase, Google/Azure AI, n8n                     |
| User Interaction Complexity | HIGH       | Real-time calls, matching, gifting, live rooms                      |
| Data Complexity             | HIGH       | 22+ tables, JSONB i18n, financial transactions, call history        |
| Geographic Distribution     | MEDIUM     | India-focused (Mumbai primary, Bangalore backup)                    |

**Primary Technical Domain:** Full-stack mobile application

- **Frontend:** Flutter 3.x (iOS + Android)
- **Backend:** FastAPI (Python 3.11+)
- **Real-time:** Agora SDK
- **Database:** PostgreSQL 15 with Redis 7
- **Cloud:** AWS (Mumbai region)

**Estimated Architectural Components:** 12-15 major components

1. Mobile App (Flutter)
2. API Gateway & Load Balancer
3. Authentication Service (Firebase Auth + JWT)
4. User Service (Profiles, Search)
5. Call Service (Agora Integration)
6. Wallet Service (Coins, Transactions)
7. Gift Service (Virtual Gifts)
8. Creator Service (Earnings, Payouts)
9. Moderation Service (AI Speech-to-Text)
10. Notification Service (FCM)
11. Analytics Service
12. Admin Service
13. N8N Automation Platform
14. Database Layer (PostgreSQL + Redis)
15. CDN & Media Storage (CloudFront + S3)

### Technical Constraints & Dependencies

**Technology Stack (Validated):**

- Flutter 3.x - Cross-platform mobile (confirmed over React Native)
- FastAPI Python 3.11+ - Backend API (validated for 150K concurrent with async drivers)
- PostgreSQL 15 - Primary database (managed RDS, deferred sharding to 1M+ users)
- Redis 7 - Caching and session management (Cluster with replication)
- Agora SDK - Real-time voice/video (Mumbai + Bangalore edges)

**External Service Dependencies:**

| Service      | Purpose                         | Criticality                 |
| ------------ | ------------------------------- | --------------------------- |
| Agora        | Voice/video calls               | CRITICAL - Core feature     |
| Razorpay     | Payments & payouts              | CRITICAL - Revenue          |
| Firebase     | Auth (OTP) & Push notifications | CRITICAL - User access      |
| Google/Azure | AI speech-to-text moderation    | HIGH - Safety compliance    |
| Karza        | Age verification (KYC)          | HIGH - Legal compliance     |
| AWS          | Infrastructure                  | CRITICAL - Platform hosting |

**Known Constraints:**

1. **Cost Sensitivity:** MVP budget ~₹9.47L for 5 months; right-sized infrastructure saves $3,480/year
2. **Timeline:** 19-24 week MVP (includes +2 weeks for AI provider evaluation)
3. **Performance:** <200ms p95 API latency non-negotiable across all growth phases
4. **Regulatory:** India-specific compliance (18+ verification, TDS on creator payouts)
5. **FastAPI Trade-off:** 30-50% higher infrastructure cost vs Node.js at scale (accepted for AI/ML benefits)

### Cross-Cutting Concerns Identified

**1. Real-Time Communication**

- Affects: Call Service, Live Rooms, Notifications
- Pattern: Agora SDK integration with server-side token generation
- Consideration: Edge server selection for South India latency optimization

**2. Financial Transactions & Compliance**

- Affects: Wallet, Gifts, Creator Payouts, Admin
- Pattern: PCI DSS compliance via Razorpay, automated reconciliation
- Consideration: T+1 payout automation, TDS deduction, audit logging

**3. Multi-Language Content**

- Affects: All user-facing services, AI moderation, notifications
- Pattern: JSONB storage for i18n content, language-specific keyword lists
- Consideration: 5 languages from MVP launch (English, Hindi, Tamil, Telugu, Bengali)

**4. Authentication & Authorization**

- Affects: All services
- Pattern: Firebase OTP + JWT with role-based access (user, creator, admin)
- Consideration: MFA for creators accessing earnings/withdrawals

**5. Content Moderation**

- Affects: Calls, Messages, Profiles, Live Rooms
- Pattern: Hybrid AI (third-party MVP → self-hosted Whisper Phase 2)
- Consideration: 30-day parallel evaluation of Google vs Azure

**6. Observability & Performance**

- Affects: All services
- Pattern: Full observability stack (Grafana, Prometheus, APM, distributed tracing)
- Consideration: <200ms SLA monitoring with automated alerting

**7. Security**

- Affects: All services, especially financial
- Pattern: Enterprise security (WAF, encryption at rest/transit, audit logs)
- Consideration: Regular penetration testing, vulnerability scanning

---

_This analysis establishes the foundation for architectural decisions. The enterprise-level complexity with real-time features, financial transactions, and regulatory requirements demands careful attention to scalability, security, and integration patterns._

---

## Starter Template Evaluation

### Primary Technology Domain

**Full-stack Mobile Application** based on project requirements analysis:

- Frontend: Flutter 3.x (mobile-first, cross-platform)
- Backend: FastAPI (Python 3.11+)
- Real-time: Agora SDK integration
- Database: PostgreSQL 15 + Redis 7 (AWS managed)

### Starter Options Considered

| Option                                | Description                               | Fit Assessment                                     |
| ------------------------------------- | ----------------------------------------- | -------------------------------------------------- |
| Flutter Official (`flutter create`) | Basic project structure                   | ✅ Good - Add architecture incrementally           |
| VGV CLI (`very_good create`)        | Production-ready with Bloc, testing, i18n | ⚠️ Consider - May impose patterns                |
| FastAPI Full-Stack Template           | Full-stack with frontend, Celery          | ❌ Overkill - Includes unnecessary frontend        |
| **fastapi_best_architecture**   | Enterprise backend with pseudo 3-tier     | ✅**Selected** - Matches LIVVLY requirements |
| Custom Monorepo                       | Tailored structure for Flutter + FastAPI  | ✅ Recommended - Wraps selected starters           |

### Selected Starters

#### Mobile: Flutter Official + Clean Architecture

**Initialization:**

```bash
flutter create --org com.livvly --project-name livvly_app mobile
```

**Rationale:**

- Start minimal, add architecture incrementally
- Allows flexibility in state management choice (Bloc/Riverpod)
- Clean architecture layers added manually for full control

#### Backend: fastapi_best_architecture

**Repository:** https://github.com/fastapi-practices/fastapi_best_architecture

**Rationale for Selection:**

1. **Enterprise-grade pseudo 3-tier architecture** - Matches LIVVLY's complexity level
2. **Full async stack** - SQLAlchemy 2.0 async with PostgreSQL 16+
3. **Production tooling included** - Celery for background tasks (T+1 payouts, notifications)
4. **Monitoring ready** - Grafana integration aligns with REQ-TA-008 (Full Observability)
5. **No frontend bloat** - Backend-only, exactly what LIVVLY needs
6. **Active community** - 2k+ GitHub stars, 314 forks, active Discord support
7. **Aligns with PRD** - Matches technology stack defined in REQ-TA-001

**Architecture Layers:**

| Layer   | Purpose             | LIVVLY Mapping                                       |
| ------- | ------------------- | ---------------------------------------------------- |
| API     | Presentation/Routes | `/api/v1/*` endpoints                              |
| Schema  | Pydantic DTOs       | Request/Response validation                          |
| Service | Business logic      | Call billing, payout calculations, coin transactions |
| CRUD    | Data access         | Database operations with async SQLAlchemy            |
| Model   | SQLAlchemy entities | 22+ tables from REQ-DB-*                             |

### Project Structure (Monorepo)

```
livvly/
├── apps/
│   ├── mobile/                          # Flutter 3.x app
│   │   ├── lib/
│   │   │   ├── core/                    # Core utilities, constants, theme
│   │   │   ├── data/                    # Data layer
│   │   │   │   ├── datasources/         # API clients, local storage
│   │   │   │   ├── models/              # Data models (JSON serialization)
│   │   │   │   └── repositories/        # Repository implementations
│   │   │   ├── domain/                  # Business logic
│   │   │   │   ├── entities/            # Business entities
│   │   │   │   ├── repositories/        # Repository interfaces
│   │   │   │   └── usecases/            # Use cases
│   │   │   ├── presentation/            # UI layer
│   │   │   │   ├── screens/             # Screen widgets
│   │   │   │   ├── widgets/             # Reusable widgets
│   │   │   │   └── blocs/               # State management
│   │   │   └── main.dart
│   │   ├── test/
│   │   ├── pubspec.yaml
│   │   └── README.md
│   │
│   └── api/                             # FastAPI backend (fastapi_best_architecture)
│       ├── app/
│       │   ├── api/                     # Presentation layer
│       │   │   ├── v1/                  # API version 1
│       │   │   │   ├── auth/            # Authentication endpoints
│       │   │   │   ├── users/           # User/profile endpoints
│       │   │   │   ├── calls/           # Call management
│       │   │   │   ├── wallet/          # Coin wallet operations
│       │   │   │   ├── gifts/           # Gift catalog and sending
│       │   │   │   ├── rooms/           # Live audio rooms
│       │   │   │   ├── creator/         # Creator dashboard, payouts
│       │   │   │   └── admin/           # Admin operations
│       │   │   └── deps.py              # Shared dependencies
│       │   ├── schemas/                 # Pydantic models (request/response)
│       │   ├── services/                # Business logic layer
│       │   │   ├── auth_service.py
│       │   │   ├── call_service.py
│       │   │   ├── wallet_service.py
│       │   │   ├── payout_service.py
│       │   │   └── moderation_service.py
│       │   ├── crud/                    # Data access layer
│       │   ├── models/                  # SQLAlchemy models (22+ tables)
│       │   ├── core/                    # Configuration, security
│       │   │   ├── config.py            # Settings management
│       │   │   ├── security.py          # JWT, encryption
│       │   │   └── deps.py              # Dependency injection
│       │   ├── utils/                   # Utilities
│       │   └── main.py                  # Application entry
│       ├── alembic/                     # Database migrations
│       ├── tests/
│       ├── celery_app/                  # Background tasks
│       │   ├── tasks/
│       │   │   ├── payout_tasks.py      # T+1 payout processing
│       │   │   ├── notification_tasks.py
│       │   │   └── moderation_tasks.py
│       │   └── celery.py
│       ├── Dockerfile
│       ├── pyproject.toml
│       └── README.md
│
├── infrastructure/                      # Terraform IaC (REQ-TA-018)
│   ├── environments/
│   │   ├── dev/
│   │   ├── staging/
│   │   └── production/
│   └── modules/
│       ├── compute/                     # EC2, Auto Scaling
│       ├── database/                    # RDS PostgreSQL
│       ├── cache/                       # ElastiCache Redis
│       ├── network/                     # VPC, Security Groups
│       └── monitoring/                  # CloudWatch, Grafana
│
├── docs/                                # Documentation
│   ├── architecture/
│   ├── api/
│   └── deployment/
│
├── .github/                             # GitHub Actions CI/CD (REQ-TA-016)
│   └── workflows/
│       ├── mobile-ci.yml                # Flutter build, test, deploy
│       ├── api-ci.yml                   # FastAPI lint, test, build
│       └── infrastructure.yml           # Terraform plan/apply
│
├── docker-compose.yml                   # Local development
├── docker-compose.prod.yml              # Production compose
├── Makefile                             # Common commands
└── README.md
```

### Initialization Commands

```bash
# 1. Create monorepo structure
mkdir -p livvly/{apps,infrastructure,docs,.github/workflows}
cd livvly

# 2. Clone fastapi_best_architecture as API base
git clone https://github.com/fastapi-practices/fastapi_best_architecture.git apps/api
cd apps/api
rm -rf .git  # Remove original git history

# 3. Initialize Flutter app
cd ../
flutter create --org com.livvly --project-name livvly_app mobile

# 4. Initialize git for monorepo
cd ../..
git init
git add .
git commit -m "Initial monorepo setup with Flutter + FastAPI"
```

### Architectural Decisions Provided by Selected Starters

**Backend (fastapi_best_architecture):**

| Category         | Decision                     | Notes                            |
| ---------------- | ---------------------------- | -------------------------------- |
| Language         | Python 3.10+ with type hints | Upgrade to 3.11+ for performance |
| Framework        | FastAPI with Pydantic v2     | Async-first, validation built-in |
| ORM              | SQLAlchemy 2.0 (async)       | asyncpg driver for PostgreSQL    |
| Migrations       | Alembic                      | Async migration support          |
| Task Queue       | Celery + Redis               | For T+1 payouts, notifications   |
| Code Quality     | Ruff linter                  | Fast, comprehensive linting      |
| Package Mgmt     | uv (or pip)                  | Modern Python packaging          |
| Containerization | Docker + docker-compose      | Local dev and production         |
| Monitoring       | Grafana integration          | Aligns with REQ-TA-008           |

**Mobile (Flutter):**

| Category      | Decision               | Notes                        |
| ------------- | ---------------------- | ---------------------------- |
| Framework     | Flutter 3.x            | Cross-platform iOS + Android |
| Architecture  | Clean Architecture     | Domain-driven, testable      |
| State Mgmt    | TBD (Bloc recommended) | Add based on team preference |
| API Client    | Dio + Retrofit         | Type-safe API calls          |
| Local Storage | Hive/SharedPreferences | For caching, offline support |
| Testing       | flutter_test + mockito | Unit + widget tests          |

### Customization Required for LIVVLY

After cloning the starters, the following customizations are needed:

**Backend Customizations:**

1. Add Agora token generation service
2. Add Razorpay integration (orders, webhooks)
3. Add Firebase Admin SDK (token verification)
4. Configure Redis for session management
5. Add AI moderation service integration (Google/Azure)
6. Implement 22+ database models from PRD Section 7
7. Configure Celery tasks for T+1 payouts

**Mobile Customizations:**

1. Add Agora Flutter SDK
2. Add Razorpay Flutter SDK
3. Add Firebase Auth + FCM
4. Implement multi-language support (5 languages)
5. Build UI screens per UX specifications

**Note:** Project initialization using this structure should be the first implementation story (Epic 0 / Story 0.1).

---

_Starter template evaluation complete. The combination of Flutter official starter with fastapi_best_architecture provides a solid foundation that aligns with LIVVLY's enterprise requirements while avoiding unnecessary complexity._

---

## Core Architectural Decisions

### Decision Summary

| #  | Category          | Decision              | Choice                         |
| -- | ----------------- | --------------------- | ------------------------------ |
| 1  | Data Modeling     | Access Pattern        | Repository Pattern             |
| 2  | Data Architecture | Caching TTLs          | As specified below             |
| 3  | Authentication    | JWT Configuration     | RS256, 1h access / 30d refresh |
| 4  | Authorization     | RBAC Structure        | user / creator / admin roles   |
| 5  | API Design        | Response Format       | Standardized JSON              |
| 6  | Real-time         | Communication Pattern | Agora + FCM + API polling      |
| 7  | Mobile State      | State Management      | Bloc/Cubit                     |
| 8  | Mobile Navigation | Router                | GoRouter                       |
| 9  | DevOps            | Environment Strategy  | Local / Dev / Staging / Prod   |
| 10 | Security          | Secret Management     | AWS Secrets Manager            |

### Data Architecture

**Database & ORM:**

| Component        | Choice             | Version        | Rationale                              |
| ---------------- | ------------------ | -------------- | -------------------------------------- |
| Primary Database | PostgreSQL         | 15.x (AWS RDS) | ACID compliance, JSON support, managed |
| ORM              | SQLAlchemy         | 2.0 (async)    | Full async with asyncpg driver         |
| Data Access      | Repository Pattern | -              | Clean separation via CRUD layer        |
| Migrations       | Alembic            | Latest         | Async support, version control         |
| Cache            | Redis Cluster      | 7.x            | HA with 3+3 nodes                      |
| Connection Pool  | PgBouncer          | Latest         | 1000+ connections                      |

**Caching Strategy (Confirmed):**

| Data Type              | TTL        | Invalidation      |
| ---------------------- | ---------- | ----------------- |
| User Profiles          | 1 hour     | On profile update |
| Creator Online Status  | 30 seconds | Mobile heartbeat  |
| Gift Catalog           | 24 hours   | Admin update      |
| Rate Limiting Counters | 1 minute   | Sliding window    |
| JWT Refresh Tokens     | 30 days    | On logout/revoke  |

### Authentication & Security

**JWT Configuration (Confirmed):**

| Parameter                      | Value                  |
| ------------------------------ | ---------------------- |
| Algorithm                      | RS256 (asymmetric)     |
| Access Token Expiry            | 1 hour                 |
| Refresh Token Expiry           | 30 days                |
| Token Storage (Mobile)         | Flutter Secure Storage |
| Refresh Token Storage (Server) | Redis                  |

**RBAC Structure (Confirmed):**

```
Roles:
├── user (default)
│   ├── profile:read/update (own)
│   ├── calls:initiate/receive
│   ├── gifts:send/receive
│   ├── wallet:read/topup
│   └── rooms:join/leave
│
├── creator (extends user)
│   ├── creator:dashboard:read
│   ├── creator:rates:update
│   ├── creator:earnings:read
│   ├── creator:payouts:request
│   └── rooms:create/manage
│
└── admin
    ├── users:manage/suspend
    ├── creators:verify/manage
    ├── moderation:review/action
    ├── payouts:approve/process
    └── system:configure
```

**Secret Management (Confirmed):**

- Provider: AWS Secrets Manager
- Rotation: 90-day automatic rotation
- Access: IAM role-based, audit logged

### API & Communication

**Response Format (Confirmed):**

```json
// Success
{
  "success": true,
  "data": { },
  "meta": { "timestamp": "ISO8601" }
}

// Error
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable",
    "details": { }
  }
}
```

**Real-time Pattern (Confirmed):**

| Feature            | Technology                |
| ------------------ | ------------------------- |
| Voice/Video Calls  | Agora RTC SDK             |
| Call Signaling     | Agora RTM + FCM           |
| Live Room Events   | Agora RTM                 |
| Creator Status     | Redis + API polling (30s) |
| Push Notifications | Firebase FCM              |

### Frontend Architecture (Flutter)

**State Management (Confirmed): Bloc/Cubit**

- Predictable state transitions
- Excellent testing support
- Mature ecosystem

**Navigation (Confirmed): GoRouter**

- Declarative routing
- Deep linking support
- Type-safe parameters

**Package Stack:**

```yaml
dependencies:
  # State & Navigation
  flutter_bloc: ^8.x
  go_router: ^13.x

  # Networking
  dio: ^5.x
  retrofit: ^4.x

  # Storage
  hive: ^2.x
  flutter_secure_storage: ^9.x

  # Firebase
  firebase_core: ^2.x
  firebase_auth: ^4.x
  firebase_messaging: ^14.x

  # Agora & Razorpay
  agora_rtc_engine: ^6.x
  razorpay_flutter: ^1.x

  # DI & Utils
  get_it: ^7.x
  injectable: ^2.x
```

### Infrastructure & Deployment

**Environment Strategy (Confirmed):**

| Environment | Purpose         | Infrastructure       |
| ----------- | --------------- | -------------------- |
| Local       | Development     | Docker Compose       |
| Dev         | Feature testing | Shared AWS (minimal) |
| Staging     | Pre-production  | AWS (prod-like)      |
| Production  | Live users      | AWS (full scale)     |

**CI/CD Pipeline:**

```
PR → Lint → Test → Build → [Dev]
Main → Lint → Test → Build → Staging → [Approval] → Prod
```

### Implementation Sequence

1. **Infrastructure** - Terraform, CI/CD, secrets
2. **Backend Foundation** - Models, auth, core APIs
3. **Mobile Foundation** - Bloc setup, navigation, DI
4. **Integrations** - Agora, Razorpay, Firebase
5. **Features** - User flows per epics

---

_Core architectural decisions confirmed and documented._

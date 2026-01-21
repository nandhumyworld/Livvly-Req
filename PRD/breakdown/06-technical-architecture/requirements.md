# Requirements: Technical Architecture

**Section**: 06-technical-architecture
**Source Lines**: 539-657
**Generated**: 2026-01-19
**Status**: Complete

**Note**: Backend technology changed from PRD specification (FastAPI) to Node.js + Supabase per stakeholder decision.

---

## System Architecture Requirements

### REQ-TA-001: Mobile Application Framework
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: The mobile application must be built using Flutter 3.x for cross-platform development targeting Android (primary) and iOS (secondary).

**Rationale**: Flutter enables fast development with single codebase while providing native performance critical for voice/video apps.

**Acceptance Criteria**:
- Flutter 3.x or later used for mobile development
- Android app supports Android 8.0+ (API 26+)
- iOS app supports iOS 13.0+
- Native performance for voice/video features
- Hot reload capability for development efficiency

**Dependencies**: None
**Related Design Decisions**: DD-TA-001

**Notes**: Prioritize Android optimization for Tier-2/3 market devices.

---

### REQ-TA-002: Backend-as-a-Service (Supabase)
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: The backend must use Supabase as the primary BaaS platform for database, authentication, storage, and realtime subscriptions.

**Rationale**: Supabase provides integrated PostgreSQL, auth, storage, and realtime - reducing development time and operational complexity vs custom FastAPI backend.

**Acceptance Criteria**:
- Supabase project provisioned with PostgreSQL database
- Supabase Auth configured for phone/OTP authentication
- Supabase Storage configured for media files
- Supabase Realtime enabled for live data updates
- Row Level Security (RLS) policies implemented for all tables
- Database migrations managed through Supabase CLI

**Dependencies**: None
**Related Design Decisions**: DD-TA-001

**Notes**: Custom Node.js Edge Functions for complex business logic not supported by Supabase directly.

---

### REQ-TA-003: Node.js Edge Functions
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Complex business logic (revenue calculations, payout processing, call management) must be implemented as Node.js Edge Functions on Supabase.

**Rationale**: Edge Functions handle business logic that requires server-side execution, while leveraging Supabase's infrastructure.

**Acceptance Criteria**:
- Edge Functions deployed via Supabase CLI
- TypeScript used for type safety
- Functions handle: revenue split calculations, payout initiation, call session management
- Functions are idempotent where possible
- Error handling with proper HTTP status codes

**Dependencies**: REQ-TA-002
**Related Design Decisions**: DD-TA-001

**Notes**: Keep Edge Functions stateless; use database for state.

---

### REQ-TA-004: PostgreSQL Database
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: All persistent data must be stored in PostgreSQL database (via Supabase) with proper schema design and indexing.

**Rationale**: PostgreSQL provides ACID compliance, JSON support, and scales to target user volumes.

**Acceptance Criteria**:
- Schema supports all entities (users, calls, transactions, rooms, etc.)
- Indexes on frequently queried columns
- Foreign key constraints for data integrity
- Soft delete pattern for audit trail
- Connection pooling for performance

**Dependencies**: REQ-TA-002
**Related Design Decisions**: DD-TA-002

**Notes**: Detailed schema in Section 07 (Database Schema).

---

### REQ-TA-005: Redis Cache Layer
**Priority**: P1 (High)
**Type**: Technical

**Description**: Redis must be used for caching frequently accessed data, session management, and real-time leaderboards.

**Rationale**: Redis reduces database load and provides sub-millisecond response for hot data.

**Acceptance Criteria**:
- Redis instance provisioned (Upstash or self-managed)
- Cache keys with appropriate TTLs
- Session data cached (user context, active calls)
- Leaderboard data in Redis sorted sets
- Cache invalidation on data updates

**Dependencies**: REQ-TA-002
**Related Design Decisions**: None

**Notes**: Use Upstash Redis for serverless compatibility with Supabase.

---

## External Services Requirements

### REQ-TA-006: Agora SDK Integration
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Voice and video communication must use Agora SDK for 1-to-1 calls and live rooms.

**Rationale**: Agora provides production-grade real-time communication with global infrastructure and India servers.

**Acceptance Criteria**:
- Agora Voice SDK integrated for audio calls
- Agora Video SDK integrated for video calls
- Agora RTM SDK for signaling
- Token generation on server side (Edge Functions)
- Quality monitoring and fallback handling
- Usage tracked for billing

**Dependencies**: REQ-FS-017, REQ-FS-018
**Related Design Decisions**: DD-FS-005

**Notes**: Budget approximately ₹0.5-1 per call minute for Agora costs.

---

### REQ-TA-007: Razorpay Payment Integration
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Payment processing must use Razorpay for UPI, cards, wallets, and bank transfers.

**Rationale**: Razorpay has best India coverage and supports all major payment methods.

**Acceptance Criteria**:
- Razorpay SDK integrated in Flutter app
- Server-side order creation and verification
- Webhook handling for payment events
- Refund API integration
- Payment link generation for web purchases
- PCI DSS compliance maintained

**Dependencies**: REQ-FS-022
**Related Design Decisions**: DD-FS-006

**Notes**: Also integrate Google Play Billing for in-app purchase compliance.

---

### REQ-TA-008: Firebase Services Integration
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Firebase must be used for OTP authentication, push notifications, and analytics.

**Rationale**: Firebase Auth provides reliable OTP delivery; FCM ensures push notification delivery.

**Acceptance Criteria**:
- Firebase Auth configured with phone provider
- OTP auto-retrieval on Android
- Firebase Cloud Messaging for push notifications
- Firebase Analytics for user behavior tracking
- Crashlytics for crash reporting

**Dependencies**: REQ-FS-003
**Related Design Decisions**: DD-FS-002

**Notes**: Firebase Auth works alongside Supabase - use Firebase for OTP, sync user to Supabase.

---

### REQ-TA-009: Cloud Storage (S3/GCS)
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Media files (profile photos, chat media, gift assets) must be stored in cloud storage with CDN delivery.

**Rationale**: Cloud storage scales infinitely and CDN ensures fast delivery across India.

**Acceptance Criteria**:
- Supabase Storage used for user-uploaded content
- CDN configured for asset delivery
- Image optimization/resizing
- Secure URLs for private content
- Storage quotas per user if needed

**Dependencies**: REQ-TA-002
**Related Design Decisions**: None

**Notes**: Supabase Storage is built on S3-compatible infrastructure.

---

## API Architecture Requirements

### REQ-TA-010: RESTful API Design
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: APIs must follow RESTful conventions with versioning, consistent naming, and proper HTTP methods.

**Rationale**: RESTful design ensures predictable, maintainable API structure.

**Acceptance Criteria**:
- API versioning: /api/v1/...
- Resource-based URLs (nouns, not verbs)
- Proper HTTP methods (GET, POST, PUT, DELETE)
- Consistent response format (JSON)
- HTTP status codes used correctly
- HATEOAS links where appropriate

**Dependencies**: REQ-TA-002
**Related Design Decisions**: DD-TA-003

**Notes**: Supabase auto-generates REST APIs for tables; Edge Functions for custom endpoints.

---

### REQ-TA-011: API Authentication & Authorization
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: All API endpoints must require authentication (except public endpoints) with proper authorization checks.

**Rationale**: Security is critical for a platform handling payments and personal data.

**Acceptance Criteria**:
- JWT-based authentication (Supabase Auth tokens)
- Token refresh mechanism
- Role-based access control (user, creator, admin)
- Row Level Security for database queries
- Rate limiting on API endpoints
- API key authentication for server-to-server calls

**Dependencies**: REQ-TA-002, REQ-TA-008
**Related Design Decisions**: None

**Notes**: Combine Firebase Auth token with Supabase session.

---

### REQ-TA-012: API Rate Limiting
**Priority**: P1 (High)
**Type**: Non-Functional

**Description**: APIs must implement rate limiting to prevent abuse and ensure fair usage.

**Rationale**: Rate limiting protects against DDoS, scraping, and ensures quality of service.

**Acceptance Criteria**:
- Rate limits per user/IP
- Different limits for different endpoint types
- 429 response with retry-after header
- Rate limit headers in responses
- Exemptions for internal services

**Dependencies**: REQ-TA-010
**Related Design Decisions**: None

**Notes**: Consider using Supabase's built-in rate limiting or API gateway.

---

## Infrastructure Requirements

### REQ-TA-013: Monitoring and Observability
**Priority**: P1 (High)
**Type**: Non-Functional

**Description**: System must have comprehensive monitoring for performance, errors, and business metrics.

**Rationale**: Monitoring enables proactive issue detection and performance optimization.

**Acceptance Criteria**:
- Application metrics (response times, error rates)
- Infrastructure metrics (CPU, memory, database)
- Business metrics (calls, transactions, GMV)
- Alerting for critical thresholds
- Log aggregation and search
- Dashboard for real-time visibility

**Dependencies**: REQ-TA-002
**Related Design Decisions**: DD-TA-004

**Notes**: Use Supabase's built-in monitoring + external tools as needed.

---

### REQ-TA-014: CI/CD Pipeline
**Priority**: P1 (High)
**Type**: Non-Functional

**Description**: Automated CI/CD pipeline must be implemented for testing and deployment.

**Rationale**: CI/CD ensures consistent, reliable deployments and catches issues early.

**Acceptance Criteria**:
- GitHub Actions for CI/CD
- Automated tests run on PR
- Staging environment for testing
- Production deployment with approval
- Rollback capability
- Database migration automation

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Supabase CLI supports migration management and Edge Function deployment.

---

### REQ-TA-015: Security Requirements
**Priority**: P0 (Critical)
**Type**: Non-Functional

**Description**: System must implement security best practices for data protection and access control.

**Rationale**: Security is essential for user trust and regulatory compliance.

**Acceptance Criteria**:
- HTTPS/TLS for all communications
- Data encryption at rest (database, storage)
- Secrets management (environment variables, not code)
- Input validation and sanitization
- SQL injection prevention (via Supabase RLS)
- XSS protection in any web components

**Dependencies**: REQ-TA-002
**Related Design Decisions**: None

**Notes**: Supabase handles many security concerns; focus on application-level security.

---

### REQ-TA-016: Scalability for 50K Users
**Priority**: P0 (Critical)
**Type**: Non-Functional

**Description**: System architecture must support 50,000 monthly active users for Phase 1 target.

**Rationale**: Must meet business goal of 50K users in 6 months.

**Acceptance Criteria**:
- Load testing validates 50K MAU capacity
- Database can handle concurrent query load
- Supabase plan supports required connections
- Agora capacity provisioned
- CDN handles media traffic
- No single points of failure

**Dependencies**: All technical requirements
**Related Design Decisions**: DD-TA-001

**Notes**: Supabase Pro plan should suffice for initial scale; plan upgrade path.

---

## Automation Requirements (Deferred)

### REQ-TA-017: Critical Automation - Direct Implementation
**Priority**: P1 (High)
**Type**: Technical

**Description**: Critical automated workflows (payout processing, notifications, moderation alerts) must be implemented directly in Edge Functions for MVP, without external automation tool dependency.

**Rationale**: n8n deferred to Phase 2; critical automations cannot wait.

**Acceptance Criteria**:
- Payout processing runs on schedule (daily)
- Notification triggers implemented in database triggers/Edge Functions
- Moderation alerts trigger on flagged content
- Scheduled jobs via Supabase pg_cron or external scheduler

**Dependencies**: REQ-TA-003
**Related Design Decisions**: DD-TA-005

**Notes**: n8n integration planned for Phase 2 for complex, visual workflow management.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-TA-001 | Flutter 3.x Mobile Framework | P0 | Technical |
| REQ-TA-002 | Supabase BaaS Platform | P0 | Technical |
| REQ-TA-003 | Node.js Edge Functions | P0 | Technical |
| REQ-TA-004 | PostgreSQL Database | P0 | Technical |
| REQ-TA-005 | Redis Cache Layer | P1 | Technical |
| REQ-TA-006 | Agora SDK Integration | P0 | Technical |
| REQ-TA-007 | Razorpay Payment Integration | P0 | Technical |
| REQ-TA-008 | Firebase Services Integration | P0 | Technical |
| REQ-TA-009 | Cloud Storage (S3/GCS) | P0 | Technical |
| REQ-TA-010 | RESTful API Design | P0 | Technical |
| REQ-TA-011 | API Authentication & Authorization | P0 | Technical |
| REQ-TA-012 | API Rate Limiting | P1 | Non-Functional |
| REQ-TA-013 | Monitoring and Observability | P1 | Non-Functional |
| REQ-TA-014 | CI/CD Pipeline | P1 | Non-Functional |
| REQ-TA-015 | Security Requirements | P0 | Non-Functional |
| REQ-TA-016 | Scalability for 50K Users | P0 | Non-Functional |
| REQ-TA-017 | Critical Automation - Direct Implementation | P1 | Technical |

**Total Requirements**: 17
**P0 (Critical)**: 12
**P1 (High)**: 5

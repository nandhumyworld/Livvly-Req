# Section 6: Technical Architecture - Requirements

**Section:** Technical Architecture
**Total Requirements:** 18
**Priority Breakdown:** P0: 10 | P1: 6 | P2: 2
**Last Updated:** 2026-02-02 (Scalability Review)
**Major Changes:** Infrastructure strategy, database sharding timeline, concurrent user targets

---

## REQ-TA-001: Technology Stack Definition

**Priority:** P0
**Type:** Technical Decision
**Status:** Approved

**Description:**
Define and implement the core technology stack for LIVVLY mobile and backend infrastructure.

**Specifications:**
- **Mobile**: Flutter 3.x for cross-platform iOS and Android development
- **Backend**: FastAPI (Python 3.11+) for API services
- **Database**: PostgreSQL 15 for primary data store
- **Cache**: Redis 7 for session management and real-time data
- **Voice/Video**: Agora SDK for audio and video calls
- **Payments**: Razorpay for payment processing
- **Auth**: Firebase Auth for OTP and social login
- **Push Notifications**: Firebase FCM
- **File Storage**: AWS S3 or Google Cloud Storage
- **Automation**: n8n (self-hosted) for workflow automation
- **Monitoring**: Grafana + Prometheus for metrics
- **CI/CD**: GitHub Actions for automated deployments

**Rationale:**
- Flutter enables single codebase for iOS + Android (faster development)
- FastAPI with Python provides excellent AI/ML integration for moderation and matching algorithms
- PostgreSQL offers ACID compliance and JSON support for flexible data models
- Proven third-party services (Agora, Razorpay, Firebase) reduce development time

**Performance and Cost Considerations:**
- FastAPI with async/await handles 150K concurrent users effectively (validated 2026-02-02)
- Infrastructure cost: 30-50% higher than Node.js alternatives at scale (trade-off for Python AI/ML benefits)
- Critical: Must use async drivers (asyncpg for PostgreSQL, aioredis for Redis) for competitive performance
- Python GIL not a blocker for I/O-bound voice-dating application
- Cost premium acceptable given AI/ML integration advantages and team expertise

**Acceptance Criteria:**
- [ ] Flutter 3.x development environment set up for iOS and Android
- [ ] FastAPI project initialized with Python 3.11+ virtual environment
- [ ] PostgreSQL 15 database instance provisioned
- [ ] Redis 7 instance configured for cache
- [ ] All SDK integrations (Agora, Razorpay, Firebase) initialized
- [ ] Technology stack documented in technical wiki

**Dependencies:**
- None (foundational requirement)

**Design Decisions:**
- **DD-TA-001**: Chose Flutter over React Native for faster cross-platform development and better performance
- **DD-TA-002**: Chose FastAPI over Node.js for superior Python AI/ML library ecosystem

**Notes:**
- This resolves conflict between PRD Section 6 (Flutter + FastAPI) and Section 5 notes (React Native + Node.js)
- Flutter + FastAPI is the confirmed final decision
- Scalability validated 2026-02-02: FastAPI handles 150K concurrent with proper optimization
- Cost premium vs Node.js: $50-150K/year at scale (acceptable for AI/ML benefits)
- Alternative considered: Node.js with Fastify (lower cost but weaker AI/ML ecosystem)

**Testing Requirements:**
- Verify Flutter app builds successfully for iOS and Android
- Verify FastAPI server starts and responds to health check endpoint
- Verify async drivers: asyncpg for PostgreSQL, aioredis for Redis
- Load testing: Validate FastAPI handles 800 concurrent users (MVP target)
- Performance benchmarking: API latency <200ms p95 with async drivers
- Verify database connections from backend
- Verify Redis cache read/write operations

---

## REQ-TA-002: Performance and Scalability Targets

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Define and achieve premium performance targets for concurrent users, call capacity, and API response times across growth phases.

**Specifications:**

**Concurrent Users (Phased Growth):**
- MVP Launch (10K MAU): 5-8% concurrent = 500-800 users
- Month 6 (50K MAU): 8-10% concurrent = 4,000-5,000 users
- Month 12 (200K MAU): 10-12% concurrent = 20,000-24,000 users
- Year 2 (1M MAU): 12-15% concurrent = 120,000-150,000 users

**Rationale for Phased Approach:** Industry benchmarks show social/dating apps typically achieve 5-15% of DAU as concurrent users during peak hours. Starting with conservative 5-8% for MVP reflects realistic engagement patterns for new platforms, scaling to 15% as product-market fit is proven and user engagement increases.

**Call Capacity (Simultaneous Voice/Video Calls):**
- MVP Launch: 1,000 simultaneous calls
- Month 6: 5,000 simultaneous calls
- Year 2: 25,000 simultaneous calls

**API Response Time SLAs:**
- User-facing endpoints (profile, wallet, calls): <200ms at p95
- Creator dashboard and earnings: <200ms at p95
- Live room join/leave: <1 second at p95
- Background operations: <500ms at p95

**Rationale:**
Premium performance targets ensure excellent user experience in Tier-2/3 cities with variable network conditions. Sub-200ms latency critical for real-time interactions (matching, calls, gifts). Phased concurrent user targets prevent over-engineering at MVP stage while maintaining upper-bound capacity planning for 150K concurrent at 1M MAU.

**Acceptance Criteria:**
- [ ] Load testing demonstrates system handles 15% concurrent users without degradation
- [ ] Stress testing validates simultaneous call capacity targets
- [ ] APM monitoring shows p95 API latency <200ms for all user-facing endpoints
- [ ] Agora call quality metrics show <100ms jitter, <2% packet loss
- [ ] Auto-scaling policies maintain SLAs during traffic spikes

**Dependencies:**
- REQ-TA-003 (Premium Infrastructure)
- REQ-TA-008 (Full Observability Stack)

**Design Decisions:**
- **DD-TA-003**: Phased concurrent user ratio (5-8% MVP → 15% @ 1M MAU) based on industry data showing realistic engagement growth patterns
- **DD-TA-004**: <200ms p95 latency chosen as premium target (acceptable user experience)

**Notes:**
- Premium <200ms SLA maintained across all phases
- Phased concurrent targets reduce MVP infrastructure costs while maintaining growth capacity
- Revised from original flat 15% assumption based on 2026-02-02 scalability review
- Must validate with load testing before MVP launch

**Testing Requirements:**
- Load test with 800 concurrent users (MVP target)
- Stress test with 1,000 simultaneous calls
- Measure API latency under load (p50, p95, p99)
- Monitor Agora call quality metrics
- Validate auto-scaling triggers at target concurrency levels

---

## REQ-TA-003: Right-Sized Auto-Scaling Infrastructure

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement right-sized auto-scaling infrastructure from MVP launch with warm pools and aggressive scaling policies to optimize cost while maintaining <200ms SLA.

**Specifications:**

**API Server Layer:**
- MVP Launch: 2-4 FastAPI server instances (right-sized baseline)
- Auto-scaling: Aggressive policies to maintain <200ms SLA
  - Scale up at 60% CPU utilization
  - Scale down at 30% CPU utilization
  - Min instances: 2
  - Max instances: 12+ (handles traffic spikes)
- Instance Type: AWS EC2 t3.medium (2 vCPU, 4GB RAM) for baseline
- Warm Pools: 2-3 pre-initialized instances (30-second scale-up)
- Load Balancer: Application Load Balancer with health checks
- Monitoring: 1-minute CloudWatch metrics (vs default 5-minute)

**Scaling Timeline:**
- MVP (10K MAU): 2-4 instances baseline, scale to 6-8 peak
- Month 6 (50K MAU): 4-6 instances baseline, scale to 10-12 peak
- Month 12 (200K MAU): 6-10 instances baseline, scale to 20+ peak
- Year 2 (1M MAU): 15-25 instances baseline, scale to 50+ peak

**Database Architecture:**
- Primary: PostgreSQL 15 (AWS RDS managed service)
- Logical partitioning for large tables (messages, call_history by created_at)
- Connection Pooling: PgBouncer with 1000+ connections
- Instance Type: AWS RDS db.t3.medium → db.r5.large (scale as needed)
- Read Replicas: 2-3 for read-heavy operations

**Redis Architecture:**
- Configuration: Redis Cluster with replication (high availability)
- Nodes: 3 primary + 3 replica nodes minimum
- Persistence: RDB snapshots every 5 minutes
- Instance Type: AWS ElastiCache cache.r6g.large or equivalent

**Cost Profile:**
- Baseline (2 instances): ~$60/month
- Average (4 instances): ~$120/month
- Peak (8 instances): ~$240/month
- Includes: Load balancer ($25), monitoring ($15), warm pools ($30-45)
- **Total MVP Cost: $150-180/month** (vs $450-500 with premium over-provisioning)
- **Annual Savings: $3,480 vs premium approach (64% cost reduction)**

**Rationale:**
Right-sized auto-scaling optimizes cost while maintaining performance. Warm pools eliminate scale-up lag (30 seconds vs 2-5 minutes cold start). Industry best practice for MVP-stage applications. Managed PostgreSQL with read replicas sufficient for MVP-200K users without physical sharding complexity.

**Acceptance Criteria:**
- [ ] Auto-scaling group configured with 2 min, 12 max instances
- [ ] Warm pool with 2-3 standby instances operational
- [ ] Scaling policies: 60% CPU scale-up, 30% CPU scale-down
- [ ] 1-minute CloudWatch metrics enabled
- [ ] Load testing validates scale-up within 30 seconds
- [ ] Cost monitoring dashboard tracks spend (target <$200/month MVP)
- [ ] PostgreSQL RDS with logical partitioning configured
- [ ] 2-3 read replicas operational and routing tested
- [ ] Redis Cluster operational with replication
- [ ] Infrastructure-as-Code (Terraform/CloudFormation) deployed

**Dependencies:**
- REQ-TA-002 (Performance Targets)
- REQ-TA-009 (Disaster Recovery)

**Design Decisions:**
- **DD-TA-005**: Right-sized auto-scaling (2-4 baseline) with warm pools chosen for 64% cost savings vs over-provisioning while maintaining <200ms SLA
- **DD-TA-006**: Managed PostgreSQL with read replicas chosen over premature physical sharding (defer to 1M+ users)
- **DD-TA-007**: Redis Cluster chosen over single instance for high availability

**Notes:**
- Right-sized approach saves $3,480/year vs premium over-provisioning
- Warm pools critical for eliminating 2-5 minute cold-start lag
- Based on 2026-02-02 scalability review research findings
- Industry best practice: Start lean, scale aggressively
- Cost validated in Section 14 (Cost Estimation) - requires update

**Testing Requirements:**
- Auto-scaling testing: Trigger scale-up at 60% CPU, verify 30-second warm pool activation
- Load testing: Validate 800 concurrent users (MVP target) performance
- Cost monitoring: Track actual spend vs $200/month budget
- Failover testing for Redis Cluster
- Read replica routing and lag monitoring
- Infrastructure disaster recovery drill

---

## REQ-TA-004: AI Moderation Infrastructure (Hybrid Approach)

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement hybrid AI speech-to-text moderation infrastructure with third-party services for MVP and migration path to self-hosted solution in Phase 2.

**Specifications:**

**MVP Phase (Months 1-6):**
- **Approach**: Parallel evaluation of Google Cloud Speech-to-Text AND Azure Cognitive Services
- **Evaluation Period**: 30 days during development
- **Languages**: Tamil, Telugu, Kannada, Hindi, English
- **Evaluation Criteria**:
  - Accuracy (primary metric)
  - Latency (target <2 seconds for 1-minute audio)
  - Cost (₹2-4L/month budget)
  - False positive rate (<5% acceptable)
- **Decision**: Choose best performer after data-driven comparison
- **Timeline Impact**: +2 weeks to MVP (19-24 weeks total)

**Phase 2 (Month 7+):**
- **Migration Target**: Self-hosted Whisper (OpenAI open-source model)
- **Infrastructure**: GPU-enabled instances for inference (AWS EC2 g5.xlarge or equivalent)
- **Benefits**: Full data privacy, lower long-term costs
- **Migration Effort**: 4-6 weeks estimated

**Integration Architecture:**
- Real-time transcription during voice calls
- Profanity detection with language-specific keyword lists
- Automated flagging of violations for manual review
- Creator warning system before suspension

**Rationale:**
Hybrid approach balances speed-to-market (proven third-party services) with long-term goals (data privacy, cost control). Parallel evaluation ensures highest accuracy for Safety-First core value.

**Acceptance Criteria:**
- [ ] Google Cloud Speech-to-Text API integrated and tested
- [ ] Azure Cognitive Services API integrated and tested
- [ ] 30-day evaluation completed with accuracy metrics for all 5 languages
- [ ] Best provider selected based on data
- [ ] Real-time transcription pipeline operational
- [ ] Profanity detection working for all 5 languages
- [ ] Migration plan to Whisper documented

**Dependencies:**
- REQ-FS-018 (AI Moderation from Section 5)
- REQ-TA-007 (Enterprise Security - E2E encryption for audio)

**Design Decisions:**
- **DD-TA-008**: Parallel evaluation chosen over single provider to ensure best accuracy
- **DD-TA-009**: Self-hosted migration planned for data privacy and cost control

**Notes:**
- +2 weeks added to MVP timeline for proper evaluation
- Duplicate AI costs for 30 days (~₹4-6L one-time)
- Critical for Safety-First core value and regulatory compliance

**Testing Requirements:**
- Accuracy testing with native speakers for all 5 languages
- Latency testing under various network conditions
- False positive rate measurement
- Load testing (100+ concurrent transcriptions)

---

## REQ-TA-005: Geographic Deployment Strategy

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Deploy infrastructure in AWS Mumbai region with CloudFront CDN and Agora edge servers optimized for South India target markets.

**Specifications:**

**Primary Region:**
- **AWS Region**: ap-south-1 (Mumbai)
- **Availability Zones**: Multi-AZ deployment across 3 AZs
- **Rationale**: Most AWS services available, good India coverage, proven reliability

**Content Delivery Network (CDN):**
- **CDN Provider**: AWS CloudFront
- **Cached Content**: Profile photos, voice messages, static assets
- **Edge Locations**: India (Mumbai, Chennai, Delhi, Bangalore)
- **Cache TTL**: 7 days for profile photos, 1 day for voice messages
- **Invalidation**: On-demand for user-initiated changes

**Agora Edge Servers:**
- **Primary Edge**: Mumbai
- **Secondary Edge**: Bangalore
- **Latency Target**: <50ms for users in South India
- **Fallback**: Agora's automatic edge selection if primary unavailable

**Rationale:**
Mumbai region provides best balance of service availability and latency for South India markets. CloudFront reduces bandwidth costs and improves performance for media delivery.

**Acceptance Criteria:**
- [ ] All infrastructure deployed in AWS ap-south-1 (Mumbai)
- [ ] Multi-AZ deployment operational across 3 availability zones
- [ ] CloudFront distribution configured for profile photos and voice messages
- [ ] Agora SDK configured to prefer Mumbai and Bangalore edges
- [ ] Latency testing validates <50ms to Agora from Tier-2/3 cities
- [ ] CDN cache hit rate >80% after 7 days

**Dependencies:**
- REQ-TA-001 (Technology Stack)
- REQ-TA-003 (Premium Infrastructure)

**Design Decisions:**
- **DD-TA-010**: Mumbai chosen over Hyderabad for broader AWS service availability
- **DD-TA-011**: CloudFront chosen as AWS-native CDN for simpler integration

**Notes:**
- Standard India setup, proven and reliable
- Multi-AZ provides high availability (99.95% SLA)
- CloudFront costs lower than origin bandwidth charges

**Testing Requirements:**
- Latency testing from 10+ Tier-2/3 cities
- CDN cache hit rate monitoring
- Agora call quality testing from target markets
- Failover testing between availability zones

---

## REQ-TA-006: API Architecture and Structure

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement RESTful API architecture with versioning, authentication, and comprehensive endpoint coverage for all platform features.

**Specifications:**

**API Structure:**
```
/api/v1
├── /auth (OTP, login, refresh tokens)
├── /users (profiles, search, photos)
├── /wallet (balance, recharge, transactions)
├── /calls (start, end, history, rates)
├── /gifts (catalog, send, received)
├── /rooms (live rooms, join, leave)
├── /creator (dashboard, earnings, withdrawals)
└── /admin (user management, reports, actions)
```

**API Standards:**
- **Protocol**: HTTPS only (TLS 1.3)
- **Format**: JSON request/response
- **Authentication**: JWT tokens with refresh tokens (REQ-TA-007)
- **Versioning**: URL-based versioning (/api/v1, /api/v2)
- **Rate Limiting**: 100 requests/minute per user, 1000/minute per IP
- **Pagination**: Cursor-based for list endpoints (limit=50 default)
- **Error Format**: Standardized JSON error responses with error codes

**Endpoint Categories:**
- Public endpoints (no auth): /auth/send-otp, /auth/verify-otp
- User endpoints (JWT required): /users/*, /wallet/*, /calls/*, /gifts/*, /rooms/*
- Creator endpoints (JWT + creator role): /creator/*
- Admin endpoints (JWT + admin role): /admin/*

**Rationale:**
RESTful API with versioning enables mobile app evolution without breaking changes. Comprehensive endpoint coverage supports all features defined in Section 5.

**Acceptance Criteria:**
- [ ] All endpoints from PRD Section 6.3 implemented
- [ ] OpenAPI (Swagger) documentation generated
- [ ] Rate limiting operational with Redis-backed counters
- [ ] JWT authentication working for all protected endpoints
- [ ] Pagination working on all list endpoints
- [ ] API versioning tested with /v1 namespace
- [ ] Error responses standardized across all endpoints

**Dependencies:**
- REQ-TA-001 (FastAPI backend)
- REQ-TA-007 (JWT authentication)
- REQ-TA-003 (Redis for rate limiting)

**Design Decisions:**
- **DD-TA-012**: URL-based versioning chosen over header-based for simplicity
- **DD-TA-013**: Cursor-based pagination chosen over offset for better performance at scale

**Notes:**
- Full API specifications defined in Section 8 (API Specifications)
- Rate limits prevent abuse and DDoS attacks
- OpenAPI documentation enables automated client generation

**Testing Requirements:**
- Unit tests for all endpoints (>90% coverage)
- Integration tests for authentication flows
- Load tests for rate limiting
- API documentation accuracy validation

---

## REQ-TA-007: Enterprise Security Architecture

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement regulatory-grade security infrastructure appropriate for financial platform handling ₹2-3 Cr GMV with creator payouts and user financial data.

**Specifications:**

**Network Security:**
- WAF (Web Application Firewall) for DDoS protection (AWS WAF or Cloudflare)
- HTTPS/TLS 1.3 for all API communication
- Security groups restricting database access to application tier only
- VPC (Virtual Private Cloud) with private subnets for database and Redis

**Authentication and Authorization:**
- JWT tokens with 1-hour expiration
- Refresh tokens with 30-day expiration, stored in Redis
- Multi-factor authentication (MFA) for creators accessing earnings/withdrawals
- Role-based access control (RBAC): user, creator, admin roles

**Data Encryption:**
- Database encryption at rest (AWS RDS encryption)
- End-to-end encryption for voice messages (AES-256)
- Field-level encryption for sensitive data (bank account numbers, PAN)
- TLS 1.3 for data in transit

**Secrets Management:**
- AWS Secrets Manager for API keys, database credentials
- Automatic secret rotation every 90 days
- No secrets in code or environment variables

**Fraud Prevention:**
- IP-based rate limiting (100 requests/min per user, 1000/min per IP)
- Geofencing (India-only access, block suspicious countries)
- Transaction velocity checks (max 5 coin purchases per hour)
- Device fingerprinting for fraud detection

**Compliance:**
- PCI DSS compliance for payment data (Razorpay handles card data)
- Security audit logging (all sensitive operations logged)
- Automated vulnerability scanning (weekly)
- Regular penetration testing (quarterly)

**Rationale:**
Enterprise security mandatory for financial platform. Regulatory-grade controls protect user funds, creator earnings, and platform reputation.

**Acceptance Criteria:**
- [ ] AWS WAF operational with rate limiting and DDoS protection
- [ ] JWT authentication with refresh tokens implemented
- [ ] MFA working for creator earnings access
- [ ] Database encryption at rest enabled
- [ ] End-to-end encryption for voice messages operational
- [ ] AWS Secrets Manager integrated for all credentials
- [ ] Security audit logging operational
- [ ] Vulnerability scanning automated (weekly)
- [ ] PCI DSS compliance validated

**Dependencies:**
- REQ-TA-001 (Technology Stack)
- REQ-TA-003 (Redis for token storage)
- REQ-TA-006 (API Architecture)

**Design Decisions:**
- **DD-TA-014**: MFA required only for creators (balance security with UX)
- **DD-TA-015**: End-to-end encryption for voice messages (privacy and compliance)
- **DD-TA-016**: AWS Secrets Manager chosen over manual secret management

**Notes:**
- Enterprise security adds infrastructure costs (WAF, Secrets Manager)
- PCI DSS compliance simplified by using Razorpay (payment processor handles card data)
- Security audit logs critical for regulatory compliance

**Testing Requirements:**
- Penetration testing before MVP launch
- JWT token expiration and refresh flow testing
- MFA flow testing for creators
- Encryption verification for voice messages
- Security audit log verification

---

## REQ-TA-008: Full Observability Stack

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement comprehensive monitoring and observability infrastructure to maintain <200ms SLA and diagnose issues quickly across infrastructure, application, and business layers.

**Specifications:**

**Infrastructure Monitoring:**
- **Tool**: Grafana + Prometheus
- **Metrics**: CPU, memory, disk, network usage for all servers
- **Targets**: API servers, database, Redis, load balancers
- **Retention**: 30 days
- **Dashboards**: Infrastructure overview, auto-scaling activity, resource utilization

**Application Performance Monitoring (APM):**
- **Tool**: AWS X-Ray or Datadog APM
- **Tracing**: Distributed tracing across API endpoints
- **Database**: Query performance monitoring (slow queries, deadlocks)
- **Redis**: Cache hit/miss rates, operation latency
- **API Metrics**: Endpoint latency breakdown (p50, p95, p99), error rates by endpoint
- **Retention**: 7 days full traces, 90 days aggregated metrics

**Real-Time User Experience Monitoring:**
- **Mobile App**: Crash reporting (Firebase Crashlytics)
- **API Latency**: User-perceived response times
- **Call Quality**: Agora SDK metrics (jitter, packet loss, bitrate, call drop rate)

**Business Metrics Dashboard:**
- **Metrics**: GMV, active calls, coin purchases, creator earnings
- **Real-time**: Live counters for active users, ongoing calls, live rooms
- **Alerts**: Low creator availability, high user churn signals

**Alerting:**
- **Channels**: PagerDuty (critical), Slack (warnings), email (info)
- **Thresholds**:
  - API latency p95 >200ms (critical)
  - Error rate >1% (critical)
  - Database CPU >80% (warning)
  - Redis memory >80% (warning)
  - Call drop rate >5% (critical)
  - Creator availability <50 online (warning)

**Log Aggregation:**
- **Tool**: AWS CloudWatch Logs Insights or ELK Stack
- **Sources**: API logs, application logs, security logs
- **Retention**: 90 days
- **Search**: Full-text search with filters by user ID, timestamp, error type

**Rationale:**
Full observability essential for maintaining <200ms SLA and diagnosing issues in complex distributed system. Business metrics enable proactive intervention (e.g., low creator availability).

**Acceptance Criteria:**
- [ ] Grafana + Prometheus operational with infrastructure dashboards
- [ ] APM (X-Ray or Datadog) tracing all API endpoints
- [ ] Database slow query monitoring operational
- [ ] Agora call quality metrics dashboard created
- [ ] Business metrics dashboard operational (GMV, calls, coins)
- [ ] PagerDuty integration configured for critical alerts
- [ ] Log aggregation operational with 90-day retention
- [ ] All p95 latency metrics <200ms visible in dashboards

**Dependencies:**
- REQ-TA-001 (Technology Stack)
- REQ-TA-002 (Performance Targets)
- REQ-TA-003 (Premium Infrastructure)

**Design Decisions:**
- **DD-TA-017**: CloudWatch Logs Insights chosen over ELK for lower operational overhead
- **DD-TA-018**: PagerDuty chosen for critical alerts (24/7 on-call requirement)

**Notes:**
- Comprehensive observability adds tooling costs (Datadog, PagerDuty)
- Essential for meeting <200ms SLA and rapid incident response
- Business metrics enable proactive platform management

**Testing Requirements:**
- Alert testing (trigger thresholds, verify notifications)
- Dashboard accuracy validation
- Log search performance testing
- Distributed tracing end-to-end validation

---

## REQ-TA-009: Disaster Recovery and Backup Strategy

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement standard disaster recovery and backup strategy to minimize data loss and downtime for financial platform operations.

**Specifications:**

**Recovery Targets:**
- **RTO (Recovery Time Objective)**: 4 hours (system back online within 4 hours)
- **RPO (Recovery Point Objective)**: 1 hour (maximum 1 hour of data loss)

**Database Backups:**
- **Frequency**: Hourly automated backups (PostgreSQL RDS automated backups)
- **Retention**: 30 days
- **Point-in-Time Recovery**: Restore to any second within 30-day window
- **Backup Storage**: AWS S3 with cross-region replication (Mumbai → Singapore)
- **Testing**: Monthly backup restore drill

**Redis Backup:**
- **Persistence**: RDB snapshots every 5 minutes
- **Retention**: 24 hours
- **Recovery**: Rebuild cache from database after failure (acceptable cold start)

**File Storage Backup:**
- **S3 Versioning**: Enabled for profile photos and voice messages
- **Retention**: 90 days
- **Cross-Region Replication**: Mumbai → Singapore

**Disaster Recovery Procedure:**
- **Primary Failure**: Switch to warm standby (database restored from latest backup)
- **Data Center Failure**: Restore infrastructure in different availability zone
- **Runbook**: Documented step-by-step recovery procedures
- **Team Training**: Quarterly disaster recovery drills

**Warm Standby Option:**
- **Scope**: Database read replica in different availability zone
- **Promotion**: Manual promotion to primary if needed
- **Lag**: <1 minute replication lag

**Rationale:**
Standard RTO/RPO balances cost with risk tolerance. Hourly backups with point-in-time recovery minimizes data loss while avoiding expensive hot standby infrastructure.

**Acceptance Criteria:**
- [ ] Hourly PostgreSQL automated backups operational
- [ ] Point-in-time recovery tested successfully
- [ ] Cross-region backup replication to Singapore operational
- [ ] Redis RDB snapshots every 5 minutes validated
- [ ] S3 versioning enabled for all user-generated content
- [ ] Disaster recovery runbook documented
- [ ] Monthly backup restore drill completed successfully
- [ ] Warm standby read replica operational with <1 minute lag

**Dependencies:**
- REQ-TA-003 (Database Architecture)
- REQ-TA-005 (Geographic Deployment)

**Design Decisions:**
- **DD-TA-019**: RTO 4h/RPO 1h chosen as balanced approach (vs. RTO 1h/RPO 15min enterprise)
- **DD-TA-020**: Warm standby chosen over hot standby to reduce costs

**Notes:**
- Balanced approach for MVP (not enterprise-grade but acceptable)
- Cross-region replication protects against regional AWS outage
- Monthly drills ensure team readiness

**Testing Requirements:**
- Backup restore testing (full database restore from backup)
- Point-in-time recovery testing (restore to specific timestamp)
- Failover testing (promote read replica to primary)
- Disaster recovery drill (simulate regional failure)

---

## REQ-TA-010: Phased Database Scaling Strategy

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement phased PostgreSQL scaling strategy deferring physical sharding to 1M+ users, using managed RDS with vertical scaling and read replicas for MVP-500K users.

**Specifications:**

**Phase 1: MVP-200K Users (Months 1-12)**
- Single AWS RDS PostgreSQL instance (db.t3.medium → db.r5.large)
- Logical partitioning for large tables (messages, call_history by created_at)
- 2-3 read replicas for read-heavy operations
- Connection pooling: PgBouncer (1000+ connections)
- Cost: $300-700/month

**Phase 2: 200K-500K Users (Year 2)**
- Vertical scaling: db.r5.xlarge → db.r5.2xlarge
- 3-5 read replicas across availability zones
- Redis caching layer for hot data (already in place)
- Elasticsearch for complex queries (search, matching) if needed
- Cost: $700-1,500/month

**Phase 3: 500K-1M Users (Year 2-3)**
- Vertical partitioning: Separate databases for table groups
  - User DB: profiles, authentication, wallet
  - Activity DB: calls, messages, gifts
  - Analytics DB: events, metrics
- 5-10 read replicas per database
- Cost: $1,500-2,500/month

**Phase 4: 1M+ Users (Year 3+)**
- Decision point: Physical sharding OR NewSQL
- Options:
  - A) Application-level sharding (user_id hash)
  - B) CockroachDB/YugabyteDB (automatic sharding)
  - C) Continue vertical partitioning + optimization
- Cost: $3,000-5,000+/month

**Connection Pooling:**
- PgBouncer for connection pooling (1000+ connections)
- Read/write splitting for replica routing
- Connection routing per database in Phase 3+

**Rationale:**
Industry benchmarks show companies shard at 1M-5M users, not earlier (Figma reached 4M users without horizontal sharding). Managed RDS with optimization handles 200K-1M users effectively. Deferred sharding reduces operational complexity and engineering overhead during critical MVP/growth phases.

**Acceptance Criteria:**
- [ ] RDS PostgreSQL instance deployed with logical partitioning schema
- [ ] PgBouncer connection pooling operational
- [ ] 2-3 read replicas configured and routing tested
- [ ] Read/write splitting working (writes to primary, reads to replicas)
- [ ] Scaling playbook documented for Phase 2, 3, 4 transitions
- [ ] Performance testing validates 200K user capacity
- [ ] Cost monitoring aligned with phase budgets

**Dependencies:**
- REQ-TA-003 (Premium Infrastructure)
- REQ-DB-* (Database Schema requirements from Section 7)

**Design Decisions:**
- **DD-TA-021**: Managed RDS with vertical scaling chosen over premature physical sharding based on industry benchmarks (1M-5M user inflection point)
- **DD-TA-022**: Phased approach (single → vertical partitioning → physical sharding) reduces MVP complexity and operational burden

**Notes:**
- Defers physical sharding to 1M+ users based on 2026-02-02 scalability review
- Figma case study: Reached 4M users without horizontal sharding
- Managed RDS simplifies operations and reduces engineering overhead
- Logical partitioning (by date for time-series tables) improves query performance
- Phase 4 decision delayed until 1M users based on actual data patterns

**Testing Requirements:**
- Read replica lag monitoring (<1 minute target)
- Read/write splitting verification
- Connection pooling load testing (1000+ connections)
- Performance testing with 200K user simulation
- Vertical scaling procedure testing (instance upgrade)
- Database backup and restore testing

---

## REQ-TA-011: Redis Architecture and Caching Strategy

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement Redis Cluster with replication for high availability caching, session management, and real-time data.

**Specifications:**

**Redis Architecture:**
- **Configuration**: Redis Cluster with replication
- **Nodes**: 3 primary + 3 replica nodes (high availability)
- **Instance Type**: AWS ElastiCache cache.r6g.large or equivalent
- **Persistence**: RDB snapshots every 5 minutes
- **Eviction Policy**: allkeys-lru (Least Recently Used)

**Caching Use Cases:**
- **Session Management**: JWT refresh tokens (30-day TTL)
- **User Profiles**: Cached for 1 hour (reduce database reads)
- **Creator Availability**: Real-time online/offline status (1-minute TTL)
- **Gift Catalog**: Cached for 24 hours (read-heavy, rarely changes)
- **Rate Limiting**: Request counters (1-minute sliding window)
- **Live Room State**: Active users in room, room metadata

**Cache Invalidation:**
- **Profile Updates**: Invalidate on user profile changes
- **Creator Status**: Update every 30 seconds from mobile app heartbeat
- **Gift Catalog**: Invalidate on admin catalog updates
- **Manual**: Admin endpoint to flush specific cache keys

**Redis Client:**
- **Library**: redis-py with asyncio support (Python)
- **Connection Pooling**: 100 connections per API server
- **Retry Logic**: 3 retries with exponential backoff

**Rationale:**
Redis Cluster with replication provides high availability (no single point of failure). Caching reduces database load and improves API response times to meet <200ms SLA.

**Acceptance Criteria:**
- [ ] Redis Cluster operational with 3 primary + 3 replica nodes
- [ ] RDB persistence configured (snapshots every 5 minutes)
- [ ] Cache hit rate >70% after 24 hours of operation
- [ ] Session management (JWT refresh tokens) working
- [ ] Rate limiting using Redis counters operational
- [ ] Cache invalidation on profile updates working
- [ ] Failover testing successful (primary node failure)

**Dependencies:**
- REQ-TA-003 (Premium Infrastructure)
- REQ-TA-007 (JWT tokens stored in Redis)
- REQ-TA-006 (Rate limiting uses Redis)

**Design Decisions:**
- **DD-TA-023**: Redis Cluster chosen over single instance for high availability
- **DD-TA-024**: allkeys-lru eviction policy prevents cache fill-up

**Notes:**
- Redis Cluster adds complexity but eliminates single point of failure
- Cache hit rate >70% indicates effective caching strategy
- RDB snapshots provide recovery after failure (cold start acceptable)

**Testing Requirements:**
- Cache hit/miss rate monitoring
- Failover testing (primary node failure, automatic replica promotion)
- Cache invalidation testing
- Connection pool exhaustion testing

---

## REQ-TA-012: Agora SDK Integration Architecture

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Integrate Agora SDK for voice and video calls with optimized edge server selection and call quality monitoring.

**Specifications:**

**Agora Configuration:**
- **SDK Version**: Agora Flutter SDK 6.x
- **Edge Servers**: Mumbai (primary), Bangalore (secondary)
- **Channel Profile**: Live Broadcasting (optimized for one-to-one calls)
- **Audio Profile**: Music Standard (48 kHz, stereo)
- **Video Profile**: 720p @ 30fps (default), 360p @ 15fps (low bandwidth mode)

**Call Flow:**
1. User initiates call → API generates Agora token (24-hour expiration)
2. Mobile app joins Agora channel using token
3. Agora handles audio/video streaming (peer-to-peer or cloud routing)
4. Call end → API logs duration, calculates coin deduction

**Token Generation:**
- **Server-Side**: FastAPI generates Agora tokens (security best practice)
- **Expiration**: 24 hours (prevents token reuse)
- **Permissions**: Audio + video based on call type

**Call Quality Monitoring:**
- **Metrics**: Jitter, packet loss, bitrate, call drop rate
- **Target**: <100ms jitter, <2% packet loss
- **Dashboard**: Real-time call quality metrics in Grafana
- **Alerts**: Call drop rate >5% triggers alert

**Bandwidth Optimization:**
- **Adaptive Bitrate**: Agora automatically adjusts based on network
- **Low Bandwidth Mode**: 360p @ 15fps for poor connections
- **Audio-Only Fallback**: Switch to audio if video quality poor

**Rationale:**
Agora provides reliable, low-latency voice/video calls with India edge servers. Server-side token generation prevents security issues.

**Acceptance Criteria:**
- [ ] Agora Flutter SDK integrated in mobile app
- [ ] Token generation endpoint implemented in API
- [ ] Mumbai and Bangalore edge servers configured
- [ ] Voice call tested (Tamil, Telugu, Kannada, Hindi, English speakers)
- [ ] Video call tested (720p quality validated)
- [ ] Call quality metrics dashboard operational
- [ ] Low bandwidth mode tested and working

**Dependencies:**
- REQ-TA-001 (Flutter mobile app)
- REQ-TA-006 (API endpoint for token generation)
- REQ-TA-008 (Call quality monitoring)

**Design Decisions:**
- **DD-TA-025**: Server-side token generation for security
- **DD-TA-026**: 720p default video profile (balance quality and bandwidth)

**Notes:**
- Agora costs based on usage (per-minute billing)
- Edge server selection reduces latency for South India users
- Call quality monitoring critical for user experience

**Testing Requirements:**
- Voice call quality testing in Tier-2/3 cities
- Video call quality testing (720p, 360p profiles)
- Low bandwidth mode testing (simulate poor network)
- Call drop scenario testing

---

## REQ-TA-013: Razorpay Payment Integration

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Integrate Razorpay payment gateway for coin purchases with UPI, cards, wallets, and PCI DSS compliance.

**Specifications:**

**Razorpay Integration:**
- **Razorpay SDK**: Flutter SDK for mobile checkout
- **Payment Methods**: UPI, credit/debit cards, wallets (Paytm, PhonePe), net banking
- **Checkout**: Standard Razorpay checkout (hosted by Razorpay for PCI DSS compliance)
- **Webhook**: Payment confirmation webhook to backend

**Payment Flow:**
1. User selects coin package → API creates Razorpay order
2. Mobile app opens Razorpay checkout with order ID
3. User completes payment on Razorpay hosted page
4. Razorpay webhook notifies backend → Coins credited to wallet
5. API confirms payment → User receives coins

**Security:**
- **PCI DSS**: Razorpay handles card data (platform never stores card details)
- **Webhook Signature**: Verify webhook signature to prevent fraud
- **Idempotency**: Prevent duplicate coin credits on webhook retries

**Payment Reconciliation:**
- **Daily**: Automated reconciliation of Razorpay settlements with coin purchases
- **Webhook Logs**: All webhook events logged for audit trail
- **Failed Payments**: Automatic retry for failed webhooks (3 retries, exponential backoff)

**Razorpay Features:**
- **Instant Refunds**: Refund coins within 5-7 business days if needed
- **Smart Routing**: Razorpay automatically routes to best payment provider
- **Analytics**: Payment success rates, failure reasons

**Rationale:**
Razorpay provides best India payment support with UPI, cards, wallets. Hosted checkout ensures PCI DSS compliance (platform never handles card data).

**Acceptance Criteria:**
- [ ] Razorpay Flutter SDK integrated in mobile app
- [ ] Order creation endpoint implemented in API
- [ ] Webhook endpoint operational and signature-verified
- [ ] Coin crediting tested for all payment methods (UPI, cards, wallets)
- [ ] Idempotency working (no duplicate credits)
- [ ] Daily reconciliation automated
- [ ] Failed payment retry logic tested

**Dependencies:**
- REQ-TA-001 (Flutter mobile app, FastAPI backend)
- REQ-TA-006 (API endpoints for orders, webhooks)
- REQ-TA-007 (Webhook signature verification)

**Design Decisions:**
- **DD-TA-027**: Razorpay Standard Checkout chosen for PCI DSS compliance
- **DD-TA-028**: Webhook-based confirmation for reliability (vs. client-side)

**Notes:**
- Razorpay charges 2% + GST per transaction
- Hosted checkout simplifies PCI DSS compliance
- Daily reconciliation critical for financial accuracy

**Testing Requirements:**
- Payment testing for all methods (UPI, cards, wallets)
- Webhook signature verification testing
- Idempotency testing (duplicate webhook handling)
- Failed payment retry testing

---

## REQ-TA-014: Firebase Integration (Auth, FCM, Analytics)

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Integrate Firebase services for authentication (OTP), push notifications (FCM), and basic analytics.

**Specifications:**

**Firebase Authentication:**
- **OTP**: Phone number authentication with SMS OTP
- **Session**: Firebase Auth session tokens (1-hour expiration)
- **Backend Verification**: FastAPI verifies Firebase tokens for API requests

**Firebase Cloud Messaging (FCM):**
- **Use Cases**: Push notifications for new messages, call requests, gift received, creator online
- **Targeting**: User-specific (by FCM token), topic-based (all users in region)
- **Delivery**: High-priority for call requests, normal for promotional

**Firebase Analytics:**
- **Events**: App opens, screen views, button clicks, conversions
- **User Properties**: User type (consumer/creator), language preference
- **Retention**: Automatic cohort analysis
- **Conversion Funnels**: Signup → Profile complete → First call → Coin purchase

**Firebase SDK:**
- **Mobile**: Firebase Flutter SDK
- **Backend**: Firebase Admin SDK (Python) for token verification

**Rationale:**
Firebase provides reliable OTP authentication, push notifications, and basic analytics with minimal setup. FCM has excellent delivery rates in India.

**Acceptance Criteria:**
- [ ] Firebase Authentication configured for phone OTP
- [ ] OTP flow tested (send OTP, verify OTP, create session)
- [ ] Firebase token verification working in FastAPI backend
- [ ] FCM integrated for push notifications
- [ ] Push notifications tested (call request, new message, gift received)
- [ ] Firebase Analytics events tracked (app open, screen views, conversions)
- [ ] Conversion funnel dashboard created in Firebase console

**Dependencies:**
- REQ-TA-001 (Flutter mobile app, FastAPI backend)
- REQ-TA-006 (API authentication)

**Design Decisions:**
- **DD-TA-029**: Firebase OTP chosen for reliability and SMS delivery rates in India
- **DD-TA-030**: FCM chosen for push notifications (free, reliable)

**Notes:**
- Firebase free tier sufficient for MVP (<100K MAU)
- FCM delivery rates >95% in India
- Firebase Analytics provides basic insights (upgrade to Google Analytics 4 if needed)

**Testing Requirements:**
- OTP flow testing (send, verify, retry)
- Push notification delivery testing (foreground, background, killed app)
- Analytics event tracking verification
- Token verification testing in backend

---

## REQ-TA-015: N8N Automation Infrastructure

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Deploy self-hosted n8n automation platform for workflow automation (creator onboarding, payouts, notifications, moderation).

**Specifications:**

**N8N Deployment:**
- **Hosting**: Self-hosted on AWS EC2 (t3.medium instance)
- **Database**: PostgreSQL (shared with main database)
- **Storage**: AWS S3 for workflow backups
- **Authentication**: OAuth 2.0 with JWT tokens

**Workflow Categories:**
- **Creator Onboarding**: Welcome email, document upload reminders, approval notifications
- **Payouts**: T+1 automated payout processing, bank transfer initiation, confirmation emails
- **Moderation**: Flagged content review workflow, creator warnings, suspension notifications
- **User Engagement**: Re-engagement emails, inactivity notifications, promotional campaigns

**N8N Integrations:**
- **API**: Custom webhooks to LIVVLY backend
- **Email**: SMTP for transactional emails
- **SMS**: Twilio or MSG91 for SMS notifications
- **Slack**: Internal notifications for admin team

**Backup and Recovery:**
- **Workflow Backups**: Daily backups to S3
- **Version Control**: Git integration for workflow version control
- **Disaster Recovery**: Restore workflows from S3 backups

**Rationale:**
Self-hosted n8n provides powerful workflow automation without per-execution costs (vs. Zapier, Make.com). Critical for creator payout automation and moderation workflows.

**Acceptance Criteria:**
- [ ] N8N deployed on AWS EC2 with PostgreSQL database
- [ ] OAuth authentication configured
- [ ] Webhook integration with LIVVLY API working
- [ ] Creator onboarding workflow operational
- [ ] T+1 payout workflow operational
- [ ] Daily workflow backups to S3 automated
- [ ] Git version control for workflows configured

**Dependencies:**
- REQ-TA-003 (AWS infrastructure)
- REQ-TA-006 (API webhooks)
- REQ-AW-* (Automation Workflows from Section 11)

**Design Decisions:**
- **DD-TA-031**: Self-hosted n8n chosen over Zapier for cost control
- **DD-TA-032**: Shared PostgreSQL database for simplicity

**Notes:**
- N8N workflows defined in Section 11 (N8N Automation Workflows)
- Self-hosted provides unlimited executions (vs. per-execution pricing)
- Git integration enables workflow version control and rollback

**Testing Requirements:**
- Workflow execution testing (creator onboarding, payouts)
- Webhook integration testing
- Backup and restore testing
- Failure handling testing

---

## REQ-TA-016: CI/CD Pipeline with GitHub Actions

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement automated CI/CD pipeline using GitHub Actions for mobile app and backend deployments with quality gates.

**Specifications:**

**GitHub Actions Workflows:**

**Backend (FastAPI) Pipeline:**
1. **Trigger**: Push to `main` branch or pull request
2. **Steps**:
   - Checkout code
   - Set up Python 3.11+ environment
   - Install dependencies (pip install -r requirements.txt)
   - Run linters (black, flake8, mypy)
   - Run unit tests (pytest with >90% coverage requirement)
   - Build Docker image
   - Push to AWS ECR (Elastic Container Registry)
   - Deploy to staging environment
   - Run integration tests on staging
   - Deploy to production (manual approval required)

**Mobile (Flutter) Pipeline:**
1. **Trigger**: Push to `main` branch or tag release
2. **Steps**:
   - Checkout code
   - Set up Flutter environment
   - Install dependencies (flutter pub get)
   - Run linters (dart analyze)
   - Run unit tests (flutter test with >80% coverage requirement)
   - Build Android APK and iOS IPA
   - Upload to Google Play (internal testing) and TestFlight
   - Manual approval → Promote to production

**Quality Gates:**
- **Code Coverage**: Backend >90%, Mobile >80%
- **Linting**: Zero errors (warnings allowed)
- **Tests**: All tests must pass
- **Security**: Dependency vulnerability scanning (Snyk or Dependabot)

**Environments:**
- **Development**: Automatic deployment on push to `dev` branch
- **Staging**: Automatic deployment on push to `main` branch
- **Production**: Manual approval required after staging validation

**Rationale:**
Automated CI/CD ensures code quality, reduces deployment errors, and enables rapid iteration during 19-24 week MVP timeline.

**Acceptance Criteria:**
- [ ] GitHub Actions workflows configured for backend and mobile
- [ ] Backend pipeline runs successfully (lint, test, build, deploy)
- [ ] Mobile pipeline runs successfully (lint, test, build, upload)
- [ ] Code coverage gates enforced (backend >90%, mobile >80%)
- [ ] Staging environment deployed automatically
- [ ] Production deployment requires manual approval
- [ ] Deployment rollback procedure documented

**Dependencies:**
- REQ-TA-001 (Flutter mobile, FastAPI backend)
- REQ-TA-003 (AWS infrastructure)

**Design Decisions:**
- **DD-TA-033**: GitHub Actions chosen over Jenkins for simpler setup
- **DD-TA-034**: Manual approval for production deploys (vs. fully automated)

**Notes:**
- GitHub Actions free for open source, paid for private repos
- Staging environment critical for validating changes before production
- Rollback procedure: revert Git commit, trigger pipeline

**Testing Requirements:**
- Pipeline testing (trigger workflows, verify deployments)
- Quality gate testing (fail pipeline on low coverage)
- Rollback testing (revert and redeploy)

---

## REQ-TA-017: AI Evaluation Period and Migration Plan

**Priority:** P2
**Type:** Implementation Plan
**Status:** Approved

**Description:**
Define 30-day parallel evaluation plan for Google Cloud Speech-to-Text and Azure Cognitive Services, with migration plan to self-hosted Whisper in Phase 2.

**Specifications:**

**Evaluation Period (30 Days):**
- **Timeline**: Weeks 3-6 of MVP development (parallel to feature development)
- **Approach**: Integrate both providers simultaneously
- **Test Data**: 100+ hours of voice samples in each language (Tamil, Telugu, Kannada, Hindi, English)
- **Speakers**: Native speakers from Tier-2/3 cities (accents, dialects)

**Evaluation Criteria:**
1. **Accuracy** (Weight: 40%)
   - Word Error Rate (WER) for each language
   - Profanity detection accuracy
   - False positive rate (<5% acceptable)

2. **Latency** (Weight: 30%)
   - Transcription time for 1-minute audio (target <2 seconds)
   - Real-time streaming latency

3. **Cost** (Weight: 20%)
   - Estimated monthly cost at 10K MAU (target ₹2-4L/month)
   - Projected cost at 50K MAU

4. **Reliability** (Weight: 10%)
   - API uptime (target 99.9%)
   - Error rate (<0.1%)

**Decision Matrix:**
- Provider with highest weighted score selected
- If scores within 5%, choose lower cost option
- If tie, choose Google Cloud (better Indic language support reputation)

**Migration Plan to Whisper (Phase 2):**
- **Timeline**: Month 7-8 (after MVP launch)
- **Infrastructure**: GPU-enabled AWS EC2 instances (g5.xlarge)
- **Effort**: 4-6 weeks development + testing
- **Parallel Run**: 2 weeks parallel (third-party + Whisper)
- **Cutover**: Gradual migration (10% → 50% → 100% traffic)

**Rationale:**
Data-driven evaluation ensures highest accuracy for Safety-First core value. Migration to Whisper after MVP launch balances speed-to-market with long-term cost control and data privacy.

**Acceptance Criteria:**
- [ ] Test dataset prepared (100+ hours per language, native speakers)
- [ ] Both providers integrated in test environment
- [ ] Evaluation completed with metrics for accuracy, latency, cost, reliability
- [ ] Decision made based on weighted score
- [ ] Whisper migration plan documented with timeline and infrastructure requirements

**Dependencies:**
- REQ-TA-004 (AI Moderation Infrastructure)
- REQ-FS-018 (AI Moderation from Section 5)

**Design Decisions:**
- **DD-TA-035**: 30-day evaluation period balances thoroughness with MVP timeline
- **DD-TA-036**: Weighted scoring ensures objective decision-making

**Notes:**
- +2 weeks added to MVP timeline (19-24 weeks total)
- Evaluation costs ~₹4-6L (duplicate AI costs for 30 days)
- Migration to Whisper saves long-term costs (₹2-4L/month → ₹20-40K/month)

**Testing Requirements:**
- Accuracy testing with native speakers
- Latency testing under various network conditions
- Cost projection modeling
- Whisper migration testing in Phase 2

---

## REQ-TA-018: Infrastructure as Code (IaC)

**Priority:** P2
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement Infrastructure as Code using Terraform or AWS CloudFormation for reproducible, version-controlled infrastructure deployments.

**Specifications:**

**IaC Tool:**
- **Primary**: Terraform (multi-cloud, better community support)
- **Alternative**: AWS CloudFormation (if AWS-only preference)

**Managed Resources:**
- **Compute**: EC2 instances, Auto Scaling Groups, Load Balancers
- **Database**: RDS PostgreSQL, ElastiCache Redis
- **Network**: VPC, Subnets, Security Groups, Internet Gateway
- **Storage**: S3 buckets, CloudFront distributions
- **Monitoring**: CloudWatch Alarms, SNS Topics
- **Security**: IAM Roles, Policies, Secrets Manager

**Repository Structure:**
```
infrastructure/
├── environments/
│   ├── dev/
│   ├── staging/
│   └── production/
├── modules/
│   ├── compute/
│   ├── database/
│   ├── network/
│   └── monitoring/
└── README.md
```

**Deployment Process:**
1. Terraform plan → Review changes
2. Terraform apply → Deploy infrastructure
3. Git commit → Version control infrastructure changes

**State Management:**
- **Backend**: Terraform state stored in S3 with DynamoDB locking
- **Versioning**: S3 versioning enabled for state file recovery
- **Access Control**: IAM policies restrict state file access

**Rationale:**
Infrastructure as Code enables reproducible deployments, version control, and disaster recovery. Critical for managing complex premium infrastructure (10+ API servers, sharded database, Redis Cluster).

**Acceptance Criteria:**
- [ ] Terraform project initialized with modules for compute, database, network
- [ ] Development environment deployed using Terraform
- [ ] Staging environment deployed using Terraform
- [ ] Production environment deployed using Terraform
- [ ] Terraform state stored in S3 with DynamoDB locking
- [ ] Infrastructure changes version-controlled in Git
- [ ] Deployment runbook documented

**Dependencies:**
- REQ-TA-003 (Premium Infrastructure)
- REQ-TA-005 (Geographic Deployment)

**Design Decisions:**
- **DD-TA-037**: Terraform chosen over CloudFormation for multi-cloud flexibility
- **DD-TA-038**: S3 + DynamoDB backend for state management (prevents concurrent modifications)

**Notes:**
- IaC enables rapid environment provisioning (dev, staging, production)
- Version control provides audit trail for infrastructure changes
- Simplifies disaster recovery (rebuild entire infrastructure from code)

**Testing Requirements:**
- Terraform plan validation (no errors)
- Infrastructure deployment testing (dev, staging, production)
- State file recovery testing
- Disaster recovery testing (rebuild infrastructure from scratch)

---

## SUMMARY

**Total Requirements:** 18
**Priority Breakdown:**
- **P0 (Critical):** 10 requirements (Technology stack, performance, infrastructure, security, observability, disaster recovery)
- **P1 (High):** 6 requirements (Database sharding, Redis, Agora, Razorpay, Firebase, n8n, CI/CD)
- **P2 (Medium):** 2 requirements (AI evaluation plan, Infrastructure as Code)

**Key Technical Decisions:**
1. **Technology Stack**: Flutter 3.x + FastAPI (Python) confirmed - validated for 150K concurrent (cost premium vs Node.js acceptable)
2. **Performance**: Premium <200ms SLA with phased concurrent targets (5-8% MVP → 12-15% @ 1M MAU)
3. **Infrastructure**: Right-sized auto-scaling (2-4 baseline → 12+ max, warm pools) - saves $3,480/year vs over-provisioning
4. **Database**: Managed PostgreSQL with phased scaling (defer physical sharding to 1M+ users)
5. **AI Moderation**: Hybrid approach (third-party MVP → self-hosted Phase 2)
6. **Security**: Enterprise-grade (WAF, JWT, MFA, E2E encryption, PCI DSS)
7. **Monitoring**: Full observability stack (APM, tracing, business metrics)
8. **Disaster Recovery**: Standard (RTO 4h, RPO 1h, hourly backups)
9. **Geographic**: AWS Mumbai + CloudFront CDN + Agora Mumbai/Bangalore edges

**Timeline Impact:**
- **Original MVP**: 17-22 weeks
- **AI Evaluation Addition**: +2 weeks
- **Updated MVP**: 19-24 weeks

**Cost Impact:**
- **Infrastructure**: Right-sized approach saves $3,480/year vs original premium over-provisioning
- **FastAPI Premium**: 30-50% higher infrastructure cost vs Node.js at scale ($50-150K/year @ 1M MAU)
- **AI Evaluation**: ₹4-6L one-time (30-day parallel testing)
- **Security**: Additional compliance and tooling costs
- **Monitoring**: Observability infrastructure costs
- **Database**: Managed RDS approach reduces operational costs vs self-managed sharding

**Dependencies:**
- Section 7 (Database Schema): Defines logical partitioning schema (sharding deferred to 1M+ users)
- Section 8 (API Specifications): Implements API architecture
- Section 11 (N8N Workflows): Defines automation workflows
- Section 14 (Cost Estimation): **UPDATE REQUIRED** - Reduce infrastructure costs by $3,480/year, add FastAPI cost notes

**Scalability Review:**
- **Date**: 2026-02-02
- **Status**: ✅ Completed with critical updates implemented
- **Changes**: Infrastructure strategy, database sharding timeline, concurrent user targets
- **Validation**: FastAPI confirmed viable for 150K concurrent users
- **Savings**: $3,480/year infrastructure cost reduction

**Downstream Actions Required:**
1. **Section 14 (Cost Estimation)**: Update infrastructure costs (-$3,480/year MVP-200K range)
2. **Section 7 (Database Schema)**: Review and simplify sharding requirements
3. **Section 13 (Implementation Roadmap)**: Validate timeline with updated infrastructure approach

**Next Section:** Section 7 - Database Schema (lines 658-1139, 482 lines)

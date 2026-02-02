# Section 6: Technical Architecture - Questions & Answers

**Section:** Technical Architecture
**Date:** 2026-01-20 (Initial), 2026-02-02 (Scalability Review)
**Status:** Completed with Scalability Updates
**Questions Asked:** 9 (Initial) + Scalability Review
**Critical Conflicts Resolved:** 2 (Mobile framework, Backend framework)
**Scalability Updates:** 3 critical changes (Infrastructure, Database, Concurrent targets)

---

## PHASE 1: CORE QUESTIONS

### Q1: TECHNOLOGY STACK CONFLICTS

**Context:** PRD Section 6 specifies Flutter 3.x + FastAPI, but Section 5 requirements specified React Native + Node.js.

**Question:** Which technology stack should we use for MVP?

**Options Presented:**
- Option A: PRD Section 6 Stack (Flutter + FastAPI)
- Option B: Section 5 Stack (React Native + Node.js)
- Option C: Hybrid Approach

**Answer:** **Flutter 3.x + FastAPI (Python) - PRD Section 6 spec**

**Confirmed Technology Stack:**
- **Mobile**: Flutter 3.x (cross-platform iOS + Android)
- **Backend**: FastAPI (Python)
- **Rationale**:
  - Fast cross-platform development (single codebase)
  - Python backend excellent for AI/ML integration (moderation, matching algorithm)
  - Smaller talent pool but faster development
  - Good performance for mobile

**Impact:** Resolves critical technology stack conflict. All subsequent requirements use Flutter + FastAPI.

---

### Q2: SCALABILITY TARGETS

**Context:** PRD doesn't specify explicit scalability requirements for growth targets (50K → 200K → 1M users).

**Question:** What are the performance requirements for concurrent users, call capacity, and API response time SLAs?

**Answer:** **High performance: 15% concurrent, <200ms API, 10K calls**

**Performance Targets:**
- **Concurrent Users**: 15% of MAU
  - Month 6 (50K MAU): 7,500 concurrent users
  - Month 12 (200K MAU): 30,000 concurrent users
  - Year 2 (1M MAU): 150,000 concurrent users

- **Call Capacity**:
  - MVP launch: 1,000 simultaneous calls
  - Month 6: 5,000 simultaneous calls
  - Year 2: 25,000 simultaneous calls

- **API Response Time SLAs**:
  - User-facing endpoints: <200ms (p95)
  - Creator dashboard: <200ms (p95)
  - Live room join/leave: <1 second

**Impact:** PREMIUM performance targets requiring robust infrastructure and 30-40% higher costs.

---

### Q3: AI MODERATION INFRASTRUCTURE

**Context:** Section 5 requires AI speech-to-text moderation in 5 languages. PRD doesn't specify AI infrastructure.

**Question:** Which AI approach should we use?

**Options Presented:**
- Option A: Third-party AI service (Google/Azure)
- Option B: Self-hosted open-source (Whisper/Vosk)
- Option C: Hybrid (third-party MVP, self-hosted later)

**Answer:** **Hybrid: Third-party MVP, self-hosted Phase 2**

**Implementation Plan:**
- **MVP (Months 1-6)**: Google Cloud Speech-to-Text or Azure Cognitive Services
  - Proven accuracy for Tamil, Telugu, Kannada, Hindi, English
  - Fast setup (2-3 weeks)
  - Cost: ~₹2-4L/month
  - Trade-off: Data sent to cloud (privacy consideration)

- **Phase 2 (Month 7+)**: Migrate to self-hosted open-source (Whisper)
  - Full data privacy
  - Lower long-term cost
  - Requires migration effort and ML infrastructure

**Impact:** Balances speed-to-market with long-term cost and privacy goals.

---

## PHASE 2: FOLLOW-UP QUESTIONS

### Q4: INFRASTRUCTURE SCALING STRATEGY

**Context:** Aggressive performance targets (15% concurrent, <200ms SLA, 25K simultaneous calls by Year 2).

**Question:** What's the infrastructure scaling approach for API servers, database, and Redis?

**Answer:** **Premium over-provisioning from day one**

**Infrastructure Configuration:**
- **API Servers**: 10+ API servers at MVP launch (over-provisioned for performance)
- **Database**: Sharded PostgreSQL ready from start (future-proof to 1M+ users)
- **Redis**: Redis Cluster with replication (high availability)
- **Auto-scaling**: Aggressive auto-scaling policies to maintain <200ms SLA
- **Cost Impact**: 30-40% higher infrastructure costs than medium approach
- **Rationale**: Meets premium performance targets, prevents performance issues at launch

**Impact:** PREMIUM infrastructure approach with significantly higher ongoing costs.

---

### Q5: MULTI-REGION AND CDN STRATEGY

**Context:** Targeting Tier-2/3 cities across South India with video calls and voice messages.

**Question:** What's the geographic deployment strategy for primary region, CDN, and Agora edge servers?

**Answer:** **Mumbai primary + CloudFront CDN**

**Geographic Deployment:**
- **Primary Region**: AWS Mumbai (ap-south-1) - most services available, good India coverage
- **CDN**: CloudFront for profile photos, voice messages, video content
- **Agora Edge**: Mumbai + Bangalore edge servers for voice/video calls
- **Standard India setup**, proven and reliable

**Impact:** Balanced approach for India-focused deployment.

---

### Q6: AI MODERATION IMPLEMENTATION DETAILS

**Context:** Hybrid approach chosen (third-party MVP → self-hosted Phase 2).

**Question:** Which speech-to-text provider, moderation pipeline, and content policy enforcement?

**Answer:** **Evaluate both Google + Azure in MVP**

**AI Provider Evaluation:**
- **Approach**: Test both Google Cloud Speech-to-Text AND Azure Cognitive Services in parallel
- **Timeline Impact**: +2 weeks to MVP for proper evaluation (now 19-24 weeks total)
- **Evaluation Criteria**: Accuracy for Tamil, Telugu, Kannada, Hindi, English; latency; cost; false positive rate
- **Duration**: 30-day evaluation period during development
- **Decision**: Choose best performer after data-driven comparison
- **Rationale**: Ensures highest accuracy for 5-language moderation (critical for Safety core value)

**Impact:** **UPDATED MVP TIMELINE: 19-24 weeks** (added 2 weeks for AI provider evaluation)

**Cost Impact:**
- Infrastructure: 30-40% higher ongoing costs
- AI evaluation: Duplicate costs for 30 days (~₹4-6L one-time)

---

## PHASE 3: GAP ANALYSIS QUESTIONS

### Q7: SECURITY ARCHITECTURE

**Context:** PRD mentions "Firebase Auth" but doesn't specify comprehensive security for financial platform handling ₹2-3 Cr GMV.

**Question:** What level of security is required for MVP?

**Options Presented:**
- Option A: Basic Security (Firebase + HTTPS + rate limiting)
- Option B: Enhanced Security (+ WAF, JWT, fraud detection, encryption)
- Option C: Enterprise Security (+ MFA, E2E encryption, audit logs, PCI DSS)
- Option D: All of the above (comprehensive)

**Answer:** **Enterprise Security (Premium)**

**Security Components:**
- WAF (Web Application Firewall) for DDoS protection
- JWT authentication with refresh tokens
- IP-based rate limiting and fraud detection
- Database encryption at rest
- AWS Secrets Manager for credentials
- Multi-factor authentication for creators (earnings access)
- End-to-end encryption for voice messages
- Security audit logging (compliance trail)
- Automated vulnerability scanning
- PCI DSS compliance for payment data
- Regular penetration testing

**Impact:** Regulatory-grade security appropriate for financial platform. Adds security infrastructure costs and compliance overhead.

---

### Q8: MONITORING AND OBSERVABILITY STRATEGY

**Context:** PRD mentions "Grafana + Prometheus" but doesn't specify comprehensive observability for <200ms SLA.

**Question:** What's the complete monitoring strategy?

**Options Presented:**
- Option A: Infrastructure Monitoring Only
- Option B: Application Performance Monitoring (APM)
- Option C: Full Observability Stack
- Option D: All monitoring + dedicated DevOps engineer

**Answer:** **Full Observability Stack**

**Monitoring Components:**
- Infrastructure monitoring (Grafana + Prometheus for CPU, memory, disk)
- Application Performance Monitoring (APM) with distributed tracing
- Database query performance monitoring
- Redis cache hit/miss rates
- API endpoint latency breakdown (p50, p95, p99)
- Error rate tracking by endpoint
- Real-time user experience monitoring
- Agora call quality metrics (jitter, packet loss, bitrate)
- Business metrics dashboard (GMV, active calls, coin purchases)
- Custom alerts for business thresholds
- Log aggregation and search (CloudWatch Logs Insights or ELK)

**Impact:** Comprehensive observability essential for maintaining <200ms SLA. Adds monitoring infrastructure and tooling costs.

---

### Q9: DISASTER RECOVERY AND BACKUP STRATEGY

**Context:** PRD doesn't mention disaster recovery. Financial platform requires backup strategy.

**Question:** What's the backup frequency and disaster recovery targets?

**Options Presented:**
- Database Backups: Daily (standard) vs. Hourly (premium) vs. Continuous (enterprise)
- Disaster Recovery: RTO 24h/RPO 24h vs. RTO 4h/RPO 1h vs. RTO 1h/RPO 15min
- Multi-Region: No vs. Warm standby vs. Hot standby
- Redis: No backup vs. RDB snapshots vs. AOF

**Answer:** **Standard: RTO 4hrs, RPO 1hr, hourly backups**

**Backup Strategy:**
- PostgreSQL: Hourly automated backups with 30-day retention
- Point-in-time recovery capability
- Recovery Time Objective (RTO): 4 hours (system back online within 4 hours)
- Recovery Point Objective (RPO): 1 hour (maximum 1 hour of data loss)
- Warm standby option available for critical failures
- Redis: RDB snapshots every 5 minutes

**Impact:** Balanced approach providing minimal data loss and reasonable recovery time for MVP.

---

## SUMMARY OF DECISIONS

### Critical Conflicts Resolved
1. **Technology Stack**: Flutter 3.x + FastAPI (Python) confirmed
2. **Backend Framework**: FastAPI (Python) confirmed (overrides React Native + Node.js from Section 5)

### Key Technical Decisions
- **Performance Targets**: Premium (15% concurrent, <200ms SLA)
- **Infrastructure**: Premium over-provisioning (10+ API servers, sharded PostgreSQL, Redis Cluster)
- **AI Moderation**: Hybrid approach with 30-day parallel evaluation (Google + Azure)
- **Security**: Enterprise-grade (WAF, JWT, MFA, E2E encryption, PCI DSS)
- **Monitoring**: Full observability stack (APM, tracing, business metrics)
- **Disaster Recovery**: Standard (RTO 4h, RPO 1h, hourly backups)
- **Geographic**: Mumbai primary + CloudFront CDN + Agora Mumbai/Bangalore edges

### Timeline Impact
- **Original MVP**: 17-22 weeks
- **AI Evaluation Addition**: +2 weeks
- **Updated MVP**: 19-24 weeks

### Cost Impact
- **Infrastructure**: 30-40% higher ongoing costs (premium approach)
- **AI Evaluation**: ₹4-6L one-time (30-day parallel testing)
- **Security**: Additional compliance and tooling costs
- **Monitoring**: Observability infrastructure costs

---

**Next Steps:**
- Extract technical architecture requirements based on these decisions
- Update Section 14 (Cost Estimation) to reflect premium infrastructure approach
- Validate 19-24 week timeline in Section 13 (Implementation Roadmap)

---

## SCALABILITY REVIEW (2026-02-02)

### Review Context
Following PRD breakdown completion, performed comprehensive scalability review focusing on:
1. Technology stack performance at 150K concurrent users
2. Infrastructure scaling strategies (cost vs performance)
3. Database scaling approaches and sharding timeline

### Research Methodology
- 3 parallel web research agents analyzing:
  - FastAPI scalability benchmarks vs Node.js/Go
  - Infrastructure scaling strategies (over-provisioning vs right-sized auto-scaling)
  - Database scaling approaches (sharding timeline validation)
- Industry benchmarks, case studies, technical documentation
- Focus: Real-world production data, not theoretical limits

---

### CRITICAL UPDATE 1: Infrastructure Strategy Changed

**Original Decision (Q4):**
- Premium over-provisioning (10+ API servers from day 1)
- Cost: $450-500/month for 10K MAU MVP
- Rationale: Prevent performance issues, support rapid growth

**Updated Decision:**
- Right-sized auto-scaling (2-4 baseline, scale to 12+, warm pools)
- Cost: $150-180/month for 10K MAU MVP
- **Savings: $3,480/year (64% cost reduction)**

**Research Findings:**
- Industry pattern: Successful startups start lean, scale aggressively
- Organizations waste 60-70% capacity with over-provisioning at MVP stage
- Warm pools eliminate scale-up lag (30 seconds vs 2-5 minutes)
- Auto-scaling with Target Tracking achieves 40-65% cost reduction
- AWS best practices recommend right-sizing over over-provisioning

**Rationale for Change:**
Premium over-provisioning appropriate for enterprise-scale (5M+ users) but over-engineering for MVP-200K user range. Right-sized auto-scaling with warm pools is industry best practice for startups, balancing cost efficiency with performance reliability.

**Implementation Changes:**
- REQ-TA-003 rewritten with right-sized auto-scaling approach
- DD-TA-005 updated to reflect new strategy
- Section 14 (Cost Estimation) flagged for update (-$3,480/year)

---

### CRITICAL UPDATE 2: Database Sharding Timeline Changed

**Original Decision (Q10 implied):**
- Sharding-ready from MVP (logical sharding)
- Physical sharding at 200K users (4 shards)
- Scale to 16 shards at 1M users

**Updated Decision:**
- Single managed PostgreSQL (AWS RDS) until 500K users
- Vertical partitioning at 500K-1M users (separate DBs by table groups)
- Physical sharding deferred to 1M+ users based on actual need

**Research Findings:**
- **Figma case study**: Reached 4M users without horizontal sharding (vertical scaling + optimization worked)
- Industry benchmark: Companies typically shard at 1M-5M users, NOT before
- AWS RDS can handle 200K-1M users with read replicas + optimization
- Physical sharding at 200K = premature optimization
- Operational burden: 2-4 weeks setup + ongoing maintenance complexity
- Managed RDS costs less than self-managed sharded infrastructure at <1M scale

**Rationale for Change:**
Reduces operational complexity during critical growth phase. Managed RDS with vertical scaling sufficient with proper optimization. Physical sharding only when data volume/write patterns actually demand it. Follows industry best practices validated by successful companies.

**Implementation Changes:**
- REQ-TA-010 rewritten with phased scaling strategy
- DD-TA-021, DD-TA-022 updated for new timeline
- Focus shifted to logical partitioning (by date) vs physical sharding

---

### MODERATE UPDATE 3: Concurrent User Targets Refined

**Original Assumption (Q2):**
- Flat 15% of MAU concurrent at all stages
- MVP (10K MAU): 1,500 concurrent
- Year 2 (1M MAU): 150,000 concurrent

**Updated Targets:**
- Phased growth: 5-8% @ MVP → 10-12% @ 200K → 12-15% @ 1M
- MVP (10K MAU): 500-800 concurrent (more realistic)
- Month 6 (50K MAU): 4,000-5,000 concurrent
- Month 12 (200K MAU): 20,000-24,000 concurrent
- Year 2 (1M MAU): 120,000-150,000 concurrent

**Research Findings:**
- Industry data: Social/dating apps typically achieve 5-15% of DAU as concurrent during peak
- 15% of MAU assumes 100% DAU with 15% concurrent at peak (unrealistic for MVP)
- More realistic MVP: 10% DAU × 50-80% peak concurrency = 5-8% of MAU
- Engagement grows with product-market fit: 5-8% MVP → 15% at maturity
- Mathematical validation: 10K MAU × 10% DAU × 50% concurrent = 500 users (5% of MAU)

**Rationale for Change:**
Conservative MVP estimates prevent over-engineering infrastructure while maintaining upper-bound capacity planning (150K at 1M MAU unchanged). Phased approach aligns with realistic user engagement growth patterns validated by industry data.

**Implementation Changes:**
- REQ-TA-002 updated with phased concurrent user table
- DD-TA-003 revised to reflect growth-based targets
- Load testing targets adjusted to 800 concurrent for MVP

---

### VALIDATION: Technology Stack Confirmed

**Decision (Q1):**
- Flutter 3.x + FastAPI (Python)
- Status: ✅ VALIDATED for 150K concurrent users

**Research Findings:**
- **FastAPI Performance**: Technically viable for 150K concurrent with optimization
  - TechEmpower benchmarks: 3K-22K requests/sec depending on configuration
  - Requires async drivers (asyncpg, aioredis) for competitive performance
  - Uvicorn with multiple workers handles high concurrency effectively
- **Cost Trade-off**: FastAPI requires 2-3x more instances than Node.js/Go at equivalent load
  - Infrastructure cost: 30-50% higher than Node.js at scale
  - Estimated premium: $50-150K/year at 1M MAU scale
- **Python GIL**: NOT a blocker for I/O-bound operations (async/await bypasses GIL for network I/O)
- **Production Examples**: Hinge uses Django (Python) at scale, proving Python viability for dating apps
- **Node.js Comparison**: Fastify achieves 8K-20K RPS vs FastAPI 3K-22K RPS (comparable with optimization)
- **Go Comparison**: Gin achieves 25K-50K RPS (faster but steeper learning curve)

**Trade-offs Acknowledged:**
- ✅ **Pro**: Faster development, superior Python AI/ML ecosystem (critical for moderation/matching)
- ✅ **Pro**: Team expertise and faster hiring (Python developers more available than Go)
- ✅ **Pro**: Proven at scale (Hinge/Django validates Python for dating platforms)
- ⚠️ **Con**: Higher infrastructure cost ($50-150K/year premium at scale vs Node.js)
- ⚠️ **Con**: Requires 2-3x more instances than Go for equivalent load
- ✅ **Decision**: Keep FastAPI - AI/ML benefits and development speed outweigh cost premium

**Implementation Changes:**
- REQ-TA-001 updated with performance and cost considerations section
- Added requirement for async drivers (asyncpg, aioredis)
- Documented cost trade-off vs Node.js alternatives
- Added note about Python GIL non-issue for I/O-bound workloads

---

### Summary of Changes

| Decision Area | Status | Impact |
|--------------|--------|--------|
| **Infrastructure Strategy** | ❌ CHANGED | Right-sized auto-scaling: -$3,480/year savings |
| **Database Sharding Timeline** | ❌ CHANGED | Deferred to 1M+ users: Reduced complexity |
| **Concurrent User Targets** | ⚠️ REFINED | MVP: 500-800 (was 1,500): More realistic |
| **Technology Stack (FastAPI)** | ✅ VALIDATED | Confirmed viable with 30-50% cost caveat |
| **AI Moderation (Hybrid)** | ✅ NO CHANGE | Third-party → self-hosted approach unchanged |
| **Security Architecture** | ✅ NO CHANGE | Enterprise security validated |
| **Monitoring Strategy** | ✅ NO CHANGE | Full observability unchanged |
| **Disaster Recovery** | ✅ NO CHANGE | RTO 4h/RPO 1h unchanged |

**Requirements Updated:**
- **REQ-TA-001**: Added FastAPI cost comparison and async driver requirements
- **REQ-TA-002**: Updated with phased concurrent user targets (5-8% → 15%)
- **REQ-TA-003**: Completely rewritten for right-sized auto-scaling approach
- **REQ-TA-010**: Completely rewritten with deferred sharding timeline

**Design Decisions Updated:**
- **DD-TA-003**: Phased concurrent ratio (was flat 15%)
- **DD-TA-005**: Right-sized auto-scaling (was premium over-provisioning)
- **DD-TA-006**: Managed RDS with vertical scaling (was sharding-ready from start)
- **DD-TA-021**: Managed RDS strategy (was user_id sharding key)
- **DD-TA-022**: Phased scaling approach (was logical → physical sharding)

**Downstream Impact:**
- **Section 14 (Cost Estimation)**: UPDATE REQUIRED
  - Reduce infrastructure costs by $3,480/year (MVP-200K MAU range)
  - Update database cost projections (managed RDS vs sharded infrastructure)
  - Add FastAPI vs Node.js cost comparison note
- **Section 7 (Database Schema)**: REVIEW RECOMMENDED
  - Remove physical sharding requirements for MVP
  - Focus on logical partitioning (by date for time-series tables)
  - Simplify schema design without sharding complexity
- **Section 13 (Implementation Roadmap)**: REVIEW RECOMMENDED
  - Validate timeline with right-sized infrastructure (may reduce setup time)
  - Remove sharding implementation from MVP phase
  - Add warm pool configuration to infrastructure setup

---

### Key Research Insights

**1. Infrastructure Scaling**
- Over-provisioning waste: 60-70% idle capacity at MVP stage
- Right-sizing with auto-scaling: 40-65% cost reduction
- Warm pools: Eliminate cold-start lag (30 sec vs 2-5 min)
- Industry pattern: Start lean, scale aggressively, optimize continuously

**2. Database Scaling**
- Sharding inflection point: 1M-5M users (industry consensus)
- Figma precedent: 4M users without horizontal sharding
- AWS RDS capacity: 200K-1M users with vertical scaling + read replicas
- Premature sharding: Adds 2-4 weeks dev time + ongoing ops burden

**3. Concurrent User Patterns**
- Social/dating apps: 5-15% of DAU concurrent at peak
- MVP engagement: Lower (5-8% of MAU) until product-market fit proven
- Growth pattern: Engagement increases with retention and network effects
- 15% of MAU concurrent: Realistic at maturity (1M+ MAU), not at MVP

**4. FastAPI at Scale**
- Viable for 150K concurrent with proper optimization
- Critical: Must use async drivers (asyncpg, aioredis, httpx)
- Cost premium: 30-50% vs Node.js (acceptable trade-off for AI/ML benefits)
- Python GIL: Non-issue for I/O-bound workloads (async/await pattern)

---

**Review Completed:** 2026-02-02
**Review Status:** ✅ Scalability validation complete with critical updates implemented
**Research Quality:** High confidence (3 parallel agents, industry benchmarks, case studies)
**Next Actions:**
1. Update Section 14 (Cost Estimation) with revised infrastructure costs
2. Review Section 7 (Database Schema) for sharding simplification
3. Review Section 13 (Implementation Roadmap) for timeline validation

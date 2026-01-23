# Section 6: Technical Architecture - Questions & Answers

**Section:** Technical Architecture
**Date:** 2026-01-20
**Status:** Completed
**Questions Asked:** 9
**Critical Conflicts Resolved:** 2 (Mobile framework, Backend framework)

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

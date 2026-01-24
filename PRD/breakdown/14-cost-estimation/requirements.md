# Section 14: Cost Estimation - Requirements

**Section**: Cost Estimation
**Section ID**: 14-cost-estimation
**Generated**: 2026-01-24
**Status**: Completed

---

## Requirements Overview

This section defines the comprehensive budget for LIVVLY Voice Dating App MVP development and launch, covering infrastructure, services, legal compliance, translation, marketing, and operational costs for the 5-month timeline (20 weeks).

**Total Requirements**: 8
**Priority Breakdown**:
- P0 (Critical): 3
- P1 (High): 3
- P2 (Medium): 2

---

## Functional Requirements

### REQ-COE-001: Updated Budget Framework (5-Month Timeline)
**ID**: REQ-COE-001
**Priority**: P0 (Critical)
**Type**: Financial
**Status**: Approved

**Description**:
The budget must be updated from the PRD's 6-month estimate to align with the approved 5-month (20-week) MVP timeline from Section 13, with team costs removed (shadowmarket team is internal) and missing services added (ClearTax, AWS Rekognition, translation, dev tools).

**Rationale**:
- **Timeline**: Section 13 approved 16-20 weeks (5 months max) vs PRD's 6 months
- **Team**: Shadowmarket agent team is internal (no hiring cost)
- **Services**: Approved scope requires additional services not in PRD budget
- **Platform Share**: 15-25% vs PRD's 38.5% affects revenue projections

**Updated Budget Breakdown**:

**1. Infrastructure (5 months): ₹4,05,000**
- AWS (servers, RDS, ElastiCache): ₹15K/month x 5 = ₹75,000
- Agora (voice/video): ₹20K x 3 months + ₹40K x 2 months = ₹1,40,000
- Firebase (auth, push, analytics): ₹5K x 5 = ₹25,000
- Redis Cluster: ₹3K x 5 = ₹15,000
- CDN (CloudFront): ₹5K x 5 = ₹25,000
- **Dev Tools** (Jira, Figma, GitHub, Sentry): ₹25K x 5 = ₹1,25,000

**2. Third-Party Services (5 months): ₹1,42,500**
- SMS/OTP (MSG91): ₹10K x 5 = ₹50,000
- Karza KYC API: ₹5K x 5 = ₹25,000
- n8n self-hosted: ₹2K x 5 = ₹10,000
- **ClearTax API** (TDS automation): ₹1.5K x 5 = ₹7,500
- **AWS Rekognition** (AI age verification): ₹10K x 5 = ₹50,000
- Razorpay: Variable (2% of GMV, deducted from transactions)

**3. One-Time Costs: ₹1,13,500**
- Legal & Compliance: ₹53,500
  - Company Registration (Pvt Ltd): ₹15,000
  - GST Registration: ₹3,000
  - TAN Registration: ₹500
  - Legal Documents (ToS, Privacy, Creator Agreement): ₹25,000
  - Trademark Application: ₹10,000
- **Translation** (4 languages, 500 strings): ₹60,000

**4. Marketing (Week 17-20 Launch Phase): ₹2,00,000**
- Launch marketing campaign (1 month intensive):
  - Google Ads (UAC): ₹60,000
  - Facebook/Instagram Ads: ₹50,000
  - Influencer Marketing: ₹60,000
  - ASO Tools & PR: ₹30,000

**5. Contingency (10%): ₹86,100**

**Total Budget: ₹9,47,100 (approx ₹9.5 Lakhs)**

**Acceptance Criteria**:
- [ ] Budget approved by stakeholders (₹9.5L vs PRD's ₹17L)
- [ ] All line items mapped to requirements (infrastructure for REQ-TA-*, services for REQ-FS-*, etc.)
- [ ] Contingency buffer adequate for identified risks (10% = ₹86K)
- [ ] Monthly burn rate tracked: Months 1-4 avg ₹1.5L/month, Month 5 peak ₹3L (marketing + infra)

**Dependencies**:
- REQ-IR-001 (5-month timeline from Section 13)
- REQ-IR-004 (Shadowmarket agent team structure)
- REQ-LC-* (Legal & compliance one-time costs)
- REQ-TA-* (Infrastructure requirements)

**Notes**:
- **Team costs removed**: Shadowmarket team is internal, no ₹8.7L hiring cost
- **Marketing timing**: Concentrated in Week 17-20 (launch phase) vs PRD's Month 3-6 spread
- **Video calls impact**: Agora cost increases from ₹20K to ₹40K/month in Months 4-5 (Phase 2 video support)

---

### REQ-COE-002: Infrastructure Budget with Dev Tools
**ID**: REQ-COE-002
**Priority**: P0 (Critical)
**Type**: Financial
**Status**: Approved

**Description**:
Infrastructure budget must include development tooling costs (Jira/Linear, Figma, GitHub Advanced, Sentry) at ₹25K/month for 5 months (₹1.25L total) to support shadowmarket agent team operations.

**Rationale**:
Even though team costs are removed (internal team), the project requires dedicated tools and licenses:
- **Project Management**: Jira or Linear (₹5K/month for team seats)
- **Design**: Figma Professional (₹3K/month for collaboration)
- **Version Control**: GitHub Advanced Security (₹2K/month)
- **Monitoring**: Sentry Error Tracking (₹5K/month)
- **Dev/Staging Environments**: Dedicated AWS environments (₹10K/month)

**Breakdown**:
| Tool | Monthly Cost | 5-Month Total | Purpose |
|------|-------------|---------------|---------|
| Jira/Linear | ₹5,000 | ₹25,000 | Sprint planning, milestone tracking (REQ-IR-005) |
| Figma Pro | ₹3,000 | ₹15,000 | UI/UX collaboration, design handoff |
| GitHub Advanced | ₹2,000 | ₹10,000 | Code security scanning, advanced CI/CD |
| Sentry | ₹5,000 | ₹25,000 | Error tracking, performance monitoring |
| Dev/Staging AWS | ₹10,000 | ₹50,000 | Separate dev and staging environments |
| **Total** | **₹25,000** | **₹1,25,000** | |

**Acceptance Criteria**:
- [ ] All tools provisioned by Week 1 Day 1
- [ ] Team onboarded to tools (Jira sprint setup, Figma design system, Sentry projects)
- [ ] Dev and staging environments isolated from production
- [ ] Tool costs tracked separately from production infrastructure

**Dependencies**:
- REQ-IR-004 (Shadowmarket team structure)
- REQ-IR-005 (Weekly milestone tracking - requires Jira/Linear)
- REQ-TA-015 (Monitoring and alerting - requires Sentry)

**Notes**:
This is an incremental cost for the project. Shadowmarket team may have some tools already, but this budget ensures dedicated project resources.

---

### REQ-COE-003: API Services Budget (ClearTax + AWS Rekognition)
**ID**: REQ-COE-003
**Priority**: P1 (High)
**Type**: Financial
**Status**: Approved

**Description**:
Budget must include two critical API services approved in previous sections but missing from PRD:
1. **ClearTax API** (₹7,500 for 5 months) - TDS automation from Section 12
2. **AWS Rekognition** (₹50,000 for 5 months) - AI age verification from Section 5

**Rationale**:
**ClearTax API**:
- Section 12 (REQ-LC-006) approved automated TDS filing
- Cost: ₹1,500/month (₹18,000/year) vs manual filing cost ₹40,000/year
- Saves ₹22,000/year + reduces compliance risk
- Required for quarterly 26Q filing and annual 16A certificate generation

**AWS Rekognition**:
- Section 5 (REQ-FS-024) approved AI-enhanced age verification
- Cost: ₹10,000/month (approx 20,000 face detections/month @ ₹0.50 per detection)
- Complements self-declaration with face age estimation
- Reduces platform liability for underage users

**Budget Impact**:
| Service | Monthly Cost | 5-Month Total | Annual Cost |
|---------|-------------|---------------|-------------|
| ClearTax API | ₹1,500 | ₹7,500 | ₹18,000 |
| AWS Rekognition | ₹10,000 | ₹50,000 | ₹1,20,000 |
| **Total** | **₹11,500** | **₹57,500** | **₹1,38,000** |

**Acceptance Criteria**:
- [ ] ClearTax API account created and integrated by Week 10
- [ ] AWS Rekognition integrated in user registration flow by Week 15
- [ ] API usage monitored (Rekognition cost per user verified <₹0.50)
- [ ] TDS automation tested in Q4 2026 filing cycle

**Dependencies**:
- REQ-LC-006 (Automated TDS filing)
- REQ-FS-024 (AI-enhanced age verification)
- REQ-API-* (API integration points)

**Notes**:
- ClearTax API saves manual filing cost (₹40K/year) and accountant fees
- AWS Rekognition cost scales with user acquisition (budget assumes 20K signups in 5 months)

---

### REQ-COE-004: Translation Budget (₹60K One-Time)
**ID**: REQ-COE-004
**Priority**: P1 (High)
**Type**: Financial
**Status**: Approved

**Description**:
Budget ₹60,000 for professional translation of approximately 500 UI strings across 4 languages (Tamil, Telugu, Kannada, Hindi), as approved in Section 13 (REQ-IR-003) for all 5 languages at launch.

**Rationale**:
- Section 13 approved "All 5 languages at launch" strategy
- Professional translation ensures cultural appropriateness and quality
- Estimated 500 UI strings (buttons, labels, error messages, onboarding flow, legal disclosures)
- 4 target languages: Tamil, Telugu, Kannada, Hindi (English is source language)
- Cost: ₹15,000 per language for professional localization

**Budget Breakdown**:
| Language | String Count | Cost per String | Total Cost |
|----------|-------------|----------------|------------|
| Tamil | 500 | ₹30 | ₹15,000 |
| Telugu | 500 | ₹30 | ₹15,000 |
| Kannada | 500 | ₹30 | ₹15,000 |
| Hindi | 500 | ₹30 | ₹15,000 |
| **Total** | | | **₹60,000** |

**Deliverables**:
- Translated JSON files for Flutter i18n framework
- Glossary for domain-specific terms ("coins", "live room", "voice bio", "creator")
- Native speaker review and QA feedback
- Ongoing translation support for new strings (Weeks 2-20)

**Acceptance Criteria**:
- [ ] Professional translators engaged by Week 2
- [ ] Initial translation batch (300 core strings) completed by Week 4
- [ ] Full 500 strings translated by Week 16 (before beta)
- [ ] Native speaker QA review completed during beta (Week 17-18)
- [ ] Translation quality score ≥4/5 from beta users

**Dependencies**:
- REQ-IR-003 (Multi-language implementation strategy)
- REQ-FS-001 (Language switcher UI)
- REQ-DB-002 (JSONB i18n_content schema)

**Notes**:
- Alternative: Machine translation (Google Translate) + manual review would cost ₹20K but lower quality
- Translation is one-time cost for MVP; ongoing costs for new features post-launch

---

### REQ-COE-005: Agora Budget Increase for Video Calls
**ID**: REQ-COE-005
**Priority**: P1 (High)
**Type**: Financial
**Status**: Approved

**Description**:
Increase Agora budget from ₹20K/month (audio-only) to ₹40K/month starting Month 4 to support video calling feature approved in Section 5 (Phase 2, Weeks 13-16).

**Rationale**:
- PRD budget: ₹20K/month for audio calls only
- Section 5 (REQ-FS-015) approved video calls in Phase 2
- Video calling costs 3-4x more than audio due to higher bandwidth and processing
- Timeline: Video integration in Weeks 13-16 (Months 4-5)

**Updated Budget**:
| Period | Service Type | Monthly Cost | Total |
|--------|-------------|-------------|-------|
| Months 1-3 | Audio calls only | ₹20,000 | ₹60,000 |
| Months 4-5 | Audio + Video calls | ₹40,000 | ₹80,000 |
| **5-Month Total** | | | **₹1,40,000** |

**Cost Assumptions**:
- Audio call: ₹1.50 per minute (Agora pricing)
- Video call: ₹5.00 per minute (Agora HD video pricing)
- Expected usage in Months 4-5:
  - Audio: 8,000 minutes/month = ₹12,000
  - Video: 5,000 minutes/month = ₹25,000
  - Buffer: ₹3,000
  - **Total**: ₹40,000/month

**Acceptance Criteria**:
- [ ] Agora account upgraded to support video by Week 12 (before Phase 2)
- [ ] Video call quality benchmarks met (720p @ 30fps, <200ms latency)
- [ ] Usage monitoring dashboard tracks audio vs video minutes
- [ ] Cost per minute validated against Agora pricing (alert if >₹6/min for video)

**Dependencies**:
- REQ-FS-015 (Video call support in Phase 2)
- REQ-TA-004 (Agora SDK integration)
- REQ-IR-002 (Phased development - video in Phase 2)

**Notes**:
- Budget assumes 30% of calls switch to video in Months 4-5
- Post-launch video adoption may be higher, requiring budget adjustment in Month 6+

---

## Non-Functional Requirements

### REQ-COE-006: Revenue Projections (15-25% Platform Share)
**ID**: REQ-COE-006
**Priority**: P0 (Critical)
**Type**: Financial
**Status**: Approved

**Description**:
Revenue projections must be updated from PRD's 38.5% platform share to the approved 15-25% tiered creator share model (75-85% creator, 15-25% platform) from Sections 4, 10, and 12.

**Rationale**:
- PRD shows 38.5% platform commission (61.5% creator)
- All previous sections approved tiered model:
  - 25% platform (75% creator) - New creators
  - 20% platform (80% creator) - ₹50K+/month earners
  - 15% platform (85% creator) - ₹2L+/month earners
- Lower platform share extends break-even timeline but builds creator loyalty

**Updated Revenue Projections (First 6 Months)**:

Assumptions:
- Average platform share: 23% (weighted average of tiered model)
- Users grow from 1,000 (Month 1) to 50,000 (Month 6)
- Paying users: 8% of total users
- Average spend per paying user: ₹200-₹450 (increasing as engagement grows)

| Month | Users | Paying (8%) | Avg Spend | GMV | Platform Share (23%) | Platform Revenue |
|-------|-------|-------------|-----------|-----|---------------------|------------------|
| 1 | 1,000 | 80 | ₹200 | ₹16,000 | 23% | ₹3,680 |
| 2 | 3,000 | 240 | ₹250 | ₹60,000 | 23% | ₹13,800 |
| 3 | 8,000 | 640 | ₹300 | ₹1,92,000 | 23% | ₹44,160 |
| 4 | 15,000 | 1,200 | ₹350 | ₹4,20,000 | 23% | ₹96,600 |
| 5 | 30,000 | 2,400 | ₹400 | ₹9,60,000 | 23% | ₹2,20,800 |
| 6 | 50,000 | 4,000 | ₹450 | ₹18,00,000 | 23% | ₹4,14,000 |
| **Total (6 months)** | | | | **₹34,48,000** | | **₹7,93,040** |

**Break-Even Analysis**:
- **Total Costs (5 months)**: ₹9,47,100
- **Revenue by Month 5**: ₹3,78,040 (Months 1-5)
- **Revenue by Month 6**: ₹7,93,040 (Months 1-6)
- **Remaining deficit at Month 6**: ₹9,47,100 - ₹7,93,040 = **₹1,54,060**
- **Break-Even**: **Month 7-8** (additional ₹1.5-2L revenue needed)

**Comparison to PRD**:
- **PRD (38.5% share)**: Break-even Month 5-6, revenue ₹13L+ by Month 6
- **Approved (23% share)**: Break-even Month 7-8, revenue ₹7.9L by Month 6
- **Impact**: 2-month delay in break-even, but stronger creator ecosystem

**Acceptance Criteria**:
- [ ] Revenue projections presented to stakeholders with 7-8 month break-even
- [ ] Monthly revenue tracking dashboard set up
- [ ] Tier migration monitored (% of creators in 75/80/85 tiers)
- [ ] GMV growth targets met (Month 6: ₹18L GMV)

**Dependencies**:
- REQ-RM-002 (Tiered creator revenue share model)
- REQ-CP-007 (Platform commission structure)
- REQ-COE-007 (8-10 month break-even acceptance)

**Notes**:
- Revenue model prioritizes creator satisfaction over short-term profitability
- VIP subscriptions and gift commissions (not in projections) may accelerate break-even

---

### REQ-COE-007: Break-Even Timeline (8-10 Months Acceptable)
**ID**: REQ-COE-007
**Priority**: P2 (Medium)
**Type**: Financial
**Status**: Approved

**Description**:
Accept extended break-even timeline of 8-10 months (vs PRD's 5-6 months) due to creator-friendly revenue share model (15-25% platform share vs PRD's 38.5%).

**Rationale**:
- Lower platform commission (15-25% vs 38.5%) reduces monthly revenue by ~40%
- Trade-off: Longer runway to profitability, but higher creator satisfaction and retention
- Strategic decision: Build sustainable creator ecosystem over short-term profit

**Financial Runway**:
- **Months 1-5 (MVP Development)**: -₹9.47L (costs) + ₹3.78L (revenue) = -₹5.69L net
- **Month 6**: +₹4.14L revenue, net loss reduced to -₹1.55L cumulative
- **Months 7-8**: Additional ₹4-5L revenue, cumulative break-even achieved
- **Month 9-10**: Profitability, positive cash flow

**Funding Requirements**:
- **Initial Investment**: ₹10L to cover 5-month MVP development and first month operations
- **Runway Buffer**: Additional ₹2-3L for Months 6-8 before break-even
- **Total Funding Needed**: ₹12-13L to reach profitability

**Acceptance Criteria**:
- [ ] Stakeholders approve 8-10 month break-even timeline
- [ ] Funding secured for ₹12-13L initial investment
- [ ] Monthly burn rate monitored and reported
- [ ] Contingency plan if break-even extends beyond Month 10 (fundraising, cost cuts, revenue optimization)

**Dependencies**:
- REQ-COE-006 (Revenue projections with 23% platform share)
- REQ-RM-002 (Tiered creator revenue model)

**Notes**:
- Alternative: Increase platform share to 30-35% for faster break-even (Month 6-7), but risks creator churn
- Recommended: Maintain creator-friendly model, optimize for sustainable growth

---

### REQ-COE-008: Marketing Budget (Week 17-20 Launch Phase)
**ID**: REQ-COE-008
**Priority**: P2 (Medium)
**Type**: Financial
**Status**: Approved

**Description**:
Allocate ₹2L marketing budget for Week 17-20 launch phase (Month 5), focused on beta user acquisition, influencer campaigns, app store optimization, and launch PR.

**Rationale**:
- PRD shows marketing starting Month 3 with ₹85K/month for 4 months (₹3.4L total)
- Section 13 approved marketing starting Week 17 (launch phase) instead of early-stage
- Concentrated budget in 1-month intensive campaign for maximum launch impact
- Beta testing (Week 17-18) requires user acquisition budget

**Budget Breakdown**:
| Channel | Allocation | Purpose |
|---------|-----------|---------|
| Google Ads (UAC) | ₹60,000 | App install campaigns targeting South India, 5-language targeting |
| Facebook/Instagram Ads | ₹50,000 | Creator recruitment ads, testimonial campaigns, retargeting |
| Influencer Marketing | ₹60,000 | 5-10 micro-influencers (10K-50K followers) in Tamil, Telugu, Kannada markets |
| ASO & PR | ₹30,000 | App Store Optimization tools, press release distribution, media outreach |
| **Total** | **₹2,00,000** | |

**Campaign Timeline**:
- **Week 17 (Day 1-7)**: Beta user recruitment (target: 200 signups, 50 creators)
  - ₹50K spend for targeted ads in 5 language regions
- **Week 18-19 (Day 8-14)**: Beta feedback collection, testimonial capture
  - ₹30K influencer seeding and content creation
- **Week 20 (Day 15-28)**: Launch PR and user acquisition
  - ₹1.2L intensive app install campaigns + PR push

**Acceptance Criteria**:
- [ ] Marketing plan finalized by Week 16
- [ ] Creatives (ad copy, visuals) ready in all 5 languages by Week 16
- [ ] Influencer partnerships confirmed by Week 16 (contracts, content calendar)
- [ ] Beta recruitment achieves 200+ signups in Week 17
- [ ] Launch week achieves 2,000+ app installs (₹100 CPI)

**Dependencies**:
- REQ-IR-007 (Beta testing program Week 17-18)
- REQ-IR-008 (Launch readiness Week 20)
- REQ-IR-003 (All 5 languages at launch)

**Notes**:
- Post-launch marketing (Month 6+) requires separate budget allocation
- CPI target: ₹100 per install (₹2L budget = 2,000 installs in Month 5)

---

## Budget Summary Table

| Category | PRD Budget (6 months) | Updated Budget (5 months) | Variance | Notes |
|----------|----------------------|---------------------------|----------|-------|
| **Team** | ₹8,70,000 | ₹0 | -₹8,70,000 | Shadowmarket team is internal |
| **Infrastructure** | ₹2,88,000 | ₹4,05,000 | +₹1,17,000 | Added dev tools (₹1.25L), video support (+₹20K Agora) |
| **Services** | ₹1,02,000 | ₹1,42,500 | +₹40,500 | Added ClearTax (₹7.5K), AWS Rekognition (₹50K) |
| **Legal** | ₹53,500 | ₹53,500 | ₹0 | Unchanged |
| **Translation** | ₹0 | ₹60,000 | +₹60,000 | Professional translation for 4 languages |
| **Marketing** | ₹3,40,000 (4 months) | ₹2,00,000 (1 month) | -₹1,40,000 | Concentrated in launch phase (Week 17-20) |
| **Contingency** | ₹1,55,350 | ₹86,100 | -₹69,250 | 10% of reduced subtotal |
| **TOTAL** | **₹17,08,850** | **₹9,47,100** | **-₹7,61,750** | 44% cost reduction (team removal) |

**Rounded Total**: **₹9.5 Lakhs**

---

## Cross-References

**Related Sections**:
- Section 04: Revenue Model (REQ-RM-002: Tiered creator share)
- Section 05: Feature Specifications (REQ-FS-015: Video calls, REQ-FS-024: AI age verification)
- Section 10: Creator Payout System (REQ-CP-007: Platform commission structure)
- Section 12: Legal & Compliance (REQ-LC-006: Automated TDS filing)
- Section 13: Implementation Roadmap (REQ-IR-001: 5-month timeline, REQ-IR-003: Translation, REQ-IR-004: Shadowmarket team)

**External Dependencies**:
- Funding secured for ₹12-13L (₹9.5L development + ₹2.5L runway to Month 8 break-even)
- Shadowmarket team capacity confirmed for 5-month commitment
- Payment gateway approvals (Razorpay onboarding, KYC clearance)
- Third-party API access (ClearTax, AWS Rekognition, Karza, Agora)

---

## Notes

**Key Budget Decisions**:
1. **Team Costs Removed**: Shadowmarket agent team is internal, no ₹8.7L hiring cost (44% budget reduction)
2. **Creator-Friendly Revenue Model**: 15-25% platform share vs PRD's 38.5% extends break-even to Month 8-10 but builds sustainable ecosystem
3. **Comprehensive Scope**: Added services (ClearTax, AWS Rekognition, translation, dev tools) increase operational costs by ₹2.2L
4. **Launch-Focused Marketing**: Concentrated ₹2L in Week 17-20 vs PRD's ₹3.4L spread across 4 months

**Risk Mitigation**:
- **Budget Overrun**: 10% contingency (₹86K) covers unexpected costs
- **Revenue Shortfall**: 8-10 month break-even timeline assumes conservative growth; actual may be faster with successful launch
- **Scope Creep**: All features mapped to requirements; any new features require budget reallocation or increase

**Post-Launch Costs** (not in this budget):
- Month 6+ marketing: ₹1-1.5L/month for sustained user acquisition
- Customer support: ₹30-50K/month (hire 2 support agents after 10K users)
- Infrastructure scaling: AWS costs may double (₹30K/month) as user base grows beyond 50K
- Feature enhancements: Budget separately for Phase 3 features post-launch

---

**Generated by**: PRD Breakdown Skill
**Section Status**: Completed
**Next Section**: 15-success-metrics

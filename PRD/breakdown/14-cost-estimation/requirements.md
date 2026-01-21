# Requirements: Cost Estimation

**Section**: 14-cost-estimation
**Source Lines**: 2826-2907
**Generated**: 2026-01-20
**Status**: Complete

---

> **Note**: Costs adjusted to reflect actual stack (Supabase vs AWS, n8n deferred).

---

## Budget Planning Requirements

### REQ-COE-001: Development Team Budget
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Allocate budget for development team over 6-month initial period.

**Rationale**: Team costs are largest expense; must be planned accurately.

**Acceptance Criteria**:
- Flutter Developer (1 FTE): ₹60,000/month = ₹3,60,000
- Backend Developer (1 FTE): ₹50,000/month = ₹3,00,000
- UI/UX Designer (part-time): ₹20,000/month = ₹1,20,000
- QA Tester (part-time): ₹15,000/month = ₹90,000
- **Total Team**: ₹8,70,000 for 6 months
- Monthly burn rate: ₹1,45,000

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Rates for Coimbatore/remote market. Can adjust based on actual hires.

---

### REQ-COE-002: Infrastructure Budget
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Allocate budget for cloud infrastructure and services.

**Rationale**: Infrastructure must be sized for expected growth.

**Acceptance Criteria**:
- Supabase Pro: ₹2,500/month = ₹15,000 (adjusted from AWS ₹90K)
- Agora (voice/video): ₹20,000/month = ₹1,20,000
- Firebase (auth, push): ₹5,000/month = ₹30,000
- CDN (media): ₹5,000/month = ₹30,000
- **Total Infrastructure**: ₹1,95,000 for 6 months
- Original PRD estimate: ₹2,88,000
- Savings: ₹93,000 via Supabase

**Dependencies**: REQ-TA-001
**Related Design Decisions**: DD-TA-001, DD-TA-002

**Notes**: Supabase includes PostgreSQL, Redis caching, and realtime subscriptions.

---

### REQ-COE-003: Third-Party Services Budget
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Budget for external service integrations.

**Rationale**: Essential services for core functionality.

**Acceptance Criteria**:
- SMS/OTP (MSG91): ₹10,000/month = ₹60,000
- Razorpay: 2% of GMV (variable)
- KYC verification (Karza/Digio): ₹5,000/month = ₹30,000
- n8n: ₹0 (deferred to Phase 2)
- **Total Services**: ₹90,000 for 6 months
- Original PRD: ₹1,02,000 (included n8n)

**Dependencies**: REQ-TA-007, REQ-TA-009
**Related Design Decisions**: DD-TA-005, DD-FS-002

**Notes**: Razorpay fees vary with GMV; estimated separately.

---

### REQ-COE-004: Legal & Compliance Budget
**Priority**: P0 (Critical)
**Type**: Business

**Description**: One-time legal and compliance costs.

**Rationale**: Required for legitimate business operations.

**Acceptance Criteria**:
- Company Registration: ₹15,000
- GST Registration: ₹3,000
- TAN Registration: ₹500
- Legal Documents (ToS, Privacy, Creator Agreement): ₹25,000
- Trademark Application: ₹10,000
- **Total Legal**: ₹53,500 (one-time)

**Dependencies**: REQ-LC-001 through REQ-LC-004
**Related Design Decisions**: None

**Notes**: Legal document costs include professional drafting.

---

### REQ-COE-005: Marketing Budget
**Priority**: P1 (High)
**Type**: Business

**Description**: Marketing spend for months 3-6 (post-beta launch).

**Rationale**: User acquisition requires paid marketing.

**Acceptance Criteria**:
- Google Ads (UAC): ₹30,000/month = ₹1,20,000
- Facebook/Instagram: ₹20,000/month = ₹80,000
- Influencer Marketing: ₹30,000/month = ₹1,20,000
- ASO Tools: ₹5,000/month = ₹20,000
- **Total Marketing**: ₹3,40,000 for 4 months
- Monthly marketing burn: ₹85,000

**Dependencies**: REQ-IR-009, REQ-IR-010
**Related Design Decisions**: None

**Notes**: Focus on Tamil Nadu market initially.

---

### REQ-COE-006: Contingency Budget
**Priority**: P1 (High)
**Type**: Business

**Description**: Reserve 10% contingency for unexpected costs.

**Rationale**: Buffer for unforeseen expenses and overruns.

**Acceptance Criteria**:
- Contingency: 10% of total planned spend
- Original estimate: ₹1,55,350
- Adjusted estimate: ~₹1,35,000
- Covers: delays, rate increases, additional resources

**Dependencies**: REQ-COE-001 through REQ-COE-005
**Related Design Decisions**: None

**Notes**: Access requires founder approval.

---

### REQ-COE-007: Total Budget Summary
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Consolidated 6-month budget requirement.

**Rationale**: Clear total investment needed for planning.

**Acceptance Criteria**:
- Team (6 months): ₹8,70,000
- Infrastructure (6 months): ₹1,95,000 (adjusted)
- Services (6 months): ₹90,000 (adjusted)
- Legal (one-time): ₹53,500
- Marketing (4 months): ₹3,40,000
- Contingency (10%): ₹1,35,000
- **TOTAL ADJUSTED**: ₹15,83,500
- Original PRD estimate: ₹17,08,850
- **Savings**: ₹1,25,350 from stack optimization

**Dependencies**: REQ-COE-001 through REQ-COE-006
**Related Design Decisions**: DD-TA-001, DD-TA-005

**Notes**: Does not include Razorpay transaction fees (variable).

---

## Revenue Tracking Requirements

### REQ-COE-008: Revenue Tracking System
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Track actual revenue against projections monthly.

**Rationale**: Financial visibility for sustainability planning.

**Acceptance Criteria**:
- Track monthly: Users, Paying Users %, Avg Spend, GMV
- Calculate: Platform Share (55% minus Razorpay 2% = 53%)
- Note: PRD shows 38.5% but actual is 55% platform share
- Month 6 target: 50,000 users, ₹18L GMV
- Dashboard or spreadsheet updated weekly
- Variance analysis monthly

**Dependencies**: REQ-DB-006, REQ-SM-001
**Related Design Decisions**: None

**Notes**: Platform share is 55% (per DD from Section 04), not 38.5% as in PRD table.

---

### REQ-COE-009: Break-Even Analysis
**Priority**: P1 (High)
**Type**: Business

**Description**: Track progress toward break-even milestone.

**Rationale**: Sustainability indicator for investors and planning.

**Acceptance Criteria**:
- Break-even target: Month 5-6
- Calculate monthly: Revenue vs Burn Rate
- Track cumulative profit/loss
- Alert if >20% variance from projections
- Update projections quarterly

**Dependencies**: REQ-COE-008
**Related Design Decisions**: None

**Notes**: Revenue projections per PRD table (adjusted for correct platform share).

---

### REQ-COE-010: Variable Cost Tracking
**Priority**: P1 (High)
**Type**: Business

**Description**: Track variable costs that scale with usage.

**Rationale**: Unit economics visibility for scaling decisions.

**Acceptance Criteria**:
- Agora: Per-minute voice/video cost
- Razorpay: 2% of all transactions
- SMS: Per-OTP cost
- KYC: Per-verification cost
- Calculate: Cost per user, Cost per paying user
- Alert if unit economics deteriorate

**Dependencies**: REQ-COE-002, REQ-COE-003
**Related Design Decisions**: None

**Notes**: Critical for pricing decisions.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-COE-001 | Development Team Budget | P0 | Business |
| REQ-COE-002 | Infrastructure Budget | P0 | Business |
| REQ-COE-003 | Third-Party Services Budget | P0 | Business |
| REQ-COE-004 | Legal & Compliance Budget | P0 | Business |
| REQ-COE-005 | Marketing Budget | P1 | Business |
| REQ-COE-006 | Contingency Budget | P1 | Business |
| REQ-COE-007 | Total Budget Summary | P0 | Business |
| REQ-COE-008 | Revenue Tracking System | P0 | Business |
| REQ-COE-009 | Break-Even Analysis | P1 | Business |
| REQ-COE-010 | Variable Cost Tracking | P1 | Business |

**Total Requirements**: 10
**P0 (Critical)**: 6
**P1 (High)**: 4

---

## Budget Summary Table

| Category | PRD Estimate | Adjusted | Savings |
|----------|-------------|----------|---------|
| Team | ₹8,70,000 | ₹8,70,000 | - |
| Infrastructure | ₹2,88,000 | ₹1,95,000 | ₹93,000 |
| Services | ₹1,02,000 | ₹90,000 | ₹12,000 |
| Legal | ₹53,500 | ₹53,500 | - |
| Marketing | ₹3,40,000 | ₹3,40,000 | - |
| Contingency | ₹1,55,350 | ₹1,35,000 | ₹20,350 |
| **TOTAL** | **₹17,08,850** | **₹15,83,500** | **₹1,25,350** |

# Requirements: Success Metrics

**Section**: 15-success-metrics
**Source Lines**: 2908-3041
**Generated**: 2026-01-20
**Status**: Complete

---

## User Metrics Requirements

### REQ-SM-001: User Growth Tracking
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Track and report user acquisition and growth metrics.

**Rationale**: Core indicator of market traction and product-market fit.

**Acceptance Criteria**:
- Track: Total Downloads (cumulative)
- Track: DAU (Daily Active Users)
- Track: MAU (Monthly Active Users)
- Track: DAU/MAU Ratio (stickiness indicator)
- Targets by milestone:
  - Month 1: 2,000 downloads, 200 DAU, 1,000 MAU
  - Month 3: 15,000 downloads, 2,000 DAU, 8,000 MAU
  - Month 6: 75,000 downloads, 10,000 DAU, 50,000 MAU
  - Month 12: 3,00,000 downloads, 50,000 DAU, 2,00,000 MAU
- Daily and weekly reports automated

**Dependencies**: REQ-TA-012, REQ-TA-014
**Related Design Decisions**: DD-TA-004

**Notes**: Use Firebase Analytics or Mixpanel for tracking.

---

### REQ-SM-002: Engagement Metrics Tracking
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Track user engagement depth metrics.

**Rationale**: Engagement quality indicates product value and monetization potential.

**Acceptance Criteria**:
- Track: Average Session Duration (target: > 15 minutes)
- Track: Sessions per User per Day (target: > 2)
- Track: Calls per Active User (target: > 1/day)
- Track: Messages per Active User (target: > 10/day)
- Track: Room Participation Rate (target: > 30%)
- Segment by user cohort (new, returning, power users)
- Weekly trend analysis

**Dependencies**: REQ-SM-001, REQ-DB-001
**Related Design Decisions**: DD-TA-004

**Notes**: Define "active" as users with at least one action per session.

---

### REQ-SM-003: Retention Tracking
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Track user retention cohorts.

**Rationale**: Retention is key indicator of product stickiness and LTV potential.

**Acceptance Criteria**:
- Track: D1 retention (Day 1 after signup)
- Track: D7 retention (Day 7)
- Track: D30 retention (Day 30)
- Track: D90 retention (quarterly)
- Targets: D1 > 40%, D7 > 20%, D30 > 10%
- Cohort analysis by signup week
- Breakdown by acquisition channel

**Dependencies**: REQ-SM-001
**Related Design Decisions**: None

**Notes**: Retention targets based on social app benchmarks.

---

## Revenue Metrics Requirements

### REQ-SM-004: Revenue Tracking
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Track all revenue-related metrics.

**Rationale**: Revenue is ultimate measure of business viability.

**Acceptance Criteria**:
- Track: GMV (Gross Merchandise Value - total coin purchases)
- Track: Paying Users % (conversion rate)
- Track: ARPU (Average Revenue Per User - all users)
- Track: ARPPU (Average Revenue Per Paying User)
- Targets by milestone:
  - Month 1: ₹16K GMV, 5% paying, ₹16 ARPU
  - Month 3: ₹1.9L GMV, 8% paying, ₹24 ARPU
  - Month 6: ₹18L GMV, 8% paying, ₹36 ARPU
  - Month 12: ₹1Cr GMV, 10% paying, ₹50 ARPU
- Daily revenue dashboard

**Dependencies**: REQ-DB-003, REQ-COE-008
**Related Design Decisions**: None

**Notes**: ARPPU targets: ₹200 → ₹500 over 12 months.

---

### REQ-SM-005: Transaction Analytics
**Priority**: P1 (High)
**Type**: Business

**Description**: Detailed breakdown of transaction types and patterns.

**Rationale**: Understanding spending patterns informs product decisions.

**Acceptance Criteria**:
- Breakdown by type: Coin purchases, Calls, Gifts, Room tips
- Track: Average transaction value
- Track: Transaction frequency per user
- Track: Peak spending hours
- Track: Package popularity distribution
- Weekly spending pattern reports

**Dependencies**: REQ-SM-004, REQ-DB-006
**Related Design Decisions**: None

**Notes**: Identify high-value user segments.

---

## Creator Metrics Requirements

### REQ-SM-006: Creator Growth Tracking
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Track creator acquisition and activity metrics.

**Rationale**: Creators are supply side of marketplace; critical to track.

**Acceptance Criteria**:
- Track: Total Creators (registered)
- Track: Active Creators (weekly activity)
- Track: Creator activity rate (active/total)
- Targets by milestone:
  - Month 1: 20 total, 10 active
  - Month 3: 100 total, 60 active
  - Month 6: 500 total, 300 active
  - Month 12: 2,000 total, 1,200 active
- Track: New creator signups per week

**Dependencies**: REQ-DB-002
**Related Design Decisions**: None

**Notes**: Define "active" as at least 2 hours online per week.

---

### REQ-SM-007: Creator Earnings Tracking
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Track creator earnings distribution and health.

**Rationale**: Creator earnings indicate marketplace health and sustainability.

**Acceptance Criteria**:
- Track: Average Creator Earning (monthly)
- Track: Median Creator Earning (monthly)
- Track: Top Creator Earning (monthly)
- Track: Earnings distribution (percentiles)
- Targets by milestone:
  - Month 1: ₹2K avg, ₹10K top
  - Month 3: ₹5K avg, ₹30K top
  - Month 6: ₹10K avg, ₹1L top
  - Month 12: ₹15K avg, ₹3L top
- Track: Creator payout success rate

**Dependencies**: REQ-SM-006, REQ-DB-005
**Related Design Decisions**: None

**Notes**: Healthy distribution = not too top-heavy.

---

## North Star Metric

### REQ-SM-008: Monthly Active Minutes Tracking
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Track Monthly Active Minutes as primary North Star metric.

**Rationale**: Directly correlates with engagement, revenue, and creator earnings.

**Acceptance Criteria**:
- Calculate: Total minutes of calls (1-to-1 audio + video)
- Calculate: Total minutes of room participation
- Track: Combined Monthly Active Minutes
- Segment by: Caller minutes, Creator minutes, Room minutes
- Target growth: 20% MoM
- Daily tracking with weekly rollups
- Executive dashboard prominence

**Dependencies**: REQ-SM-002, REQ-DB-007
**Related Design Decisions**: None

**Notes**: Primary metric for investor reporting.

---

## Dashboard Requirements

### REQ-SM-009: Admin Analytics Dashboard
**Priority**: P0 (Critical)
**Type**: UX

**Description**: Build comprehensive analytics dashboard for internal use.

**Rationale**: Real-time visibility into business health.

**Acceptance Criteria**:
- Real-time counters: DAU, GMV, Active Calls, Active Creators
- Trend charts: Revenue (7-day), Users (30-day)
- Leaderboards: Top Creators, Top Spenders
- Comparison: Today vs Yesterday, This Week vs Last Week
- Filter by date range
- Export capability (CSV)
- Mobile-responsive design

**Dependencies**: REQ-SM-001 through REQ-SM-008
**Related Design Decisions**: DD-TA-004

**Notes**: Consider Metabase, Grafana, or custom dashboard.

---

### REQ-SM-010: Automated Reporting
**Priority**: P1 (High)
**Type**: Technical

**Description**: Automated daily and weekly metric reports.

**Rationale**: Consistent reporting without manual effort.

**Acceptance Criteria**:
- Daily email: Key metrics summary
- Weekly email: Detailed breakdown with trends
- Monthly email: Full business review
- Recipients configurable
- Include: All KPIs from REQ-SM-001 to REQ-SM-008
- Format: Clean HTML email with charts
- Alert threshold breaches (e.g., DAU drops > 20%)

**Dependencies**: REQ-SM-009
**Related Design Decisions**: DD-TA-005

**Notes**: Implement via scheduled Edge Function.

---

### REQ-SM-011: Alert System
**Priority**: P1 (High)
**Type**: Technical

**Description**: Real-time alerts for metric anomalies.

**Rationale**: Early warning for issues enables quick response.

**Acceptance Criteria**:
- Alert triggers:
  - DAU drops > 20% from previous day
  - Error rate > 1%
  - Payment failure rate > 5%
  - Active creator count drops > 10%
- Alert channels: Email, Slack (Phase 2)
- Alert acknowledgment tracking
- Configurable thresholds

**Dependencies**: REQ-SM-010
**Related Design Decisions**: DD-TA-004

**Notes**: Start with email; add Slack in Phase 2.

---

## Glossary Requirements

### REQ-SM-012: Metric Definitions Documentation
**Priority**: P1 (High)
**Type**: Documentation

**Description**: Document all metric definitions for consistency.

**Rationale**: Consistent definitions ensure aligned understanding.

**Acceptance Criteria**:
- Document all terms:
  - Coins: Virtual currency purchased by users
  - Diamonds: Virtual currency earned by creators
  - GMV: Gross Merchandise Value (total transactions)
  - ARPU: Average Revenue Per User
  - ARPPU: Average Revenue Per Paying User
  - DAU: Daily Active Users
  - MAU: Monthly Active Users
  - TDS: Tax Deducted at Source
  - KYC: Know Your Customer
- Include calculation formulas
- Version controlled

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Include in internal wiki/documentation.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-SM-001 | User Growth Tracking | P0 | Business |
| REQ-SM-002 | Engagement Metrics Tracking | P0 | Business |
| REQ-SM-003 | Retention Tracking | P0 | Business |
| REQ-SM-004 | Revenue Tracking | P0 | Business |
| REQ-SM-005 | Transaction Analytics | P1 | Business |
| REQ-SM-006 | Creator Growth Tracking | P0 | Business |
| REQ-SM-007 | Creator Earnings Tracking | P0 | Business |
| REQ-SM-008 | Monthly Active Minutes Tracking | P0 | Business |
| REQ-SM-009 | Admin Analytics Dashboard | P0 | UX |
| REQ-SM-010 | Automated Reporting | P1 | Technical |
| REQ-SM-011 | Alert System | P1 | Technical |
| REQ-SM-012 | Metric Definitions Documentation | P1 | Documentation |

**Total Requirements**: 12
**P0 (Critical)**: 8
**P1 (High)**: 4

---

## KPI Summary Tables

### User Metrics Targets

| Metric | M1 | M3 | M6 | M12 |
|--------|-----|-----|-----|------|
| Downloads | 2K | 15K | 75K | 300K |
| DAU | 200 | 2K | 10K | 50K |
| MAU | 1K | 8K | 50K | 200K |
| DAU/MAU | 20% | 25% | 20% | 25% |

### Revenue Metrics Targets

| Metric | M1 | M3 | M6 | M12 |
|--------|-----|-----|-----|------|
| GMV | ₹16K | ₹1.9L | ₹18L | ₹1Cr |
| Paying % | 5% | 8% | 8% | 10% |
| ARPU | ₹16 | ₹24 | ₹36 | ₹50 |
| ARPPU | ₹200 | ₹300 | ₹450 | ₹500 |

### Creator Metrics Targets

| Metric | M1 | M3 | M6 | M12 |
|--------|-----|-----|-----|------|
| Total | 20 | 100 | 500 | 2K |
| Active | 10 | 60 | 300 | 1.2K |
| Avg Earning | ₹2K | ₹5K | ₹10K | ₹15K |
| Top Earning | ₹10K | ₹30K | ₹1L | ₹3L |

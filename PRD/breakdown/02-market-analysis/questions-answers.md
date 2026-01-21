# Questions & Answers: Market Analysis

**Section**: 02-market-analysis
**Generated**: 2026-01-19

---

## Session 1: Core Questions

### Q1: Creator Revenue Share Structure

**Question**: The PRD shows competitors give creators 45% of net revenue, but LIVVLY promises 75-85%. Which creator share split should we use for requirements?

**Rationale**: The exact revenue share structure impacts financial modeling, creator acquisition messaging, and payout system design. Need to define the specific tiers.

**Answer**: 75% base, up to 85% for top creators - Tiered system where new creators get 75%, top performers get up to 85%.

**Impact on Requirements**:
- REQ-MA-004 created with tiered structure (75% base to 85% top)
- REQ-MA-005 created for tier visibility in creator dashboard
- Creator Payout section must implement tier calculation logic
- Marketing can advertise "up to 85%" to attract top creators

**Related Requirements**: REQ-MA-004, REQ-MA-005, REQ-ES-005

**Follow-up Actions**:
- Define specific tier criteria (earnings thresholds, rating requirements)
- Document tier progression in Creator Payout section

---

### Q2: App Store Fee Strategy

**Question**: The SWOT mentions 'Platform policy changes (Google/Apple)' as a threat. How should we handle the 30% app store fee?

**Rationale**: 30% app store fee significantly impacts creator payouts and platform margins. Different strategies have different risk/reward profiles.

**Answer**: Offer web purchase option - Allow users to buy coins via website at lower prices (bypasses app store fee).

**Impact on Requirements**:
- REQ-MA-003 created for web purchase portal
- Web purchases should offer 15-20% better value
- Must ensure Google Play policy compliance (cannot directly promote in-app)
- Need web payment integration (UPI, cards, wallets)

**Related Requirements**: REQ-MA-003, REQ-MA-008

**Follow-up Actions**:
- Research Google Play policies on alternative payment mentions
- Design web purchase flow in API Specifications section
- Consider deep linking strategy from app to web

---

## Session 2: Gap Analysis

No additional gaps identified in this section. The PRD provides clear market analysis data.

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| Creator Revenue Share | 75% base, up to 85% for top | High - Affects financial model |
| App Store Fee Strategy | Web purchase option | High - New feature requirement |

**Total Questions**: 2
**Decisions Made**: 2
**Open Items**: 0

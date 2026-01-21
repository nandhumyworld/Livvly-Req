# Questions & Answers: Product Vision

**Section**: 03-product-vision
**Generated**: 2026-01-19

---

## Session 1: Core Questions

### Q1: Matching System Approach

**Question**: The user journey shows 'Match' as an engagement step. What type of matching system should LIVVLY have for MVP?

**Rationale**: The user journey diagram shows "Match" as part of engagement, but doesn't specify the matching mechanism. This impacts both UX complexity and technical requirements.

**Answer**: Basic preference matching - Simple filters (language, age, interests) to surface relevant profiles.

**Impact on Requirements**:
- REQ-PV-012 created for basic preference matching
- Discovery feed will use filter-based prioritization
- No ML/algorithmic matching infrastructure needed for MVP
- Simpler technical implementation, faster time to market

**Related Requirements**: REQ-PV-012, REQ-PV-013

**Follow-up Actions**:
- Define specific preference categories in Feature Specifications
- Plan for algorithmic matching upgrade in Phase 2

---

### Q2: Content Moderation Level

**Question**: The core value 'Safety' mentions robust moderation. What level of content moderation is needed for MVP?

**Rationale**: Moderation approach significantly impacts both safety and operational costs. Need to balance user protection with scalability.

**Answer**: Automated + manual - AI flags suspicious content for human review, plus user reports.

**Impact on Requirements**:
- REQ-PV-005 created with automated flagging + human review model
- Need AI/ML content detection system for text and images
- Need moderation queue and review interface for human moderators
- User reporting system required
- Operational cost for human moderators to be considered

**Related Requirements**: REQ-PV-005, REQ-PV-006

**Follow-up Actions**:
- Define content policy rules in Legal & Compliance section
- Specify moderation automation in N8N Workflows section
- Budget for moderation team in Cost Estimation

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| Matching System | Basic preference matching | Medium - Affects discovery UX |
| Moderation Level | Automated + manual | High - Affects safety & costs |

**Total Questions**: 2
**Decisions Made**: 2
**Open Items**: 0

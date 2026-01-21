# Questions & Answers: Revenue Model

**Section**: 04-revenue-model
**Generated**: 2026-01-19

---

## Session 1: Core Questions

### Q1: Revenue Split Model Selection

**Question**: The PRD shows multiple revenue split models. Which model should be the primary implementation for MVP?

**Rationale**: The PRD presents three different split models (Standard, Agency, High Creator Share). Need to determine which drives the implementation.

**Answer**: High Creator Share (65% of net) - Creator gets 65% of net revenue, which matches the 75-85% promise after tier adjustments.

**Impact on Requirements**:
- REQ-RM-003 set to 65% of net revenue for creators
- REQ-RM-004 set to 35% of net for platform (24.5% of gross)
- Lower platform margin than competitors - requires efficient operations
- Strong competitive positioning for creator acquisition

**Related Requirements**: REQ-RM-003, REQ-RM-004, REQ-MA-004

**Follow-up Actions**:
- Ensure financial model accounts for lower margin
- Update marketing messaging with specific percentages
- Define tier bonuses that can push top creators toward 75-85%

---

### Q2: Subscription Tiers for MVP

**Question**: Which subscription tiers should be available at MVP launch?

**Rationale**: The PRD shows 4 tiers (Free, VIP, Premium, Creator+). Need to balance feature completeness with development scope.

**Answer**: Free + VIP only - Simpler launch with just 2 tiers (Free and VIP at ₹199/month).

**Impact on Requirements**:
- REQ-RM-006 (Free tier) - included
- REQ-RM-007 (VIP tier) - included
- Premium (₹499) and Creator+ (₹999) - deferred to Phase 2
- Simpler subscription management implementation
- Focused value proposition for MVP

**Related Requirements**: REQ-RM-006, REQ-RM-007, REQ-RM-008

**Follow-up Actions**:
- Document Premium and Creator+ as Phase 2 features
- Design VIP benefits to be compelling without Premium tier
- Plan upgrade path when additional tiers launch

---

### Q3: TDS (Tax) Handling

**Question**: Should TDS (Tax Deducted at Source - 10%) be handled by the platform for creator payouts?

**Rationale**: TDS handling has compliance and operational implications. Need to decide responsibility.

**Answer**: Creator responsibility - Platform pays gross amount, creators handle their own tax compliance.

**Impact on Requirements**:
- REQ-RM-012 created with creator tax responsibility
- No TDS deduction infrastructure needed for MVP
- Platform must provide earnings statements for creator tax filing
- Terms of service must clearly state tax responsibility

**Related Requirements**: REQ-RM-012, REQ-CP-xxx

**Follow-up Actions**:
- Include tax responsibility clause in creator terms
- Provide annual earnings summary feature
- Monitor regulatory changes that might require platform TDS

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| Revenue Split Model | High Creator Share (65% of net) | High - Core business model |
| Subscription Tiers | Free + VIP only | Medium - Simplifies MVP |
| TDS Handling | Creator responsibility | Medium - Simplifies operations |

**Total Questions**: 3
**Decisions Made**: 3
**Open Items**: 0

# Questions & Answers: Creator Payout System

**Section**: 10-creator-payout-system
**Generated**: 2026-01-20

---

## Session 1: Payout System Questions

### Q1: Holding Period Configuration

**Question**: The PRD specifies a 7-day holding period before earnings become withdrawable. Should this be configurable or fixed for MVP?

**Rationale**: Holding periods protect against fraud/chargebacks. Shorter periods for trusted creators could improve satisfaction, but adds complexity.

**Answer**: Fixed 7 days (Recommended) - Keep simple - fixed 7-day hold for all creators.

**Impact on Requirements**:
- REQ-CP-002 specifies fixed 7-day hold period
- No need for tiered hold period logic
- Simplifies diamond wallet state machine
- No admin configuration needed for hold periods
- Can be revisited in Phase 2 for trusted creator program

**Related Requirements**: REQ-CP-002, REQ-CP-003

**Follow-up Actions**:
- Implement simple date-based hold calculation
- Daily job to move pending -> available after 7 days
- Consider "trusted creator" tier for Phase 2

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| Holding Period | Fixed 7 days for all | Low - Simplifies logic |

**Total Questions**: 1
**Decisions Made**: 1
**Open Items**: 0

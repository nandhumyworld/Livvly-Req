# Questions & Answers: Coin Economy Design

**Section**: 09-coin-economy-design
**Generated**: 2026-01-20

---

## Session 1: Economy Questions

### Q1: Coin Expiry Implementation

**Question**: The PRD shows coin expiry rules (365 days inactive, 90 days bonus, 30 days promo). Should coin expiry be implemented for MVP or deferred?

**Rationale**: Coin expiry adds complexity (tracking expiry dates, running expiry jobs, user notifications). However, it's important for revenue recognition and preventing liability accumulation.

**Answer**: Implement for MVP - Full expiry system from launch - aligns with PRD.

**Impact on Requirements**:
- REQ-CE-007 created for coin expiry system
- Need coin_type field to track purchased vs bonus vs promo
- Need expiry_at timestamp on coin records or wallet
- Need scheduled job to process expired coins
- Need user notifications before expiry (7 days, 1 day)
- UI must show expiring coins prominently

**Related Requirements**: REQ-CE-007, REQ-CE-008

**Follow-up Actions**:
- Design coin tracking schema (batch vs individual)
- Implement expiry job (pg_cron or Edge Function)
- Add expiry notifications to notification system

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| Coin Expiry | Implement for MVP | Medium - Additional tracking |

**Total Questions**: 1
**Decisions Made**: 1
**Open Items**: 0

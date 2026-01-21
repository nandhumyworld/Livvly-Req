# Questions & Answers: Cost Estimation

**Section**: 14-cost-estimation
**Generated**: 2026-01-20

---

## Pre-Resolved Adjustments

### Infrastructure Cost Adjustments

Per design decisions, some cost items need adjustment:

1. **n8n**: Deferred to Phase 2 (per DD-TA-005)
   - PRD: ₹2,000/month for self-hosted
   - Adjusted: ₹0 for MVP (savings: ₹12,000)

2. **Supabase**: Using Supabase instead of AWS/GCP (per DD-TA-001)
   - PRD: AWS/GCP ₹15,000/month
   - Adjusted: Supabase Pro ₹2,500/month (approx $25)
   - Savings: ~₹75,000 over 6 months

3. **Redis**: Included in Supabase (per DD-TA-001)
   - PRD: ₹3,000/month
   - Adjusted: ₹0 (included in Supabase)
   - Savings: ₹18,000 over 6 months

### Adjusted Total Budget

Original: ₹17,08,850
Savings: ~₹1,05,000
Adjusted: ~₹16,03,850

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| n8n cost | Remove for MVP | Low - ₹12K savings |
| Server cost | Supabase vs AWS | Medium - ₹75K savings |
| Redis cost | Included in Supabase | Low - ₹18K savings |

**Total Questions**: 0 (Pre-resolved via design decisions)
**Decisions Made**: 0
**Open Items**: 0

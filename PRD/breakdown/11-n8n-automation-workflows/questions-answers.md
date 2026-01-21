# Questions & Answers: N8N Automation Workflows

**Section**: 11-n8n-automation-workflows
**Generated**: 2026-01-20

---

## Pre-Resolved Decisions

### Q1: n8n vs Direct Implementation

**Question**: Should we use n8n for workflow automation or implement directly in the backend?

**Rationale**: n8n provides visual workflow building but adds infrastructure complexity. Direct implementation integrates better with Supabase stack.

**Answer**: Direct implementation for MVP (per DD-TA-005)

**Resolution Source**: Design Decision DD-TA-005 from Section 06 (Technical Architecture)

**Impact on Requirements**:
- All workflows converted to Edge Function + pg_cron implementations
- No n8n infrastructure required for MVP
- Workflows implemented as Supabase Edge Functions
- Scheduled tasks via pg_cron extension
- Can migrate to n8n in Phase 2 if needed

**Related Requirements**: All REQ-AW-* requirements

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| n8n vs Direct | Direct implementation | High - Architecture |

**Total Questions**: 1
**Decisions Made**: 1 (Pre-resolved from DD-TA-005)
**Open Items**: 0

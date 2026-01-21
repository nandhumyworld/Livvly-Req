# Questions & Answers: API Specifications

**Section**: 08-api-specifications
**Generated**: 2026-01-20

---

## Session 1: Core API Questions

### Q1: API Technology Stack Reference

**Question**: The API code samples use FastAPI (Python), but we've decided on Node.js + Supabase. Should the API requirements reference the new stack or keep the PRD's FastAPI examples as reference?

**Rationale**: Need to align API documentation with actual implementation stack to avoid confusion during development.

**Answer**: Node.js reference (Recommended) - Requirements should reference Node.js Edge Functions and Supabase - the actual stack being used.

**Impact on Requirements**:
- All API requirements written for Node.js/TypeScript
- Reference Supabase Edge Functions for custom endpoints
- Reference PostgREST for auto-generated CRUD endpoints
- DD-API-001 created for stack migration approach

**Related Requirements**: REQ-API-001, REQ-TA-003

**Follow-up Actions**:
- Translate FastAPI patterns to Edge Function equivalents
- Document Supabase-specific authentication flow
- Update code samples to TypeScript

---

### Q2: Call Billing Model

**Question**: The PRD shows real-time call billing (charge per minute during call). Should billing be real-time or calculated at call end?

**Rationale**: Real-time billing is more complex and requires continuous balance checks during call. End-of-call billing is simpler and proven by competitors like FRND.

**Answer**: End-of-call billing (Recommended) - Calculate total at call end - simpler, fewer transactions, proven by FRND.

**Impact on Requirements**:
- REQ-API-011 specifies end-of-call billing
- No real-time coin deduction during calls
- Reserve minimum balance (3 minutes) at call start
- Single transaction created at call end
- DD-API-002 created for billing approach

**Related Requirements**: REQ-API-010, REQ-API-011, REQ-API-012

**Follow-up Actions**:
- Implement balance reservation at call start
- Create end-call billing Edge Function
- Handle edge cases (disconnect, insufficient balance mid-call)

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| API Stack Reference | Node.js/Supabase | Medium - Documentation alignment |
| Call Billing Model | End-of-call | Medium - Simplifies billing logic |

**Total Questions**: 2
**Decisions Made**: 2
**Open Items**: 0

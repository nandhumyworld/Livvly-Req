# Questions & Answers: Database Schema

**Section**: 07-database-schema
**Generated**: 2026-01-20

---

## Session 1: Core Database Design Questions

### Q1: Soft Delete Strategy

**Question**: The schema uses hard deletes (ON DELETE CASCADE). For audit trail and compliance, should we implement soft deletes with deleted_at timestamps for user-related data?

**Rationale**: Financial applications and compliance (GST, TDS) typically require retaining historical data. Hard deletes would lose audit trail needed for financial reconciliation and dispute resolution.

**Answer**: Yes, soft delete (Recommended) - Add deleted_at column to users, transactions, calls - preserves audit trail for compliance.

**Impact on Requirements**:
- REQ-DB-003 created for soft delete pattern
- All user-related tables need deleted_at column
- RLS policies must filter deleted records
- Cascade deletes replaced with soft delete triggers
- Periodic archival process needed for old soft-deleted data

**Related Requirements**: REQ-DB-003, REQ-DB-011

**Follow-up Actions**:
- Update schema to add deleted_at columns
- Create views for active-only records
- Implement soft delete triggers

---

### Q2: PII Data Encryption

**Question**: The schema stores sensitive data (PAN, bank accounts). What encryption approach should be used for PII data at rest?

**Rationale**: PAN numbers and bank account details are sensitive PII requiring protection. Need to balance security with operational simplicity.

**Answer**: Supabase default encryption (Recommended) - Rely on Supabase's database-level encryption at rest - simpler to manage.

**Impact on Requirements**:
- REQ-DB-012 created for encryption requirements
- Supabase handles encryption transparently
- No application-level encryption needed
- Simpler implementation and maintenance
- Must ensure Supabase encryption is enabled in project settings

**Related Requirements**: REQ-DB-012, REQ-TA-015

**Follow-up Actions**:
- Verify Supabase encryption settings
- Document encryption approach in security policy
- Consider column-level encryption for Phase 2 if regulations require

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| Soft Delete Strategy | Yes, implement soft deletes | Medium - Schema changes |
| PII Encryption | Supabase default encryption | Low - Configuration only |

**Total Questions**: 2
**Decisions Made**: 2
**Open Items**: 0

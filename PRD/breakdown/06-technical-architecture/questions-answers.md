# Questions & Answers: Technical Architecture

**Section**: 06-technical-architecture
**Generated**: 2026-01-19

---

## Session 1: Core Questions

### Q1: Backend Technology Selection

**Question**: The PRD specifies FastAPI (Python) for backend. Is this the confirmed choice, or should we consider alternatives?

**Rationale**: Backend technology choice significantly impacts development speed, team requirements, and long-term maintainability. FastAPI is a strong choice but alternatives may better fit team skills or project constraints.

**Answer**: Node.js + Supabase - Use Supabase BaaS with built-in auth, realtime, and storage, plus Node.js Edge Functions for custom logic.

**Impact on Requirements**:
- REQ-TA-002 created for Supabase as primary backend platform
- REQ-TA-003 created for Node.js Edge Functions
- DD-TA-001 documents the technology change from PRD
- DD-TA-003 defines hybrid API approach (PostgREST + Edge Functions)
- Technical context updated in context.json
- Faster development expected with BaaS approach

**Related Requirements**: REQ-TA-001, REQ-TA-002, REQ-TA-003

**Follow-up Actions**:
- Update all downstream sections to reflect Supabase backend
- Ensure Database Schema section aligns with Supabase patterns
- Update Cost Estimation for Supabase pricing

---

### Q2: n8n Automation Hosting

**Question**: The PRD mentions 'n8n (self-hosted)' for automation. How should n8n be deployed?

**Rationale**: n8n is specified for workflow automation, but self-hosting adds operational complexity. Need to decide if it's critical for MVP.

**Answer**: Defer to later - Build critical automations directly, add n8n in Phase 2.

**Impact on Requirements**:
- REQ-TA-017 created for direct automation implementation
- DD-TA-005 documents deferral decision
- n8n Automation Workflows section (11) should focus on Phase 2 design
- Critical automations (payouts, notifications) built with pg_cron and triggers
- Reduced MVP complexity

**Related Requirements**: REQ-TA-017

**Follow-up Actions**:
- Update Section 11 (N8N Automation Workflows) to reflect Phase 2 scope
- Document which automations are critical for MVP
- Design automation code to be portable to n8n later

---

## Session 2: Technology Change Summary

The stakeholder decisions result in significant technology stack changes from the PRD:

| Component | PRD Specification | Stakeholder Decision | Impact |
|-----------|-------------------|---------------------|--------|
| Backend API | FastAPI (Python) | Node.js + Supabase | Major change |
| Database | PostgreSQL (self-managed) | PostgreSQL (Supabase managed) | Simplified ops |
| Auth | Firebase Auth | Firebase + Supabase Auth | Combined approach |
| Automation | n8n (self-hosted) | Direct implementation (MVP), n8n (Phase 2) | Deferred |
| Cache | Redis 7 | Upstash Redis (serverless) | Aligned with BaaS |
| Monitoring | Grafana + Prometheus | Supabase + Sentry | Simplified |

**Rationale for Changes**:
1. **Supabase over FastAPI**: Faster development, integrated tooling, reduced DevOps
2. **n8n Deferred**: Simplify MVP, critical automations don't need visual workflows

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| Backend Technology | Node.js + Supabase (changed from FastAPI) | High - Major architecture change |
| n8n Hosting | Defer to Phase 2 | Medium - Simplifies MVP |

**Total Questions**: 2
**Decisions Made**: 2
**PRD Changes**: 2 significant technology changes

**Context Updated**: technical_context and notes updated in context.json

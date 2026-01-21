# Design Decisions: Technical Architecture

**Section**: 06-technical-architecture
**Generated**: 2026-01-19
**Status**: Complete

**Note**: Key decision changed from PRD - Backend technology is Node.js + Supabase instead of FastAPI (Python).

---

## DD-TA-001: Backend Technology Stack

**Status**: Accepted (Modified from PRD)

### Context
The PRD specifies FastAPI (Python) for the backend API. Need to evaluate this choice against alternatives considering team skills, development speed, and operational complexity.

### Decision
Use **Node.js + Supabase (BaaS)** instead of FastAPI, with Supabase Edge Functions for custom business logic.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| FastAPI (Python) - PRD spec | High performance, good async | Requires more DevOps, separate from Flutter ecosystem |
| **Node.js + Supabase (chosen)** | Faster development, integrated BaaS, TypeScript across stack | Platform dependency, less control |
| Express.js (self-hosted) | Full control, JavaScript | More operational burden |
| Go/Gin | Extremely fast | Different language, learning curve |

### Rationale
Supabase provides integrated database, auth, storage, and realtime - dramatically reducing development time. Node.js Edge Functions handle custom logic while staying in JavaScript/TypeScript ecosystem (aligns with Flutter team skills). This change prioritizes speed-to-market over maximum control.

### Consequences
- **Positive**: Faster development, less DevOps, integrated tooling
- **Negative**: Platform dependency on Supabase, less customization flexibility
- **Risks**: Supabase outages affect entire backend; cost scales with usage
- **Mitigation**: Design for portability; database schema is standard PostgreSQL

### Related Requirements
REQ-TA-002, REQ-TA-003, REQ-TA-004

### Implementation Notes
- Use Supabase for: Database, Auth (supplement Firebase OTP), Storage, Realtime
- Use Edge Functions for: Revenue calculations, payout logic, Agora token generation
- Use Redis (Upstash) for: Caching, sessions, leaderboards

### References
- Supabase Documentation
- Supabase vs Firebase comparison

---

## DD-TA-002: Database Strategy

**Status**: Accepted

### Context
Need to design database strategy that supports all application entities with proper relationships, performance, and audit capabilities.

### Decision
Use **PostgreSQL via Supabase** with Row Level Security (RLS) for access control, soft deletes for audit trail, and strategic indexing.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **PostgreSQL + RLS (chosen)** | ACID, JSON support, RLS for security | More complex RLS policies |
| PostgreSQL without RLS | Simpler queries | Security handled in app layer |
| MongoDB | Flexible schema | No ACID, harder for financial data |
| Supabase + separate analytics DB | Separation of concerns | More complexity |

### Rationale
PostgreSQL handles both transactional (payments, calls) and analytical (metrics) workloads. RLS provides database-level security, reducing attack surface. Supabase's PostgreSQL is fully managed with automatic backups.

### Consequences
- **Positive**: Strong consistency for financial data, secure by default
- **Negative**: RLS policies add complexity, must be carefully designed
- **Risks**: Complex queries may be slow; RLS can cause unexpected access issues
- **Mitigation**: Thorough testing of RLS policies, proper indexing, query monitoring

### Related Requirements
REQ-TA-004, REQ-TA-011

### Implementation Notes
- Enable RLS on all tables
- Create policies per user role (user, creator, admin)
- Use soft delete (deleted_at timestamp) for all entities
- Implement proper indexes based on query patterns

### References
- Supabase RLS Documentation
- PostgreSQL Performance Tuning

---

## DD-TA-003: API Design Approach

**Status**: Accepted

### Context
Need to decide between auto-generated APIs (Supabase PostgREST) and custom APIs (Edge Functions) for different use cases.

### Decision
Use **hybrid approach**: Supabase auto-generated REST APIs for simple CRUD operations, Edge Functions for complex business logic.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Hybrid (chosen)** | Fast for simple ops, flexible for complex | Two API patterns |
| All Edge Functions | Full control, consistent | Slower development, reinventing wheel |
| All PostgREST | Fast development | Limited for complex logic |
| GraphQL (Supabase) | Flexible queries | Learning curve, complexity |

### Rationale
PostgREST auto-generates REST APIs directly from database schema - perfect for user profiles, listings, etc. Edge Functions handle complex operations like payment processing, call management, and revenue calculations that need server-side logic.

### Consequences
- **Positive**: Fast development for CRUD, full control for complex logic
- **Negative**: Two API patterns to maintain, potential inconsistency
- **Risks**: Confusion about when to use which approach
- **Mitigation**: Clear documentation, consistent naming conventions

### Related Requirements
REQ-TA-010

### Implementation Notes
- PostgREST for: Users, Rooms (read), Gifts catalog, Transactions history
- Edge Functions for: Auth flow, Call start/end, Payment processing, Payout requests
- All APIs under /api/v1 prefix for versioning

### References
- Supabase PostgREST Documentation
- Edge Functions Best Practices

---

## DD-TA-004: Observability Stack

**Status**: Accepted

### Context
Need to implement monitoring and observability for a production application with financial transactions.

### Decision
Use **Supabase built-in monitoring + Sentry for errors + custom dashboard** for business metrics.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Supabase + Sentry + Custom (chosen)** | Balanced, cost-effective | Multiple tools |
| Grafana + Prometheus (PRD spec) | Powerful, flexible | Heavy ops burden, overkill for MVP |
| Datadog | All-in-one | Expensive at scale |
| Just Supabase built-in | Simple | Limited alerting |

### Rationale
MVP doesn't need full Grafana/Prometheus stack. Supabase provides database and API metrics. Sentry handles error tracking and crash reporting. Custom dashboard (or simple tool like Metabase) for business metrics (GMV, calls, etc.).

### Consequences
- **Positive**: Lower operational burden, cost-effective
- **Negative**: Less powerful than dedicated observability stack
- **Risks**: May need to migrate to more powerful tools as scale increases
- **Mitigation**: Design metrics collection to be portable

### Related Requirements
REQ-TA-013

### Implementation Notes
- Supabase Dashboard for: Database metrics, API usage, Auth stats
- Sentry for: Error tracking, crash reporting, performance monitoring
- Metabase or Supabase SQL for: Business dashboards
- Alerting via Supabase/Sentry webhooks to Slack

### References
- Supabase Monitoring
- Sentry Documentation

---

## DD-TA-005: Automation Strategy (MVP)

**Status**: Accepted (Deferred n8n)

### Context
PRD specifies n8n for workflow automation. Need to decide if n8n is required for MVP or can be deferred.

### Decision
**Defer n8n to Phase 2**. Implement critical automations directly in Edge Functions and database triggers for MVP.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Direct implementation, defer n8n (chosen)** | Simpler MVP, fewer moving parts | Less visual workflow management |
| n8n self-hosted (PRD spec) | Visual workflows, flexible | DevOps burden, another service |
| n8n Cloud | Managed, reliable | Monthly cost, still adds complexity |
| Temporal/Inngest | Modern workflow engines | Learning curve, overkill |

### Rationale
MVP needs only a few critical automations: daily payout processing, notification triggers, and moderation alerts. These can be implemented with Supabase pg_cron (scheduled jobs) and database triggers. n8n adds value when workflows become more complex in Phase 2.

### Consequences
- **Positive**: Simpler MVP architecture, faster launch
- **Negative**: Less flexibility for non-dev workflow changes
- **Risks**: Complex workflows harder to implement without n8n
- **Mitigation**: Design automation code to be portable to n8n later

### Related Requirements
REQ-TA-017

### Implementation Notes
- pg_cron for: Daily payout processing, weekly reports
- Database triggers for: Notification events, moderation flags
- Edge Functions for: Webhook handlers, scheduled tasks
- Phase 2: Migrate visual workflows to n8n

### References
- Supabase pg_cron Extension
- Database Triggers Documentation

---

## Summary

| ID | Decision | Status | Impact |
|----|----------|--------|--------|
| DD-TA-001 | Node.js + Supabase (not FastAPI) | Accepted | Backend technology |
| DD-TA-002 | PostgreSQL + RLS Strategy | Accepted | Database design |
| DD-TA-003 | Hybrid API (PostgREST + Edge Functions) | Accepted | API architecture |
| DD-TA-004 | Supabase + Sentry Observability | Accepted | Monitoring stack |
| DD-TA-005 | Defer n8n, Direct Automation | Accepted | Workflow approach |

**Total Design Decisions**: 5
**Accepted**: 5
**Modified from PRD**: 2 (DD-TA-001, DD-TA-005)

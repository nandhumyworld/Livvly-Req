# Design Decisions: Database Schema

**Section**: 07-database-schema
**Generated**: 2026-01-20
**Status**: Complete

---

## DD-DB-001: Soft Delete Pattern

**Status**: Accepted

### Context
The original schema uses ON DELETE CASCADE for foreign key relationships, which permanently removes data. For a financial platform with compliance requirements, audit trails are essential.

### Decision
Implement **soft delete pattern** with `deleted_at` timestamp columns on all user-related and financial tables. Replace CASCADE deletes with soft delete triggers.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Soft delete with deleted_at (chosen)** | Audit trail preserved, reversible | Queries more complex, storage grows |
| Hard delete (CASCADE) - PRD spec | Simpler queries, auto cleanup | Loses history, compliance issues |
| Archive tables | Separation of active/historical | More complex, two-table maintenance |
| Event sourcing | Complete history | Major architecture change, overkill |

### Rationale
Financial transactions, calls, and user data must be retained for compliance (GST, TDS documentation), dispute resolution, and analytics. Soft deletes allow data recovery and maintain referential integrity for historical queries.

### Consequences
- **Positive**: Full audit trail, data recovery possible, compliance-ready
- **Negative**: All queries must filter `WHERE deleted_at IS NULL`, larger database
- **Risks**: Developers may forget to filter deleted records
- **Mitigation**: Create database views for active records, use RLS policies

### Related Requirements
REQ-DB-003, REQ-DB-011

### Implementation Notes
- Add `deleted_at TIMESTAMP NULL` to: users, transactions, calls, gift_transactions, rooms, withdrawals
- Create views: `active_users`, `active_transactions`, etc.
- RLS policies should include `deleted_at IS NULL` condition
- Implement archive job to move old soft-deleted records to archive tables

### References
- Soft Delete Pattern Best Practices
- Supabase RLS Documentation

---

## DD-DB-002: Dual Wallet System

**Status**: Accepted

### Context
The platform needs to track two distinct currencies: Coins (purchased by users for spending) and Diamonds/Gems (earned by creators for withdrawal). Need to decide on wallet architecture.

### Decision
Implement **separate wallet tables** for Coins (coin_wallets) and Diamonds (diamond_wallets) with distinct balance tracking and transaction types.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Separate wallet tables (chosen)** | Clear separation, simpler queries | Two tables to maintain |
| Single wallet with currency type | One table | Complex queries, mixing concerns |
| Ledger-only (no balance cache) | Always accurate | Slow balance queries |
| Multi-currency wallet | Flexible | Overkill for two currencies |

### Rationale
Coins and Diamonds have fundamentally different use cases: Coins are purchased and spent, Diamonds are earned and withdrawn. Separate tables make business logic clearer and prevent accidental mixing of currencies.

### Consequences
- **Positive**: Clear business logic, simple balance queries, easier auditing
- **Negative**: Two wallet creation operations on user signup
- **Risks**: Inconsistency between wallet balance and transaction sum
- **Mitigation**: Use database triggers to maintain balance consistency

### Related Requirements
REQ-DB-004, REQ-DB-005

### Implementation Notes
- Create coin_wallet on user creation (trigger)
- Create diamond_wallet only when user becomes creator (lazy creation)
- Use database transactions for all balance modifications
- Implement balance verification triggers

---

## DD-DB-003: Unified Transaction Ledger

**Status**: Accepted

### Context
Multiple transaction types exist (coin purchases, call payments, gifts, withdrawals). Need to decide between separate transaction tables or unified ledger.

### Decision
Use **single unified transactions table** with type column distinguishing transaction categories.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Unified ledger (chosen)** | Single source of truth, easier reporting | Larger table, type column required |
| Separate tables per type | Type-specific columns | Complex joins for reporting |
| CQRS (event sourcing) | Complete audit | Architecture complexity |

### Rationale
A unified ledger simplifies financial reporting, balance reconciliation, and analytics. All monetary movements are in one place, making audits straightforward.

### Consequences
- **Positive**: Single query for user transaction history, easy reconciliation
- **Negative**: Table grows faster, needs proper indexing
- **Risks**: Performance on large transaction history queries
- **Mitigation**: Partition by date, archive old transactions, proper indexes

### Related Requirements
REQ-DB-006

### Implementation Notes
- Index on (user_id, type, created_at)
- Consider table partitioning by month for scale
- Use reference_type and reference_id for linking to calls/gifts/etc.

---

## DD-DB-004: RLS-First Security Model

**Status**: Accepted

### Context
Need to implement authorization at database level to prevent unauthorized data access. Options include application-level checks or database-level Row Level Security.

### Decision
Implement **Row Level Security (RLS)** as the primary authorization mechanism, with application-level validation as secondary.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **RLS-first (chosen)** | Defense in depth, can't bypass | Complex policies, debugging harder |
| Application-only auth | Simpler policies | Single point of failure |
| Hybrid without RLS default | More flexible | Risk of missing auth checks |

### Rationale
RLS provides defense-in-depth: even if application code has bugs, the database won't return unauthorized data. Critical for financial data and user privacy. Aligns with DD-TA-002 from Technical Architecture.

### Consequences
- **Positive**: Secure by default, can't accidentally expose data
- **Negative**: Complex RLS policies, harder to debug auth issues
- **Risks**: Performance impact from RLS policy evaluation
- **Mitigation**: Optimize policies, test thoroughly, monitor query performance

### Related Requirements
REQ-DB-002, REQ-TA-011

### Implementation Notes
- Enable RLS on ALL tables
- Define policies per role: anon, authenticated, creator, admin
- Users can only read/write their own data
- Creators can see their call/gift history
- Admins bypass RLS with service role key

---

## DD-DB-005: Agora Channel Management

**Status**: Accepted

### Context
Calls and Rooms need Agora channel identifiers for real-time communication. Need to decide on channel naming and storage strategy.

### Decision
Store **Agora channel names** in calls and rooms tables, using UUID-based naming convention.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **UUID channel names (chosen)** | Unique, no collisions | Not human-readable |
| Sequential IDs | Simpler | Predictable, security risk |
| Composite keys | Descriptive | Longer strings |

### Rationale
UUID-based channel names prevent enumeration attacks and ensure uniqueness across distributed systems. Channel names are internal and don't need to be human-readable.

### Consequences
- **Positive**: Secure, unique, no conflicts
- **Negative**: Harder to debug from logs
- **Risks**: None significant
- **Mitigation**: Include call/room ID in logs alongside channel

### Related Requirements
REQ-DB-007, REQ-DB-008

### Implementation Notes
- Generate channel as: `call_{call_uuid}` or `room_{room_uuid}`
- Store full channel string for quick lookup
- Index agora_channel column for lookup by channel

---

## Summary

| ID | Decision | Status | Impact |
|----|----------|--------|--------|
| DD-DB-001 | Soft Delete Pattern | Accepted | Data retention strategy |
| DD-DB-002 | Dual Wallet System | Accepted | Currency management |
| DD-DB-003 | Unified Transaction Ledger | Accepted | Financial data model |
| DD-DB-004 | RLS-First Security Model | Accepted | Authorization approach |
| DD-DB-005 | Agora Channel Management | Accepted | Real-time identifiers |

**Total Design Decisions**: 5
**Accepted**: 5
**Modified from PRD**: 1 (DD-DB-001 - soft delete)

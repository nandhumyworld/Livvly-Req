# Design Decisions: API Specifications

**Section**: 08-api-specifications
**Generated**: 2026-01-20
**Status**: Complete

**Note**: APIs implemented in Node.js Edge Functions on Supabase, not FastAPI as shown in PRD code samples.

---

## DD-API-001: API Implementation Stack

**Status**: Accepted (Modified from PRD)

### Context
PRD provides FastAPI (Python) code samples, but stakeholder decision changed backend to Node.js + Supabase. Need to define how APIs will be structured with the new stack.

### Decision
Implement APIs using **Supabase hybrid approach**:
- PostgREST auto-generated APIs for simple CRUD operations
- Edge Functions (Node.js/TypeScript) for complex business logic

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Supabase Hybrid (chosen)** | Fast development, type-safe | Two API patterns |
| Port FastAPI to Express | Closer to PRD design | More custom code |
| All Edge Functions | Full control | Slower development |
| All PostgREST | Simplest | Limited for complex logic |

### Rationale
Supabase's PostgREST handles 60-70% of API needs (users, packages, gifts catalog, transaction history) automatically. Edge Functions handle remaining complex logic (call management, payment processing, payout calculations). This matches DD-TA-003 from Technical Architecture.

### Consequences
- **Positive**: Faster development, less boilerplate, type-safe
- **Negative**: Developers need to understand both patterns
- **Risks**: Inconsistent API style between auto-generated and custom
- **Mitigation**: Consistent naming conventions, comprehensive documentation

### Related Requirements
REQ-API-001, REQ-TA-003

### Implementation Notes
- PostgREST for: GET /users, GET /packages, GET /gifts, GET /transactions
- Edge Functions for: POST /auth/*, POST /calls/*, POST /wallet/recharge/*, POST /wallet/withdraw

---

## DD-API-002: End-of-Call Billing

**Status**: Accepted

### Context
Need to decide when to charge users for calls - in real-time (per-minute during call) or at call completion.

### Decision
Implement **end-of-call billing** with minimum balance reservation at call start.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **End-of-call billing (chosen)** | Simpler, fewer transactions, proven model | User sees charge after call |
| Real-time per-minute | Immediate feedback | Complex, many transactions, race conditions |
| Pre-paid time blocks | Simple billing | Poor UX, arbitrary limits |

### Rationale
FRND and similar apps use end-of-call billing successfully. Real-time billing creates database contention, potential race conditions, and more transactions to reconcile. Reserving minimum balance (3 minutes) at call start ensures user can pay.

### Consequences
- **Positive**: Simpler implementation, fewer transactions, better performance
- **Negative**: Users may be surprised by total charge
- **Risks**: Caller runs out of balance mid-call
- **Mitigation**: Show estimated cost, implement auto-disconnect warning at low balance

### Related Requirements
REQ-API-010, REQ-API-011, REQ-API-012

### Implementation Notes
- Reserve 3 minutes worth of coins at call start (check balance >= rate * 3)
- Track call duration in memory/Redis during call
- On call end: calculate total, deduct from caller, credit receiver
- Handle edge cases: abrupt disconnect, balance exhausted

---

## DD-API-003: JWT Token Strategy

**Status**: Accepted

### Context
Need to decide authentication token strategy when combining Firebase Auth (OTP) with Supabase.

### Decision
Use **Supabase JWT tokens** as primary authentication, with Firebase only for OTP verification at login.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Supabase JWT primary (chosen)** | Integrated with RLS, single token | Need to sync Firebase -> Supabase |
| Firebase JWT primary | Single auth system | Doesn't integrate with Supabase RLS |
| Custom JWT | Full control | Reinventing wheel |
| Dual tokens | Maximum compatibility | Complex, two tokens to manage |

### Rationale
Supabase RLS policies require Supabase JWT tokens. Firebase is only used for the OTP verification step (reliable SMS delivery). After OTP verification, we issue Supabase tokens for all subsequent API calls.

### Consequences
- **Positive**: Seamless RLS integration, single token for all Supabase operations
- **Negative**: Additional sync step after Firebase OTP
- **Risks**: Token sync failure leaves user unable to access data
- **Mitigation**: Robust error handling, clear user messaging on auth failures

### Related Requirements
REQ-API-002, REQ-API-003

### Implementation Notes
- Firebase OTP -> Verify -> Exchange Firebase token for Supabase session
- Store user in Supabase `auth.users` table
- All subsequent requests use Supabase access_token
- Implement refresh token rotation

---

## DD-API-004: Webhook-First Payment Verification

**Status**: Accepted

### Context
Need to decide how to verify Razorpay payments - client-side verification or server-side webhooks.

### Decision
Use **webhook-first verification** with client verification as fallback.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Webhook-first (chosen)** | Most reliable, handles edge cases | Slight delay |
| Client-only verification | Immediate | Can be spoofed, misses failures |
| Polling | No webhooks needed | Inefficient, delayed |

### Rationale
Webhooks are the most reliable way to confirm payment status. They handle network failures, app crashes, and edge cases automatically. Client verification is kept as immediate feedback but webhook is source of truth.

### Consequences
- **Positive**: Reliable payment tracking, handles all edge cases
- **Negative**: Webhook delivery delays possible
- **Risks**: Webhook endpoint downtime
- **Mitigation**: Implement retry logic, idempotent handlers, client fallback

### Related Requirements
REQ-API-007, REQ-API-008

### Implementation Notes
- Register Razorpay webhook for payment.captured, payment.failed events
- Edge Function handles webhook, verifies signature, credits coins
- Client can call verify endpoint for immediate status
- Implement idempotency to handle duplicate webhooks

---

## DD-API-005: Agora Token Generation Strategy

**Status**: Accepted

### Context
Agora requires tokens for channel access. Need to decide when and how to generate these tokens.

### Decision
Generate **short-lived Agora tokens** on-demand via Edge Functions, with channel-specific privileges.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **On-demand short-lived (chosen)** | Secure, fresh tokens | Edge Function call per token |
| Long-lived tokens | Fewer generations | Security risk if leaked |
| Pre-generated pool | Fast retrieval | Complex management |

### Rationale
Agora tokens should be short-lived (24 hours max) and generated for specific channels. Edge Functions provide secure server-side generation. Token includes user privileges (publisher/subscriber).

### Consequences
- **Positive**: Secure, granular control, no token leakage risk
- **Negative**: Additional Edge Function call overhead
- **Risks**: Edge Function latency delays call start
- **Mitigation**: Optimize Edge Function, consider caching short-term

### Related Requirements
REQ-API-010, REQ-TA-006

### Implementation Notes
- Use Agora Token Builder library (Node.js version)
- Generate caller token as publisher, receiver token as publisher
- Token validity: 24 hours
- Include channel name in call record for lookup

---

## Summary

| ID | Decision | Status | Impact |
|----|----------|--------|--------|
| DD-API-001 | Supabase Hybrid API Stack | Accepted | API architecture |
| DD-API-002 | End-of-Call Billing | Accepted | Billing model |
| DD-API-003 | Supabase JWT Primary | Accepted | Authentication |
| DD-API-004 | Webhook-First Payment | Accepted | Payment verification |
| DD-API-005 | On-Demand Agora Tokens | Accepted | Real-time communication |

**Total Design Decisions**: 5
**Accepted**: 5
**Modified from PRD**: 1 (DD-API-001 - stack change)

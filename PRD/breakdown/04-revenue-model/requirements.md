# Requirements: Revenue Model

**Section**: 04-revenue-model
**Source Lines**: 162-227
**Generated**: 2026-01-19
**Status**: Complete

---

## Revenue Stream Requirements

### REQ-RM-001: Three Primary Revenue Streams
**Priority**: P0 (Critical)
**Type**: Business

**Description**: The platform must implement three primary revenue streams: Coins (target 70%), Subscriptions (target 25%), and Ads (target 5%).

**Rationale**: Diversified revenue streams reduce business risk and match competitor models.

**Acceptance Criteria**:
- All three revenue streams are implemented and generating revenue
- Revenue dashboard tracks contribution from each stream
- Each stream has dedicated tracking and reporting
- Revenue targets are monitored against actuals

**Dependencies**: REQ-MA-001 (Multi-stream revenue)
**Related Design Decisions**: None

**Notes**: Actual percentages may vary; these are targets based on competitor analysis.

---

### REQ-RM-002: Coin-Based Monetization
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users must be able to purchase coins (virtual currency) and spend them on recharge (call time), gifts, and paid calls.

**Rationale**: Coins are the primary monetization mechanism (70% target revenue).

**Acceptance Criteria**:
- Coin packages available at multiple price points
- Coins can be spent on: call minutes, virtual gifts, room entries
- Coin balance is visible at all times during monetized activities
- Coin transactions are logged and auditable
- Coin purchase history available to users

**Dependencies**: REQ-CE-xxx (Coin Economy design)
**Related Design Decisions**: None

**Notes**: Coin-to-INR ratio must be clearly communicated.

---

### REQ-RM-003: High Creator Revenue Share (65% of Net)
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Creators must receive 65% of net revenue (after payment gateway fees), resulting in approximately 45.5% of gross user spend.

**Rationale**: High creator share model is the key differentiator for creator acquisition. This translates to the promised 75-85% range when factoring tier bonuses.

**Acceptance Criteria**:
- Base creator share is 65% of net revenue (after 30% app store fee)
- Revenue split is calculated automatically on each transaction
- Creator dashboard shows earnings breakdown
- Split percentages are documented in creator terms

**Dependencies**: REQ-MA-004 (Tiered creator share), REQ-ES-005 (75-85% promise)
**Related Design Decisions**: None

**Notes**: 65% of net = 45.5% of gross. With tier bonuses, top creators can reach ~75-85% of net.

---

### REQ-RM-004: Platform Revenue Margin
**Priority**: P0 (Critical)
**Type**: Business

**Description**: The platform must retain 35% of net revenue (24.5% of gross) after app store fees to sustain operations.

**Rationale**: Platform margin funds operations, development, marketing, and growth.

**Acceptance Criteria**:
- Platform share is automatically calculated and retained
- Platform revenue is tracked separately from creator earnings
- Margin analysis reports available for business planning
- Margin remains consistent across transaction types

**Dependencies**: REQ-RM-003 (Creator share)
**Related Design Decisions**: None

**Notes**: Platform margin is lower than competitors (45-55%) to fund higher creator share.

---

### REQ-RM-005: Agency Commission Handling
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Agency-managed creators must have their agency commission automatically deducted from their share before payout.

**Rationale**: Agency model is required for MVP; agencies need automated commission tracking.

**Acceptance Criteria**:
- Agency commission rate is configurable per agency (default 25% of creator share)
- Commission is automatically calculated and shown in creator dashboard
- Agencies have dashboard showing their commission earnings
- Creator sees both gross and net (after agency) earnings

**Dependencies**: REQ-ES-013 (Agency support)
**Related Design Decisions**: None

**Notes**: From ₹500 spend with 65% creator share: Creator gets ₹227.50, Agency gets ₹56.88 (25%), Creator net ₹170.62.

---

## Subscription Requirements

### REQ-RM-006: Free Tier
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: The platform must offer a free tier with limited features to enable user acquisition without payment barrier.

**Rationale**: Free tier reduces friction for new users and enables conversion funnel.

**Acceptance Criteria**:
- Free users can access the app without payment
- Free users limited to 5 outgoing calls per day
- Free users have limited room access
- Free users see ads (if implemented)
- Clear upgrade prompts when limits are reached

**Dependencies**: REQ-PV-014 (Monetization journey)
**Related Design Decisions**: None

**Notes**: Limits should create desire for upgrade without frustrating users completely.

---

### REQ-RM-007: VIP Subscription Tier
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: The platform must offer a VIP subscription at ₹199/month with premium features including unlimited calls, 20% coin discount, and VIP badge.

**Rationale**: VIP tier is the primary subscription product for MVP (Free + VIP only at launch).

**Acceptance Criteria**:
- VIP subscription available at ₹199/month
- VIP users have unlimited outgoing calls
- VIP users receive 20% discount on all coin purchases
- VIP badge displayed on user profile
- VIP status is checked in real-time for feature access
- Subscription can be cancelled anytime

**Dependencies**: REQ-API-xxx (Subscription API)
**Related Design Decisions**: None

**Notes**: Premium and Creator+ tiers deferred to post-MVP.

---

### REQ-RM-008: Subscription Management
**Priority**: P1 (High)
**Type**: Functional

**Description**: Users must be able to purchase, manage, and cancel subscriptions through the app.

**Rationale**: Subscription lifecycle management is essential for the subscription revenue stream.

**Acceptance Criteria**:
- Subscription purchase flow with payment gateway integration
- Subscription status visible in user profile/settings
- Auto-renewal with advance notification
- One-click cancellation option
- Grace period for payment failures (3 days)
- Subscription history available to user

**Dependencies**: REQ-API-xxx (Payment APIs)
**Related Design Decisions**: None

**Notes**: Consider Google Play Billing for in-app subscription management.

---

## Ad Revenue Requirements

### REQ-RM-009: Rewarded Video Ads
**Priority**: P2 (Medium)
**Type**: Functional

**Description**: The platform may offer rewarded video ads that give users free coins for watching ads.

**Rationale**: Rewarded ads provide monetization path for non-paying users and drive engagement.

**Acceptance Criteria**:
- Users can watch video ads to earn free coins
- Ad availability is clearly indicated
- Coin reward is credited immediately after ad completion
- Daily limit on rewarded ads (e.g., 5 per day)
- VIP users can opt out of seeing ads

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Ad integration may be deferred if not critical for MVP metrics. Target 5% revenue.

---

## Revenue Calculation Requirements

### REQ-RM-010: Real-Time Revenue Calculation
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Revenue split calculations must happen in real-time as transactions occur, with immediate balance updates for all parties.

**Rationale**: Real-time calculation ensures transparency and accurate creator earnings display.

**Acceptance Criteria**:
- Creator gem balance updates within 5 seconds of transaction
- Platform revenue is logged immediately
- Agency commission calculated in real-time
- No manual reconciliation required for standard transactions

**Dependencies**: REQ-DB-xxx (Database schema)
**Related Design Decisions**: None

**Notes**: Use database transactions to ensure consistency.

---

### REQ-RM-011: Revenue Audit Trail
**Priority**: P1 (High)
**Type**: Non-Functional

**Description**: All revenue transactions must have a complete audit trail for financial compliance and dispute resolution.

**Rationale**: Audit trail is required for tax compliance, financial reporting, and creator dispute resolution.

**Acceptance Criteria**:
- Every transaction is logged with timestamp, parties, amounts
- Original values preserved even if rates change
- Audit logs are immutable (append-only)
- Reports can be generated for any time period
- Data retention meets regulatory requirements (7 years)

**Dependencies**: REQ-LC-xxx (Legal compliance)
**Related Design Decisions**: None

**Notes**: Consider separate audit database for compliance and performance isolation.

---

### REQ-RM-012: Tax Compliance - Creator Responsibility
**Priority**: P1 (High)
**Type**: Business

**Description**: Tax compliance (including TDS) is the creator's responsibility; the platform pays gross creator earnings without deductions.

**Rationale**: Stakeholder decision to simplify MVP operations; creators handle their own tax obligations.

**Acceptance Criteria**:
- Platform pays gross earnings to creators (no TDS deducted)
- Creator terms clearly state tax is creator's responsibility
- Annual earnings summary provided to creators for tax filing
- Platform provides earnings data in tax-friendly format (PDF/CSV)

**Dependencies**: REQ-CP-xxx (Creator Payout)
**Related Design Decisions**: None

**Notes**: May need to revisit if regulations require platform TDS collection.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-RM-001 | Three Primary Revenue Streams | P0 | Business |
| REQ-RM-002 | Coin-Based Monetization | P0 | Functional |
| REQ-RM-003 | High Creator Revenue Share (65% of Net) | P0 | Business |
| REQ-RM-004 | Platform Revenue Margin (35% of Net) | P0 | Business |
| REQ-RM-005 | Agency Commission Handling | P0 | Business |
| REQ-RM-006 | Free Tier | P0 | Functional |
| REQ-RM-007 | VIP Subscription Tier (₹199/month) | P0 | Functional |
| REQ-RM-008 | Subscription Management | P1 | Functional |
| REQ-RM-009 | Rewarded Video Ads | P2 | Functional |
| REQ-RM-010 | Real-Time Revenue Calculation | P0 | Functional |
| REQ-RM-011 | Revenue Audit Trail | P1 | Non-Functional |
| REQ-RM-012 | Tax Compliance - Creator Responsibility | P1 | Business |

**Total Requirements**: 12
**P0 (Critical)**: 8
**P1 (High)**: 3
**P2 (Medium)**: 1

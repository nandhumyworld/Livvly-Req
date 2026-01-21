# Requirements: Automation Workflows

**Section**: 11-n8n-automation-workflows
**Source Lines**: 2126-2444
**Generated**: 2026-01-20
**Status**: Complete

---

> **Note**: Per DD-TA-005, n8n is deferred to Phase 2. These requirements document direct implementation using Supabase Edge Functions and pg_cron for MVP.

---

## Creator Onboarding Automation

### REQ-AW-001: Creator Application Handler
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Edge Function to handle creator application submissions and initiate KYC verification process.

**Rationale**: Automates creator onboarding flow from application to verification.

**Acceptance Criteria**:
- Edge Function triggered on creator application submission
- Validates applicant age >= 18
- Stores application data in creator_kyc table
- Initiates PAN verification workflow
- Returns application ID and status

**Dependencies**: REQ-CP-008, REQ-DB-011
**Related Design Decisions**: DD-TA-005

**Notes**: Replaces n8n "Creator Onboarding Automation" workflow.

---

### REQ-AW-002: PAN Verification Integration
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Integration with Karza API (or equivalent) for PAN verification during KYC process.

**Rationale**: Automated PAN verification is required for creator compliance and TDS processing.

**Acceptance Criteria**:
- Edge Function calls Karza/Digio PAN verification API
- Validates PAN format (XXXXX0000X pattern)
- Verifies PAN name against submitted name
- Updates creator_kyc.pan_verified status
- Stores verification timestamp
- Handles API failures with retry logic
- Rate limiting to prevent API abuse

**Dependencies**: REQ-AW-001, REQ-CP-009
**Related Design Decisions**: DD-TA-005

**Notes**: Consider multiple verification providers for redundancy.

---

### REQ-AW-003: KYC Status Notification
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Send SMS notifications for KYC status changes (approved/rejected).

**Rationale**: Creators need immediate feedback on their application status.

**Acceptance Criteria**:
- Triggered by KYC status change (verified/rejected)
- Integration with MSG91 SMS API
- Template-based messages for approval and rejection
- Rejection includes reason in message
- Push notification sent alongside SMS
- Notification logged in system

**Dependencies**: REQ-AW-002, REQ-TA-009
**Related Design Decisions**: DD-TA-005

**Notes**: Replaces n8n SMS nodes in Creator Onboarding workflow.

---

## Daily Payout Processing

### REQ-AW-004: Payout Processing Scheduler
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Scheduled job (pg_cron) to process pending withdrawals daily at 6 AM IST.

**Rationale**: Automated payout processing ensures timely creator payments.

**Acceptance Criteria**:
- pg_cron job runs daily at 6:00 AM IST
- Fetches pending withdrawals older than 1 hour
- Joins with creator_kyc for bank details
- Processes in batches of 50 to prevent timeouts
- Transaction isolation for each withdrawal
- Logs all processing attempts

**Dependencies**: REQ-CP-004, REQ-CP-006
**Related Design Decisions**: DD-TA-005

**Notes**: Replaces n8n "Daily Payout Processing" workflow trigger.

---

### REQ-AW-005: Razorpay X Payout Creation
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Edge Function to create Razorpay X payouts for approved withdrawals.

**Rationale**: Integrates with Razorpay X for actual fund transfers to creators.

**Acceptance Criteria**:
- Creates Razorpay Fund Account if not exists
- Initiates payout via Razorpay X API
- Supports IMPS (primary) and NEFT (fallback)
- Amount in paise (multiply by 100)
- Includes withdrawal ID as reference
- Updates withdrawal status to "processing"
- Stores Razorpay payout ID for tracking
- Handles API errors with appropriate status

**Dependencies**: REQ-AW-004, REQ-CP-006, REQ-TA-007
**Related Design Decisions**: DD-API-004

**Notes**: Amount calculation: Math.round(net_amount * 100) for paise.

---

### REQ-AW-006: Payout Webhook Handler
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Webhook endpoint to receive Razorpay payout status updates.

**Rationale**: Real-time payout status tracking for transparency and reconciliation.

**Acceptance Criteria**:
- Webhook endpoint for Razorpay callbacks
- Validates webhook signature
- Updates withdrawal status (completed/failed/reversed)
- Stores failure reason if applicable
- Triggers notification to creator
- Logs all webhook events for audit

**Dependencies**: REQ-AW-005, REQ-CP-007
**Related Design Decisions**: DD-API-004

**Notes**: Same pattern as payment webhooks from DD-API-004.

---

### REQ-AW-007: Payout Notification
**Priority**: P1 (High)
**Type**: Technical

**Description**: Send SMS/push notifications for payout status changes.

**Rationale**: Creators need timely updates on their payout status.

**Acceptance Criteria**:
- Notification on payout initiation (with amount)
- Notification on payout completion
- Notification on payout failure (with reason)
- SMS via MSG91 + in-app push
- Includes net amount after TDS

**Dependencies**: REQ-AW-006, REQ-CP-007
**Related Design Decisions**: DD-TA-005

**Notes**: Templates: payout_initiated, payout_completed, payout_failed.

---

## Fraud Detection Automation

### REQ-AW-008: Transaction Fraud Check Trigger
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Database trigger or Edge Function to check transactions for fraud patterns in real-time.

**Rationale**: Real-time fraud detection protects platform and legitimate users.

**Acceptance Criteria**:
- Triggered on new gift/call transactions
- Runs fraud checks before completing transaction
- Minimal latency impact (< 100ms)
- Configurable fraud rules
- Does not block legitimate transactions

**Dependencies**: REQ-DB-003, REQ-DB-006
**Related Design Decisions**: DD-TA-005

**Notes**: Replaces n8n "Real-time Fraud Detection" webhook trigger.

---

### REQ-AW-009: Gift Loop Detection
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Detect suspicious gift exchange patterns between users (potential money laundering).

**Rationale**: Gift loops can be used for fraudulent coin recycling.

**Acceptance Criteria**:
- Check for bidirectional gifts between same users
- Threshold: > 5 exchanges within 1 hour flags suspicion
- Query: Count reverse gifts in last hour
- Flag both sender and receiver accounts
- Different severity for different volumes

**Dependencies**: REQ-AW-008, REQ-DB-006
**Related Design Decisions**: DD-TA-005

**Notes**: Query pattern from PRD workflow preserved.

---

### REQ-AW-010: Spending Anomaly Detection
**Priority**: P1 (High)
**Type**: Technical

**Description**: Detect unusual spending patterns compared to user's historical behavior.

**Rationale**: Sudden high spending may indicate compromised accounts or fraud.

**Acceptance Criteria**:
- Calculate user's 30-day spending average and std deviation
- Flag transactions > 3 standard deviations above mean
- Minimum history of 10 transactions before applying
- New users exempt from anomaly detection
- Configurable threshold multiplier

**Dependencies**: REQ-AW-008, REQ-DB-003
**Related Design Decisions**: DD-TA-005

**Notes**: Formula: amount > (avg_spend + 3 * std_spend) = anomaly.

---

### REQ-AW-011: Fraud Flag Management
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Store and manage fraud flags with automatic account actions.

**Rationale**: Flagged accounts need investigation and may require payout holds.

**Acceptance Criteria**:
- fraud_flags table for storing flag events
- Flag types: gift_loop, spending_anomaly, velocity, etc.
- Severity levels: low, medium, high, critical
- Auto-hold payouts on high/critical flags
- Store detection details as JSON for review
- Admin dashboard access to flag queue

**Dependencies**: REQ-AW-009, REQ-AW-010, REQ-DB-013
**Related Design Decisions**: DD-TA-005

**Notes**: High severity flags auto-set diamond_wallets.payout_hold = true.

---

### REQ-AW-012: Fraud Alert Notification
**Priority**: P1 (High)
**Type**: Technical

**Description**: Alert system for notifying admins of fraud detections.

**Rationale**: Admins need real-time visibility into potential fraud for quick action.

**Acceptance Criteria**:
- Slack webhook integration for fraud alerts (Phase 2)
- In-app admin notification for MVP
- Alert includes: user_id, flag_type, severity, details
- Grouped alerts for repeated flags
- Daily summary email option

**Dependencies**: REQ-AW-011
**Related Design Decisions**: DD-TA-005

**Notes**: Slack integration deferred to Phase 2; in-app notifications for MVP.

---

## Daily Balance Settlement

### REQ-AW-013: Balance Settlement Scheduler
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Scheduled job (pg_cron) to move matured pending earnings to available balance at midnight IST.

**Rationale**: Automates the 7-day holding period transition for creator earnings.

**Acceptance Criteria**:
- pg_cron job runs daily at 00:00 IST
- Processes earnings older than 7 days
- Atomic transaction for each creator
- Updates both pending_balance and available_balance
- Marks transactions as settled
- Creates settlement log entry

**Dependencies**: REQ-CP-002, REQ-CP-003
**Related Design Decisions**: DD-TA-005, DD-DB-003

**Notes**: Replaces n8n "Daily Balance Settlement" workflow.

---

### REQ-AW-014: Settlement Query Logic
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: SQL logic for calculating and moving settled balances.

**Rationale**: Core business logic for the 7-day holding period release.

**Acceptance Criteria**:
- CTE to aggregate unsettled earnings per creator
- Filter: created_at < NOW() - INTERVAL '7 days' AND settled = false
- Transaction types: earn_call, earn_gift, earn_room
- Atomic UPDATE of diamond_wallets balances
- Atomic UPDATE of transactions.settled flag
- No partial settlements (all-or-nothing per creator)

**Dependencies**: REQ-AW-013, REQ-DB-005, REQ-DB-006
**Related Design Decisions**: DD-DB-003

**Notes**: SQL pattern preserved from PRD workflow.

---

### REQ-AW-015: Settlement Logging
**Priority**: P1 (High)
**Type**: Technical

**Description**: Log all settlement runs for audit and troubleshooting.

**Rationale**: Settlement logs enable reconciliation and issue diagnosis.

**Acceptance Criteria**:
- settlement_logs table for recording runs
- Fields: id, settled_at, creators_processed, total_amount, records_count
- Log success and failure outcomes
- Retain logs for 1 year minimum
- Queryable for reporting

**Dependencies**: REQ-AW-013
**Related Design Decisions**: DD-TA-005

**Notes**: Part of operational audit trail.

---

## Coin Expiry Automation

### REQ-AW-016: Coin Expiry Scheduler
**Priority**: P1 (High)
**Type**: Technical

**Description**: Scheduled job to process coin expiry based on type and age.

**Rationale**: Implements coin expiry rules (365d/90d/30d) for different coin types.

**Acceptance Criteria**:
- pg_cron job runs daily at 02:00 IST
- Identifies expired coin batches per REQ-CE-007
- Purchased coins: 365 days of account inactivity
- Bonus coins: 90 days from grant
- Promotional coins: 30 days from grant
- Deducts expired amount from coin_wallet.balance
- Creates expiry transaction record
- Does not process already-expired batches

**Dependencies**: REQ-CE-007, REQ-CE-008, REQ-DB-004
**Related Design Decisions**: DD-TA-005

**Notes**: Linked to coin type tracking from Section 09.

---

### REQ-AW-017: Expiry Warning Notification
**Priority**: P1 (High)
**Type**: Technical

**Description**: Notify users before their coins expire.

**Rationale**: Fair warning helps users spend coins before expiry.

**Acceptance Criteria**:
- Push notification 7 days before expiry
- Push notification 1 day before expiry
- Include: coin amount, expiry date, call-to-action
- In-app banner for expiring coins
- Configurable notification timing

**Dependencies**: REQ-AW-016, REQ-CE-007
**Related Design Decisions**: DD-TA-005

**Notes**: Drives engagement and coin consumption.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-AW-001 | Creator Application Handler | P0 | Technical |
| REQ-AW-002 | PAN Verification Integration | P0 | Technical |
| REQ-AW-003 | KYC Status Notification | P0 | Technical |
| REQ-AW-004 | Payout Processing Scheduler | P0 | Technical |
| REQ-AW-005 | Razorpay X Payout Creation | P0 | Technical |
| REQ-AW-006 | Payout Webhook Handler | P0 | Technical |
| REQ-AW-007 | Payout Notification | P1 | Technical |
| REQ-AW-008 | Transaction Fraud Check Trigger | P0 | Technical |
| REQ-AW-009 | Gift Loop Detection | P0 | Technical |
| REQ-AW-010 | Spending Anomaly Detection | P1 | Technical |
| REQ-AW-011 | Fraud Flag Management | P0 | Technical |
| REQ-AW-012 | Fraud Alert Notification | P1 | Technical |
| REQ-AW-013 | Balance Settlement Scheduler | P0 | Technical |
| REQ-AW-014 | Settlement Query Logic | P0 | Technical |
| REQ-AW-015 | Settlement Logging | P1 | Technical |
| REQ-AW-016 | Coin Expiry Scheduler | P1 | Technical |
| REQ-AW-017 | Expiry Warning Notification | P1 | Technical |

**Total Requirements**: 17
**P0 (Critical)**: 11
**P1 (High)**: 6

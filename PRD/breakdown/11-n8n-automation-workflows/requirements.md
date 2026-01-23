# N8N Automation Workflows - Requirements

**Section**: 11. N8N Automation Workflows
**Lines**: 2126-2444 (319 lines)
**Complexity**: Moderate
**Last Updated**: 2026-01-23

---

## Functional Requirements

### REQ-AW-001: Creator Onboarding Automation Workflow
**Priority**: P1 (High)
**Type**: Functional
**User Story**: As a platform, I want to automate creator KYC verification so new creators are approved/rejected within minutes.

**Description**:
Webhook-triggered n8n workflow that validates creator applications, verifies PAN via Karza API, updates database, and sends SMS notifications.

**Workflow Steps** (from PRD lines 2128-2221):
```
1. Webhook Trigger: POST /creator-application
2. Validate Age: Check age ≥ 18
3. Verify PAN: Call Karza API for PAN + name verification
4. PAN Valid Check: If valid → Approve, else → Reject
5. Update Database: Set kyc_status = 'verified' or 'rejected'
6. Send SMS: Approval or rejection notification via MSG91
```

**Acceptance Criteria**:
- Webhook receives creator application payload (age, PAN, name, phone)
- Age validation rejects applications with age <18
- Karza API verifies PAN against government database
- Database updated with verification result within 2 seconds
- SMS sent within 5 seconds of approval/rejection
- Error workflow catches Karza API failures and alerts admin

**Dependencies**: REQ-CP-004 (lazy KYC), REQ-DB-006 (creator_kyc table), Karza API subscription

**Technical Notes**:
- Use Karza API (user-approved, simpler than NSDL/UTI direct integration)
- Store Karza API key in n8n credentials (encrypted)
- Webhook authentication: Validate HMAC signature from backend
- Template IDs: `creator_approved`, `creator_rejected` (MSG91)

**Conflicts Resolved**:
- ✅ **Karza vs NSDL/UTI**: User approved Karza API (Q3 answer)

---

### REQ-AW-002: Real-time Payout Processing Workflow (Webhook-Triggered)
**Priority**: P0 (Critical)
**Type**: Functional
**User Story**: As a creator, I want my withdrawal processed immediately when I request it, not queued until next day.

**Description**:
Webhook-triggered n8n workflow that processes creator withdrawal requests in real-time. Backend validates withdrawal, triggers n8n webhook, n8n creates Razorpay payout and updates database.

**Workflow Steps** (modified from PRD lines 2223-2310):
```
1. Webhook Trigger: POST /process-payout (from backend after validation)
2. Get Withdrawal Details: Read payload (withdrawal_id, user_id, amount, method, bank details)
3. Create Razorpay Payout: Call Razorpay X API with IMPS or NEFT mode
4. Update Withdrawal Status: Set status = 'processing', store payout_reference_id
5. Send SMS: Notify creator "Payout initiated, credited within T+1"
```

**Acceptance Criteria**:
- Webhook triggered within 1 second of backend validation
- Razorpay payout created within 3 seconds of webhook trigger
- Payout mode (IMPS/NEFT) determined by backend, n8n uses `withdrawal.payout_method` field
- Withdrawal status updated to 'processing' before SMS sent
- SMS includes amount and estimated credit time
- Failed payouts logged, backend retry logic handles retries (REQ-CP-006)

**Dependencies**: REQ-CP-002 (T+1 payout), REQ-CP-009 (Razorpay X integration), REQ-API-038 (withdrawal API)

**Technical Notes**:
- Backend calculates payout method (IMPS <₹2L, NEFT ≥₹2L) per REQ-CP-005
- N8n does NOT handle retries (backend owns retry logic per Q5 answer)
- Use Razorpay X idempotency key: `withdrawal.id` (prevent duplicate payouts)
- Amount in paise: `Math.round(net_amount * 100)`

**Conflicts Resolved**:
- ⚠️ **PRD shows scheduled 6 AM daily processing** → CHANGED to webhook-triggered (Q1 answer)
- ⚠️ **PRD hardcodes IMPS mode** → CHANGED to read from backend-calculated field (Q6 answer)
- ✅ **Retry logic**: Backend handles retries, not n8n (Q5 answer)

---

### REQ-AW-003: Fraud Detection Workflow
**Priority**: P1 (High)
**Type**: Functional
**User Story**: As a platform, I want to automatically detect and prevent fraudulent transactions to minimize loss.

**Description**:
Webhook-triggered n8n workflow that runs real-time fraud checks on transactions, flags suspicious activity, holds payouts, and alerts admins.

**Fraud Detection Rules** (from PRD lines 2312-2403):

**Rule 1: Gift Loop Detection**
- Query: Check if sender/receiver have exchanged >5 gifts in last 1 hour (reciprocal gifting)
- Action: Flag as `gift_loop`, hold payouts for both users

**Rule 2: Unusual Spending Detection**
- Query: Calculate user's 30-day average spend + standard deviation
- Anomaly: Current transaction >3 standard deviations above mean
- Action: Flag as `spending_anomaly`, hold payouts

**Workflow Steps**:
```
1. Webhook Trigger: POST /fraud-check (from backend after transaction)
2. Check Gift Loop: Query gift_transactions for reciprocal gifts
3. Loop Detected? If >5 reciprocal gifts → Flag
4. Check Unusual Spending: Query user's spending history, calculate mean + stddev
5. Spending Anomaly? If amount > mean + 3*stddev → Flag
6. Flag Account: Insert into fraud_flags table
7. Hold Payouts: Set diamond_wallets.payout_hold = true
8. Alert Admin: Send Slack alert to #fraud-alerts channel
```

**Acceptance Criteria**:
- Fraud checks complete within 500ms of transaction
- Gift loop detection covers last 1 hour window
- Spending anomaly uses 30-day rolling window
- Payout hold prevents all withdrawal attempts until admin review
- Slack alert includes user_id, flag_type, transaction details
- Admin dashboard shows all flagged accounts for review

**Dependencies**: REQ-DB-012 (transactions table), REQ-DB-013 (diamond_wallets with payout_hold column), REQ-FS-022 (AI moderation)

**Technical Notes**:
- Fraud checks run AFTER transaction is recorded (not blocking)
- Payout hold: `payout_hold = true` in diamond_wallets table
- Admin review required to unfreeze account (manual workflow)
- Slack webhook URL stored in n8n credentials

**Conflicts Resolved**:
- ✅ **Payout hold without holding period**: Immediate hold on available balance (Q4 answer)
- ⚠️ **Section 10 removed holding period, but fraud hold still needed for flagged accounts** (clarified)

---

### REQ-AW-004: ~~Move Pending to Available Balance~~ (REMOVED)
**Priority**: N/A
**Type**: Removed
**Rationale**: Section 10 Q3 decision removed 7-day holding period. Earnings instantly go to available balance at transaction time. This workflow (PRD lines 2405-2440) is NO LONGER NEEDED.

**User Decision**: Q2 answer - "Remove workflow entirely"

---

### REQ-AW-005: Daily Tier Recalculation Workflow (NEW)
**Priority**: P1 (High)
**Type**: Functional
**User Story**: As a creator, I want my revenue share tier automatically updated when I cross performance thresholds so I earn more.

**Description**:
Scheduled n8n workflow (runs daily at 1 AM IST) that recalculates creator tiers (75%/80%/85%) based on trailing 30-day earnings and updates creator_performance table.

**Tier Thresholds** (from REQ-RM-003):
- **Tier 1 (75%)**: <₹50K in trailing 30 days
- **Tier 2 (80%)**: ₹50K - ₹2L in trailing 30 days
- **Tier 3 (85%)**: >₹2L in trailing 30 days

**Workflow Steps**:
```
1. Schedule Trigger: Daily at 1:00 AM IST
2. Calculate Trailing 30-Day Earnings:
   SELECT user_id, SUM(diamonds * COIN_TO_INR) as monthly_earnings
   FROM transactions
   WHERE type IN ('earn_call', 'earn_gift', 'earn_room')
     AND created_at > NOW() - INTERVAL '30 days'
   GROUP BY user_id
3. Determine New Tier: Apply thresholds (75%, 80%, 85%)
4. Update creator_performance Table:
   - monthly_earnings (trailing 30-day sum)
   - tier_percent (0.75, 0.80, or 0.85)
   - tier_updated_at (NOW())
5. Log Changes: Insert tier changes into tier_history table for audit
6. Notify Tier Upgrades: Send congratulations SMS to creators upgraded to Tier 2 or 3
```

**Acceptance Criteria**:
- Runs daily at 1:00 AM IST (off-peak hours)
- Calculates trailing 30-day earnings for ALL creators (active and inactive)
- Updates creator_performance table with new tier_percent
- Tier changes apply to FUTURE earnings only (no retroactive adjustment per REQ-CP-010)
- SMS sent only for tier UPGRADES (not downgrades, to avoid negativity)
- Execution log records: creators updated, tier distribution, execution time

**Dependencies**: REQ-RM-003 (tiered revenue model), REQ-DB-014 (creator_performance table), REQ-CP-010 (no retroactive adjustment)

**Technical Notes**:
- Use PostgreSQL window functions for efficient calculation
- Create index on transactions(user_id, type, created_at) for performance
- Store tier history for compliance and analytics
- SMS template: "Congratulations! You've reached Creator Tier X (Y% revenue share) 🎉"

---

### REQ-AW-006: Failed Webhook Retry Monitor (NEW)
**Priority**: P2 (Medium)
**Type**: Functional
**User Story**: As a platform, I want to retry failed webhooks so no critical notifications are lost.

**Description**:
Scheduled n8n workflow (runs every 15 minutes) that monitors failed webhook executions and retries them with exponential backoff.

**Workflow Steps**:
```
1. Schedule Trigger: Every 15 minutes
2. Query Failed Executions:
   SELECT * FROM n8n_execution_entity
   WHERE finished = false
     AND stoppedAt IS NOT NULL
     AND mode = 'webhook'
     AND startedAt > NOW() - INTERVAL '24 hours'
3. Loop Each Failed Execution:
   - Check retry count (max 3 retries)
   - Calculate backoff: 15 min, 1 hour, 4 hours
   - Re-trigger webhook with original payload
4. Update Retry Count: Increment webhook_retries field
5. Alert After 3 Failures: Send Slack alert if all retries exhausted
```

**Acceptance Criteria**:
- Monitors last 24 hours of webhook executions
- Retries failed webhooks up to 3 times with exponential backoff
- Does not retry if failure is due to validation error (4xx response)
- Logs all retry attempts with timestamps
- Slack alert after 3 failed retries includes webhook type, payload, error message

**Dependencies**: N8n execution history table, Slack integration

**Technical Notes**:
- Only retry technical failures (5xx errors, timeouts)
- Skip validation errors (4xx errors) - these need manual fix
- Use n8n HTTP Request node to re-trigger webhooks
- Store retry metadata in custom n8n variable or separate DB table

---

### REQ-AW-007: Daily Analytics Aggregation Workflow (NEW)
**Priority**: P2 (Medium)
**Type**: Functional
**User Story**: As a platform admin, I want daily aggregated analytics so dashboards load quickly without heavy DB queries.

**Description**:
Scheduled n8n workflow (runs daily at 2 AM IST) that aggregates daily metrics into analytics tables for fast dashboard queries.

**Metrics to Aggregate**:
1. **Daily Active Users (DAU)**: Unique users who opened app, made call, or sent gift
2. **Daily Revenue**: Total coins purchased, total spent, total earned by creators
3. **Top Creators**: Top 100 creators by earnings (daily, weekly, monthly)
4. **Call Metrics**: Total calls, average duration, total minutes
5. **Retention Cohorts**: 1-day, 7-day, 30-day retention by signup date

**Workflow Steps**:
```
1. Schedule Trigger: Daily at 2:00 AM IST
2. Aggregate DAU:
   INSERT INTO analytics_daily_metrics (date, metric_name, value)
   SELECT CURRENT_DATE - 1, 'dau', COUNT(DISTINCT user_id)
   FROM user_activity_log
   WHERE date = CURRENT_DATE - 1
3. Aggregate Revenue:
   INSERT INTO analytics_daily_metrics (date, metric_name, value)
   SELECT CURRENT_DATE - 1, 'revenue_inr',
     SUM(CASE WHEN type = 'buy_coins' THEN amount ELSE 0 END)
   FROM transactions WHERE date = CURRENT_DATE - 1
4. Aggregate Top Creators:
   INSERT INTO analytics_top_creators (date, user_id, earnings_inr, rank)
   SELECT CURRENT_DATE - 1, user_id, SUM(diamonds * COIN_TO_INR), RANK()
   FROM transactions WHERE type LIKE 'earn%' AND date = CURRENT_DATE - 1
   GROUP BY user_id ORDER BY earnings DESC LIMIT 100
5. Log Completion: Insert aggregation log with row counts
```

**Acceptance Criteria**:
- Runs daily at 2:00 AM IST (after tier recalculation at 1 AM)
- Aggregates previous day's data (CURRENT_DATE - 1)
- Inserts into analytics_* tables (separate from transactional tables)
- Dashboard queries read from analytics tables (sub-second response time)
- Execution log tracks: metrics aggregated, row counts, execution time
- Failed aggregations trigger Slack alert

**Dependencies**: REQ-DB-012 (transactions table), analytics tables (analytics_daily_metrics, analytics_top_creators, etc.)

**Technical Notes**:
- Create separate analytics database schema for isolation
- Use materialized views for complex aggregations (refresh daily)
- Dashboard queries use analytics tables, not live transactions
- Retention logic: Compare user_id in current day vs signup date

---

## Non-Functional Requirements

### REQ-AW-008: N8N Self-Hosted Deployment
**Priority**: P1 (High)
**Type**: Infrastructure

**Description**:
Deploy n8n as self-hosted instance on AWS EC2 (ap-south-1 Mumbai region) with Docker, PostgreSQL backend, and automated backups.

**Infrastructure Requirements**:
- **Instance**: AWS EC2 t3.medium (2 vCPU, 4GB RAM) - scales to t3.large if needed
- **Database**: PostgreSQL 15 for n8n execution history (separate from app DB)
- **Docker**: Docker Compose setup with n8n, PostgreSQL, Redis containers
- **Region**: ap-south-1 (Mumbai) - same region as backend for low latency
- **Backups**: Daily automated backups to S3, 30-day retention
- **Monitoring**: CloudWatch metrics + alerts for CPU, memory, disk usage

**Acceptance Criteria**:
- N8n accessible at https://n8n.livvly.app (internal domain, VPN-only access)
- PostgreSQL database for n8n with automated daily backups
- Docker containers auto-restart on failure
- Workflow executions logged and retained for 30 days
- SSL certificate via Let's Encrypt (auto-renewal)
- Admin access restricted to IP whitelist or VPN

**Dependencies**: AWS account, domain DNS configuration, SSL certificate

**Technical Notes**:
- Use n8n Docker image: `n8nio/n8n:latest`
- PostgreSQL connection string in environment variables
- Redis for n8n queue management (optional, for high-volume workflows)
- Cost estimate: ~₹5K-8K per month (EC2 + storage + data transfer)

**User Decision**: Q9 answer - Self-hosted on AWS (vs n8n Cloud)

---

### REQ-AW-009: Error Workflow with Retries
**Priority**: P1 (High)
**Type**: Reliability

**Description**:
Every n8n workflow must have error sub-flow that logs errors, retries with exponential backoff, and alerts admins after exhausting retries.

**Error Workflow Pattern**:
```
1. Error Trigger: Catches any node failure in main workflow
2. Log Error:
   INSERT INTO n8n_error_logs (workflow_name, node_name, error_message, payload, created_at)
3. Check Retry Count: Read custom variable workflow_retries
4. Retry Logic:
   - Retry 1: Wait 30 seconds, re-run failed node
   - Retry 2: Wait 5 minutes, re-run failed node
   - Retry 3: Wait 30 minutes, re-run failed node
5. After 3 Failures:
   - Send Slack alert to #n8n-errors channel
   - Include: workflow name, error message, payload snapshot
   - Create incident ticket in admin dashboard
```

**Acceptance Criteria**:
- All workflows have error sub-flow configured
- Errors logged to n8n_error_logs table with full context
- Retry logic: 3 attempts with exponential backoff (30s, 5m, 30m)
- Slack alert sent after 3 failed retries
- Admin dashboard shows all n8n errors requiring manual intervention

**Dependencies**: Slack integration, n8n_error_logs table

**Technical Notes**:
- Use n8n "Error Trigger" node type
- Store retry count in workflow custom variable
- Exponential backoff: 30s, 5m, 30m (total 35.5 minutes max retry window)
- Critical workflows (payout, fraud) have stricter monitoring

**User Decision**: Q8 answer - Error workflow + retries (vs fail silently)

---

### REQ-AW-010: Workflow Execution Performance
**Priority**: P2 (Medium)
**Type**: Performance

**Description**:
- Webhook-triggered workflows: <3 seconds end-to-end execution time
- Scheduled workflows: <5 minutes execution time (except analytics aggregation: <30 minutes)
- 99% uptime for n8n instance (tolerates ~7 hours downtime per month)
- Webhook endpoint latency: <500ms response time

---

### REQ-AW-011: Workflow Security
**Priority**: P1 (High)
**Type**: Security

**Description**:
- All webhook endpoints require HMAC signature verification (validate requests from backend only)
- API keys stored in n8n credentials (encrypted at rest)
- Database credentials rotated every 90 days
- N8n admin UI restricted to VPN or IP whitelist (not publicly accessible)
- Workflow execution logs sanitized (no PII in logs: mask phone, PAN, bank account)

---

## Summary

**Total Requirements**: 11 (7 functional, 4 non-functional)
**Priority Breakdown**:
- P0 (Critical): 1 requirement
- P1 (High): 6 requirements
- P2 (Medium): 3 requirements
- N/A (Removed): 1 requirement

**Workflows for MVP**:
1. ✅ Creator Onboarding Automation (existing, modified)
2. ✅ Real-time Payout Processing (changed from scheduled to webhook)
3. ✅ Fraud Detection (existing, kept)
4. ❌ Move Pending to Available Balance (REMOVED - no holding period)
5. ✅ Daily Tier Recalculation (NEW - user-requested)
6. ✅ Failed Webhook Retry Monitor (NEW - user-requested)
7. ✅ Daily Analytics Aggregation (NEW - user-requested)

**Total MVP Workflows**: 6 workflows

**Critical Conflicts Resolved**:
- ⚠️ **Scheduled daily payout → Webhook-triggered** (aligns with on-demand withdrawals)
- ⚠️ **Move Pending to Available workflow → REMOVED** (no holding period)
- ⚠️ **IMPS hardcoded → Backend calculates method** (IMPS/NEFT auto-select)
- ✅ **Karza API for PAN verification** (user-approved vs NSDL/UTI)
- ✅ **Backend handles retry logic** (n8n just triggers initial payout)
- ✅ **Self-hosted on AWS** (vs n8n Cloud)

**Implementation Priority**:
1. **Sprint 1**: REQ-AW-001, 002, 003 (core workflows: onboarding, payout, fraud)
2. **Sprint 2**: REQ-AW-005, 008, 009 (tier recalc, infrastructure, error handling)
3. **Sprint 3**: REQ-AW-006, 007, 010, 011 (monitoring, analytics, performance, security)

**Infrastructure Costs** (estimated):
- AWS EC2 t3.medium: ₹4,500/month
- PostgreSQL RDS storage: ₹1,500/month
- Data transfer: ₹1,000/month
- **Total**: ~₹7,000/month for n8n infrastructure

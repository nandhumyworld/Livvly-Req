# N8N Automation Workflows - Questions & Answers

**Section**: 11. N8N Automation Workflows
**Date**: 2026-01-23
**Answered By**: User
**Total Questions**: 9

---

## Core Questions (Q1-Q3)

### Q1: The PRD shows a scheduled 6 AM daily payout workflow, but Section 10 approved on-demand withdrawals. What should trigger payout processing?

**Answer**: Webhook on withdrawal request (Recommended)

**Rationale**:
- **Real-time processing**: Creator requests withdrawal → Backend validates → N8n webhook processes payout immediately
- **Aligns with Section 10 Q8 decision**: On-demand withdrawals mean creators can withdraw anytime, not wait for batch processing
- **Better UX**: Creators see "Processing" status immediately, not "Queued until 6 AM"
- **T+1 guarantee**: With webhook trigger, payouts initiated within seconds of request (not up to 24h queue delay)

**PRD Conflict**:
- ⚠️ PRD lines 2223-2310 show **scheduled trigger at 6 AM daily** → CHANGED to webhook-triggered
- Original approach: Queue withdrawal requests, process all at 6 AM (batch processing)
- New approach: Webhook `/process-payout` triggered by backend after validation

**Impact on Requirements**:
- ✅ REQ-AW-002: Payout workflow triggered by webhook (POST /process-payout)
- ✅ Backend handles validation (amount, limits, KYC), then triggers n8n
- ✅ N8n creates Razorpay payout and updates withdrawal status immediately

**Implementation**:
```javascript
// Backend API: POST /api/v1/creators/withdraw
async function createWithdrawal(userId, amount) {
  // 1. Validate withdrawal (balance, limits, KYC)
  const validation = await validateWithdrawal(userId, amount);
  if (!validation.valid) throw new Error(validation.error);

  // 2. Create withdrawal record (status = 'pending')
  const withdrawal = await createWithdrawalRecord(userId, amount);

  // 3. Trigger n8n webhook for payout processing
  await axios.post('https://n8n.livvly.app/webhook/process-payout', {
    withdrawal_id: withdrawal.id,
    user_id: userId,
    amount: withdrawal.net_amount,
    method: withdrawal.payout_method, // Backend calculates IMPS/NEFT
    bank_details: { /* from KYC */ }
  }, {
    headers: { 'X-Signature': generateHMAC(payload) }
  });

  return withdrawal;
}
```

---

### Q2: The PRD includes a 'Move Pending to Available Balance' workflow with 7-day holding period, but Section 10 removed holding period (T+1 only). What should happen to this workflow?

**Answer**: Remove workflow entirely (Recommended)

**Rationale**:
- **Section 10 Q3 decision**: No holding period, T+1 from withdrawal request only
- **Earnings flow**: User spends coins → Diamonds **instantly** credited to creator's **available balance** (no "pending" state)
- **No pending balance concept**: Without holding period, there's no "pending" state to move to "available"
- **Simpler architecture**: Remove unnecessary workflow and pending_balance column from diamond_wallets

**PRD Conflict**:
- ⚠️ PRD lines 2405-2440 show **"Daily Balance Settlement" workflow** → REMOVED entirely
- Original workflow: Midnight cron job moves earnings >7 days old from pending_balance to available_balance
- New approach: Earnings go directly to available_balance at transaction time (no workflow needed)

**Impact on Requirements**:
- ❌ REQ-AW-004: Workflow marked as REMOVED in requirements document
- ✅ Database schema simplified: Remove `diamond_wallets.pending_balance` column
- ✅ Transaction processing logic: Credit available_balance directly

**Database Schema Change**:
```sql
-- OLD (with holding period):
CREATE TABLE diamond_wallets (
  user_id UUID PRIMARY KEY,
  pending_balance DECIMAL(12,2) DEFAULT 0,   -- 7-day hold
  available_balance DECIMAL(12,2) DEFAULT 0  -- Can withdraw
);

-- NEW (no holding period):
CREATE TABLE diamond_wallets (
  user_id UUID PRIMARY KEY,
  available_balance DECIMAL(12,2) DEFAULT 0  -- Instant availability
);
```

**Trade-off Acknowledged**:
- ⚠️ **Fraud risk**: Without 7-day buffer, platform absorbs cost if user disputes charge after creator withdrew
- ✅ **Mitigation**: AI moderation (REQ-FS-022), daily limits (REQ-CP-008), fraud detection workflow (REQ-AW-003)

---

### Q3: The PRD uses Karza API for PAN verification in creator onboarding. Section 10 mentioned NSDL/UTI for KYC. Which should n8n use?

**Answer**: Keep Karza API (Recommended)

**Rationale**:
- **Karza is a reputable KYC aggregator**: Offers PAN, Aadhaar, bank verification, GST verification in one platform
- **Simpler integration**: Single vendor vs multiple government APIs (NSDL for PAN, UIDAI for Aadhaar, NPCI for bank)
- **PRD already shows Karza**: No need to redesign integration (faster MVP)
- **Production-ready**: Many fintech companies use Karza (PolicyBazaar, Paytm, Groww)

**Alternative Considered**:
- NSDL/UTI direct: More authoritative (government source of truth), but requires:
  - Separate integrations for PAN, Aadhaar, bank verification
  - More complex API authentication (government portals)
  - Longer setup time (registrations, approvals)

**Impact on Requirements**:
- ✅ REQ-AW-001: Creator onboarding workflow uses Karza API for PAN verification
- ✅ Karza API endpoint: `https://api.karza.in/v3/pan-verification`
- ✅ Future enhancement: Add Aadhaar verification via Karza if needed for higher withdrawal limits

**API Integration**:
```json
// Karza PAN Verification Request
POST https://api.karza.in/v3/pan-verification
Headers: {
  "x-karza-key": "{{ $env.KARZA_API_KEY }}",
  "Content-Type": "application/json"
}
Body: {
  "pan": "ABCDE1234F",
  "name": "John Doe",
  "consent": "Y"
}

// Response:
{
  "status": "success",
  "result": {
    "valid": true,
    "name": "JOHN DOE",
    "pan_status": "Active",
    "name_match_score": 95
  }
}
```

**Cost**: Karza pricing ~₹5 per PAN verification (cheaper than government APIs with aggregation overhead)

---

## Follow-up Questions (Q4-Q6)

### Q4: Without a holding period, how should fraud flags affect creator payouts?

**Answer**: Immediate hold on available balance (Recommended)

**Rationale**:
- **Protects platform**: When fraud detected (gift loop, spending anomaly), freeze payouts immediately to prevent loss
- **Requires manual review**: Admin investigates fraud flag and unfreezes account if false positive
- **Trade-off**: False positives frustrate legitimate creators, but fraud prevention outweighs this risk

**Implementation**:
- **Fraud flag triggers**: Gift loop (>5 reciprocal gifts/hour), spending anomaly (>3 stddev above mean)
- **Database column**: `diamond_wallets.payout_hold = true` (prevents withdrawals)
- **Admin dashboard**: Shows all flagged accounts with fraud details for review
- **Unfreeze workflow**: Admin marks account as "reviewed", sets `payout_hold = false`

**Fraud Detection Workflow** (from REQ-AW-003):
```
1. Transaction occurs → Backend triggers POST /fraud-check webhook
2. N8n checks fraud rules (gift loop, spending anomaly)
3. If fraud detected:
   - Insert into fraud_flags table (user_id, flag_type, details)
   - Update diamond_wallets SET payout_hold = true
   - Send Slack alert to #fraud-alerts channel
4. Creator attempts withdrawal → Backend checks payout_hold
   - If true: Block withdrawal, show "Account under review" message
5. Admin reviews → Marks as false positive or confirms fraud
   - False positive: Set payout_hold = false, allow withdrawals
   - Confirmed fraud: Ban account, issue refunds to victims
```

**Impact on Requirements**:
- ✅ REQ-AW-003: Fraud workflow sets `payout_hold = true` when anomaly detected
- ✅ REQ-DB-013: diamond_wallets table includes `payout_hold BOOLEAN DEFAULT false` column
- ✅ REQ-API-038: Withdrawal API checks payout_hold before processing

**Notification to Creator**:
```
"Your account is currently under review for unusual activity.
Withdrawals are temporarily on hold. Our team will review your account within 24 hours.
If you believe this is an error, please contact support."
```

---

### Q5: Should failed payout retries be handled in n8n workflows or in the backend API?

**Answer**: Backend API handles retries (Recommended)

**Rationale**:
- **Backend owns business logic**: Retry strategy (3 attempts, exponential backoff) defined in REQ-CP-006
- **Cleaner separation**: N8n triggers initial payout, backend handles retry orchestration via cron or queue
- **Easier testing**: Retry logic in backend is unit-testable, n8n workflows harder to test
- **Reusable**: If we add other payout methods (UPI, wallet), retry logic works for all

**Implementation**:

**N8n workflow** (REQ-AW-002):
```javascript
// N8n Payout Workflow (simplified)
1. Webhook Trigger: POST /process-payout
2. Create Razorpay Payout: Call Razorpay X API
3. Update Withdrawal Status:
   - If success: status = 'processing'
   - If failure: status = 'failed', failure_reason = {{ error }}
4. Return response to backend
```

**Backend retry logic** (separate from n8n):
```javascript
// Backend Cron Job: Runs every 6 hours
async function retryFailedPayouts() {
  const failedPayouts = await db.query(`
    SELECT * FROM withdrawals
    WHERE status = 'failed'
      AND failure_reason LIKE 'technical%'  -- Only retry technical failures
      AND retry_count < 3
      AND last_retry_at < NOW() - INTERVAL '6 hours'
  `);

  for (const payout of failedPayouts) {
    // Re-trigger n8n webhook
    await axios.post('https://n8n.livvly.app/webhook/process-payout', {
      withdrawal_id: payout.id,
      retry_attempt: payout.retry_count + 1
    });

    // Update retry count
    await db.query(`
      UPDATE withdrawals
      SET retry_count = retry_count + 1, last_retry_at = NOW()
      WHERE id = $1
    `, [payout.id]);
  }
}
```

**Impact on Requirements**:
- ✅ REQ-AW-002: N8n payout workflow triggers initial payout only (no retry logic in workflow)
- ✅ REQ-CP-006: Backend cron job handles 3 retries over 24h (0h, 6h, 18h)
- ✅ REQ-DB-015: withdrawals table includes `retry_count` and `last_retry_at` columns

**Why NOT handle retries in n8n?**
- ❌ Complex workflow with nested retry logic and timing
- ❌ Hard to unit test retry scenarios
- ❌ Duplicates logic if we add other payout methods
- ❌ Backend loses visibility into retry state

---

### Q6: The PRD hardcodes IMPS mode in payout workflow. Section 10 approved auto-select IMPS/NEFT by amount. Where should this logic live?

**Answer**: Backend calculates, n8n uses (Recommended)

**Rationale**:
- **Backend owns business logic**: REQ-CP-005 defines auto-select rule (IMPS <₹2L, NEFT ≥₹2L)
- **N8n just executes**: N8n reads `withdrawal.payout_method` field and passes to Razorpay X
- **Testable**: Unit test backend method selection logic easily
- **Reusable**: If thresholds change (e.g., IMPS limit increases to ₹5L), update backend only

**Implementation**:

**Backend calculates method** (during withdrawal creation):
```javascript
// Backend: POST /api/v1/creators/withdraw
function calculatePayoutMethod(amount) {
  const IMPS_THRESHOLD = 200000; // ₹2 Lakhs

  if (amount <= IMPS_THRESHOLD) {
    return {
      method: 'IMPS',
      estimated_credit: 'Within 30 minutes',
      platform_fee: 5 // ₹5 absorbed by platform
    };
  } else {
    return {
      method: 'NEFT',
      estimated_credit: 'Next banking day (T+1)',
      platform_fee: 0 // Free
    };
  }
}

// Store in withdrawal record
const withdrawal = await db.query(`
  INSERT INTO withdrawals (user_id, amount, payout_method, estimated_credit)
  VALUES ($1, $2, $3, $4) RETURNING *
`, [userId, netAmount, method.method, method.estimated_credit]);
```

**N8n reads and uses method**:
```json
{
  "name": "Create Razorpay Payout",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "url": "https://api.razorpay.com/v1/payouts",
    "method": "POST",
    "body": {
      "amount": "={{ Math.round($json.net_amount * 100) }}",
      "currency": "INR",
      "mode": "={{ $json.payout_method }}",  // Read from backend field
      "purpose": "payout"
    }
  }
}
```

**Impact on Requirements**:
- ✅ REQ-CP-005: Backend implements auto-select logic (IMPS <₹2L, NEFT ≥₹2L)
- ✅ REQ-AW-002: N8n payout workflow reads `withdrawal.payout_method` field (no calculation in n8n)
- ✅ REQ-API-038: Withdrawal API stores `payout_method` and `estimated_credit` in response

**Why NOT calculate in n8n?**
- ❌ Business logic duplicated in workflow (harder to maintain)
- ❌ If threshold changes, must update n8n workflow (requires n8n admin access)
- ❌ Testing requires triggering actual n8n webhook (slow, unreliable)

**PRD Conflict**:
- ⚠️ PRD line 2278 hardcodes `"mode": "IMPS"` → CHANGED to read from backend field `"mode": "={{ $json.payout_method }}"`

---

## Gap Analysis Questions (Q7-Q9)

### Q7: The PRD shows 4 workflows. Are there missing workflows that should be automated in MVP?

**Answer**: Daily tier recalculation (Recommended), Failed webhook retry monitor, Daily analytics aggregation

**Rationale**:

**1. Daily Tier Recalculation (MUST HAVE)**
- **Why**: Section 10 Q4 decision requires automatic tier updates (75%/80%/85%) based on trailing 30-day earnings
- **Frequency**: Daily at 1 AM IST (off-peak)
- **Impact**: Creators automatically get higher revenue share when they cross thresholds
- **Without this**: Manual tier updates or backend cron job (less visible, harder to monitor)

**2. Failed Webhook Retry Monitor (NICE TO HAVE)**
- **Why**: N8n webhooks can fail (backend downtime, network issues). Need retry mechanism.
- **Frequency**: Every 15 minutes
- **Impact**: Prevents lost notifications (payout confirmations, KYC approvals)
- **Without this**: Failed webhooks require manual re-triggering or customer support tickets

**3. Daily Analytics Aggregation (NICE TO HAVE)**
- **Why**: Dashboards need fast queries. Aggregating daily metrics (DAU, revenue, top creators) into analytics tables prevents slow queries on transactional tables.
- **Frequency**: Daily at 2 AM IST (after tier recalculation)
- **Impact**: Admin dashboard loads in <1 second vs 10+ seconds with live queries
- **Without this**: Dashboard queries hit live transactions table (slow, locks tables)

**Rejected Option**: "None - keep MVP minimal"
- While tempting for faster MVP, tier recalculation is NOT optional (required by approved requirements)
- Webhook retry and analytics can be deferred to post-MVP if time constrained

**Impact on Requirements**:
- ✅ REQ-AW-005: Daily Tier Recalculation Workflow (NEW - user-approved)
- ✅ REQ-AW-006: Failed Webhook Retry Monitor (NEW - user-approved)
- ✅ REQ-AW-007: Daily Analytics Aggregation (NEW - user-approved)

**Total MVP Workflows**: 6 workflows
1. Creator Onboarding
2. Real-time Payout Processing
3. Fraud Detection
4. ~~Move Pending to Available~~ (REMOVED)
5. Daily Tier Recalculation (NEW)
6. Failed Webhook Retry Monitor (NEW)
7. Daily Analytics Aggregation (NEW)

**Final Count**: 6 workflows (removed 1, added 3)

---

### Q8: How should n8n workflows handle errors and ensure reliability?

**Answer**: Error workflow + retries (Recommended)

**Rationale**:
- **Production reliability**: Critical workflows (payout, KYC) cannot fail silently
- **Auto-recovery**: Retry transient errors (network timeout, API downtime) automatically
- **Alerting**: After exhausting retries, alert admin for manual intervention
- **Debugging**: Error logs provide full context (workflow name, node, payload, error message)

**Error Workflow Pattern** (applied to ALL workflows):
```
Main Workflow:
  1. Trigger (webhook or schedule)
  2. Execute workflow logic
  3. On success: Log completion
  4. On failure: Trigger error sub-flow

Error Sub-Flow (connected to all main workflows):
  1. Error Trigger Node: Catches any node failure
  2. Log Error:
     INSERT INTO n8n_error_logs (workflow_name, node_name, error_message, payload, created_at)
  3. Check Retry Count: Read workflow_retries variable (stored in n8n context)
  4. Retry Logic:
     - Attempt 1: Wait 30 seconds → Re-run failed node
     - Attempt 2: Wait 5 minutes → Re-run failed node
     - Attempt 3: Wait 30 minutes → Re-run failed node
  5. After 3 Failures:
     - Send Slack alert: "#n8n-errors channel with workflow name, error, payload"
     - Create admin incident ticket
```

**Exponential Backoff Schedule**:
- Retry 1: 30 seconds (for quick transient errors like network blips)
- Retry 2: 5 minutes (for short API downtimes)
- Retry 3: 30 minutes (for extended outages)
- Total retry window: 35.5 minutes

**Impact on Requirements**:
- ✅ REQ-AW-009: Error Workflow with Retries (all workflows must implement)
- ✅ n8n_error_logs table: Stores workflow execution errors
- ✅ Slack integration: #n8n-errors channel for critical alerts

**Example Error Alert**:
```
🚨 N8N Workflow Failure (3 retries exhausted)

Workflow: Real-time Payout Processing
Node: Create Razorpay Payout
Error: Request timeout after 30 seconds
Payload: { withdrawal_id: "uuid-123", amount: 5000 }
Timestamp: 2026-01-23 14:30:00 IST

Action Required: Manual investigation and retry
Link: https://n8n.livvly.app/execution/abc123
```

**Alternative Considered**: "Fail silently, log only"
- ❌ No active alerts: Admin must check logs periodically (errors missed for hours)
- ❌ No auto-recovery: Every transient error requires manual retry
- ❌ Poor UX: Creators experience failed payouts/KYC with no notification

---

### Q9: Should n8n be self-hosted or use n8n Cloud for production?

**Answer**: Self-hosted on AWS (Recommended)

**Rationale**:

**Self-Hosted Pros**:
- ✅ **Full control**: Customize Docker setup, backup strategy, monitoring
- ✅ **Lower cost at scale**: ~₹7K/month vs ₹25K-50K/month for n8n Cloud
- ✅ **Data sovereignty**: All data stays in India (ap-south-1 Mumbai region)
- ✅ **Same region as backend**: Low latency (<5ms) for webhook calls
- ✅ **Security**: VPN-only access to n8n UI (not publicly accessible)

**Self-Hosted Cons**:
- ⚠️ **DevOps setup required**: Docker, PostgreSQL, backups, monitoring (1-2 weeks setup)
- ⚠️ **Maintenance overhead**: OS patches, Docker updates, backup verification

**N8n Cloud Pros**:
- ✅ **Faster setup**: Sign up and start building workflows in 10 minutes
- ✅ **Automatic backups**: N8n handles workflow backups and disaster recovery
- ✅ **No DevOps needed**: No server management, Docker, database setup

**N8n Cloud Cons**:
- ❌ **Higher cost**: ₹25K-50K/month for 10K+ workflow executions
- ❌ **Data location**: N8n Cloud may store data outside India (compliance risk)
- ❌ **Less control**: Cannot customize infrastructure, monitoring, or backup schedule
- ❌ **Latency**: Webhook calls from India to n8n Cloud EU/US (~200-300ms latency)

**Decision Factors**:
- **Cost**: Self-hosted saves ~₹18K-43K per month (₹2.16L-5.16L per year)
- **Compliance**: Self-hosted ensures data stays in India (important for financial platform)
- **Scale**: At 10K+ workflow executions per day, self-hosted is more economical

**Infrastructure Setup** (self-hosted):
```yaml
# docker-compose.yml
version: '3.8'
services:
  n8n:
    image: n8nio/n8n:latest
    ports:
      - '5678:5678'
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD={{ secure_password }}
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_DATABASE=n8n
      - WEBHOOK_URL=https://n8n.livvly.app
    volumes:
      - n8n_data:/home/node/.n8n

  postgres:
    image: postgres:15
    environment:
      - POSTGRES_DB=n8n
      - POSTGRES_USER=n8n
      - POSTGRES_PASSWORD={{ db_password }}
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

volumes:
  n8n_data:
  postgres_data:
  redis_data:
```

**Impact on Requirements**:
- ✅ REQ-AW-008: N8N Self-Hosted Deployment on AWS EC2 (ap-south-1 Mumbai)
- ✅ Infrastructure: t3.medium EC2 instance, PostgreSQL 15, Docker Compose
- ✅ Security: VPN-only access, IP whitelist, SSL with Let's Encrypt
- ✅ Backups: Daily automated backups to S3 (30-day retention)

**Cost Comparison**:
| Item | Self-Hosted (AWS) | N8n Cloud |
|------|-------------------|-----------|
| Compute (EC2 t3.medium) | ₹4,500/month | Included |
| Database (PostgreSQL) | ₹1,500/month | Included |
| Data transfer | ₹1,000/month | Included |
| N8n license | Free (open-source) | ₹25,000-50,000/month |
| **Total** | **₹7,000/month** | **₹25,000-50,000/month** |

**Savings**: ₹18K-43K per month = **₹2.16L-5.16L per year**

---

## Summary

**Total Questions Answered**: 9
**Core Questions**: 3
**Follow-up Questions**: 3
**Gap Analysis Questions**: 3

**Critical Decisions Made**:
1. ✅ Webhook-triggered payout processing (vs scheduled batch)
2. ✅ Remove "Move Pending to Available" workflow entirely
3. ✅ Keep Karza API for PAN verification
4. ✅ Immediate payout hold for fraud flags
5. ✅ Backend handles retry logic (n8n triggers only)
6. ✅ Backend calculates IMPS/NEFT method
7. ✅ Add 3 new workflows: tier recalc, webhook retry, analytics
8. ✅ Error workflow + retries for all workflows
9. ✅ Self-hosted on AWS (vs n8n Cloud)

**Requirements Impacted**: 11 total requirements (REQ-AW-001 through REQ-AW-011)

**PRD Conflicts Identified & Resolved**:
1. ⚠️ Scheduled 6 AM payout → **CHANGED to webhook-triggered**
2. ⚠️ Move Pending to Available workflow → **REMOVED (no holding period)**
3. ⚠️ IMPS hardcoded → **Backend calculates method, n8n reads field**
4. ✅ Karza API → **Kept (user-approved vs NSDL/UTI)**

**MVP Workflows**: 6 workflows
1. Creator Onboarding Automation
2. Real-time Payout Processing (modified)
3. Fraud Detection
4. ~~Move Pending to Available~~ (REMOVED)
5. Daily Tier Recalculation (NEW)
6. Failed Webhook Retry Monitor (NEW)
7. Daily Analytics Aggregation (NEW)

**Infrastructure Decision**: Self-hosted on AWS (saves ₹2.16L-5.16L per year vs n8n Cloud)

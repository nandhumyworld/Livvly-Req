# Creator Payout System - Requirements

**Section**: 10. Creator Payout System
**Lines**: 2003-2125 (123 lines)
**Complexity**: Moderate
**Last Updated**: 2026-01-23

---

## Functional Requirements

### REQ-CP-001: Minimum Withdrawal Amount
**Priority**: P0 (Critical)
**Type**: Functional
**User Story**: As a creator, I want to withdraw earnings with a low minimum threshold so I can access funds frequently.

**Description**:
System must allow creators to withdraw earnings with a minimum threshold of ₹100.

**Acceptance Criteria**:
- Withdrawal requests below ₹100 are rejected with clear error message
- UI displays current available balance and minimum threshold
- Withdrawal button disabled until ₹100 threshold is met

**Dependencies**: REQ-FS-024 (creator wallet)

**Conflicts**:
- ⚠️ **PRD shows ₹500 minimum** (line 2117) - CORRECTED to ₹100 per user approval
- ⚠️ **Updates REQ-ES-004** to reflect ₹100 minimum (was inconsistent)

---

### REQ-CP-002: T+1 Payout Processing (No Holding Period)
**Priority**: P0 (Critical)
**Type**: Functional
**User Story**: As a creator, I want fast access to my earnings so I can receive bank credit within 24 hours of withdrawal request.

**Description**:
Creators can withdraw available balance immediately (no holding period). Bank credit occurs within T+1 (next calendar day) of withdrawal request.

**Payout Flow**:
```
1. User spends coins on creator service (call/gift/room)
2. Diamonds instantly credited to creator's "available balance"
3. Creator initiates withdrawal request anytime (if ≥₹100)
4. System calculates TDS and processes via Razorpay X
5. Bank credit within T+1 (next calendar day, e.g., Mon 11pm request → Tue credit)
```

**Acceptance Criteria**:
- Earnings immediately available for withdrawal (no pending state)
- Withdrawal processed within 24 hours (T+1 guarantee)
- Cutoff time: 11:59 PM IST (requests after rollover to next T+1 cycle)
- Status updates: requested → processing → completed/failed

**Dependencies**: REQ-FS-024, REQ-API-038 (withdrawal endpoint)

**Conflicts**:
- ⚠️ **PRD shows 7-day holding period** (line 2022) - REMOVED per user approval for faster payouts
- ⚠️ **Eliminates "Pending Balance" state** from original flow diagram

**Impact Analysis**:
- ✅ Faster payouts improve creator satisfaction
- ⚠️ Increased fraud risk - mitigate with AI moderation (REQ-FS-022) and daily limits (REQ-CP-008)
- ⚠️ Refund complexity - if user disputes charge after creator withdrew, platform absorbs cost

---

### REQ-CP-003: TDS Withholding (Section 194J)
**Priority**: P0 (Critical)
**Type**: Functional
**User Story**: As a platform, I must comply with Indian Income Tax Act by withholding TDS on creator earnings.

**Description**:
Deduct TDS @ 10% for creator earnings exceeding ₹30,000 per financial year (April 1 - March 31) as per Section 194J.

**TDS Calculation Logic** (from PRD lines 2054-2100):
```python
# 1. Calculate total earnings this FY (sum of earn_call, earn_gift, earn_room)
# 2. Calculate already withdrawn amount this FY
# 3. Determine taxable portion:
#    - If total_with_current ≤ ₹30K: No TDS
#    - If already_withdrawn ≥ ₹30K: Full TDS on current withdrawal
#    - If partially crosses threshold: TDS only on amount exceeding ₹30K

TDS_THRESHOLD = ₹30,000 per FY
TDS_RATE = 10%

Example:
- Creator has earned ₹35K this FY, withdrawn ₹25K
- New withdrawal request: ₹8K
- Taxable: (₹25K + ₹8K) - ₹30K = ₹3K
- TDS deducted: ₹3K × 10% = ₹300
- Creator receives: ₹8K - ₹300 = ₹7,700
```

**Acceptance Criteria**:
- TDS calculated correctly for partial threshold crossing
- TDS deducted only from taxable portion (not full withdrawal)
- Financial year resets on April 1st
- TDS amount displayed before withdrawal confirmation
- Form 16A issued quarterly for TDS deducted (compliance requirement)

**Dependencies**: REQ-DB-012 (transactions table), REQ-DB-015 (withdrawals table)

**Conflicts**:
- ⚠️ **REQ-ES-004 shows ₹10K threshold** - CORRECTED to ₹30K per legal standards (Section 194J)

**Technical Notes**:
- Use `DECIMAL(12,2)` for all financial calculations to prevent rounding errors
- TDS deduction recorded in `withdrawals.tds_amount` column
- Generate TDS certificates via integration with income tax portal

---

### REQ-CP-004: KYC Verification (Lazy KYC)
**Priority**: P0 (Critical)
**Type**: Functional
**User Story**: As a creator, I want to start earning immediately and complete KYC only when I'm ready to withdraw funds.

**Description**:
Implement lazy KYC - creators can earn immediately, but must complete KYC verification before first withdrawal request.

**Required KYC Fields** (from PRD lines 2104-2111):
| Field | Required | Verification Method |
|-------|----------|---------------------|
| PAN Number | ✅ Yes | NSDL/UTI API verification |
| PAN Name | ✅ Yes | Must match bank account name |
| Bank Account Number | ✅ Yes | Penny drop verification (₹1 test deposit) |
| IFSC Code | ✅ Yes | RBI IFSC API validation |
| UPI ID | ❌ Optional | UPI handle verification |
| Aadhaar | ❌ Optional | DigiLocker / UIDAI eKYC (for higher limits) |

**Acceptance Criteria**:
- Creators can accept paid calls and earn diamonds without KYC
- First withdrawal request triggers KYC prompt (modal/screen)
- All required fields validated via government APIs in real-time
- PAN name must match bank account name (case-insensitive)
- Penny drop verification confirms active bank account
- KYC status: pending → in_review → approved/rejected
- Approved KYC cached for future withdrawals (one-time verification)

**Dependencies**: REQ-DB-006 (users table with KYC fields), REQ-API-039 (KYC submission endpoint)

**Technical Notes**:
- Use Razorpay X Fund Account Validation API for penny drop
- Use ClearTax / Surepass for PAN verification
- Store KYC documents encrypted (AES-256) if file uploads added later
- PAN and bank account numbers stored as encrypted fields

---

### REQ-CP-005: Auto-Select Payout Method by Amount
**Priority**: P1 (High)
**Type**: Functional
**User Story**: As a creator, I want the system to automatically choose the fastest cost-effective payout method for my withdrawal.

**Description**:
System automatically selects optimal payout method based on withdrawal amount:
- **IMPS**: For amounts ₹1 - ₹2,00,000 (instant transfer, ₹5 platform fee)
- **NEFT**: For amounts > ₹2,00,000 (next banking day, free)

**Acceptance Criteria**:
- Amounts ≤₹2L processed via IMPS (typically credited within 30 minutes)
- Amounts >₹2L processed via NEFT (credited same/next banking day depending on cutoff)
- Withdrawal summary shows selected method and estimated credit time
- Platform absorbs IMPS ₹5 fee (not deducted from creator)
- Creator cannot override auto-selection (simplifies UX, prevents confusion)

**Dependencies**: REQ-CP-002 (payout processing), REQ-CP-009 (Razorpay X integration)

**Technical Notes**:
- Razorpay X supports both IMPS and NEFT via single API
- NEFT cutoff times: 8:00 AM - 6:30 PM on banking days
- Requests after NEFT cutoff processed next banking day

---

### REQ-CP-006: Failed Payout Retry Strategy
**Priority**: P1 (High)
**Type**: Functional
**User Story**: As a creator, I want the system to automatically retry failed payouts for technical issues so I don't have to re-initiate.

**Description**:
Implement hybrid retry approach for failed payout attempts:

**Retry Logic**:
1. **Technical Failures** (bank downtime, network timeout):
   - Auto-retry 3 times over 24 hours (attempts at 0h, 6h, 18h)
   - If all retries fail, mark as "failed_technical" and notify creator

2. **Validation Failures** (invalid account, incorrect IFSC):
   - Fail immediately, no retries
   - Notify creator to update bank details

3. **Both Cases**: Balance returns to "available" state (can be re-withdrawn)

**Failure Categories**:
- `failed_technical`: Bank/gateway downtime, network errors → Auto-retry eligible
- `failed_validation`: Invalid account number, incorrect IFSC → Immediate failure
- `failed_insufficient`: Insufficient platform balance (rare) → Manual resolution

**Acceptance Criteria**:
- Failed payouts marked with specific failure reason
- Creator notified via push notification and in-app alert
- Balance returned to available state within 5 minutes of final failure
- Retry history visible in withdrawal details (attempt 1/3, 2/3, etc.)
- After 3 failed retries, creator must re-initiate withdrawal

**Dependencies**: REQ-CP-002 (payout flow), REQ-API-038 (withdrawal status webhook)

**Technical Notes**:
- Use Razorpay X webhook for payout status updates
- Store retry count in `withdrawals.retry_count` column
- Exponential backoff: 0h, 6h, 18h (total 24h window)

---

### REQ-CP-007: On-Demand Withdrawal Initiation
**Priority**: P0 (Critical)
**Type**: Functional
**User Story**: As a creator, I want to initiate withdrawals whenever I choose (not wait for fixed schedule) so I have maximum flexibility.

**Description**:
Creators can initiate withdrawal requests anytime once minimum threshold (₹100) is met. No fixed weekly/monthly payout schedules.

**Acceptance Criteria**:
- Withdrawal button enabled when available balance ≥ ₹100
- No limit on withdrawal frequency (subject to daily/monthly limits)
- Creator can withdraw multiple times per day (if balance permits)
- Standard for gig economy platforms (Uber, Swiggy, Upwork model)

**Dependencies**: REQ-CP-001 (minimum threshold), REQ-CP-008 (daily/monthly limits)

**UX Notes**:
- Display estimated credit time based on current time and payout method
- Show remaining daily/monthly limit on withdrawal screen

---

### REQ-CP-008: Withdrawal Limits (Fraud Prevention)
**Priority**: P1 (High)
**Type**: Functional
**User Story**: As a platform, I want to enforce withdrawal limits to prevent fraud and money laundering while allowing legitimate creators to cash out.

**Description**:
Enforce daily and monthly withdrawal limits as per PRD (lines 2113-2122):

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| Minimum withdrawal | ₹100 | Creator-friendly, frequent access |
| Maximum per day | ₹50,000 | Fraud prevention, AML compliance |
| Maximum per month | ₹5,00,000 | Prevents large-scale money laundering |
| Processing time | T+1 (within 24 hours) | Standard banking timeline |

**Acceptance Criteria**:
- Withdrawal requests exceeding daily limit rejected with clear error message
- Limits reset: Daily at 12:00 AM IST, Monthly on 1st of month
- Display remaining limits on withdrawal screen
- High-volume creators (>₹5L/month) directed to contact support for higher limits
- Limits tracked per creator (not per bank account)

**Dependencies**: REQ-CP-002 (payout processing), REQ-DB-015 (withdrawals table)

**Technical Notes**:
- Query sum of withdrawals in current day/month for limit check
- Use UTC timestamps, convert to IST for limit reset calculations
- Future enhancement: Dynamic limits based on creator tier or history

---

### REQ-CP-009: Razorpay X Payout Integration
**Priority**: P0 (Critical)
**Type**: Technical
**User Story**: As a platform, I need a reliable payment gateway to process creator payouts with high success rates and compliance.

**Description**:
Integrate Razorpay X (Razorpay's payout product) for all creator withdrawals. Razorpay X supports IMPS, NEFT, UPI, and provides webhook notifications for payout status.

**Integration Requirements**:
1. **Fund Account Creation**: Store creator bank details as Razorpay Fund Account
2. **Payout API**: Create payout via `/v1/payouts` endpoint
3. **Webhook Handling**: Listen for `payout.processed`, `payout.failed` events
4. **Balance Management**: Monitor Razorpay X account balance, alert if <₹1L

**Acceptance Criteria**:
- All payouts processed through Razorpay X API
- Webhook updates withdrawal status in real-time
- Failed payouts trigger retry logic (REQ-CP-006)
- Razorpay X dashboard accessible for manual reconciliation
- Payout reference IDs stored in `withdrawals.gateway_reference_id`

**Dependencies**: REQ-CP-004 (KYC bank details), REQ-API-038 (withdrawal API)

**Technical Notes**:
- Use Razorpay X test mode for staging environment
- Store Razorpay API keys in environment variables (not hardcoded)
- Implement idempotency keys to prevent duplicate payouts
- Webhook signature verification required for security

**Razorpay X Pricing** (as of 2026):
- IMPS: ₹5 per transaction
- NEFT: Free for businesses
- UPI: ₹2 per transaction (if enabled later)

---

### REQ-CP-010: No Retroactive Tier Adjustment
**Priority**: P1 (High)
**Type**: Functional
**User Story**: As a creator, I want my earnings locked at the tier rate when earned so I know exactly how much I'll receive.

**Description**:
When a creator's revenue share tier changes (75% → 80% → 85%), the new tier applies only to future earnings. Existing available balance is NOT recalculated.

**Example**:
```
Jan 1-15: Creator earns ₹10,000 at Tier 1 (75%) = ₹7,500 available
Jan 16: Creator crosses ₹50K/month threshold → Tier 2 (80%)
Jan 16-31: Creator earns ₹15,000 at Tier 2 (80%) = ₹12,000 available

Total available: ₹7,500 + ₹12,000 = ₹19,500
NOT: (₹10K + ₹15K) × 80% = ₹20,000 ❌
```

**Acceptance Criteria**:
- Earnings locked at tier rate at time of transaction
- Tier changes do not retroactively adjust available balance
- Creator dashboard shows current tier and next tier threshold
- Tier calculation runs daily via cron job (REQ-AW-005)

**Dependencies**: REQ-RM-003 (tiered revenue model), REQ-DB-014 (creator_performance table)

**Technical Notes**:
- Store tier_percent in each transaction record (transactions.creator_tier_percent)
- Withdrawal calculates sum of (diamonds × tier_percent) from transactions table
- Simpler logic, fairer to creators, prevents balance volatility

---

## Non-Functional Requirements

### REQ-CP-011: Payout Processing Time
**Priority**: P1 (High)
**Type**: Performance

**Description**:
- Withdrawal request to Razorpay X API call: <2 seconds
- IMPS bank credit: Typically 30 minutes (T+0)
- NEFT bank credit: T+1 (within 24 hours)
- 99.5% uptime for payout service (tolerates 3.6 hours downtime/month)

---

### REQ-CP-012: Financial Accuracy
**Priority**: P0 (Critical)
**Type**: Security

**Description**:
- All financial calculations use `DECIMAL(12,2)` precision (no floating-point errors)
- TDS calculation accurate to nearest rupee (no rounding errors that accumulate)
- Withdrawal amount + TDS deducted + platform fee = Total debited from available balance (exact reconciliation)

---

### REQ-CP-013: Payout Audit Trail
**Priority**: P1 (High)
**Type**: Compliance

**Description**:
- Every withdrawal logged with: creator_id, amount, TDS, method, status, timestamps
- Audit trail includes: request time, processing time, completion time, failure reason
- Logs retained for 7 years (Indian tax compliance requirement)
- Withdrawals table indexed by creator_id, status, created_at for fast queries

---

## Summary

**Total Requirements**: 13 (10 functional, 3 non-functional)
**Priority Breakdown**:
- P0 (Critical): 7 requirements
- P1 (High): 6 requirements

**Key Design Decisions**:
1. ✅ **T+1 without holding period** - Fast payouts prioritized over fraud buffer
2. ✅ **₹100 minimum** - Creator-friendly, enables frequent small withdrawals
3. ✅ **₹30K TDS threshold** - Legally accurate per Section 194J
4. ✅ **Lazy KYC** - Earn first, verify at withdrawal (better conversion)
5. ✅ **Auto-select IMPS/NEFT** - System optimizes cost vs speed
6. ✅ **Direct creators only (MVP)** - Defer agency split payouts to post-MVP

**Critical Conflicts Resolved**:
- ⚠️ **PRD ₹500 minimum → ₹100** (user approval)
- ⚠️ **REQ-ES-004 ₹10K TDS → ₹30K** (legal accuracy)
- ⚠️ **PRD 7-day hold → Removed** (faster payouts)

**Implementation Priority**:
1. **Sprint 1**: REQ-CP-001, 002, 003, 004, 007, 009 (core payout flow)
2. **Sprint 2**: REQ-CP-005, 006, 008, 010 (optimization + limits)
3. **Sprint 3**: REQ-CP-011, 012, 013 (performance + compliance)

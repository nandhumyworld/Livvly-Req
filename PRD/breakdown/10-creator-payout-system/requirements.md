# Requirements: Creator Payout System

**Section**: 10-creator-payout-system
**Source Lines**: 2003-2125
**Generated**: 2026-01-20
**Status**: Complete

---

## Payout Flow Requirements

### REQ-CP-001: Diamond Earning Flow
**Priority**: P0 (Critical)
**Type**: Business

**Description**: When users spend coins on a creator (calls, gifts, room contributions), diamonds must be credited instantly to the creator's pending balance.

**Rationale**: Creators need to see their earnings immediately for engagement and trust.

**Acceptance Criteria**:
- Diamonds credited within seconds of coin spend
- Creator earns 45% of coin value as diamonds (₹0.45 per coin)
- Earnings credited to pending_balance (not available)
- Real-time notification to creator of earnings
- Earnings visible in creator earnings dashboard
- Transaction record created for each earning event

**Dependencies**: REQ-CE-006, REQ-DB-005
**Related Design Decisions**: DD-DB-002

**Notes**: Formula: Diamonds = Coins × ₹1 × 0.45

---

### REQ-CP-002: 7-Day Holding Period
**Priority**: P0 (Critical)
**Type**: Business

**Description**: All diamond earnings must have a fixed 7-day holding period before becoming available for withdrawal.

**Rationale**: Holding period protects against fraud, chargebacks, and allows time for dispute resolution.

**Acceptance Criteria**:
- All new earnings go to pending_balance
- After exactly 7 days, earnings move to available_balance
- Daily batch job processes matured earnings
- Creator dashboard shows: pending, available, total earned
- Pending earnings show estimated availability date
- Fixed 7 days for all creators (no tier-based variation)

**Dependencies**: REQ-CP-001
**Related Design Decisions**: None

**Notes**: Stakeholder confirmed fixed 7-day hold for MVP simplicity.

---

### REQ-CP-003: Balance Movement Job
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: A scheduled job must run daily to move matured pending earnings to available balance.

**Rationale**: Automates the pending -> available transition based on holding period.

**Acceptance Criteria**:
- Job runs daily (e.g., 00:00 IST)
- Processes all earnings older than 7 days
- Atomic transaction for balance updates
- Logging of processed amounts per creator
- Error handling with retry mechanism
- No partial processing (all-or-nothing per creator)

**Dependencies**: REQ-CP-002, REQ-TA-017
**Related Design Decisions**: DD-TA-005

**Notes**: Implement via Supabase pg_cron or Edge Function scheduler.

---

## Withdrawal Requirements

### REQ-CP-004: Withdrawal Request
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Creators must be able to request withdrawal of available balance with specified limits.

**Rationale**: Core payout functionality - creators need to access their earnings.

**Acceptance Criteria**:
- Minimum withdrawal: ₹500
- Maximum per request: ₹50,000
- Maximum per day: ₹50,000
- Maximum per month: ₹5,00,000
- Can only withdraw from available_balance
- Withdrawal amount deducted from available immediately
- Creates withdrawal record with "pending" status

**Dependencies**: REQ-CP-002, REQ-API-015
**Related Design Decisions**: None

**Notes**: Limits are configurable by admin for future adjustments.

---

### REQ-CP-005: TDS Calculation
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: TDS (Tax Deducted at Source) must be calculated at 10% for earnings exceeding ₹30,000 per financial year.

**Rationale**: Required by Indian Income Tax Act Section 194J for professional services.

**Acceptance Criteria**:
- TDS threshold: ₹30,000 per financial year (April-March)
- TDS rate: 10% on amount exceeding threshold
- Calculate total FY earnings before applying TDS
- Handle partial threshold crossing correctly
- TDS amount deducted from withdrawal
- Net amount = Gross - TDS
- TDS certificate generation capability (Form 16A)

**Dependencies**: REQ-DB-013
**Related Design Decisions**: None

**Notes**: Creator responsible for tax filing; platform only deducts TDS.

---

### REQ-CP-006: Payout Processing
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Withdrawal requests must be processed via Razorpay X for bank transfers with T+1 settlement.

**Rationale**: Razorpay X provides reliable payout infrastructure with India-wide coverage.

**Acceptance Criteria**:
- Integration with Razorpay X Payout API
- Supports bank transfer (IMPS/NEFT) and UPI
- Processing within 24 hours of request (T+1)
- Automatic retry on temporary failures
- Webhook handling for payout status updates
- Final status update to withdrawal record

**Dependencies**: REQ-CP-004, REQ-TA-007
**Related Design Decisions**: None

**Notes**: IMPS preferred for instant settlement; NEFT as fallback.

---

### REQ-CP-007: Payout Status Tracking
**Priority**: P1 (High)
**Type**: Business

**Description**: Creators must be able to track withdrawal status through completion.

**Rationale**: Transparency in payout status builds creator trust.

**Acceptance Criteria**:
- Status flow: pending -> processing -> completed/failed
- Real-time status visible in app
- Push notification on status changes
- Failure reason displayed if payout fails
- Retry option for failed payouts
- Historical payout records accessible

**Dependencies**: REQ-CP-006
**Related Design Decisions**: None

**Notes**: Include expected completion time in status display.

---

## KYC Requirements

### REQ-CP-008: KYC Submission
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Creators must submit and verify KYC before making withdrawals.

**Rationale**: KYC required for regulatory compliance and secure payouts.

**Acceptance Criteria**:
- Required: PAN number, PAN name, bank account, IFSC code
- Optional: UPI ID, Aadhaar
- KYC submission flow in app
- Document upload for PAN card image
- Status tracking: pending, submitted, verified, rejected
- Rejection reason communicated to creator

**Dependencies**: REQ-DB-011
**Related Design Decisions**: None

**Notes**: KYC verification blocks withdrawals until complete.

---

### REQ-CP-009: PAN Verification
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: PAN numbers must be verified against NSDL/UTI database.

**Rationale**: PAN verification ensures identity and enables TDS compliance.

**Acceptance Criteria**:
- Integration with PAN verification API (NSDL/UTI or third-party)
- Verify PAN format (XXXXX0000X pattern)
- Verify PAN name matches provided name
- Verify PAN is active and valid
- Store verification timestamp and status
- Handle verification failures gracefully

**Dependencies**: REQ-CP-008
**Related Design Decisions**: None

**Notes**: Consider third-party services like Digio, Karza for verification.

---

### REQ-CP-010: Bank Account Verification
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Bank accounts must be verified via penny drop before enabling payouts.

**Rationale**: Penny drop confirms account exists and is linked to correct person.

**Acceptance Criteria**:
- Penny drop verification (₹1 transfer)
- Verify IFSC code via RBI API
- Verify account number format for bank type
- Store verification status and timestamp
- Bank name auto-populated from IFSC
- Re-verification required if details change

**Dependencies**: REQ-CP-008, REQ-TA-007
**Related Design Decisions**: None

**Notes**: Razorpay provides penny drop API via Fund Account Validation.

---

### REQ-CP-011: Name Matching Validation
**Priority**: P1 (High)
**Type**: Compliance

**Description**: PAN name must match bank account holder name for payout eligibility.

**Rationale**: Prevents payouts to third-party accounts; compliance requirement.

**Acceptance Criteria**:
- Compare PAN registered name with bank account name
- Fuzzy matching for minor variations (Mr., Mrs., initials)
- Flag mismatches for manual review
- Block payouts on significant name mismatches
- Allow admin override with documentation

**Dependencies**: REQ-CP-009, REQ-CP-010
**Related Design Decisions**: None

**Notes**: Consider allowing verified UPI as alternative verification.

---

## Dashboard Requirements

### REQ-CP-012: Creator Earnings Dashboard
**Priority**: P0 (Critical)
**Type**: UX

**Description**: Creators must have a comprehensive dashboard showing earnings, balances, and payout history.

**Rationale**: Transparency and easy access to financial information drives creator satisfaction.

**Acceptance Criteria**:
- Show: pending balance, available balance, total earned
- Today's earnings breakdown (calls, gifts, room)
- Weekly/monthly earnings trends
- Pending earnings with availability dates
- Recent transactions list
- Quick withdraw button from dashboard

**Dependencies**: REQ-DB-005, REQ-DB-006
**Related Design Decisions**: None

**Notes**: Real-time updates for new earnings.

---

### REQ-CP-013: Payout History
**Priority**: P1 (High)
**Type**: UX

**Description**: Creators must be able to view complete payout history with details.

**Rationale**: Financial record-keeping for creators.

**Acceptance Criteria**:
- List of all withdrawal requests
- Each shows: date, amount, TDS, net amount, status
- Filter by status, date range
- Download statement option (PDF)
- TDS certificate download for tax filing
- Pagination for long history

**Dependencies**: REQ-DB-013
**Related Design Decisions**: None

**Notes**: Statement should include all info needed for tax filing.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-CP-001 | Diamond Earning Flow | P0 | Business |
| REQ-CP-002 | 7-Day Holding Period | P0 | Business |
| REQ-CP-003 | Balance Movement Job | P0 | Technical |
| REQ-CP-004 | Withdrawal Request | P0 | Business |
| REQ-CP-005 | TDS Calculation | P0 | Compliance |
| REQ-CP-006 | Payout Processing | P0 | Technical |
| REQ-CP-007 | Payout Status Tracking | P1 | Business |
| REQ-CP-008 | KYC Submission | P0 | Compliance |
| REQ-CP-009 | PAN Verification | P0 | Technical |
| REQ-CP-010 | Bank Account Verification | P0 | Technical |
| REQ-CP-011 | Name Matching Validation | P1 | Compliance |
| REQ-CP-012 | Creator Earnings Dashboard | P0 | UX |
| REQ-CP-013 | Payout History | P1 | UX |

**Total Requirements**: 13
**P0 (Critical)**: 10
**P1 (High)**: 3

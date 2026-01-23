# Creator Payout System - Questions & Answers

**Section**: 10. Creator Payout System
**Date**: 2026-01-23
**Answered By**: User
**Total Questions**: 9

---

## Core Questions (Q1-Q3)

### Q1: What should be the minimum withdrawal amount for creator payouts?

**Answer**: ₹100 minimum (Recommended)

**Rationale**:
- Creator-friendly approach enables frequent smaller withdrawals
- Matches approved REQ-ES-004 for consistency across requirements
- Builds trust with new creators who can test payout system with lower risk
- Standard for gig economy platforms (Swiggy, Zomato allow small daily cashouts)

**PRD Conflict**:
- ⚠️ PRD line 2117 shows **₹500 minimum** → CORRECTED to ₹100 per user approval
- Justification: Lower barrier to entry for new creators, better UX

**Impact on Requirements**:
- ✅ REQ-CP-001: Minimum withdrawal set to ₹100
- ✅ REQ-CP-007: On-demand withdrawals more accessible

---

### Q2: What should be the TDS withholding threshold for creator earnings?

**Answer**: ₹30,000 threshold (Recommended)

**Rationale**:
- Legally accurate per **Section 194J of Indian Income Tax Act**
- Standard threshold for "professional services" and "technical services"
- Reduces compliance risk compared to non-standard ₹10K threshold
- Aligns with PRD implementation (lines 2054-2100)

**REQ Conflict**:
- ⚠️ REQ-ES-004 currently shows **₹10,000 threshold** → REQUIRES UPDATE to ₹30K
- Justification: Legal compliance > earlier informal assumption

**Impact on Requirements**:
- ✅ REQ-CP-003: TDS @ 10% for earnings >₹30K per financial year
- ⚠️ **Action Required**: Update REQ-ES-004 from ₹10K to ₹30K in Executive Summary section

**Legal Reference**:
- Section 194J: "Any person responsible for paying any sum to a resident by way of... professional services... shall deduct TDS @ 10% if aggregate payment exceeds ₹30,000 in a financial year."
- Financial Year: April 1 - March 31

---

### Q3: How should the holding period and T+1 payout flow work?

**Answer**: T+1 only (no holding period)

**Rationale**:
- Prioritizes creator satisfaction with fastest possible payouts (next day bank credit)
- Reduces friction - creators can withdraw immediately after earning
- Competitive advantage over platforms with 7-14 day holding periods
- REQ-ES-004 states "T+1 payout" which technically means T+1 without additional holding

**PRD Conflict**:
- ⚠️ PRD lines 2022-2024 show **7-day holding period ("Pending Balance" state)** → REMOVED per user approval
- ⚠️ Simplifies flow diagram: Earnings → Available Balance → Withdrawal → T+1 Bank Credit

**Impact on Requirements**:
- ✅ REQ-CP-002: Diamonds instantly credited to "available balance" (no pending state)
- ✅ REQ-CP-007: On-demand withdrawals immediately after earning

**Trade-offs Acknowledged**:
- ⚠️ **Increased fraud risk**: Without 7-day buffer, platform absorbs cost if user disputes charge after creator withdrew
- ✅ **Mitigation strategies**:
  - AI-powered moderation (REQ-FS-022) detects fraudulent calls in real-time
  - Daily withdrawal limit ₹50K (REQ-CP-008) caps maximum fraud exposure
  - User payment authentication (OTP, biometric) reduces unauthorized charges
  - Platform insurance fund for chargebacks (budget ₹5L/year buffer)

**Payout Timeline Example**:
```
Monday 3:00 PM: User spends 100 coins on creator call
Monday 3:00 PM: Creator's available balance instantly increases by ₹68 (75% tier)
Monday 11:00 PM: Creator initiates withdrawal request for ₹500
Tuesday 2:00 PM: Bank credit appears in creator's account (T+1)

Total time: Earning to bank credit = ~23 hours
```

---

## Follow-up Questions (Q4-Q6)

### Q4: How should creator tier changes affect pending earnings?

**Answer**: No retroactive adjustment (Recommended)

**Rationale**:
- **Simplicity**: Earnings locked at tier rate when earned, no complex recalculations
- **Fairness**: Creator knows exact amount immediately upon earning
- **Predictability**: Available balance doesn't change based on future performance
- Prevents creator confusion ("Why did my balance decrease?")

**Implementation**:
- Store `tier_percent` (0.75, 0.80, or 0.85) in each transaction record at time of earning
- Withdrawal sums: `(diamonds × tier_percent)` from transactions table
- New tier applies only to earnings AFTER tier change timestamp

**Example**:
```
Jan 1-15: Creator earns ₹10,000 at Tier 1 (75%) = ₹7,500 available
Jan 16: Creator crosses ₹50K trailing 30-day earnings → Tier 2 (80%)
Jan 16-31: New earnings ₹15,000 at Tier 2 (80%) = ₹12,000 available

Total available balance: ₹7,500 + ₹12,000 = ₹19,500

NOT retroactive: (₹10K + ₹15K) × 80% = ₹20K ❌ (would confuse creators)
```

**Impact on Requirements**:
- ✅ REQ-CP-010: Tier changes affect only future earnings
- ✅ REQ-DB-010: Transactions table includes `creator_tier_percent` column

---

### Q5: When should creators complete KYC verification?

**Answer**: Before first withdrawal (Recommended)

**Rationale**:
- **Lazy KYC**: Maximizes creator conversion - start earning immediately, verify identity later
- **Compliance**: RBI guidelines allow earning accumulation before KYC (KYC required for fund disbursement)
- **Better UX**: Creators can test platform and build earnings before committing personal info
- **Industry standard**: Uber, Swiggy, YouTube all use lazy KYC (earn first, verify at payout)

**KYC Trigger Flow**:
```
1. New creator signs up → No KYC prompt (can immediately go live)
2. Creator accepts paid calls, earns diamonds → Available balance accumulates
3. Creator clicks "Withdraw" when balance ≥₹100 → KYC modal appears
4. Creator submits: PAN, Bank Account, IFSC → Real-time verification via APIs
5. KYC approved → Withdrawal proceeds, future withdrawals skip KYC (one-time)
```

**Required KYC Fields** (PRD lines 2104-2111):
| Field | Required | Verification API |
|-------|----------|------------------|
| PAN Number | ✅ Yes | NSDL/UTI API (government) |
| PAN Name | ✅ Yes | Must match bank account name |
| Bank Account | ✅ Yes | Razorpay Penny Drop (₹1 test deposit) |
| IFSC Code | ✅ Yes | RBI IFSC API |

**Impact on Requirements**:
- ✅ REQ-CP-004: Lazy KYC implementation
- ✅ REQ-API-039: POST /api/v1/creators/kyc endpoint
- ✅ REQ-FS-024: Creator wallet accessible before KYC completion

---

### Q6: How should payout method selection work?

**Answer**: Auto-select IMPS/NEFT by amount (Recommended)

**Rationale**:
- **Optimal cost vs speed**: System automatically chooses best method for each withdrawal amount
- **Simplifies UX**: Creators don't need to understand IMPS vs NEFT differences
- **Cost efficiency**: Platform absorbs ₹5 IMPS fee for small amounts, uses free NEFT for large amounts

**Auto-Selection Logic**:
```python
if withdrawal_amount <= 200_000:  # ₹2 Lakhs
    method = "IMPS"
    estimated_credit = "Within 30 minutes (instant)"
    platform_fee = 5  # ₹5 absorbed by platform
else:
    method = "NEFT"
    estimated_credit = "Next banking day (T+1)"
    platform_fee = 0  # Free
```

**Rationale for ₹2L Threshold**:
- 90%+ of creator withdrawals likely <₹2L (based on ₹50K daily limit)
- IMPS instant credit worth ₹5 fee for smaller amounts (better creator experience)
- NEFT free for large amounts (>₹2L) where instant credit less critical

**Impact on Requirements**:
- ✅ REQ-CP-005: Auto-select payout method by amount
- ✅ REQ-CP-009: Razorpay X integration supports both IMPS and NEFT via single API

**NEFT Cutoff Times** (Important):
- Banking hours: 8:00 AM - 6:30 PM IST on banking days (Mon-Fri, excluding holidays)
- Requests after 6:30 PM or on weekends/holidays queued for next banking day

---

## Gap Analysis Questions (Q7-Q9)

### Q7: How should failed payout attempts be handled?

**Answer**: Hybrid retry approach (Recommended)

**Rationale**:
- **Better UX**: Automatically retry technical failures (bank downtime) without creator action
- **Prevents unnecessary friction**: Creator shouldn't re-initiate withdrawal for issues beyond their control
- **Fail fast for validation errors**: Don't waste time retrying invalid account numbers

**Retry Strategy**:

**Category 1: Technical Failures** (Auto-retry)
- Examples: Bank gateway timeout, Razorpay X downtime, network errors
- Action: Auto-retry 3 times over 24 hours
- Schedule: Attempt 1 (immediate), Attempt 2 (+6 hours), Attempt 3 (+18 hours)
- If all retries fail → Status: `failed_technical`, notify creator to contact support

**Category 2: Validation Failures** (Immediate fail)
- Examples: Invalid account number, incorrect IFSC, account closed, name mismatch
- Action: Fail immediately, no retries
- Notify creator to update bank details in KYC section

**Both Cases**: Balance returned to "available" state within 5 minutes of failure

**Impact on Requirements**:
- ✅ REQ-CP-006: Failed payout retry strategy
- ✅ REQ-DB-015: `withdrawals.retry_count` and `withdrawals.failure_reason` columns
- ✅ REQ-API-038: Webhook handling for `payout.failed` events from Razorpay X

**Notification Examples**:
```
Technical failure after 3 retries:
"Your withdrawal of ₹500 could not be processed due to bank downtime.
Your balance has been restored. Please try again later or contact support."

Validation failure:
"Your withdrawal failed: Invalid bank account number.
Please update your bank details in Profile > KYC and try again."
```

---

### Q8: What payout schedule should creators follow?

**Answer**: On-demand withdrawals (Recommended)

**Rationale**:
- **Maximum flexibility**: Creators control when they receive money (not platform-dictated schedule)
- **Industry standard**: All major gig platforms (Uber, Swiggy, Upwork, Fiverr) offer on-demand withdrawals
- **Better creator retention**: Flexibility improves creator satisfaction and reduces churn
- **Emergency access**: Creators can access earnings immediately for urgent needs

**Implementation**:
- Withdrawal button enabled whenever available balance ≥ ₹100
- No restrictions on withdrawal frequency (subject to daily ₹50K, monthly ₹5L limits)
- Creator can withdraw multiple times per day if desired

**Comparison to Fixed Schedule**:
| Approach | Pros | Cons |
|----------|------|------|
| **On-Demand** ✅ | Flexibility, creator satisfaction, competitive advantage | Slightly higher transaction costs (more frequent payouts) |
| Fixed Schedule ❌ | Predictable, lower transaction volume | Frustrates creators needing faster access, not industry standard |

**Impact on Requirements**:
- ✅ REQ-CP-007: On-demand withdrawal initiation
- ✅ REQ-CP-001 & REQ-CP-008: Withdrawal enabled when ≥₹100 and within daily/monthly limits

**UX Note**:
- Display estimated credit time on withdrawal screen:
  - "Your ₹1,500 withdrawal will be credited within 30 minutes via IMPS"
  - "Your ₹3,00,000 withdrawal will be credited tomorrow via NEFT"

---

### Q9: Should agency-represented creators be supported in MVP?

**Answer**: Direct creators only for MVP (Recommended)

**Rationale**:
- **Reduces MVP complexity**: Defer agency split payouts (85% creator / 15% agency) to post-MVP
- **Focuses on core UX**: Perfect direct creator experience first before adding complexity
- **Limited MVP usage**: Most early creators likely direct (agencies come later at scale)
- **Faster time to market**: Shipping core features 2-3 weeks earlier

**Agency Support Requirements (Deferred to Post-MVP)**:
If added later, would require:
1. **Dual onboarding**: Separate KYC for agency entity (GST registration, business PAN)
2. **Split payout logic**: Single earning split into two payouts (85/15)
3. **Agency dashboard**: Track all represented creators' earnings
4. **Reconciliation**: Monthly statements for agencies with earnings breakdown
5. **Dispute resolution**: Handle creator-agency payment conflicts

**Impact on Requirements**:
- ✅ REQ-CP-002, 004, 007: All payout requirements assume direct creator-to-platform relationship
- ✅ Simplifies database schema: No `creator_agency` or `agency_split_config` tables in MVP
- ⏭️ **Future Enhancement**: Agency support tagged as "Phase 2" in implementation roadmap

**Post-MVP Timeline**:
- Estimate: 2-3 weeks development for agency payout system after MVP launch
- Trigger: When ≥10 agencies request representation feature (product-market fit signal)

---

## Summary

**Total Questions Answered**: 9
**Core Questions**: 3
**Follow-up Questions**: 3
**Gap Analysis Questions**: 3

**Critical Decisions Made**:
1. ✅ ₹100 minimum withdrawal (PRD conflict resolved)
2. ✅ ₹30K TDS threshold (legal accuracy, REQ-ES-004 update required)
3. ✅ T+1 without holding period (PRD conflict, prioritizes speed over fraud buffer)
4. ✅ No retroactive tier adjustment (simplicity and fairness)
5. ✅ Lazy KYC (earn first, verify at withdrawal)
6. ✅ Auto-select IMPS/NEFT (optimizes cost vs speed)
7. ✅ Hybrid retry for failed payouts (better UX)
8. ✅ On-demand withdrawals (maximum flexibility)
9. ✅ Direct creators only for MVP (reduces complexity)

**Requirements Impacted**: 13 total requirements (REQ-CP-001 through REQ-CP-013)

**PRD Conflicts Identified & Resolved**:
1. ⚠️ Minimum withdrawal: PRD ₹500 → **CORRECTED to ₹100**
2. ⚠️ Holding period: PRD 7 days → **REMOVED (T+1 only)**
3. ⚠️ TDS threshold: REQ-ES-004 ₹10K → **UPDATE to ₹30K**

**Next Steps**:
- ✅ Extract design decisions for payout architecture
- ✅ Update REQ-ES-004 to reflect ₹30K TDS threshold
- ✅ Document trade-offs of removing holding period (fraud risk mitigation)

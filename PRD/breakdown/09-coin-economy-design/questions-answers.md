# Section 9: Coin Economy Design - Questions & Answers

**Section:** Coin Economy Design
**Lines:** 1928-2002 (75 lines)
**Complexity:** Moderate
**Date:** 2026-01-20

---

## CRITICAL CONFLICTS IDENTIFIED

### Conflict 1: Revenue Split Formula (MOST CRITICAL)
**PRD Section 9.4 (Lines 1984-1987):**
```
Platform Revenue = X × ₹1 × 55% = ₹0.55X
Creator Diamonds = X × ₹1 × 45% = ₹0.45X
```

**Conflicts with Approved Requirements:**
- **REQ-RM-001** (Section 4): 75-85% tiered creator share (NOT 45%)
- **REQ-ES-004** (Section 1): Dynamic tiered model
- **REQ-API-002** (Section 8): CRITICAL FIX implemented - 75-85% calculation
- **REQ-DB-006** (Section 7): Seed data corrected to match 75-85% model

**Impact:** This is the SAME 45% error we fixed in Section 8 API Specifications!

---

### Conflict 2: Call Pricing Structure
**PRD Section 9.2 (Lines 1943-1961):**
- Fixed rates by user type: Regular (5/8), Popular (8/12), Verified (12/18), Celebrity (20/30)
- Peak hour multiplier (1.5x)

**Conflicts with Approved Requirements:**
- **REQ-RM-005** (Section 4): Creators set their own rates (₹8-25/min)
- **REQ-DB-004** (Section 7): creator_call_rates table for custom rates
- **REQ-API-001** (Section 8): PUT /api/v1/creator/rates endpoint

**Issue:** PRD shows fixed rates by user tier, but approved model allows creator flexibility

---

### Conflict 3: Gift Pricing Creator Earnings
**PRD Section 9.3 (Lines 1965-1976):**
- Shows "Creator Earns" using 45% calculation
- Example: Rose (10 coins) → Creator earns ₹4.50 (45%)

**Should be:**
- Rose (10 coins) → Creator earns ₹7.50-8.50 (75-85% based on tier)

---

### Conflict 4: Coin Package Discrepancy
**PRD Section 9.1 (Lines 1932-1939):**
- Shows Starter package: ₹49 = 50 coins

**Conflicts with:**
- **REQ-RM-004** (Section 4): Minimum package ₹99 = 110 coins
- **REQ-DB-006** (Section 7): Seed data starts at ₹99

**Issue:** PRD includes ₹49 package not in approved requirements

---

## Core Questions (Q1-Q3)

### Q1: Revenue Split Correction (CRITICAL)
The PRD shows 45% creator share (platform 55%, creator 45%) in Section 9.4, but this conflicts with all approved requirements showing 75-85% tiered creator share.

**Question:**
How should we reconcile the coin economy revenue split?

**Options:**
1. **Correct to 75-85% tiered model** (aligns with REQ-RM-001, REQ-API-002, all approved decisions)
2. Keep PRD's 45% (contradicts all previous approvals)

**Recommendation:** Option 1 - Correct to 75-85% tiered model

**Dependencies:**
- Gift pricing "Creator Earns" column needs recalculation
- Conversion formula needs update
- All financial projections need adjustment

---

### Q2: Call Pricing Model Selection
The PRD shows fixed rates by user type (Regular, Popular, Verified, Celebrity), but approved requirements show creator-set rates (₹8-25/min).

**Question:**
Which call pricing model should the coin economy implement?

**Options:**
1. **Creator-set rates (₹8-25/min)** - Approved in REQ-RM-005, REQ-DB-004, REQ-API-001
   - Pros: Creator autonomy, competitive pricing, aligns with approved architecture
   - Cons: More complex implementation (already implemented in Section 7 & 8)

2. **Fixed rates by tier (PRD's model)** - Regular (5), Popular (8), Verified (12), Celebrity (20)
   - Pros: Simpler coin economy
   - Cons: Less creator flexibility, contradicts approved requirements

3. **Hybrid model** - Default rates by tier + creator override option
   - Pros: Balance of simplicity and flexibility
   - Cons: Most complex

**Recommendation:** Option 1 - Creator-set rates (already approved and implemented)

**Follow-up:** Should we keep peak hour pricing (1.5x during 10 PM - 2 AM IST)?

---

### Q3: Coin Package Minimum
The PRD shows a ₹49 Starter package (50 coins), but approved requirements show minimum ₹99 (110 coins).

**Question:**
Should we include the ₹49 package or start at ₹99?

**Options:**
1. **Start at ₹99** (aligns with REQ-RM-004, REQ-DB-006)
   - Pros: Higher revenue per transaction, already in seed data
   - Cons: Higher barrier to entry

2. **Add ₹49 package** (follow PRD)
   - Pros: Lower barrier to entry, trial package
   - Cons: Contradicts approved seed data, requires database update

**Recommendation:** Ask user preference - lower barrier (₹49) vs approved minimum (₹99)

**Impact on seed data:** If ₹49 added, REQ-DB-006 needs update

---

## Follow-up Questions (Q4-Q6)

### Q4: Coin Expiry Implementation
The PRD defines three expiry rules (purchased: 365 days inactivity, bonus: 90 days, promotional: 30 days).

**Question:**
How should coin expiry be tracked and enforced?

**Options:**
1. **Separate coin balance fields** - purchased_balance, bonus_balance, promo_balance
   - Pros: Clear separation, accurate expiry tracking
   - Cons: More complex wallet logic, multiple balance fields

2. **Transaction-level expiry** - Track expiry_date per coin_transactions record
   - Pros: Granular tracking, audit trail
   - Cons: Complex balance calculation (sum non-expired transactions)

3. **Cron job expiry** - Single balance field, daily job expires coins
   - Pros: Simple balance management
   - Cons: Less granular, harder to show users which coins expire when

**Recommendation:** Option 1 - Separate balance fields (simplest for user UX)

**Database Impact:** coin_wallets table needs purchased_balance, bonus_balance, promo_balance columns

---

### Q5: Refund Policy Implementation
PRD states: "Refunds only within 7 days for technical issues, unused coins only"

**Question:**
How should "unused coins" be determined for refunds?

**Options:**
1. **All-or-nothing** - Refund only if 100% of coins from purchase unused
   - Pros: Simple logic
   - Cons: Harsh (if user spent 1 coin, loses refund eligibility)

2. **Proportional refund** - Refund value of unused coins from specific purchase
   - Pros: Fair to users
   - Cons: Complex tracking (which coins from which purchase were spent)

3. **No partial refunds** - Must be technical issue preventing coin use (not user regret)
   - Pros: Clear policy
   - Cons: Need technical issue verification

**Recommendation:** Option 3 - Technical issues only, verified by support

**Process:** User submits ticket → Support verifies technical issue → Manual refund approval

---

### Q6: Peak Hour Pricing Enforcement
PRD shows 1.5x multiplier during peak hours (10 PM - 2 AM IST).

**Question:**
If we implement creator-set rates (Q2, Option 1), should peak hour pricing still apply?

**Options:**
1. **Apply peak multiplier to creator rates** - Creator sets ₹10/min → peak becomes ₹15/min
   - Pros: Higher creator earnings during peak demand
   - Cons: Unexpected price changes for callers

2. **No peak pricing for creator rates** - Creator rate is fixed 24/7
   - Pros: Predictable pricing, creator controls rate
   - Cons: Doesn't incentivize creators to be online during peak hours

3. **Optional peak pricing** - Creators can opt-in to peak multiplier
   - Pros: Creator choice
   - Cons: More complex implementation

**Recommendation:** Option 2 - No peak pricing (creator-set rates are fixed 24/7)

**Rationale:** If creator wants higher peak rates, they can set higher base rate. Simplifies pricing UX.

---

## Gap Analysis Questions (Q7-Q9)

### Q7: Coin-to-Rupee Conversion Rate
PRD uses 1 coin = ₹1 for all calculations, but coin packages show variable per-coin pricing (₹0.71 - ₹0.98).

**Question:**
What is the canonical coin-to-rupee conversion rate for spending?

**Clarification Needed:**
- When user spends 100 coins on a call/gift, does creator receive ₹100 × tier_percent?
- Or does it depend on how much the user paid per coin (₹0.71 - ₹0.98)?

**Assumption for calculations:** 1 coin = ₹1 when spent (regardless of purchase price)

**Impact:** Gift pricing "Creator Earns" assumes 1 coin = ₹1

**Recommendation:** Confirm 1 coin = ₹1 for all spending calculations (standard for virtual currency)

---

### Q8: Bonus Coins in Revenue Split
PRD shows bonus coins (5-1050 per package), which expire after 90 days.

**Question:**
When users spend bonus coins, do creators earn the same revenue share (75-85%)?

**Options:**
1. **Same revenue share** - Bonus coins = regular coins when spent
   - Pros: Simpler logic, fair to creators
   - Cons: Platform loses more on bonus coins

2. **Lower revenue share for bonus coins** - e.g., 50% instead of 75-85%
   - Pros: Reduces platform loss on bonuses
   - Cons: Complex tracking, unfair to creators

3. **Platform absorbs bonus cost** - Bonus coins deducted from platform share, not creator share
   - Pros: Fair to creators, incentivizes purchases
   - Cons: Platform bears full bonus cost

**Recommendation:** Option 1 - Same revenue share (bonus is marketing cost, not creator penalty)

**Implementation:** No distinction between purchased and bonus coins when calculating creator earnings

---

### Q9: Gift Category Pricing Strategy
PRD defines 4 gift categories: Basic (10-50 coins), Premium (100-200), Luxury (500-1000), Exclusive (5000-10000).

**Question:**
Should gift pricing tiers align with any specific strategy?

**Considerations:**
1. **Psychological pricing** - End prices in 0 or 5 (10, 20, 50, 100, 500, 1000)
2. **Value perception** - Higher-tier gifts should feel exclusive
3. **Call rate equivalency** - 500-coin gift = ~62 minutes at ₹8/min (reference point)

**Question:** Are the gift price points (10, 20, 50, 100, 150, 200, 500, 1000, 5000, 10000) optimal?

**Recommendation:** Keep PRD's price points (psychologically sound)

**Additional Question:** Should we add mid-tier gifts (₹250-400 range)?

---

## Summary of Critical Decisions Needed

### Must-Resolve Before Requirements Extraction:

1. **Q1 (CRITICAL):** Correct revenue split to 75-85% tiered model (vs PRD's 45%)
2. **Q2:** Implement creator-set rates (₹8-25/min) vs fixed tier rates
3. **Q3:** Include ₹49 package or start at ₹99 minimum
4. **Q4:** Coin expiry tracking method (separate balances vs transaction-level)
5. **Q5:** Refund policy enforcement (technical issues only vs proportional)
6. **Q6:** Peak hour pricing (apply to creator rates or not)
7. **Q7:** Confirm 1 coin = ₹1 for spending calculations
8. **Q8:** Bonus coins revenue share (same 75-85% or different)
9. **Q9:** Gift pricing optimization (keep current or adjust)

### Recommended Decisions:
- **Q1:** Correct to 75-85% (aligns with all approved requirements)
- **Q2:** Creator-set rates (already approved and implemented)
- **Q3:** User preference (₹49 trial vs ₹99 minimum)
- **Q4:** Separate balance fields (simplest UX)
- **Q5:** Technical issues only (clear policy)
- **Q6:** No peak pricing (creator rates fixed 24/7)
- **Q7:** Confirm 1 coin = ₹1
- **Q8:** Same revenue share (fair to creators)
- **Q9:** Keep current price points

---

**Next Steps:**
1. User answers Q1-Q9
2. Extract 8-10 requirements based on answers
3. Create coin economy requirements document
4. Present Section 9 demo for approval

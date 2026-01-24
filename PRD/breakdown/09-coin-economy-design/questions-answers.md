# Section 9: Coin Economy Design - Questions & Answers

**Section:** Coin Economy Design
**Lines:** 1928-2002 (75 lines)
**Complexity:** Moderate
**Date:** 2026-01-24
**Session:** Updated with new answers from current session

---

## Question Round 1: Core Questions

### Q1: Revenue Split Model for Coin Economy

**Context:** The PRD shows 45% creator share (platform 55%, creator 45%) in Section 9.4, but this conflicts with previously approved requirements showing 75-85% tiered creator share for calls.

**Question:** How should we reconcile the coin economy revenue split?

**Options Provided:**
1. Correct to 75-85% tiered model (aligns with all previous approvals)
2. Use 55% platform / 45% creator for gifts only (dual model)
3. Keep PRD's 45% for everything

**User Answer:** "Use 55% platform / 45% creator for gifts only"

**Impact:**
- Calls: Use 75-85% tiered model (based on creator performance)
- Gifts: Use 45% fixed creator share (simplified, no tier variation)
- Room gifts: Use 75-85% tiered model (to room owner)
- Blended platform margin: ~23% weighted average (assuming 60% calls, 30% gifts, 10% rooms)

**Decision Recorded:** REQ-CE-002 updated to reflect dual revenue model

---

### Q2: Call Pricing Model

**Context:** The PRD shows fixed rates by user type (Regular, Popular, Verified, Celebrity), but previously approved requirements show creator-set rates (₹8-25/min).

**Question:** Which call pricing model should the coin economy implement?

**Options Provided:**
1. Creator-set rates in rupees (Recommended) - Approved in previous sections
2. Fixed rates by tier (PRD's model) - Regular (5), Popular (8), Verified (12), Celebrity (20)
3. Hybrid model - Default rates + creator override option

**User Answer:** "Creator-set rates in rupees (Recommended)"

**Impact:**
- Creators control their own call rates (₹8-25/min audio, ₹12-35/min video)
- NO fixed tier rates by platform
- NO automatic peak hour pricing
- Simplifies user experience (predictable pricing)

**Decision Recorded:** REQ-CE-003 confirms creator-set rates, no fixed tiers

---

### Q3: Peak Hour Pricing (Surge Pricing)

**Context:** PRD shows 1.5x multiplier during peak hours (10 PM - 2 AM IST). If we implement creator-set rates, should peak hour pricing still apply?

**Question:** Should we apply peak hour pricing to creator-set rates?

**Options Provided:**
1. Apply peak multiplier to creator rates - Higher earnings during peak demand
2. Remove surge pricing (Recommended) - Creator rate fixed 24/7
3. Optional peak pricing - Creators opt-in to peak multiplier

**User Answer:** "Remove surge pricing (Recommended)"

**Impact:**
- Creator rates are fixed 24/7 (no automatic surge)
- Predictable pricing for callers (no surprise price changes)
- If creator wants higher peak rates, they can manually adjust their base rate
- Simplifies billing logic (no time zone conversions)

**Decision Recorded:** REQ-CE-009 - No peak hour pricing

---

## Question Round 2: Package & Gift Configuration

### Q4: Coin Package Selection

**Context:** The PRD shows a ₹49 Starter package (50 coins), but previously approved requirements show minimum ₹99 (110 coins).

**Question:** Should we include the ₹49 package or start at ₹99?

**Options Provided:**
1. Start at ₹99 (aligns with previous approvals) - Higher revenue per transaction
2. Keep PRD packages (Recommended) - Includes ₹49 trial package
3. Add ₹49 AND keep ₹99+ packages (7 total packages)

**User Answer:** "Keep PRD packages (Recommended)"

**Impact:**
- 6 coin packages total: ₹49, ₹99, ₹299, ₹499, ₹999, ₹1999
- Lower barrier to entry with ₹49 trial package
- All packages have 10% bonus (simplified from previous progressive bonuses)
- Database seed data updated to include ₹49 package

**Decision Recorded:** REQ-CE-001 updated to 6 packages (₹49-₹1999)

---

### Q5: Gift Catalog

**Context:** PRD defines 10 gifts (Rose to Castle). Previous breakdown added 3 mid-tier gifts (250, 300, 400 coins) for 13 total.

**Question:** Should we use PRD's 10-gift catalog or the expanded 13-gift version?

**Options Provided:**
1. Keep PRD gift catalog (Recommended) - 10 gifts (10-10,000 coins)
2. Keep 13 gifts (previous breakdown) - Added mid-tier gifts (250, 300, 400)
3. Expand further - Add more mid-tier options

**User Answer:** "Keep PRD gift catalog (Recommended)"

**Impact:**
- 10 gifts total: Rose (10), Heart (20), Coffee (50), Teddy (100), Bouquet (150), Diamond (200), Crown (500), Sports Car (1000), Private Jet (5000), Castle (10000)
- Removed mid-tier gifts: Gift Box (250), Star (300), Luxury Gift (400)
- Matches PRD Section 9.3 exactly
- Creator earnings calculated at 45% fixed (per Q1 dual model)

**Decision Recorded:** REQ-CE-004 updated to 10 gifts with 45% revenue

---

### Q6: Coin Expiry Rules

**Context:** PRD defines three expiry rules: purchased (365 days inactivity), bonus (90 days), promotional (30 days).

**Question:** Should we implement the PRD's expiry rules?

**Options Provided:**
1. Keep expiry rules (Recommended) - 365d/90d/30d per PRD
2. No expiry - Coins never expire (user-friendly but risky)
3. Modified expiry - Extend timeframes (e.g., 730d/180d/60d)

**User Answer:** "Keep expiry rules (Recommended)"

**Impact:**
- Purchased coins: Expire after 365 days of account inactivity (no spending)
- Bonus coins: Expire 90 days from credit date
- Promo coins: Expire 30 days from credit date
- Three-tier balance tracking required (separate fields: purchased_balance, bonus_balance, promo_balance)
- FIFO spending: Promo → Bonus → Purchased (use expiring coins first)

**Decision Recorded:** REQ-CE-005 - Three-tier balance tracking with PRD expiry rules

---

## Question Round 3: Free Coins & Policies

### Q7: Free Coins (Signup & Daily Bonuses)

**Context:** Need to determine if/how to provide free coins to new and daily users to reduce friction and drive engagement.

**Question:** Should we offer free coins at signup and/or daily login?

**Options Provided:**
1. 20 coins at signup only (one-time welcome bonus)
2. 5 coins/day for daily login (engagement driver)
3. Both (20 signup + 5/day daily login) - Best for onboarding & retention
4. No free coins (purchase-only)

**User Answer:** "keep option 1 and also add daily login bonus of 5 coins/day only if they login"

**Impact:**
- **Signup bonus:** 20 coins credited once at account creation (90-day expiry)
- **Daily login bonus:** 5 coins credited every 24 hours if user opens app (90-day expiry)
- Reduces initial friction (users can try features before purchasing)
- Drives DAU (daily active users) with login incentive
- Combined with ₹49 package, creates strong onboarding funnel
- Database: Add last_login_bonus_at timestamp to coin_wallets table

**Decision Recorded:** REQ-CE-011 - Daily login bonus program (20 signup + 5/day)

---

### Q8: Refund Policy

**Context:** PRD states "Refunds only within 7 days for technical issues, unused coins only". Need to clarify refund enforcement.

**Question:** How strict should the refund policy be?

**Options Provided:**
1. Technical issues only - Support verifies technical problem (e.g., coins not credited)
2. All-or-nothing - Refund only if 100% of coins unused
3. Non-refundable (virtual goods policy) - Standard for virtual currency platforms
4. Proportional - Refund value of unused coins

**User Answer:** "Non-refundable (virtual goods policy)"

**Impact:**
- Virtual goods are non-refundable (industry standard)
- Exceptions: Only technical issues verified by support (payment succeeded but coins not credited)
- 7-day window for technical issue refunds
- Prevents abuse (users buying coins, using them, requesting refund)
- Clear policy: "All coin purchases are final. Refunds only for verified technical issues preventing coin use."

**Decision Recorded:** REQ-CE-008 - Technical refunds only policy (strict enforcement)

---

### Q9: Pricing Transparency

**Context:** Should we show rupee value alongside coin prices to help users understand real value?

**Question:** How should we display coin pricing and value?

**Options Provided:**
1. Coins only - Show prices in coins (e.g., "10 coins")
2. Transparent: Show rupee value (Recommended) - Show "10 coins (₹10 value)" for clarity
3. Hide value - Don't show rupee equivalent (obscures value)

**User Answer:** "Transparent: Show rupee value (Recommended)"

**Impact:**
- **Gift display:** "Rose: 10 coins (₹10 value) → Creator earns ₹4.50"
- **Call rates:** "₹12/min (12 coins/min)"
- **Coin packages:** "₹99 = 100 coins (₹1/coin when spent)"
- Users clearly understand value (1 coin = ₹1 when spent)
- Transparency builds trust, reduces confusion
- UI shows both coins AND rupee value consistently

**Decision Recorded:** Transparency requirement added across REQ-CE-001, REQ-CE-003, REQ-CE-004

---

## Summary of Decisions

### Core Model Changes:
1. **Dual Revenue Model:** Calls use 75-85% tiered, Gifts use 45% fixed
2. **Creator-Set Rates:** No fixed tier rates, creators control pricing
3. **No Surge Pricing:** Creator rates fixed 24/7

### Package & Catalog:
4. **6 Coin Packages:** ₹49-₹1999 (including entry-level ₹49)
5. **10 Gifts:** Per PRD catalog (removed mid-tier additions)
6. **Expiry Rules:** 365d/90d/30d per PRD

### Free Coins & Policies:
7. **Login Bonuses:** 20 coins signup + 5 coins/day daily login
8. **Refund Policy:** Non-refundable (virtual goods), technical issues only
9. **Transparency:** Show rupee value alongside coins everywhere

---

## Requirements Generated

Based on these answers, 11 requirements were created/updated:

1. **REQ-CE-001:** Coin Package Pricing (6 packages: ₹49-₹1999)
2. **REQ-CE-002:** Dual Revenue Split Model (75-85% calls, 45% gifts)
3. **REQ-CE-003:** Creator-Set Call Rates (₹8-25/min, no tiers)
4. **REQ-CE-004:** Gift Pricing (10 gifts, 45% fixed revenue)
5. **REQ-CE-005:** Three-Tier Balance Tracking (purchased/bonus/promo)
6. **REQ-CE-006:** 1 Coin = ₹1 Spending Standard
7. **REQ-CE-007:** Bonus Coins Equal Revenue Share
8. **REQ-CE-008:** Technical Refunds Only Policy
9. **REQ-CE-009:** No Peak Hour Pricing
10. **REQ-CE-010:** Coin Economy Analytics & Monitoring
11. **REQ-CE-011:** Daily Login Bonus Program (NEW - added from Q7)

---

**Next Steps:**
1. Update .metadata.json to mark Section 9 as completed (11 requirements, 9 questions)
2. Update CHANGELOG.md with Section 9 updates
3. Continue to final deliverables (master-index.md, dependency-graph.md)

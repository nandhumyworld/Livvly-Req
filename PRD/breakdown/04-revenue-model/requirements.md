# Revenue Model - Requirements

**Section ID:** 04-revenue-model
**Section Title:** Revenue Model
**Last Updated:** 2026-01-19
**Status:** Completed

---

## Overview

This section defines LIVVLY's comprehensive revenue model including pricing strategy, revenue streams, subscription tiers, virtual gifts catalog, and financial projections. It corrects conflicts from the original PRD and aligns with the 75-85% creator revenue share commitment.

---

## Critical Corrections from Original PRD

**IMPORTANT:** Section 4 of the original PRD contained revenue split models showing 31.5%-45.5% creator share, which **conflicts** with LIVVLY's core positioning. The following requirements supersede and correct the original PRD:

1. **Revenue Split:** Platform 15-25%, Creator 75-85% (not 45-65% shown in PRD)
2. **Free Tier:** Removed (no "5 free calls/day" plan) - one-time trial only
3. **Revenue Streams:** Detailed 5-stream model (not simplified 3-stream PRD model)
4. **Subscription Tiers:** 2 tiers (VIP, Premium) not 4 tiers (Free, VIP, Premium, Creator+)

---

## Requirements

### REQ-RM-001: Revenue Split Model (Corrected - 75-85% Creator Share)
**Priority:** P0 (Critical)
**Type:** Business
**Category:** Revenue Economics

**Description:**
LIVVLY must implement the creator-first revenue split model with 75-85% of net revenue (after app store fees) going to creators, maintaining competitive differentiation and alignment with core values.

**Acceptance Criteria:**
1. **Revenue Split Calculation:**
   - Step 1: Google Play/Apple takes 30% app store fee from gross transaction
   - Step 2: Net revenue = Gross - 30% app store fee
   - Step 3: Platform takes 15-25% of net revenue (varies by creator tier)
   - Step 4: Creator receives 75-85% of net revenue (varies by creator tier)
2. **Creator Tier Revenue Shares (from REQ-ES-004):**
   - New creators: 75% of net (Platform gets 25%)
   - Mid-tier creators (₹50K+ monthly): 80% of net (Platform gets 20%)
   - Top performers (₹2L+ monthly): 85% of net (Platform gets 15%)
3. **Example Calculation (₹500 transaction, 80% tier creator):**
   - Gross: ₹500
   - App store fee (30%): ₹150
   - Net revenue: ₹350
   - Creator share (80%): ₹280
   - Platform share (20%): ₹70
4. **Transparency:** All calculations shown in creator dashboard with breakdown

**Dependencies:**
- REQ-ES-004 (Creator revenue share tiers)
- REQ-MA-004 (Revenue split calculation model)
- REQ-PV-007 (Transparency core value)

**Related Requirements:** REQ-RM-002, REQ-RM-003

**Notes:**
This supersedes PRD Section 4.2 which incorrectly showed 45-65% creator shares. The 75-85% model is fundamental to LIVVLY's competitive positioning and cannot be compromised.

---

### REQ-RM-002: Revenue Stream Distribution
**Priority:** P0 (Critical)
**Type:** Business
**Category:** Revenue Strategy

**Description:**
LIVVLY must track and optimize five distinct revenue streams with target contribution percentages based on market analysis and competitor benchmarks.

**Acceptance Criteria:**
1. **Five Revenue Streams with Target Contributions:**
   - **Coins/Credits Purchases:** 60-70% of total revenue (primary monetization)
   - **Paid Voice Calls:** 15-20% of revenue (per-minute billing)
   - **Virtual Gifts:** 10-15% of revenue (sent during calls/rooms)
   - **VIP Subscriptions:** 5-10% of revenue (monthly recurring)
   - **Ads:** 2-5% of revenue (Phase 2+, not in MVP)
2. **Separate Tracking:** Analytics dashboard tracks revenue contribution by stream
3. **Optimization:** Quarterly review of revenue mix, adjust pricing/features to meet targets
4. **MVP Focus:** First 4 streams in MVP (ads deferred to Phase 2+)

**Dependencies:**
- REQ-MA-002 (Competitor revenue model benchmarking)
- REQ-MA-003 (Revenue stream implementation priority)
- REQ-RM-004, 005, 006, 007 (Specific pricing for each stream)

**Related Requirements:** REQ-RM-001, REQ-RM-003

**Notes:**
This corrects PRD Section 4.1 which showed simplified "Coins 70%, Subscriptions 25%, Ads 5%". The detailed 5-stream model enables more granular revenue optimization.

---

### REQ-RM-003: Monetization Strategy (No Free Tier)
**Priority:** P0 (Critical)
**Type:** Business
**Category:** Monetization

**Description:**
LIVVLY must implement a one-time free trial monetization strategy with NO ongoing free tier, maximizing conversion and revenue per user while maintaining accessible entry point.

**Acceptance Criteria:**
1. **One-Time Free Trial (from REQ-PV-011):**
   - New users receive 10-20 free coins OR 5-minute free call credit upon signup
   - One-time only (not recurring daily/weekly/monthly)
   - Designed to let users experience value before first purchase
2. **No Free Tier Subscription:**
   - No "Free Plan" with ongoing benefits (5 calls/day, limited rooms, etc.)
   - After free trial coins depleted, users must purchase to continue engaging
3. **Incentivized First Purchase:**
   - Buy ₹99, get 110 coins (10% bonus)
   - 24-hour time limit on first purchase bonus
   - Target: 10% onboarding-to-purchase conversion (REQ-PV-013)
4. **All Features Require Coins or Subscription:**
   - Voice calls: Cost coins per minute
   - Gifts: Cost coins
   - Live rooms: May require coins or VIP subscription for participation
   - No unlimited free features

**Dependencies:**
- REQ-PV-011 (Early monetization strategy)
- REQ-PV-013 (Funnel conversion metrics)
- REQ-RM-004 (Coin pricing)

**Related Requirements:** REQ-RM-001, REQ-RM-002

**Notes:**
This removes the "Free" tier from PRD Section 4.4 which showed "5 free calls/day, limited rooms". That model conflicts with early monetization strategy and would cannibalize paid conversion.

---

### REQ-RM-004: Coin Pricing Structure
**Priority:** P0 (Critical)
**Type:** Business
**Category:** Pricing

**Description:**
LIVVLY must implement a tiered coin pricing structure with increasing bonus percentages for larger purchases, using ₹1 base coin value for simple mental math.

**Acceptance Criteria:**
1. **Base Coin Value:** ₹1 per coin (before bonuses)
2. **Four Pricing Tiers with Bonuses:**
   - **Starter Pack:** ₹99 = 110 coins (10% bonus, ₹0.90 per coin)
   - **Popular Pack:** ₹299 = 350 coins (17% bonus, ₹0.85 per coin)
   - **Value Pack:** ₹499 = 600 coins (20% bonus, ₹0.83 per coin)
   - **Mega Pack:** ₹999 = 1,300 coins (30% bonus, ₹0.77 per coin)
3. **Psychological Pricing:**
   - Entry point ₹99 (accessible for tier-2/3 cities)
   - Bonuses incentivize larger purchases (higher average transaction value)
   - Clear value communication: "Buy more, save more"
4. **VIP/Premium Discounts Applied:**
   - VIP members: 15% extra coins on all purchases
   - Premium members: 25% extra coins on all purchases
   - Example: VIP buys ₹299 pack → Gets 350 + 52 (15%) = 402 coins total
5. **Display Format:**
   - Primary: "₹299 = 350 coins" (shows bonus already included)
   - Secondary: "17% bonus! Regular ₹299 = 300 coins, you get 350!"

**Dependencies:**
- REQ-RM-007 (VIP/Premium subscription benefits)
- REQ-PV-007 (Transparency core value - clear coin value communication)

**Related Requirements:** REQ-RM-002, REQ-RM-005

**Notes:**
₹1 base value (not ₹0.99) chosen for tier-2/3 target market where simple math reduces cognitive friction. Bonuses (10-30%) competitive with mobile gaming industry standards.

---

### REQ-RM-005: Per-Minute Call Pricing (Creator-Set Rates)
**Priority:** P0 (Critical)
**Type:** Business
**Category:** Pricing

**Description:**
LIVVLY must implement a creator-set pricing model for voice calls, allowing creators to set their own per-minute rates within platform-defined limits, empowering creators while maintaining market-appropriate pricing.

**Acceptance Criteria:**
1. **Creator-Set Rate Model:**
   - Creators choose their own per-minute rate (in coins)
   - Minimum rate: ₹8/minute (8 coins/minute)
   - Maximum rate: ₹25/minute (25 coins/minute)
   - Creators can adjust rates anytime (changes apply to new calls, not active calls)
2. **Platform Rate Suggestions:**
   - New creators: Suggested ₹10-12/minute (competitive baseline)
   - Mid-tier creators (₹50K+ monthly): Suggested ₹15-18/minute
   - Top performers (₹2L+ monthly): Suggested ₹20-25/minute
   - Suggestions based on creator rating, engagement metrics, earnings tier
3. **Rate Display:**
   - Prominently shown on creator profile: "₹12/min" or "12 coins/min"
   - User sees total call cost before initiating: "Estimated 5 min call = 60 coins (₹60)"
   - Real-time meter during call: "Current call: 3 mins, 36 coins spent"
4. **Market Dynamics:**
   - Creators compete on value (quality/personality vs price)
   - Natural tiering emerges (budget creators ₹8-10, premium creators ₹20-25)
   - Platform analytics show creators their rate vs avg creator earnings correlation
5. **Revenue Split Applied:**
   - Example: ₹12/min call, 5 minutes, 80% tier creator
   - User spends: 60 coins (₹60 if purchased at ₹1 base)
   - Gross transaction: ₹60
   - After 30% app store: ₹42 net
   - Creator (80%): ₹33.60
   - Platform (20%): ₹8.40

**Dependencies:**
- REQ-RM-001 (Revenue split model)
- REQ-RM-004 (Coin pricing)
- REQ-ES-004 (Creator revenue tiers)
- REQ-PV-004 (Creator-First core value)

**Related Requirements:** REQ-RM-002, REQ-RM-006

**Notes:**
Creator-set pricing empowers creators (aligns with Creator-First value) while platform limits prevent exploitation (₹8 min protects creator earnings, ₹25 max prevents user gouging). Market-driven model allows natural discovery of optimal pricing.

---

### REQ-RM-006: Virtual Gifts Catalog & Pricing
**Priority:** P1 (High)
**Type:** Business
**Category:** Pricing

**Description:**
LIVVLY must implement a comprehensive virtual gifts catalog with 20+ gift types across three price tiers, supporting 10-15% revenue contribution target through emotional engagement and social signaling.

**Acceptance Criteria:**
1. **Gift Catalog (20+ gifts in MVP):**

   **Budget Tier (10-25 coins each):**
   - Rose (15 coins, static)
   - Heart (10 coins, animated)
   - Coffee (20 coins, static)
   - Smile Emoji (10 coins, static)
   - Wave (15 coins, animated)
   - Thumbs Up (10 coins, static)

   **Mid Tier (50-100 coins each):**
   - Cake (50 coins, animated)
   - Champagne (75 coins, animated)
   - Teddy Bear (60 coins, static)
   - Chocolate Box (50 coins, static)
   - Bouquet (80 coins, animated)
   - Kiss (100 coins, animated)

   **Premium Tier (200-500 coins each):**
   - Diamond Ring (200 coins, animated)
   - Crown (250 coins, animated)
   - Luxury Car (300 coins, animated)
   - Gold Jewelry (200 coins, animated)
   - Mansion (400 coins, animated)
   - Private Jet (500 coins, animated)

2. **Gift Categories for Discovery:**
   - **Romantic:** Rose, Heart, Kiss, Diamond Ring (dating use case)
   - **Social:** Coffee, Smile, Wave, Champagne (friendship use case)
   - **Luxury/Whale:** Crown, Luxury Car, Mansion, Private Jet (high spenders)
   - **Cultural:** Gifts adapted for Tamil/Telugu cultural relevance where appropriate

3. **Gift Mechanics:**
   - Gifts sent during 1-on-1 calls or in live audio rooms
   - Animated gifts cost 20-30% more than equivalent static gifts
   - Gifts trigger visual/audio effects visible to all room participants (social signaling)
   - Recipient creator receives 75-85% of gift value (per revenue split model)

4. **Purchase Flow:**
   - User browses gift menu during call/room
   - Selects gift, confirms purchase (deducts coins from balance)
   - Gift animation displays for recipient and (if in room) all participants
   - Notification: "You sent [Gift Name] to [Creator]!"

5. **Revenue Contribution Target:** 10-15% of total revenue

**Dependencies:**
- REQ-RM-001 (Revenue split applies to gift value)
- REQ-RM-004 (Gifts purchased with coins)
- REQ-RM-002 (10-15% revenue contribution target)
- REQ-FS-xxx (Gift UI/UX, animation implementation)

**Related Requirements:** REQ-RM-002, REQ-RM-005

**Notes:**
20+ gifts is ambitious for MVP (+1-2 weeks design/development). Rationale: Rich gift catalog drives revenue through (1) emotional engagement (express feelings via gifts), (2) social signaling (impress others in rooms), (3) whale monetization (luxury gifts capture high spenders). Competitive apps (FRND, Tango) have 30-50+ gifts, so 20+ positions LIVVLY competitively.

---

### REQ-RM-007: VIP Subscription Tiers & Benefits
**Priority:** P0 (Critical)
**Type:** Business
**Category:** Subscriptions

**Description:**
LIVVLY must implement two VIP subscription tiers (VIP ₹199/month, Premium ₹499/month) providing clear value through coin discounts, free coins, and priority features, targeting 5-10% revenue contribution.

**Acceptance Criteria:**
1. **VIP Tier (₹199/month):**
   - **Benefits:**
     - 15% bonus coins on all purchases (on top of tier bonuses)
     - VIP badge on profile (gold badge, visible to all users)
     - Priority customer support (response within 4 hours vs 24 hours standard)
   - **Value Proposition:** "Break even at ₹1,327 coin purchases/month" (₹1,327 × 15% = ₹199 savings)
   - **Target Audience:** Regular users spending ₹1,500-3,000/month on coins

2. **Premium Tier (₹499/month):**
   - **Benefits:**
     - 25% bonus coins on all purchases (on top of tier bonuses)
     - 100 free coins per month (₹100 value)
     - Premium badge on profile (diamond badge, visible to all users)
     - Priority matching (appear higher in match algorithm)
     - Priority customer support (response within 2 hours)
     - Early access to new features (beta features before public release)
   - **Value Proposition:** "Break even at ₹1,596 coin purchases/month" (₹1,596 × 25% = ₹399 + ₹100 free = ₹499)
   - **Target Audience:** Power users spending ₹2,500-5,000/month on coins

3. **Subscription Mechanics:**
   - Monthly auto-renewal (user can cancel anytime)
   - Google Play Billing / Apple In-App Purchase subscriptions
   - Benefits activate immediately upon purchase
   - Benefits expire if subscription lapses (grace period: 48 hours)
   - Free coins credited on subscription date each month

4. **Coin Purchase with Subscription Bonus Calculation:**
   - Example: Premium member buys ₹299 Popular Pack
   - Base: 350 coins (17% tier bonus already included)
   - Premium bonus: 350 × 25% = 87.5 coins (rounded to 88)
   - Total: 350 + 88 = 438 coins received
   - Effective price: ₹299 / 438 = ₹0.68 per coin (vs ₹0.90 non-member Starter Pack)

5. **Revenue Contribution Target:** 5-10% of total revenue

6. **No Free Tier:** Corrects PRD Section 4.4 which showed "Free ₹0" tier - removed per REQ-RM-003

**Dependencies:**
- REQ-RM-003 (No free tier)
- REQ-RM-004 (Coin pricing structure - bonuses stack)
- REQ-RM-002 (5-10% revenue contribution target)
- REQ-PV-012 (Priority matching feature in comprehensive MVP)

**Related Requirements:** REQ-RM-002, REQ-RM-004

**Notes:**
Two tiers (not 3-4) keeps options simple while capturing both moderate and power users. VIP ₹199 is affordable entry point (competitive with Netflix/Amazon Prime in India). Premium ₹499 targets whales (₹6K/year spend signals high engagement). Break-even calculations help users self-select appropriate tier.

---

### REQ-RM-008: Financial Projections Model
**Priority:** P0 (Critical)
**Type:** Business
**Category:** Financial Planning

**Description:**
LIVVLY must develop comprehensive financial projections including revenue forecasts, Customer Lifetime Value (LTV), Revenue Per User (RPU), break-even analysis, and Creator Acquisition Cost (CAC) limits to validate business model viability and guide investment decisions.

**Acceptance Criteria:**

1. **Revenue Per User (RPU) Calculation:**

   **Assumptions (based on competitor benchmarks and funnel metrics):**
   - 30% of users purchase coins at least once (from 10% onboarding + 20% later conversion)
   - Avg paying user spends ₹400/month on coins (mix of ₹99, ₹299, ₹499 packs)
   - 8% of users subscribe to VIP/Premium (₹199 or ₹499/month)
   - Avg gift spending: ₹50/month per active user

   **RPU Calculation:**
   - Coin revenue per user: ₹400 × 30% = ₹120/user/month
   - Subscription revenue: (₹199 × 6% + ₹499 × 2%) = ₹11.94 + ₹9.98 = ₹21.92/user/month
   - Gift revenue (incremental to coins): ₹50 × 30% = ₹15/user/month
   - **Total RPU: ₹156.92/user/month (~₹157/user/month)**

2. **Customer Lifetime Value (LTV) Calculation:**

   **Assumptions:**
   - Avg user lifespan: 8 months (based on 40% Week 1 retention → declining retention curve)
   - Monthly churn: ~12-15% per month after first 3 months

   **LTV Calculation:**
   - LTV = RPU × Avg Lifespan = ₹157 × 8 months = **₹1,256 per user**
   - Conservative LTV (assuming 20% higher churn): ₹157 × 6 months = ₹942

3. **Monthly Revenue Projections:**

   **6-Month Target (50K MAU per REQ-ES-008):**
   - Gross Monthly Revenue (GMV): 50,000 users × ₹157 RPU = ₹78.5 Lakh/month
   - Net Revenue (after 30% app store fees): ₹78.5L × 70% = ₹54.95 Lakh/month
   - **Comparison to Goal:** ₹54.95L vs ₹20L target (REQ-ES-008) → **275% of target! (Conservative target)**

   **12-Month Target (200K MAU per REQ-ES-009):**
   - Gross Monthly Revenue (GMV): 200,000 users × ₹157 RPU = ₹3.14 Crore/month
   - Net Revenue (after app store fees): ₹3.14Cr × 70% = ₹2.20 Crore/month
   - **Comparison to Goal:** ₹2.20Cr vs ₹1Cr target (REQ-ES-009) → **220% of target!**

   **24-Month Target (1M MAU per REQ-ES-010):**
   - Gross Monthly Revenue (GMV): 1,000,000 users × ₹157 RPU = ₹15.7 Crore/month
   - Net Revenue (after app store fees): ₹15.7Cr × 70% = ₹10.99 Crore/month
   - **Comparison to Goal:** ₹10.99Cr vs ₹5Cr target (REQ-ES-010) → **220% of target!**

4. **Creator Payout Projections (75-85% of Net Revenue):**

   **6-Month:**
   - Net revenue: ₹54.95L/month
   - Creator pool (avg 80%): ₹54.95L × 80% = ₹43.96 Lakh/month to creators
   - Platform revenue (20%): ₹54.95L × 20% = ₹10.99 Lakh/month

   **12-Month:**
   - Creator pool (80%): ₹2.20Cr × 80% = ₹1.76 Crore/month to creators
   - Platform revenue: ₹2.20Cr × 20% = ₹44 Lakh/month

   **24-Month:**
   - Creator pool (80%): ₹10.99Cr × 80% = ₹8.79 Crore/month to creators
   - Platform revenue: ₹10.99Cr × 20% = ₹2.20 Crore/month

5. **Break-Even Analysis:**

   **Estimated Monthly Operating Costs:**
   - Engineering team (10 engineers × ₹1.5L avg): ₹15L/month
   - Cloud infrastructure (AWS/GCP): ₹3L/month (scales with users)
   - Moderation team (20 moderators × ₹40K): ₹8L/month
   - Customer support (10 agents × ₹35K): ₹3.5L/month
   - Marketing/Growth: ₹10L/month (initial phase, scales down)
   - Office/Admin/Legal: ₹3L/month
   - **Total Operating Costs: ₹42.5 Lakh/month**

   **Break-Even Users:**
   - Platform revenue needed: ₹42.5L/month
   - Platform revenue (20% of net): ₹42.5L = 20% of X
   - Net revenue needed: X = ₹42.5L / 0.20 = ₹212.5L/month
   - Users needed: ₹212.5L / ₹157 RPU = **13,535 MAU to break even**
   - **Break-even timing: Month 4-5** (between 50K target and initial traction)

6. **Customer Acquisition Cost (CAC) Limits:**

   **LTV-Based CAC Limit:**
   - LTV = ₹1,256
   - Target LTV:CAC ratio = 3:1 (healthy SaaS metric)
   - **Maximum CAC: ₹1,256 / 3 = ₹419 per user**

   **Acquisition Channel CAC Estimates:**
   - ASO (organic): ₹0-50 per user (mostly free, some ASO tools)
   - Referrals: ₹100-150 per user (referral bonus costs)
   - Paid Ads: ₹300-500 per user (Facebook/Google ads in tier-2/3)
   - **Blended CAC target: ₹200-250 per user** (mix of channels)

   **Payback Period:**
   - CAC ₹250 / RPU ₹157/month = 1.6 months to recover CAC (healthy)

7. **Sensitivity Analysis:**

   **Scenario 1: Lower RPU (₹120 instead of ₹157):**
   - 6-month revenue: 50K × ₹120 = ₹60L GMV vs ₹78.5L base (still exceeds ₹20L target)
   - Break-even: ₹212.5L / (₹120 × 0.7) / 0.2 = 25,595 users (still achievable)

   **Scenario 2: Higher Churn (10-month LTV instead of 8):**
   - Higher retention would increase LTV to ₹1,570 (₹157 × 10)
   - Allows higher CAC spend (₹523 max vs ₹419 base)

**Dependencies:**
- REQ-ES-008, 009, 010 (Business goals for 6/12/24 months)
- REQ-PV-013 (Funnel conversion metrics)
- REQ-RM-004, 005, 006, 007 (Pricing structure for revenue assumptions)
- REQ-COE-xxx (Cost Estimation for operating cost validation)

**Related Requirements:** All RM requirements, REQ-ES-008/009/010

**Notes:**
**Key Insight:** Revenue projections (₹54.95L, ₹2.20Cr, ₹10.99Cr) are **220-275% of original business goals** (₹20L, ₹1Cr, ₹5Cr). This suggests either:
1. Original goals were conservative (good for investors - under-promise, over-deliver)
2. RPU assumptions (₹157/month) are optimistic and should be validated in MVP
3. Opportunity to capture more revenue than initially projected

**Break-even at 13.5K users (Month 4-5) is very healthy**, giving significant runway before needing profitability. Platform revenue at scale (₹2.2Cr/month at 24 months) supports aggressive reinvestment in product, marketing, and team growth.

---

### REQ-RM-009: Pricing Strategy - Hybrid Affordable/Premium Positioning
**Priority:** P1 (High)
**Type:** Business
**Category:** Strategy

**Description:**
LIVVLY must position pricing as hybrid affordable entry with premium upsell options, using uniform national pricing (no geographic variation) to align with tier-2/3 target market while capturing whale users.

**Acceptance Criteria:**
1. **Affordable Entry Positioning:**
   - Lowest coin pack: ₹99 (accessible for tier-2/3 cities, ~1-2 hours minimum wage)
   - Lowest call rate: ₹8/minute (creator minimum, competitive with phone call rates)
   - Lowest VIP subscription: ₹199/month (competitive with OTT platforms)
   - **Messaging:** "Start for just ₹99 - Connect in your language"

2. **Premium Upsell Options:**
   - Highest coin pack: ₹999 (30% bonus, targets whales)
   - Highest call rate: ₹25/minute (premium creator positioning)
   - Highest subscription: ₹499/month Premium (power user tier)
   - Premium gifts: ₹500 Private Jet (whale social signaling)
   - **Messaging:** "Upgrade for more - VIP experience, exclusive features"

3. **Geographic Pricing:**
   - **Uniform national pricing** (no tier-1 vs tier-2/3 variation)
   - Rationale:
     - Complexity: Different pricing by location requires geolocation, enforcement, creates confusion
     - Fairness: Users can travel between cities, consistent pricing avoids perception of discrimination
     - Simplicity: Single price list easier to communicate, market, and manage
     - Platform cost: No cost difference to platform based on user location
   - Exception: Currency conversion for diaspora users (₹ to $, £, etc. at market rates)

4. **Competitive Positioning:**
   - vs FRND/Tango: Similar ₹99-999 coin range, but higher creator share (75-85% vs 50-60%)
   - vs Tinder/Bumble: Lower price point (₹99 entry vs ₹500+ Tinder Plus), voice-first vs text-first
   - vs Clubhouse: Monetization (coins/gifts) vs purely free, regional language vs English-first

5. **Value Ladder Strategy:**
   - Entry: Free trial (10-20 coins) → Experience platform with zero risk
   - Starter: ₹99 coin pack → Accessible first purchase, builds habit
   - Regular: ₹299-499 coin packs → Repeat purchasing, better value with bonuses
   - Subscriber: ₹199 VIP or ₹499 Premium → Monthly commitment, recurring revenue
   - Whale: ₹999 mega packs + ₹500 luxury gifts → High spenders, 10-20% of revenue
   - Target: Move users up value ladder over 3-6 month period

**Dependencies:**
- REQ-RM-004, 005, 006, 007 (Pricing tiers across all revenue streams)
- REQ-MA-013 (Target user personas - tier-2/3 cities)
- REQ-PV-002 (Hybrid social + dating positioning)

**Related Requirements:** All RM requirements

**Notes:**
"Accessible for everyone, premium when you want it" positioning allows broad market entry (tier-2/3 cities, lower income) while capturing high-value users (tier-1 cities, higher income, whales). Uniform national pricing simplifies operations and avoids fairness concerns.

---

### REQ-RM-010: Revenue Optimization & A/B Testing Framework
**Priority:** P2 (Medium)
**Type:** Business
**Category:** Optimization

**Description:**
LIVVLY must implement systematic revenue optimization through A/B testing of pricing, packaging, and promotional strategies to maximize revenue per user while maintaining user satisfaction.

**Acceptance Criteria:**
1. **A/B Testing Roadmap (Post-MVP Launch, Month 2+):**

   **Test 1: Coin Pack Pricing**
   - Hypothesis: Higher ₹999 pack bonus (35% vs 30%) increases large pack purchases
   - Segments: 50% users see 30% bonus, 50% see 35% bonus
   - Metrics: Avg transaction value, ₹999 pack purchase rate, total coin revenue

   **Test 2: VIP Subscription Pricing**
   - Hypothesis: ₹249 VIP price with same benefits maintains conversion at higher revenue
   - Segments: New users see ₹199 or ₹249 VIP pricing
   - Metrics: VIP subscription rate, total subscription revenue, user satisfaction (NPS)

   **Test 3: First Purchase Bonus**
   - Hypothesis: 15% first purchase bonus (vs 10%) increases conversion at profitable margin
   - Segments: 50% see "Buy ₹99 get 110 coins", 50% see "Buy ₹99 get 115 coins"
   - Metrics: Onboarding-to-purchase conversion, repeat purchase rate within 30 days

   **Test 4: Gift Pricing Tiers**
   - Hypothesis: Mid-tier gifts (₹50-100) drive more revenue than extreme budget/premium
   - Segments: Vary mid-tier gift prices (₹50 vs ₹75 for same gift)
   - Metrics: Gift purchase frequency, total gift revenue, gifts per user

   **Test 5: Subscription Packaging**
   - Hypothesis: Premium tier with 200 free coins (vs 100) increases Premium adoption
   - Segments: 50% see 100 coins, 50% see 200 coins (adjust price or benefits)
   - Metrics: Premium/VIP ratio, total subscription revenue, upgrade rate VIP→Premium

2. **Testing Discipline:**
   - Minimum test duration: 2 weeks (capture weekly purchasing patterns)
   - Minimum sample size: 5,000 users per variant (statistical significance)
   - Success criteria defined before test launch (no p-hacking)
   - Document learnings in shared knowledge base

3. **Promotional Strategies to Test:**
   - Limited-time coin sales (e.g., "₹299 pack, get 400 coins for 48 hours")
   - Bundle offers (e.g., "Buy ₹499 coins + ₹199 VIP = ₹599 total, save ₹99")
   - Seasonal promotions (Diwali, New Year bonus coins)
   - Re-engagement offers (lapsed users: "Come back, get 50 free coins")

4. **Optimization Metrics Dashboard:**
   - Revenue per user (RPU) by cohort, channel, region
   - Conversion funnel by pricing tier (what % buy ₹99 vs ₹299 vs ₹999?)
   - Subscription mix (what % VIP vs Premium?)
   - Gift revenue contribution (which gifts drive most revenue?)
   - LTV by acquisition cohort (Month 1 users vs Month 6 users)

**Dependencies:**
- REQ-RM-008 (Financial model provides baseline metrics)
- REQ-SM-xxx (Success Metrics analytics infrastructure)
- REQ-FS-xxx (A/B testing feature flag system)

**Related Requirements:** All RM pricing requirements

**Notes:**
Priority P2 (Medium) because optimization should start post-MVP launch (Month 2+), not during MVP development. However, planning A/B test framework now ensures analytics instrumentation captures necessary data from day one.

---

## Summary Statistics

- **Total Requirements:** 10
- **P0 (Critical):** 7
- **P1 (High):** 2
- **P2 (Medium):** 1
- **Business Requirements:** 10

---

## Cross-References

### Depends On (from other sections):
- Executive Summary (business goals ₹20L/₹1Cr/₹5Cr, creator revenue tiers 75-85%)
- Market Analysis (revenue split model, revenue stream distribution, competitor benchmarks)
- Product Vision (early monetization strategy, funnel metrics, no free tier)
- Coin Economy (detailed coin usage mechanics - will reference pricing from this section)
- Feature Specifications (gift UI/UX, subscription features, payment integration)
- Success Metrics (analytics for revenue tracking, A/B testing infrastructure)
- Cost Estimation (operating costs for break-even analysis)

### Blocks (other sections depend on this):
- Coin Economy (uses coin pricing and gift catalog from this section)
- Creator Payout (uses revenue split model 75-85%)
- Cost Estimation (revenue projections inform budgeting)
- Implementation Roadmap (20+ gift catalog adds 1-2 weeks to MVP timeline)
- Success Metrics (revenue metrics defined here)

---

## Notes

1. **Critical Corrections:** This section corrects multiple conflicts in original PRD:
   - Revenue split: 75-85% creator (not 45-65% shown in PRD Section 4.2)
   - Free tier: Removed entirely (not "5 free calls/day" from PRD Section 4.4)
   - Revenue streams: 5-stream detailed model (not simplified 3-stream from PRD 4.1)

2. **Financial Projections Insight:** Revenue projections (₹54.95L, ₹2.20Cr, ₹10.99Cr) are 220-275% of business goals (₹20L, ₹1Cr, ₹5Cr), suggesting either conservative goals or optimistic RPU assumptions. Break-even at 13.5K users (Month 4-5) provides healthy runway.

3. **Virtual Gifts Scope:** 20+ gifts adds 1-2 weeks to MVP timeline but justified by:
   - 10-15% revenue contribution target
   - Emotional engagement driver (express feelings, social signaling)
   - Competitive necessity (FRND/Tango have 30-50+ gifts)

4. **Creator-Set Call Pricing:** Empowers creators (aligns with Creator-First value) while platform limits (₹8-25/min) prevent exploitation. Market-driven model allows price discovery.

5. **Hybrid Pricing Strategy:** Affordable entry (₹99, ₹8/min, ₹199 VIP) targets tier-2/3 mass market. Premium options (₹999 packs, ₹25/min calls, ₹499 Premium, ₹500 gifts) capture whales. Uniform national pricing simplifies operations.

6. **LTV:CAC Ratio:** ₹1,256 LTV with ₹250 blended CAC target = 5:1 ratio (excellent, well above 3:1 healthy threshold). 1.6 month payback period enables aggressive growth investment.

---

## Financial Model Summary Tables

### Revenue Per User (RPU) Breakdown
| Component | Monthly Revenue | % of Total |
|-----------|----------------|------------|
| Coin purchases (30% paying × ₹400 avg) | ₹120 | 76.4% |
| Subscriptions (8% subscribe × avg ₹274) | ₹21.92 | 14.0% |
| Gifts (incremental, 30% × ₹50) | ₹15 | 9.6% |
| **Total RPU** | **₹156.92** | **100%** |

### Revenue Projections vs Goals
| Milestone | Users (MAU) | Revenue Projection | Business Goal | vs Goal |
|-----------|-------------|-------------------|---------------|---------|
| 6 months | 50,000 | ₹54.95 Lakh/mo | ₹20 Lakh | 275% |
| 12 months | 200,000 | ₹2.20 Crore/mo | ₹1 Crore | 220% |
| 24 months | 1,000,000 | ₹10.99 Crore/mo | ₹5 Crore | 220% |

### Platform Economics (80% Creator Tier Example)
| Metric | 6 Month | 12 Month | 24 Month |
|--------|---------|----------|----------|
| Net Revenue (after app store) | ₹54.95L | ₹2.20Cr | ₹10.99Cr |
| Creator Payout (80%) | ₹43.96L | ₹1.76Cr | ₹8.79Cr |
| Platform Revenue (20%) | ₹10.99L | ₹44L | ₹2.20Cr |
| Operating Costs (est.) | ₹42.5L | ₹60L | ₹1Cr |
| **Net Profit/Loss** | **-₹31.51L** | **-₹16L** | **+₹1.20Cr** |

Note: Profitability achieved by Month 18-20 based on this model.

# Revenue Streams

**Document ID:** 04-01
**Section:** 4.1 Revenue Architecture & Revenue Streams
**Status:** Finalized
**Last Updated:** 2026-01-20

---

## Overview

LIVVLY generates revenue from three primary streams, collectively target 70% Coins, 25% Subscriptions, 5% Ads (as % of total platform revenue). Each stream serves different user segments and monetization behaviors.

---

## Revenue Stream 1: Coins (Virtual Currency)

**Contribution to Revenue:** 70% of total platform revenue
**Primary User Type:** All users (casual to power spenders)
**Use Cases:** Gifts during calls/rooms, premium features, content unlocking

### What are Coins?

Coins are LIVVLY's virtual currency. Users buy coins with real money (INR), then spend coins to:
- **Send Gifts** during calls or in rooms (direct support to creators)
- **Make Premium Calls** (if applicable)
- **Access Premium Rooms** or features
- **Unlock Limited Content** (creator profiles, special events)

### Coin Economy

**Purchasing:**
- Users buy coins via Google Play In-App Purchase, direct card payment, or UPI
- Google Play takes 30% cut immediately
- LIVVLY receives 70% of purchase amount (net)

**Spending:**
- 1 coin = ₹1 (or fractional equivalent)
- Users can spend coins only; cannot "cash out" unused coins
- Coins expire after 12 months of inactivity (retention driver)

**Creator Share from Coins:**
- When user spends coins → Creator receives percentage of coin value
- Using High Creator Share model: Creator gets 40.95% (net) of user coin spend
- Example: User sends ₹100 gift (100 coins)
  - Google Play: ₹30
  - LIVVLY Platform: ₹24.50
  - Creator (Gross): ₹45.50
  - TDS (10%): ₹4.55
  - Creator (Net): ₹40.95

### Coin Purchase Flow

```
User decides to send gift
    ↓
Selects gift amount (₹50, ₹100, ₹500, etc.)
    ↓
System shows: "Need 150 coins, you have 50"
    ↓
User taps: "Buy Coins"
    ↓
Redirected to coin purchase screen
    ↓
User selects package (₹99, ₹299, ₹499, ₹999)
    ↓
Google Play payment flow
    ↓
Coins added to account (instant)
    ↓
Gift sent to creator
    ↓
Creator receives payout (T+1 next business day)
```

### Coin Packages (Psychological Pricing)

| Price (INR) | Coins | Value/₹ | Psychology | Use Case |
|-----------|-------|---------|-----------|----------|
| **₹99** | 100 | ₹0.99 | Entry-level price | First-time buyer, trial |
| **₹299** | 400 | ₹0.747 | Best seller (mid-tier) | Regular user |
| **₹499** | 700 | ₹0.714 | Good value | Frequent gifter |
| **₹999** | 1600 | ₹0.624 | Best value (anchor) | Power user, bulk buy |
| **₹1999** | 3500 | ₹0.571 | Premium bulk | Heavy user, creator support |

**Psychological Strategies:**
1. **₹99 as entry:** Low commitment, high conversion for first-time buyers
2. **₹299 as featured:** Positioned as "Popular" or "Best Seller" (social proof)
3. **₹499 as transition:** Step between casual (₹299) and heavy (₹999)
4. **₹999 as anchor:** High price anchors perception; ₹299-₹499 seem reasonable
5. **₹1999 as ultra-premium:** For whales; shows they can spend more

**Customization by User Segment:**
- **New users:** Larger "first purchase bonus" (e.g., 50% extra coins on first purchase)
- **Lapsed users:** Retargeting offers (e.g., "20% bonus if you recharge today")
- **Power users:** Loyalty discounts or VIP coin multipliers
- **Subscription users:** Bonus coins as subscription perk

---

## Revenue Stream 2: Subscriptions

**Contribution to Revenue:** 25% of total platform revenue
**Primary User Type:** Regular users (seeking unlimited features or analytics)
**Recurring Revenue:** Monthly/annual billing (predictable, high retention)

### Subscription Tiers (Revised to 3 Tiers)

Based on clarification: Consolidate to 3 tiers (Free, VIP, Premium). Remove Creator+ from subscription; make analytics a standalone feature or free for creator-activated users.

| Plan | Monthly Price | Features | Target User |
|------|---------------|----------|------------|
| **Free** | ₹0 | 5 free calls/day, browse rooms, limited features | Casual user, trialist |
| **VIP** | ₹199/month | Unlimited calls, 20% coin discount, VIP badge | Regular user |
| **Premium** | ₹499/month | All VIP + priority matching, 30% coin discount, analytics | Power user, light creator |

**Note:** Creator+ (₹999) functionality (analytics, promotion tools, 40% coin discount) moved to separate "Creator Dashboard" subscription or as free upgrade when user becomes verified creator.

### Subscription Revenue Model

**Per User:**
- VIP subscriber: ₹199/month × 12 = ₹2,388/year
- Premium subscriber: ₹499/month × 12 = ₹5,988/year

**ARPU Impact:**
- Free user: ₹0 from subscription (but can buy coins)
- VIP subscriber: ₹199/month subscription PLUS coin purchases
- Premium subscriber: ₹499/month subscription PLUS coin purchases (reduced coin spend due to discount)

### Subscription Acquisition

**Trigger Points:**
1. **Call Limit Hit:** User reaches 5 free calls/day → Upgrade prompt
2. **Feature Lock:** User tries premium feature (priority matching) → Upgrade required
3. **Comparison Screen:** Show VIP/Premium benefits → Soft upsell
4. **Push Notification:** "Unlimited calling for just ₹6/day" (periodically)

**Conversion Funnel:**
- 100% of users start on Free tier
- Target: 10% convert to VIP (₹199/month)
- Target: 3% convert to Premium (₹499/month)
- Churn target: <5% monthly churn on subscribers

### Subscription Economics

**LIVVLY keeps 100% of subscription revenue** (no platform cuts like Google Play don't apply to direct subscriptions in India). So from ₹199 VIP subscription:
- LIVVLY: ₹199 (100%)
- Gross margin: ₹199

This is why subscriptions are high-margin revenue compared to coins (which have 30% Google Play cut).

---

## Revenue Stream 3: Ads (Rewarded & Display)

**Contribution to Revenue:** 5% of total platform revenue
**Primary User Type:** Free users, VIP users (who skip ads)
**Ad Types:** Rewarded videos, banner ads, native ads

### Ad Inventory

#### Rewarded Video Ads
- **Placement:** "Watch ad to earn 10 free coins" or "Watch to unlock feature"
- **Frequency:** 1-2x per session for free users
- **eCPM (Earnings Per 1000 Impressions):** ₹10-30 in India
- **Revenue per user per month:** ₹20-50 (conservative estimate)

#### Banner & Native Ads
- **Placement:** Bottom of home feed, between matches, between rooms
- **Frequency:** 3-5 banners visible per session
- **eCPM:** ₹5-15 in India
- **Non-intrusive:** Never block core functionality

#### Branded Partnerships
- **Music streaming services:** Ads for music platforms
- **Dating apps:** Cross-promotion (non-competing)
- **FMCG brands:** Targeted at regional audiences
- **Potential:** Sponsored "rooms" or "events"

### Ad Revenue Model

**Ad Network Partners:**
- Primary: Google AdMob (highest fill rates, best for India)
- Secondary: Facebook Audience Network (backup, CPM ₹5-15)
- Tertiary: Direct brand partnerships (higher CPM, lower volume)

**Estimated Ad Revenue:**
- 100K DAU × ₹30 ARPU (ads only) = ₹30L/month
- But: Users with subscriptions don't see ads (reduces volume)
- Adjusted: 70K DAU × ₹25 ARPU = ₹17.5L/month
- As % of total revenue: 5% (assuming coins/subscriptions are 95%)

### Ad Privacy & User Trust

**Important:** LIVVLY does NOT compromise privacy for ad revenue.
- No personal data sold to ad networks
- Only anonymized, aggregated behavioral data
- Clear "Ad Choices" in settings (opt-out rewarded ads)
- Ads never interfere with safety/moderation

---

## Revenue Stream Mix: Rationale

**Why 70% Coins, 25% Subscriptions, 5% Ads?**

### 70% Coins Rationale
- Matches competitor models (FRND, Tango: 60-70% from coins/gifting)
- Voice gifting is primary monetization in voice dating
- Aligns with creator payout model (creators earn most from gifts)
- Predictable: Users predictably gift to creators they like

### 25% Subscriptions Rationale
- High margin (100% to LIVVLY, no platform cuts)
- Recurring revenue (better cash flow predictability)
- Growing segment (10-15% conversion is realistic)
- Can grow as user base matures (more power users)

### 5% Ads Rationale
- Supplementary; not core business
- Protects free users (ads motivate subscription upgrade)
- Privacy-first: Minimal data collection
- International precedent: Apps typically earn 2-10% from ads

**Flexibility:** Mix can shift based on market conditions:
- If ads underperform: Increase subscription value or coin pricing
- If subscription saturation: Lean harder on coins + ads
- If TDS/regulatory changes: May need to adjust creator share

---

## Revenue Growth Scenarios

### Conservative Case (Realistic)
- Month 1: 10K DAU, 2% monetization = ₹5-10L revenue
- Month 6: 50K DAU, 5% monetization = ₹30-50L revenue
- Month 12: 200K DAU, 8% monetization = ₹150-200L revenue

### Base Case (Target)
- Month 1: 20K DAU, 3% monetization = ₹15-20L revenue
- Month 6: 100K DAU, 7% monetization = ₹80-120L revenue
- Month 12: 500K DAU, 10% monetization = ₹400-500L revenue

### Aggressive Case (If marketing works)
- Month 1: 50K DAU, 5% monetization = ₹50-75L revenue
- Month 6: 200K DAU, 10% monetization = ₹200-300L revenue
- Month 12: 1M DAU, 12% monetization = ₹1Cr+ revenue

---

## Related Documents
- [Revenue Split Model](02-revenue-split.md) - How revenue is divided (Platform, Google Play, Creators)
- [Pricing Strategy](03-pricing-strategy.md) - Detailed coin/subscription pricing logic
- [Unit Economics](04-unit-economics.md) - CAC, LTV, ARPU calculations
- [Coin Economy Design](../../09-coin-economy-design/README.md) - Detailed coin mechanics

---

## Open Questions

1. **Bundle Offers:** Should we offer multi-pack discounts (e.g., buy 5x ₹99 packages, get 20% off)?
2. **Regional Pricing:** Should coin prices vary by region (e.g., cheaper in Tier-3 cities)?
3. **Ad Revenue Share:** Do we work with individual ad networks or aggregator (AdPel, etc.)?
4. **Crypto/Blockchain:** Should LIVVLY offer blockchain-based rewards (NFT tips, crypto rewards)?
5. **Seasonal Promotions:** Should revenue model support seasonal coin sales (e.g., 50% off during festivals)?

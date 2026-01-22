# Unit Economics

**Document ID:** 04-04
**Section:** Unit Economics & Financial Metrics
**Status:** Finalized
**Last Updated:** 2026-01-20

---

## Overview

Unit economics quantify the financial viability of LIVVLY's business model on a per-user basis. Key metrics: CAC (Customer Acquisition Cost), LTV (Lifetime Value), Payback Period, ARPU (Average Revenue Per User).

---

## Key Metrics Definitions

### ARPU (Average Revenue Per User)
**Definition:** Average monthly revenue generated per active user

**Calculation:**
```
ARPU = (Coin Revenue + Subscription Revenue + Ad Revenue) / Monthly Active Users
```

**Example (Month 1 Forecast):**
- 50K MAU
- Coin revenue: ₹25L
- Subscription revenue: ₹5L (5% conversion × ₹199 avg)
- Ad revenue: ₹2.5L
- **ARPU = ₹32.5L / 50K = ₹650/month**

**Targets by Phase:**
- Month 1-3: ₹200-300/month (early adopters, high monetization)
- Month 6-9: ₹150-250/month (lower monetization as scale increases)
- Month 12+: ₹200-350/month (mature, optimized pricing)

---

### CAC (Customer Acquisition Cost)
**Definition:** Average cost to acquire one new active user

**Calculation:**
```
CAC = (Marketing + User Acquisition Spend) / New Users Acquired
```

**Breakdown by Channel:**
- **App Store Ads (UAC):** ₹30-50 per install → ₹15-30 CAC (not all install → active)
- **Facebook/Instagram Ads:** ₹40-80 per click → ₹20-40 CAC
- **Organic/ASO:** ₹0 CAC (search visibility)
- **Referral Program:** ₹50-100 CAC (incentive borne by platform)
- **Influencer/Creator:** ₹100-200 CAC (one-time, high quality)

**Blended CAC Target:**
- Month 1-2: ₹50-100 (early, paid channels)
- Month 6: ₹30-50 (organic grows, avg decreases)
- Month 12+: ₹20-30 (strong organic, viral growth)

---

### LTV (Lifetime Value)
**Definition:** Total profit expected from a user over their lifetime on LIVVLY

**Calculation:**
```
LTV = (ARPU × Average Lifetime Months × Gross Margin) - CAC
```

**Where:**
- ARPU: ₹250/month (conservative average)
- Average Lifetime: 12 months (conservative for dating app)
- Gross Margin: 70% (after platform costs: moderation, support, infrastructure)
- CAC: ₹40 (blended average)

**Example:**
```
LTV = (₹250 × 12 × 70%) - ₹40
    = (₹250 × 12 × 0.70) - ₹40
    = ₹2,100 - ₹40
    = ₹2,060 per user lifetime
```

**Interpretation:**
- Each user generates ₹2,060 lifetime profit
- Payback period: ₹40 CAC / ₹250 ARPU = ~4.8 days
- **Very healthy unit economics** (payback < 1 month is gold standard)

---

### Payback Period
**Definition:** How many months until CAC is recovered from user revenue

**Calculation:**
```
Payback Period (months) = CAC / (ARPU × Gross Margin)
```

**Example:**
```
Payback = ₹40 / (₹250 × 70%)
        = ₹40 / ₹175
        = 0.23 months
        = ~7 days
```

**Targets:**
- Excellent: < 1 month payback (sustainable growth)
- Good: 1-3 months (acceptable)
- Concerning: > 3 months (may not be profitable)

**LIVVLY Target:** < 1 month payback (very efficient monetization)

---

### Retention Metrics

**Day 1 Retention (D1):** % of users returning on day 1 after install
- **Target:** 40-50% (typical for apps)

**Day 7 Retention (D7):** % of users still active on day 7
- **Target:** 25-35%
- **Calculation:** 100K installs → 30K still active on day 7

**Day 30 Retention (D30):** % of users still active on day 30
- **Target:** 10-15%
- **Calculation:** 100K installs → 12K still active on day 30

**Churn Rate:** Monthly rate of user drop-off
- **Formula:** (Users at start - New Users + Resurrected) / Users at start
- **Target:** <15% monthly churn (85%+ retention)

**LTV Impact:**
```
If Monthly Churn = 15% → Average Lifetime = 6.7 months
If Monthly Churn = 10% → Average Lifetime = 10 months
If Monthly Churn = 5% → Average Lifetime = 20 months
```

---

## Unit Economics by User Segment

### Segment 1: Regular User (70% of DAU)

**Profile:** Casual user, monthly spend ₹100-300

| Metric | Value |
|--------|-------|
| Monthly Spend | ₹150 |
| CAC | ₹40 |
| Lifetime (months) | 8 |
| Gross Margin | 70% |
| LTV | (₹150 × 8 × 0.70) - ₹40 = ₹800 |
| Payback | ₹40 / ₹105 = ~11 days |

**Implication:** Most users are sustainable (payback <2 weeks)

---

### Segment 2: Heavy User (20% of DAU)

**Profile:** Regular spender, monthly spend ₹500-1000, likely VIP subscriber

| Metric | Value |
|--------|-------|
| Monthly Spend | ₹700 (coins ₹500 + sub ₹200) |
| CAC | ₹30 (organic/referral) |
| Lifetime (months) | 12 |
| Gross Margin | 65% (subscription revenue is pure, coins are shared) |
| LTV | (₹700 × 12 × 0.65) - ₹30 = ₹5,430 |
| Payback | ₹30 / ₹455 = ~2 days |

**Implication:** Heavy users are extremely valuable; focus retention efforts here

---

### Segment 3: Casual User (10% of DAU)

**Profile:** Sporadic, minimal spend ₹0-50/month

| Metric | Value |
|--------|-------|
| Monthly Spend | ₹20 |
| CAC | ₹50 (paid channel) |
| Lifetime (months) | 4 (lower engagement) |
| Gross Margin | 70% |
| LTV | (₹20 × 4 × 0.70) - ₹50 = -₹44 |
| Payback | ₹50 / ₹14 = ~3.5 months |

**Implication:** Casual users are unprofitable if acquired via paid channels. Acquire via organic only.

---

## Unit Economics by Acquisition Channel

### Channel 1: Organic/ASO (Best)

| Metric | Value |
|--------|-------|
| CAC | ₹0 (no paid spend) |
| LTV | ₹2,000+ (no payback needed) |
| Volume | 30-40% of total users |
| **ROAS** | **Infinite** (no spend) |

**Strategy:**
- Maximize ASO (app store optimization)
- Strong viral coefficient (referral growth)
- Long-term sustainability

---

### Channel 2: Performance Ads (Facebook, Google)

| Metric | Value |
|--------|-------|
| CAC | ₹40-60 per user |
| LTV | ₹2,000 (₹250 ARPU × 12 × 70%) |
| **Payback** | **~10-15 days** |
| **ROAS** | **15-20x** (strong) |
| Volume | 40-50% of total users |

**Optimization:**
- Target regional language speakers (lower CPC)
- Lookalike audiences from existing users
- Retargeting lapsed users (cheaper)

---

### Channel 3: Influencer/Creator Seeding

| Metric | Value |
|--------|-------|
| CAC | ₹100-200 per user (expensive) |
| LTV | ₹2,000+ (high-quality users) |
| **Payback** | **~15-20 days** |
| **ROAS** | **8-15x** (good but expensive) |
| Volume | 5-10% of total users |

**Rationale:**
- Expensive CAC but better retention (trusted source)
- Creator recommendations carry weight
- Good for early launch credibility

---

## Growth & Profitability Analysis

### Month 1-3: Growth Phase (Focus on User Growth)

**Metrics:**
- 50K → 200K users (4x growth)
- Spend: ₹1Cr+ on marketing
- CAC: ₹50-75
- LTV: ₹1,500-2,000

**Economics:**
- ₹1Cr marketing ÷ ₹50-75 CAC = 133K-200K users acquired
- Revenue: 50K → 200K MAU × ₹250 ARPU = ₹5Cr/month revenue
- Gross Profit (70%): ₹3.5Cr
- **Net: Operating loss** (marketing spend > profit)

**Strategy:** Accept losses to build user base. Focus on retention, not profitability.

---

### Month 6-9: Optimization Phase (Balance Growth & Profitability)

**Metrics:**
- 200K → 500K users (2.5x growth)
- Spend: ₹75L on marketing (reducing)
- CAC: ₹30-40 (organic growing)
- LTV: ₹2,000+

**Economics:**
- ₹75L marketing ÷ ₹35 CAC = 214K users acquired
- Revenue: 500K MAU × ₹250 ARPU = ₹12.5Cr/month revenue
- Gross Profit (70%): ₹8.75Cr
- Marketing Spend: ₹75L
- **Net Profit: ₹8Cr/month**

**Strategy:** Positive unit economics achieved. Increase profitability while maintaining growth.

---

### Month 12+: Maturity Phase (Optimize Profitability)

**Metrics:**
- 500K → 1M+ users
- Spend: ₹50L on marketing (highly efficient)
- CAC: ₹20-30 (strong organic)
- LTV: ₹2,100+

**Economics:**
- Revenue: 1M MAU × ₹300 ARPU = ₹30Cr/month revenue
- Gross Profit (70%): ₹21Cr
- Marketing Spend: ₹50L
- Operations/Team: ₹5Cr (estimate)
- **Net Profit: ₹15.5Cr/month**

**Strategy:** Mature business with strong profitability and sustainable growth.

---

## Sensitivity Analysis

### What if ARPU is 20% lower? (₹200 instead of ₹250)

**Impact:**
- LTV drops: ₹2,060 → ₹1,600 (22% reduction)
- Payback period: ~10 days → ~14 days
- Still profitable, but lower margins

**Response:**
- Increase coin prices 10% (test ₹109 instead of ₹99)
- Push subscriptions harder (higher ARPU)
- Improve creator discovery (higher gifting frequency)

---

### What if retention drops? (D30 = 8% instead of 12%)

**Impact:**
- Average lifetime: 12 months → 8 months (33% reduction)
- LTV: ₹2,060 → ₹1,400 (32% reduction)

**Response:**
- Increase retention spending (push notifications, events, community)
- Improve product experience (call quality, matching algorithm)
- Loyalty program (rewards for consistent usage)

---

### What if CAC doubles? (₹80 instead of ₹40)

**Impact:**
- Payback period: 7 days → 14 days (still under 1 month)
- LTV: ₹2,060 → ₹2,010 (minimal impact due to high ARPU)
- Can still sustain

**Response:**
- Reduce paid spend; focus on organic
- Optimize creative/messaging
- Negotiate better rates with ad networks

---

## Benchmarking vs. Industry

### LIVVLY vs. FRND / Tango

| Metric | LIVVLY (Target) | FRND (Est.) | Tango (Est.) |
|--------|-----------------|------------|------------|
| ARPU | ₹250-300 | ₹300-400 | ₹200-250 |
| CAC | ₹30-50 | ₹40-60 | ₹25-40 |
| LTV | ₹2,000-2,500 | ₹2,500-3,500 | ₹1,500-2,000 |
| Payback (days) | 7-10 | 10-15 | 5-10 |
| D30 Retention | 12-15% | 15-20% | 10-12% |

**LIVVLY Positioning:**
- Lower CAC (regional focus, organic growth)
- Competitive ARPU (not as high as FRND, but good)
- Strong LTV (healthy unit economics)
- Excellent payback period (sustainable)

---

## Path to Profitability

**Breakeven Timeline:**
1. **Month 3:** Revenue covers operations, not marketing
2. **Month 6:** Positive unit economics (profit per user)
3. **Month 9:** Revenue covers operations + marketing
4. **Month 12:** Significant net profitability (₹5-10Cr/month)

**Cumulative Profit:**
- Months 1-3: -₹2Cr (growth investment)
- Months 4-6: -₹1Cr (improving)
- Months 7-9: ₹2Cr (breakeven month 8-9)
- Months 10-12: ₹15Cr (profitability compounds)

**Total Year 1:** ~₹14Cr net (with heavy growth spending)

---

## Related Documents
- [Revenue Streams](01-revenue-streams.md) - Revenue sources
- [Pricing Strategy](03-pricing-strategy.md) - How pricing drives ARPU
- [Cost Estimation](../../14-cost-estimation/README.md) - CAC, operational costs
- [Success Metrics](../../15-success-metrics/README.md) - KPIs and targets

---

## Open Questions

1. **Geographic CAC Variance:** Should CAC targets vary by region (lower in Tier-3)?
2. **Cohort Retention:** How do we handle seasonal cohorts (e.g., college users in Jan)?
3. **Creator ARPU:** Should creators have separate ARPU tracking (different economics)?
4. **Lifetime Reassessment:** When/how often should we reassess average lifetime (currently 12 months)?
5. **Viral Coefficient:** What's our viral loop? Can we achieve >1.0 coefficient for exponential growth?

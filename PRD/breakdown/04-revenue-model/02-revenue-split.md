# Revenue Split Model

**Document ID:** 04-02
**Section:** 4.2 Revenue Split Model
**Status:** Finalized
**Last Updated:** 2026-01-20

---

## Executive Summary

LIVVLY uses the **High Creator Share Model** as primary split, offering creators 40.95% net (after TDS) of user spending. This competitive advantage attracts top creators from competitors like FRND (50-60% share).

**Primary Model: High Creator Share**
- Google Play Cut: 30%
- LIVVLY Platform: 24.5% (35% of net revenue)
- Creator (Gross): 45.5% (65% of net revenue)
- TDS Deduction: 10%
- **Creator (Net): 40.95%**

---

## Revenue Flow Diagram

```
User Spends ₹500
    │
    ├─────────────────────────────────────┐
    │ Google Play Cut: ₹150 (30%)         │
    │ ├─ Apple (Apple ecosystem)          │
    │ └─ Google                           │
    │
    └─ Net Revenue to LIVVLY: ₹350 (70%)
            │
            ├─ LIVVLY Platform: ₹87.50 (25% of ₹350)
            │  ├─ Infrastructure costs (30%)
            │  ├─ Moderation team (40%)
            │  ├─ Support/Operations (20%)
            │  └─ Profit (10%)
            │
            └─ Creator Pool: ₹262.50 (75% of ₹350)
                    │
                    ├─ Creator (Gross): ₹227.50 (65% of ₹350)
                    │   ├─ TDS Deduction: ₹22.75 (10%)
                    │   └─ Creator (Net): ₹204.75 (40.95% of original ₹500)
                    │
                    └─ Platform Commission (Retained): ₹35 (10% of pool)
```

---

## Split Models Comparison

### Model 1: Standard Split (31.5% creator) - *Not Recommended*

| Receiver | % of ₹500 | Amount |
|----------|-----------|---------|
| Google Play | 30% | ₹150 |
| LIVVLY Platform | 55% (of net) | ₹193 |
| Creator | 45% (of net) | ₹157 |

**Rationale for rejection:** Only 31.5% of original user spend reaches creator (after Google Play cut). Uncompetitive vs. FRND's 50-60%.

---

### Model 2: Agency Support (23.6% creator) - *For Multi-Channel Creators*

| Receiver | % of ₹500 | Amount |
|----------|-----------|---------|
| Google Play | 30% | ₹150 |
| LIVVLY Platform | 38.5% (of net) | ₹193 |
| Agency (Middle-man) | 7.9% (of net) | ₹39 |
| Creator | 23.6% (of net) | ₹118 |

**Use Case:** For creators who want management/promotion services. Agency handles creator recruitment, content strategy, dispute resolution.

**Implications:**
- Creates revenue stream from agencies (B2B opportunity)
- Some creators willing to accept lower share for agency support
- Risk: Agencies might not serve all creators fairly
- Recommendation: Only offer to top creators (Top 20%+)

---

### Model 3: High Creator Share (40.95% creator) - **RECOMMENDED PRIMARY**

| Receiver | % of ₹500 | Amount |
|----------|-----------|---------|
| Google Play | 30% | ₹150 |
| LIVVLY Platform | 35% (of net) | ₹122.50 |
| Creator (Gross) | 65% (of net) | ₹227.50 |
| TDS Deduction (10%) | - | -₹22.75 |
| **Creator (Net)** | **40.95%** | **₹204.75** |

**Why This Model:**

1. **Competitive Advantage:** 40.95% net is highest in India industry
   - FRND: 50-60% gross (before TDS) ≈ 45-54% net (with TDS)
   - Tango: 50-60% gross ≈ 45-54% net
   - LIVVLY: 65% gross = 40.95% net (but lower platform cut)
   - *Positioning: "High creator revenue share"*

2. **Creator Acquisition:** Attracts FRND/Tango creators
   - "Switch to LIVVLY for higher earnings"
   - Signing bonuses for top creators (e.g., ₹50k guarantee first month)

3. **Platform Sustainability:** Still 24.5% net for LIVVLY operations
   - Sufficient for: moderation, support, infrastructure
   - Growth: Marketing, team hiring
   - Profit: Long-term sustainability

4. **User Value:** Users see creators getting paid (trust)
   - Transparency builds ecosystem trust
   - Creator success = user retention (network effects)

**Trade-off:** Lower LIVVLY profit margin vs. higher creator loyalty

---

## TDS (Tax Deducted at Source) Explanation

**What is TDS?**
- Indian government requires platforms to deduct 10% tax from creator payments over ₹20,000/month
- LIVVLY must deduct and remit this to Indian government
- Creator receives net amount after TDS

**Example:**
- Creator earns ₹100,000 in a month
- TDS deduction: ₹10,000 (10%)
- Creator receives: ₹90,000
- LIVVLY pays ₹10,000 to government (on creator's behalf)

**Implementation:**
- TDS calculated at time of payout
- Clear communication to creators (show gross and net)
- TDS form issued to creator for tax filing
- LIVVLY must maintain compliance records

**Impact on Model:**
- Creator advertises 65% gross share
- But actually receives 40.95% net (after TDS + Google Play cuts)
- Critical: Educate creators upfront about all deductions

---

## Subscription Revenue Split

**Subscriptions:** LIVVLY keeps 100% (no creator share)
- ₹199 VIP subscription → LIVVLY: ₹199
- ₹499 Premium subscription → LIVVLY: ₹499
- No creator commission (subscriptions are pure platform revenue)

**Rationale:**
- Subscription provides platform services (unlimited calls, priority matching)
- Not direct creator support; general platform benefit
- Enables lower coin costs for subscribers (20-30% discount)

---

## Ad Revenue Split

**Ads:** LIVVLY keeps 70-80%, Ad Network takes 20-30%
- Google AdMob: LIVVLY keeps 70%, AdMob keeps 30%
- Facebook Audience Network: LIVVLY keeps 75%, FAN keeps 25%
- Direct brand deals: Negotiate individually (LIVVLY keeps 80-90%)

**No creator commission** on ads (pure platform revenue)

---

## Example Scenarios

### Scenario 1: User Spends ₹1000 on Coins

| Participant | Amount | % of Original |
|------------|---------|---------------|
| Google Play | ₹300 | 30% |
| LIVVLY Platform | ₹245 | 24.5% |
| Creator (Gross) | ₹455 | 45.5% |
| TDS (10%) | -₹45.50 | -9.1% |
| **Creator (Net)** | **₹409.50** | **40.95%** |

**Creator earnings:** ₹409.50 (from ₹1000 user spend)

---

### Scenario 2: Creator Gets Top Creator Status (Negotiated Better Terms)

For top 5% creators, LIVVLY might offer:
- Gross share: 70% (up from 65%)
- TDS: Still 10%
- Creator (Net): 63% (70% × 90%)

| Participant | Amount | % of Original |
|------------|---------|---------------|
| Google Play | ₹300 | 30% |
| LIVVLY Platform | ₹105 | 21% |
| Creator (Gross) | ₹595 | 59.5% |
| TDS (10%) | -₹59.50 | -5.95% |
| **Creator (Net)** | **₹535.50** | **53.55%** |

**Strategy:** Top creators get premium share → High retention → Network effects

---

### Scenario 3: Creator Joins Agency Program

Creator uses agency intermediary (gets support + promotion):

| Participant | Amount | % of Original |
|------------|---------|---------------|
| Google Play | ₹300 | 30% |
| LIVVLY Platform | ₹193 | 38.5% |
| Agency | ₹39 | 7.9% |
| Creator (Gross) | ₹118 | 23.6% |
| TDS (10%) | -₹11.80 | -2.36% |
| **Creator (Net)** | **₹106.20** | **21.24%** |

**Trade-off:** Lower revenue but professional support (better for new creators)

---

## Implementation Requirements

### Payment Processing
- [ ] Bank integration for payouts (NEFT, IMPS, UPI)
- [ ] TDS calculation engine (10% threshold logic)
- [ ] Payout reconciliation (match coins spent to creator payout)
- [ ] Compliance: TDS forms, 26AS reporting

### Transparency
- [ ] Creator dashboard shows gross vs. net earnings
- [ ] Real-time earnings updates (refreshed hourly)
- [ ] Payout history with breakdown per stream
- [ ] TDS form download (for creator tax filing)

### Fraud Prevention
- [ ] Detect fake gifting (user buying coins, giving to own account)
- [ ] Monitor unusual payout patterns
- [ ] Require minimum creator activity (prevent money laundering)
- [ ] KYC checks before payout (already in place)

---

## Split Model Evolution

### Phase 1 (Launch): High Creator Share (65% gross)
- Attract creators from competitors
- Build loyalty with strong economics
- Premium positioning

### Phase 2 (6 months): Tiered Creator Share
- Top 10% creators: 70%+ gross (best retention)
- Top 50% creators: 65% gross
- Others: 60% gross (still competitive)
- Incentivizes creator growth and performance

### Phase 3 (12 months): Agency Program Maturity
- 30% of revenue from agency partnerships
- Agencies handle creator onboarding/support
- LIVVLY focuses on platform/technology
- Reduces moderation burden

---

## Regulatory & Compliance

### TDS Compliance
- LIVVLY must comply with Section 194O (TCS on e-commerce)
- Maintain TDS records for 6 years
- File quarterly TDS returns (quarterly TCS returns)
- Issue Form 26AS to creators annually

### GST Considerations
- Coin purchases: Subject to 18% GST (paid by user)
- Creator payouts: Not subject to GST (payments for services, reverse charge)
- Ads: May be subject to GST (depends on treatment of digital ads)

### AML/KYC
- Creator KYC required before payout (PAN, bank account)
- AML checks for large payouts (>₹10L/month)
- Suspicious activity reporting (SAR) to FIU

---

## Related Documents
- [Revenue Streams](01-revenue-streams.md) - Coins, subscriptions, ads
- [Pricing Strategy](03-pricing-strategy.md) - Coin pricing psychology
- [Unit Economics](04-unit-economics.md) - CAC, LTV, ARPU
- [Creator Payout System](../../10-creator-payout-system/README.md) - Implementation details

---

## Open Questions

1. **Top Creator Premium:** Should top creators get 70%+ gross share, or keep uniform 65%?
2. **Agency Viability:** Is 7.9% agency commission sustainable for agencies?
3. **Regional Pricing:** Should splits vary by region (lower creator share in Tier-1 cities)?
4. **Crypto Payouts:** Should LIVVLY offer crypto/blockchain payouts?
5. **Minimum Payout:** What's minimum balance before creator can withdraw (₹100, ₹500, ₹1000)?

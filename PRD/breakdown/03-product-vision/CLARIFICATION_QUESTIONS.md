# Clarification Questions for Product Vision

**Document ID:** 03-CQ
**Section:** Product Vision (Section 3)
**Date:** 2026-01-20
**Status:** Open - Awaiting Response

---

## Questions by Category

### 📊 Product Strategy & Goals

#### Q1: Timeline Realism
**Question:** Is the target of 10M users within 24 months realistic, or should it be extended to 36 months?

**Context:**
- Target given: 6 months → 50K users, 12 months → 200K users, 24 months → 10M users
- Growth rate implies 40x growth in 12 months (months 13-24)
- Comparable apps (FRND, Tango) took 3-5 years to reach similar scale

**Options:**
- A) Keep 24-month target (aggressive growth strategy)
- B) Extend to 30 months
- C) Extend to 36 months
- D) Different trajectory: more conservative early, aggressive post-PMF

**Implications:** Affects marketing spend, team hiring, cash burn, fundraising strategy

---

#### Q2: Regional Expansion Roadmap
**Question:** After achieving dominance in South India (Tamil Nadu, Andhra Pradesh, Karnataka), what's the geographic expansion strategy?

**Context:**
- Vision focuses on South India + regional languages
- North India has different languages (Hindi, Punjabi) and player bases (FRND dominant)
- Options: Expand to other South Indian languages first, then North India? Or simultaneous?

**Options:**
- A) South-first: Launch additional South Indian languages (Malayalam, Kannada variants)
- B) North-expansion: Add Hindi, Punjabi support and enter North India market
- C) Phased: 12 months South India dominance, then explore North India
- D) Dual-track: South consolidation + limited North India test simultaneously

**Implications:** Product roadmap, localization priorities, market positioning messaging

---

#### Q3: AI Integration Timing
**Question:** When should AI companions launch relative to human creators?

**Context:**
- Vision mentions "hybrid AI + human model" as competitive advantage
- But timeline not specified: Launch together in V1? AI in V1.5? AI in V2?
- AI requires significant R&D, can impact creator experience negatively if poorly done

**Options:**
- A) V1 (Launch): Both AI + human creators simultaneously
- B) V1.5 (Months 3-6): Launch human creators first, then add AI once creator base is stable
- C) V2 (Months 9-12): Perfect human creator experience first, then introduce AI
- D) Never: Focus exclusively on human creators, deprioritize AI

**Implications:** Product scope, engineering roadmap, creator communication, competitive positioning

---

### 💰 Creator Economics & Payout

#### Q4: Creator Payout Structure
**Question:** Should the 75-85% creator share be uniform across all creators, or tiered based on performance/status?

**Context:**
- Current model: All creators get same 75-85% (or 35% platform, 65% creator split)
- Alternative: Tier 1 creators (top 10%) get 85%, Tier 2 (top 50%) get 75%, Others get 65%
- Trade-off: Simplicity vs. incentive alignment

**Options:**
- A) Uniform (all creators 75-85%): Simple, fair, competitive
- B) Tiered: Reward top creators with higher share, incentivize performance
- C) Hybrid: Base 75%, bonus to top 20% (e.g., 85% for top performers)
- D) Time-based: 65% first 3 months (ramp-up), then 75-85% (retention)

**Implications:** Creator messaging, contractual complexity, platform revenue, retention dynamics

---

#### Q5: Minimum Payout Threshold
**Question:** Should creators be eligible for payout only after reaching a minimum earnings threshold?

**Context:**
- Example: Only payout when creator has earned ≥₹500, or every ₹1000 earned
- Rationale: Reduce payout processing costs, prevent micro-transactions, improve cash flow
- Downside: Creators feel untrusted, creates additional barrier to first payout

**Options:**
- A) No minimum: T+1 payout regardless of amount (highest trust)
- B) Minimum ₹500: Payout when earned ≥₹500
- C) Minimum ₹1000: Payout when earned ≥₹1000
- D) Minimum + scheduling: Minimum ₹500, but only payout on Friday (weekly batch)

**Implications:** Creator experience, operational costs, cash flow management, regulatory compliance (TDS)

---

#### Q6: International Creator Support
**Question:** Should LIVVLY support creator payouts to international (non-Indian) creators in non-INR currency?

**Context:**
- Indian diaspora might want to use platform from abroad
- Regulatory complexity: FEMA, forex conversion, tax treaties
- Support cost: Integration with international payment processors
- Market: Likely very small initially

**Options:**
- A) India-only: INR payouts only, Indian bank accounts only
- B) Indian diaspora: Support USD/EUR to international bank accounts (future phase)
- C) Open: Support international creators from day 1 (complexity)
- D) TBD: Defer decision until creator requests arise

**Implications:** Payment processor integration, legal/compliance complexity, market positioning ("Indian first" vs. "Global")

---

#### Q7: Creator+ Subscription & Payout Interaction
**Question:** Should Creator+ subscription (₹999/month, currently shows 40% coin discount) also affect payout percentage?

**Context:**
- Currently: Creator+ gives analytics + promotion tools + coin discount
- Option: Also offer higher payout share (e.g., 80% or 85%) exclusively to Creator+ subscribers
- Implication: Creates two-tier creator economics

**Options:**
- A) No payout change: Creator+ only gives discount + analytics, not higher payout
- B) Higher payout: Creator+ subscribers get 85% payout vs. 75% for free
- C) Bonus program: Creator+ subscribers get bonus pool (5% extra on top)
- D) Performance-based: Creator+ unlocks ability to earn higher share through performance

**Implications:** Creator subscription adoption, complexity of payout model, retention mechanics

---

### 🔒 Safety & Moderation

#### Q8: Moderation Scale & Budget
**Question:** What budget and team size is allocated for 24/7 content moderation in regional languages?

**Context:**
- Target: 100k+ DAU within 6 months requires substantial moderation team
- Regional languages (Tamil, Telugu, Kannada) require native speakers
- 24/7 coverage implies ~12-15 FTE per language minimum
- Cost: ₹50-100 per audio minute for human moderation

**Options:**
- A) Lean AI: 95% AI, 5% human (cost-efficient, quality risk)
- B) Balanced: 60% AI, 40% human (quality + cost balance)
- C) Human-heavy: 30% AI, 70% human (quality-first, high cost)
- D) Phased: Start with 70% human (quality launch), ramp AI over 12 months

**Implications:** Launch timing, content moderation quality, operational costs, team hiring

---

#### Q9: Moderation Escalation Process
**Question:** When content moderation decisions require executive/legal review, what's the escalation process?

**Context:**
- Most reports are clear violations (explicit content, harassment)
- Edge cases require nuance: Cultural context, intent, regulatory interpretation
- Escalation delays can frustrate users; clarity prevents arbitrary decisions

**Options:**
- A) Documented SLAs: Category X requires legal review within 24 hours, etc.
- B) Escalation tier: Moderator → Team Lead → Manager → Legal (multi-step)
- C) Rapid escalation: Any moderation decision can be escalated by user; 48-hour guarantee
- D) TBD: Define during trust & safety design phase

**Implications:** Moderation training, legal team capacity, user trust in system fairness

---

### ♿ Accessibility & Inclusion

#### Q10: Disability Accessibility Timeline
**Question:** When should LIVVLY implement disability accessibility features (screen readers, captions, audio descriptions)?

**Context:**
- V1 focus: Regional language localization
- Deferred to V2: Disability accessibility (based on your clarification)
- But: What's the firm commitment? V2.0 (month 6)? V2.5 (month 9)? V3 (month 12+)?

**Options:**
- A) V1: Include from launch (WCAG 2.1 AA compliance)
- B) V1.5 (Months 3-4): Add after human creator stability
- C) V2 (Months 6-9): Dedicated disability accessibility push
- D) Community-driven: When users request, we implement

**Implications:** Engineering timeline, team hiring (accessibility specialist), market positioning

---

#### Q11: Multi-Regional Language Moderation Parity
**Question:** When expanding to new regional languages (e.g., Malayalam, Telugu, Kannada variants), should moderation standards be identical to Tamil launch?

**Context:**
- Tamil: Strongest initial moderation team, highest QA standards
- Subsequent languages: May have smaller speaker communities, less mature moderation resources
- Risk: Moderation quality degrades as we expand languages

**Options:**
- A) Identical standards: Every language gets same moderation rigor (expensive, slow expansion)
- B) Tiered standards: Core language (Tamil) strictest, new languages slightly lower bar
- C) Community-moderated: Leverage creator/user community for new languages
- D) Phased: Launch language in "beta" with lighter moderation, ramp over 3 months

**Implications:** Expansion speed, quality perception across regions, cost scaling

---

### 💼 Competitive & Business

#### Q12: Acquisition vs. Independence
**Question:** Is LIVVLY positioned for acquisition by a larger player (e.g., FRND parent company, ByteDance), or for long-term independence?

**Context:**
- Acquisition: Might prioritize reaching profitability + user base quickly, then sell
- Independence: Longer time horizon, focus on sustainable creator economics, potential IPO
- Impacts: Hiring, partnership strategy, pricing decisions, investor communications

**Options:**
- A) Acquisition-focused: Build to $1B valuation, then sell to FRND/BIGO/others
- B) Independence-focused: Build long-term sustainable business, potential IPO in 5-7 years
- C) Flexible: Build strong fundamentals, evaluate offers if they come
- D) TBD: Decide based on market conditions at Year 2

**Implications:** Financial planning, hiring senior team, partnership negotiations, investor deck

---

#### Q13: Competitive Response if FRND Matches Payout
**Question:** If FRND (or Tango) matches the 75-85% creator payout, what's LIVVLY's next competitive lever?

**Context:**
- Current differentiation: Highest payout (75-85% vs. 50-60%)
- If matched: We can't compete on payout alone
- Alternative levers: Regional language depth, creator support, payment speed, AI, etc.

**Options:**
- A) Language moat: Deepen regional language features, make it non-replicable
- B) Creator loyalty: Multi-year contracts, guarantees, community investment
- C) Hybrid AI: Lean into AI+human hybrid that competitors can't match
- D) Payment speed: Offer same-day payout (instead of T+1), raise the bar
- E) Multiple strategies: All of the above

**Implications:** Product roadmap, marketing messaging, long-term positioning

---

#### Q14: Creator Payment Guarantees & Lock-In
**Question:** Should LIVVLY offer performance guarantees or minimum payments to lock-in top creators from competitors?

**Context:**
- Example: Offer FRND's top creators ₹50k/month minimum guarantee for 12 months if they switch
- Rationale: Lock-in top creators, create network effects, build perception of "highest paying"
- Cost: Significant financial commitment; risk if creator doesn't perform

**Options:**
- A) No guarantees: Pure 75-85% share, no minimums
- B) Top creator guarantees: Top 100 creators get ₹10k-50k/month minimums
- C) Tiered guarantees: Performance tiers unlock guaranteed minimums (e.g., hit 100k coins → ₹5k guarantee)
- D) Bonus pool: Rather than guarantees, offer performance bonuses (e.g., top creator each month gets bonus)

**Implications:** Acquisition budget for creators, contract complexity, cash flow management

---

#### Q15: Competitive Expansion Timing to North India
**Question:** What's the timeline for expanding LIVVLY's footprint into North India (Hindi-speaking, FRND-dominated region)?

**Context:**
- Current positioning: South India focus (Tamil Nadu, Andhra Pradesh, Karnataka)
- North India: FRND dominates, higher CAC, different languages/culture
- Timing question: Do we expand after South India dominance, or test North India in parallel?

**Options:**
- A) Sequential: 12 months South India consolidation, then North India expansion (months 13-24)
- B) Year 2: Defer North India to Year 2 entirely
- C) Parallel: Limited North India testing in months 6-12 while dominating South
- D) Defensive: Only if FRND or Tango try to enter South India with local language focus

**Implications:** Marketing spend allocation, localization roadmap, team hiring strategy, competitive positioning

---

## Summary Table

| # | Category | Status | Priority | Depends On |
|----|----------|--------|----------|-----------|
| Q1 | Strategy | Open | High | Fundraising |
| Q2 | Strategy | Open | High | Product roadmap |
| Q3 | Strategy | Open | Medium | Tech feasibility |
| Q4 | Economics | Open | High | Creator contracts |
| Q5 | Economics | Open | Medium | Payment systems |
| Q6 | Economics | Open | Low | Future phase |
| Q7 | Economics | Open | Medium | Creator+ strategy |
| Q8 | Safety | Open | High | Launch readiness |
| Q9 | Safety | Open | Medium | Legal team input |
| Q10 | Accessibility | Open | Low | V2 planning |
| Q11 | Accessibility | Open | Medium | Expansion planning |
| Q12 | Business | Open | High | Investor strategy |
| Q13 | Competitive | Open | High | Long-term strategy |
| Q14 | Competitive | Open | Medium | Budget planning |
| Q15 | Competitive | Open | Medium | 24-month roadmap |

---

## Next Steps

1. **Review & Prioritization:** Product/leadership team reviews and prioritizes questions
2. **Decision Making:** Team decides on answers and documents rationale
3. **Documentation:** Decisions documented back to relevant PRD sections
4. **Stakeholder Communication:** Answers communicated to affected teams (product, finance, legal, ops)

---

## Related Documents
- [Vision Statement](01-vision-statement.md) - Contains some open questions
- [Mission Statement](02-mission-statement.md) - Contains creator economics questions
- [Core Values](03-core-values.md) - Contains moderation/safety questions
- [User Journey](04-user-journey.md) - Contains monetization questions
- [Competitive Positioning](07-competitive-positioning.md) - Contains competitive questions

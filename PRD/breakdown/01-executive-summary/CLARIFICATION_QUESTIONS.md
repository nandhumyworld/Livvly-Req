# Executive Summary - Clarification Questions

## Instructions

Please provide answers to these 22 questions to validate and refine the Executive Summary section. These answers will be documented and used to create a final, validated summary.

---

## Section 1: Product Definition (5 Questions)

### Q1.1: Voice Room Capacity

**Question**: What's the maximum concurrent users in a single audio room?

- Current assumption: Not specified
- Impact: Affects server architecture, QoS design, feature rollout strategy
- Related file: `01-product-overview.md`

**Your Answer:**

```
Lets keep about 10 users for now
```

---

### Q1.2: AI Companion Features

**Question**: What specific interactions does the AI companion provide?

- Options: Friendship, dating advice, language practice, general chat, other?
- Current assumption: Unspecified
- Impact: Feature development roadmap, content moderation strategy, user expectations
- Related file: `01-product-overview.md`

**Your Answer:**

```
Friendship, dating advice, general chat
```

---

### Q1.3: Gift System Mechanics

**Question**: How does the virtual gifting system work?

- Sub-questions:
  - Are gifts currency-based (e.g., 1 gift = ₹5)?
  - Can users earn gifts or only purchase them?
  - What's the gift price range?
- Current assumption: Currency-based but details unspecified
- Impact: Revenue model, user psychology, creator earnings calculations
- Related file: `01-product-overview.md`

**Your Answer:**

```
yes gifts are coin based, and coins to be purchaced based on currency, like buy 100 coins using ₹10 and send gifts example heart is 10 coins cucumber 50 coins carrot 55 coins car 10000 coins
```

---

### Q1.4: Call Duration Limits

**Question**: Are there time limits for individual calls or rooms?

- Sub-questions:
  - Maximum call duration for 1-on-1 calls?
  - Maximum room session duration?
  - Different limits for paid vs free users?
- Current assumption: No limits specified
- Impact: Server capacity planning, user experience design, retention strategy
- Related file: `01-product-overview.md`

**Your Answer:**

```
Free users: 15 min per call, Paid users: Unlimited calls, Rooms: Unlimited duration for all users
```

---

### Q1.5: Offline Functionality

**Question**: What features work offline vs requiring real-time connection?

- Sub-questions:
  - Can users browse profiles offline?
  - Can users record voice messages for later?
  - What metadata is synced when online?
- Current assumption: All features require real-time connection
- Impact: App architecture, data sync design, user experience in low-connectivity areas
- Related file: `01-product-overview.md`

**Your Answer:**

```
Users can browse profiles offline, but calling/messaging require real-time connection. No offline voice recording capability.
```

---

## Section 2: Competitive Strategy (5 Questions)

### Q2.1: Revenue Share Tiers

**Question**: Is the 75-85% creator revenue share fixed or tiered by performance?

- Sub-questions:
  - Fixed 75% for all creators or ranges 75-85%?
  - If tiered, what are the tiers? (e.g., 75% for <₹10K, 85% for >₹50K earnings/month)
  - Are there monthly minimum earnings to access higher tiers?
- Current assumption: 75-85% range suggested but mechanics unclear
- Impact: Creator satisfaction, competitive positioning, operations complexity
- Related file: `02-key-differentiators.md`

**Your Answer:**

```
Three-tier system: 75% for earnings <₹10K/month, 80% for ₹10-50K/month, 85% for >₹50K/month
```

---

### Q2.2: Payout Mechanism (T+1)

**Question**: How is next-day (T+1) payout guaranteed?

- Sub-questions:
  - Direct bank transfer, wallet, or other?
  - Which banks/payment processors are supported?
  - Minimum payout threshold?
  - Any processing delays (e.g., bank hours)?
  - What happens on weekends/holidays?
- Current assumption: T+1 is a guarantee but mechanism unclear
- Impact: Backend payment system architecture, creator trust, compliance requirements
- Related file: `02-key-differentiators.md`

**Your Answer:**

```
T+1 payout via Bank Transfer + UPI. Minimum threshold: ₹100. No weekend delays. Processing fee: None (platform absorbs costs).
```

---

### Q2.3: AI Companion Monetization

**Question**: How do users monetize interactions with the AI companion?

- Sub-questions:
  - Can users tip/gift to the AI? If yes, does this revenue go to LIVVLY or a creator?
  - Are there subscription tiers for AI premium features?
  - How do AI earnings compare to human creator earnings?
  - Who owns the AI revenue (platform vs creators)?
- Current assumption: AI exists but monetization model is unclear
- Impact: Revenue model, creator perception, AI feature differentiation
- Related file: `02-key-differentiators.md`

**Your Answer:**

```
Free AI basic features. Premium AI subscription tier: ₹99/month for extended conversations and advanced features. Revenue goes to LIVVLY platform.
```

---

### Q2.4: Regional Expansion Timeline

**Question**: What are the specific dates for Telugu and Kannada support rollout?

- Sub-questions:
  - When does Telugu support launch? (Q2 2026? Q3 2026?)
  - When does Kannada support launch?
  - Any other regional languages planned? (Malayalam, Gujarati, etc.)
  - Phased rollout to existing users or new user acquisition?
- Current assumption: Expansion planned but timeline not specified
- Impact: Product roadmap, hiring decisions, marketing strategy, market entry sequencing
- Related file: `02-key-differentiators.md`

**Your Answer:**

```
Tamil Nadu launch first, then Telugu (Q2 2026), followed by Kannada and Malayalam (Q3 2026). Sequential 1 region per quarter expansion.
```

---

### Q2.5: Creator Onboarding Requirements

**Question**: What are the minimum requirements to access high creator payouts?

- Sub-questions:
  - Minimum follower count to qualify for 75-85% share?
  - Verification requirements (ID, phone, etc.)?
  - Country/region restrictions for creators?
  - Account age minimum?
  - Any rejection criteria (content violations, etc.)?
- Current assumption: No minimum specified; 75-85% available to all
- Impact: Creator acquisition strategy, content moderation, compliance, fraud prevention
- Related file: `02-key-differentiators.md`

**Your Answer:**

```
No minimum requirements. All verified creators get 75-85% revenue share (tiered by earnings). Only verification: valid phone number and account email.
```

---

## Section 3: Market Understanding (7 Questions)

### Q3.1: Total Addressable Market (TAM)

**Question**: What's the total potential user base in target regions/age groups?

- Sub-questions:
  - Population of Tamil Nadu, AP, Karnataka aged 18-35?
  - What % have smartphones?
  - What % have internet access (3G+)?
- Current assumption: TAM not quantified in original PRD
- Impact: Growth projections validation, investor communications, market potential assessment
- Related file: `03-target-market.md`

**Your Answer:**

```
TAM: 75-100M potential users (Tamil Nadu, Andhra Pradesh, Karnataka aged 18-35 with smartphones and internet access)
```

---

### Q3.2: Serviceable Addressable Market (SAM)

**Question**: What % of TAM is realistically addressable by LIVVLY?

- Sub-questions:
  - What % of smartphone users download dating/social apps?
  - Regional preference for voice-first apps vs text-based?
  - Estimated SAM as % of TAM?
- Current assumption: SAM not quantified
- Impact: Market penetration goals validation, competitive positioning, growth ceiling estimation
- Related file: `03-target-market.md`

**Your Answer:**

```
SAM: 10-15% of TAM (7.5-15M users) - conservative estimate based on voice-first app adoption and dating/social app preferences
```

---

### Q3.3: Market Penetration Goals

**Question**: What % market penetration is targeted by end of Year 1?

- Sub-questions:
  - 200K users in Year 1 = what % of addressable market?
  - Realistic penetration ceiling for Year 2?
  - Competitive market share target vs FRND/Tango?
- Current assumption: Goals set but penetration % not specified
- Impact: Growth strategy realism, competitive analysis, regional prioritization
- Related file: `03-target-market.md`

**Your Answer:**

```
Year 1 target: 200K users = 1-2% of TAM penetration. Realistic and achievable based on regional market size and competitive landscape.
```

---

### Q3.4: Creator Profile Priority

**Question**: What creator profile is the priority for initial recruitment?

- Sub-questions:
  - Recruit established influencers (mass appeal, quick user growth)?
  - Recruit new talents (authentic, long-term engagement)?
  - Mix approach? (If so, what ratio?)
  - Geographic preference for creators (South India, diaspora, pan-India)?
- Current assumption: Not specified; assumes mixed approach
- Impact: Creator recruitment strategy, platform authenticity, content quality, marketing messaging
- Related file: `03-target-market.md`

**Your Answer:**

```
50-50 mix of established influencers and new talents. Established for quick initial user growth, new talents for authentic long-term community engagement. Geographic focus: South India primarily.
```

---

### Q3.5: Age Range Flexibility

**Question**: Is the 18-35 age range a hard boundary or flexible?

- Sub-questions:
  - Should 16-17 year olds be supported?
  - Should 36+ year olds be supported?
  - Different features/restrictions by age group?
  - Parental consent requirements for under-18?
- Current assumption: 18-35 is primary focus
- Impact: User acquisition ceiling, regulatory compliance, content moderation complexity, legal liability
- Related file: `03-target-market.md`

**Your Answer:**

```
Age range extended to 18-45 (not 18-35). Focus remains on 18-35, but open to 36-45 age group for broader market reach. No under-18 support.
```

---

### Q3.6: Regional Expansion Pace

**Question**: How many new regions should be added per quarter?

- Sub-questions:
  - After Tamil Nadu stabilizes, add 1 region/quarter (Q2: Telugu, Q3: Kannada)?
  - Or faster expansion (2 regions/quarter)?
  - Minimum user threshold in one region before expanding?
  - Parallel expansion or sequential?
- Current assumption: Sequential expansion (Tamil → Telugu → Kannada) but pace unclear
- Impact: Resource allocation, ops scaling, market timing, cash burn rate
- Related file: `03-target-market.md`

**Your Answer:**

```
Sequential expansion: 1 new region per quarter. After TN stabilizes → Telugu (Q2) → Kannada & Malayalam (Q3). Allows focus on quality and stability.
```

---

### Q3.7: Diaspora as Revenue Driver

**Question**: Is Indian diaspora a secondary or primary growth driver?

- Sub-questions:
  - What % of Year 1 users expected from diaspora?
  - Should diaspora have separate marketing/onboarding?
  - Time zone considerations for diaspora engagement?
  - Special features for diaspora (e.g., group call discounts for families)?
- Current assumption: Listed as secondary market
- Impact: Marketing messaging, feature prioritization, timezone infrastructure, monetization strategy
- Related file: `03-target-market.md`

**Your Answer:**

```
Diaspora: Secondary but important market. Target 10-20% of Year 1 users from diaspora. No separate features, but timezone-aware marketing for peak engagement times.
```

---

## Section 4: Business Metrics & Goals (5 Questions)

### Q4.1: Seasonality Adjustments

**Question**: Do the GMV targets account for seasonal variations?

- Sub-questions:
  - Peak seasons: Festival months (Diwali, Tamil New Year), college admission season?
  - Low seasons: Exams, migration seasons, monsoon?
  - Expected GMV variance: ±20%, ±30%, ±50%?
  - How should targets be adjusted for predictable seasonality?
- Current assumption: Goals presented as flat monthly targets
- Impact: Cash flow planning, inventory management, creator payout scheduling, performance tracking
- Related file: `04-business-goals.md`

**Your Answer:**

```
No adjustments for seasonality. Targets are flat monthly GMV. Plan for flexible payout scheduling during peak seasons (Diwali, Tamil New Year).
```

---

### Q4.2: User Retention Targets

**Question**: What's the expected DAU/MAU ratio at each phase?

- Sub-questions:
  - 6-month phase: Target DAU/MAU ratio?
  - 12-month phase: Target DAU/MAU ratio?
  - 24-month phase: Target DAU/MAU ratio?
  - What about Day-7, Day-30, Day-90 retention rates?
  - Target churn rate (monthly)?
- Current assumption: Retention metrics not specified
- Impact: User acquisition cost calculations, LTV calculations, feature roadmap prioritization, engagement targets
- Related file: `04-business-goals.md`

**Your Answer:**

```
Retention targets: Day-7 retention 40%, Day-30 retention 20%, Day-90 retention 10%. DAU/MAU ratio: 30% average. Monthly churn: ~20% acceptable.
```

---

### Q4.3: Creator Supply Requirements

**Question**: How many active creators are needed to support these user numbers?

- Sub-questions:
  - 6-month: 50K users → how many creators needed?
  - 12-month: 200K users → how many creators needed?
  - 24-month: 1M users → how many creators needed?
  - User-to-creator ratio target? (e.g., 10:1, 20:1?)
  - Revenue distribution across creator tiers?
- Current assumption: Not specified
- Impact: Creator acquisition roadmap, creator marketplace balance, feature prioritization, moderation complexity
- Related file: `04-business-goals.md`

**Your Answer:**

```
User-to-creator ratio: 20:1. 6-month: 2.5K creators for 50K users. 12-month: 10K creators for 200K users. 24-month: 50K creators for 1M users.
```

---

### Q4.4: Geographic Sequencing

**Question**: Is the 6-month goal Tamil Nadu only, or multi-region?

- Sub-questions:
  - 6-month (50K users): Tamil Nadu only? Or include AP/Karnataka?
  - 12-month (200K users): Multi-region? Which regions?
  - 24-month (1M users): All South Indian regions + diaspora?
  - Impact of geographic sequencing on growth rate?
- Current assumption: Assumed progressive regional rollout
- Impact: Resource allocation, hiring timeline, marketing budget distribution, server infrastructure placement
- Related file: `04-business-goals.md`

**Your Answer:**

```
6-month: Tamil Nadu only (50K users). 12-month: Tamil Nadu + Andhra Pradesh (200K users). 24-month: All South India regions (1M users).
```

---

### Q4.5: Break-Even Timeline

**Question**: When does the product aim for profitability?

- Sub-questions:
  - Profitability target: Year 2? Year 3? Year 5?
  - Break-even point (total investment recovery)?
  - Expected monthly burn rate in Year 1?
  - Year 2 profitability assumptions (GMV plateau, cost optimization, etc.)?
- Current assumption: Not specified; assumed venture-backed growth model
- Impact: Funding requirements, cost structure optimization, feature prioritization, investor expectations
- Related file: `04-business-goals.md`

**Your Answer:**

```
Break-even: Year 3. Year 1 moderate burn rate (invest in user acquisition & infrastructure). Year 2 continued growth with optimization. Year 3 profitability through scale + creator base growth.
```

---

## Summary Table

| Question # | Category             | Status    | Priority |
| ---------- | -------------------- | --------- | -------- |
| Q1.1-Q1.5  | Product Definition   | Completed | HIGH     |
| Q2.1-Q2.5  | Competitive Strategy | Completed | HIGH     |
| Q3.1-Q3.7  | Market Understanding | Completed | MEDIUM   |
| Q4.1-Q4.5  | Business Metrics     | Completed | HIGH     |

---

## Submission Format

Please provide answers in one of these formats:

1. **Fill in this file** with your answers
2. **Provide a separate document** with all answers
3. **Answer via Slack/Email** and I'll update the files

**Deadline:** Please provide answers within 3-5 business days for timeline efficiency.

---

## Next Steps After Clarification

Once you provide answers:

1. Update all four executive summary files with clarified assumptions
2. Create a final "Executive Summary - Validated" document
3. Cross-reference these decisions with downstream sections (Feature Specs, Tech Architecture, etc.)
4. Proceed to breakdown remaining 14 PRD sections
5. Flag any contradictions or dependencies across sections

# Section 14: Cost Estimation - Questions & Answers

**Section**: Cost Estimation
**Section ID**: 14-cost-estimation
**Generated**: 2026-01-24
**Total Questions**: 9

---

## Core Questions

### Question 1: Team Costs vs Shadowmarket Team

**Question**:
The PRD shows ₹8.7L team hiring costs over 6 months (Flutter Developer ₹60K/month, Backend Developer ₹50K/month, UI/UX Designer ₹20K/month part-time, QA Tester ₹15K/month part-time). However, Section 13 confirmed you have an existing "shadowmarket agent team". Should the budget reflect:

**A)** Remove team costs entirely (shadowmarket team is internal, no incremental hiring cost)
**B)** Keep opportunity cost estimate (₹8-10L allocation cost to reflect true project investment)
**C)** Replace with contractor budget (shadowmarket team needs external help for specialized tasks)

**User Answer**: **A) Remove team costs (shadowmarket team is internal)**

**Rationale**:
The user confirmed shadowmarket agent team is an internal resource already on payroll. There is no incremental hiring cost for this project, so the ₹8.7L team budget should be removed from the cost estimation.

**Impact on Requirements**:
- **REQ-COE-001**: Updated budget framework removes ₹8.7L team costs, reducing total from ₹17L to ₹9.5L (44% reduction)
- **Budget Calculation**: Total budget now ₹9,47,100 (infrastructure + services + legal + translation + marketing + contingency)

**Trade-offs Accepted**:
- **Pro**: Significant cost reduction (₹8.7L savings) makes project more affordable
- **Pro**: Internal team has existing context and established velocity
- **Con**: Opportunity cost not reflected in budget (team could work on other projects)
- **Con**: If shadowmarket team is at capacity, project timeline may extend

**Follow-up Assumption**:
Shadowmarket team has necessary skills (Flutter, Python/FastAPI, PostgreSQL, DevOps) and capacity for 5-month commitment. If upskilling or additional resources needed, budget may need adjustment.

---

### Question 2: Platform Share Revenue Model

**Question**:
The PRD's revenue projections use 38.5% platform share (61.5% creator share), projecting ₹6,160-₹6,93,000 platform revenue in Months 1-6, with break-even at Month 5-6.

However, all previous sections approved a tiered creator-friendly model:
- **75% creator / 25% platform** - New creators (0-₹50K/month earnings)
- **80% creator / 20% platform** - Mid-tier (₹50K-₹2L/month)
- **85% creator / 15% platform** - Top-tier (₹2L+/month)

This conflicts with the PRD's 38.5% platform commission. Which revenue model should the budget and break-even analysis reflect?

**A)** Use 15-25% platform share (align with approved tiered model - creator-friendly but longer break-even)
**B)** Revert to 38.5% platform share (higher revenue, faster break-even, but conflicts with legal agreements)
**C)** Hybrid model: 38.5% Year 1, transition to 75-85% creator share in Year 2

**User Answer**: **A) Use 15-25% platform share (Recommended)**

**Rationale**:
User confirmed alignment with the approved tiered creator revenue model from Sections 4, 10, and 12. This ensures consistency across:
- Terms of Service (Section 12, REQ-LC-002)
- Creator Agreement (Section 12, REQ-LC-004)
- API implementation (Section 8, REQ-API-019: commission calculation)
- Database schema (Section 7: creator_stats.commission_tier)

**Impact on Requirements**:
- **REQ-COE-006**: Revenue projections updated to use weighted average 23% platform share (75-85% creator)
- **REQ-COE-007**: Break-even timeline extended from Month 5-6 to **Month 8-10**
- **Revenue Reduction**: Month 6 platform revenue drops from ₹6.93L (38.5%) to ₹4.14L (23%) - 40% reduction

**Updated Revenue Projections**:
| Month | GMV | PRD Platform Revenue (38.5%) | Updated Platform Revenue (23%) | Variance |
|-------|-----|----------------------------|-------------------------------|----------|
| 1 | ₹16,000 | ₹6,160 | ₹3,680 | -40% |
| 2 | ₹60,000 | ₹23,100 | ₹13,800 | -40% |
| 3 | ₹1,92,000 | ₹73,920 | ₹44,160 | -40% |
| 4 | ₹4,20,000 | ₹1,61,700 | ₹96,600 | -40% |
| 5 | ₹9,60,000 | ₹3,69,600 | ₹2,20,800 | -40% |
| 6 | ₹18,00,000 | ₹6,93,000 | ₹4,14,000 | -40% |
| **Cumulative** | ₹34,48,000 | **₹13,27,480** | **₹7,93,040** | **-40%** |

**Break-even Analysis**:
- **Costs**: ₹9,47,100 (5-month development)
- **Revenue by Month 6**: ₹7,93,040
- **Remaining Deficit**: ₹1,54,060
- **Break-even**: Month 7-8 (need additional ₹1.5L revenue)

**Trade-offs Accepted**:
- **Con**: 2-3 month delay in break-even (Month 8-10 vs PRD's Month 5-6)
- **Con**: Lower short-term revenue (₹7.9L vs ₹13.3L in first 6 months)
- **Pro**: Creator loyalty and retention (competitive with other platforms)
- **Pro**: Legal consistency (ToS, Creator Agreement aligned)
- **Pro**: Sustainable ecosystem (creators earn more, stay longer)

---

### Question 3: Budget Timeline Adjustment

**Question**:
The PRD shows a 6-month budget (₹17.08L), but Section 13 approved a 16-20 week (4-5 month) MVP timeline. Additionally, the approved scope includes services not in the PRD budget:
- **Translation**: ₹50-80K for professional localization (4 languages, 500 strings)
- **ClearTax API**: ₹15-20K/year for TDS automation (Section 12)
- **AWS Rekognition**: ₹10K/month for AI age verification (Section 5)
- **Video call costs**: Agora budget should increase from ₹20K to ₹40K/month in Months 4-5 (Section 5 video support)

Should the budget be:

**A)** Update to 5-month budget with full scope (add translation, ClearTax, Rekognition, higher Agora costs)
**B)** Keep 6-month budget as conservative estimate (provides 1-month buffer beyond Week 20 launch)
**C)** Reduce to 4-month lean budget (16-week minimum timeline, removes contingency and marketing)

**User Answer**: **A) Update to 5-month budget with full scope (Recommended)**

**Rationale**:
Aligning budget with Section 13's 20-week (5-month) timeline and including all approved services ensures accurate cost estimation. The 5-month timeline covers:
- Months 1-4: Development (Weeks 1-16)
- Month 5: Beta testing, launch preparation, and launch (Weeks 17-20)

**Impact on Requirements**:
- **REQ-COE-001**: Budget timeline updated to 5 months (from PRD's 6 months)
- **REQ-COE-003**: ClearTax (₹7,500) and AWS Rekognition (₹50,000) added to services budget
- **REQ-COE-004**: Translation budget added (₹60,000 one-time)
- **REQ-COE-005**: Agora budget increased to ₹1,40,000 (₹20K x 3 months + ₹40K x 2 months)

**Budget Adjustment**:
| Item | PRD (6 months) | Updated (5 months) | Variance | Reason |
|------|---------------|-------------------|----------|--------|
| Infrastructure | ₹2,88,000 | ₹4,05,000 | +₹1,17,000 | Dev tools (₹1.25L) + Agora video (+₹20K) |
| Services | ₹1,02,000 | ₹1,42,500 | +₹40,500 | ClearTax (₹7.5K) + Rekognition (₹50K) |
| Translation | ₹0 | ₹60,000 | +₹60,000 | Professional translation (new) |
| Marketing | ₹3,40,000 (4 months) | ₹2,00,000 (1 month) | -₹1,40,000 | Concentrated in Week 17-20 |
| **Subtotal** | ₹15,53,500 | ₹8,61,000 | -₹6,92,500 | Team costs removed (-₹8.7L) |

**Trade-offs Accepted**:
- **Pro**: Accurate 5-month budget aligned with realistic timeline
- **Pro**: All approved services included (no surprises later)
- **Pro**: Conservative estimate (Month 5 includes full launch costs)
- **Con**: No buffer month beyond Week 20 (6-month budget would provide cushion)

**Contingency Coverage**:
10% contingency (₹86K) provides buffer for:
- API cost overruns (Agora, Rekognition usage higher than expected)
- Infrastructure scaling (AWS costs increase with beta user load)
- Translation revisions (additional native speaker feedback iterations)

---

## Follow-up Questions

### Question 4: Development Tooling Budget

**Question**:
Since team costs are removed (shadowmarket team is internal), should we budget for additional operational costs like development tools, licenses, or infrastructure for the dev team?

**Context**:
Even though the team is internal, the project may require:
- **Project Management**: Jira or Linear (₹5K/month for team seats)
- **Design Collaboration**: Figma Professional (₹3K/month)
- **Version Control**: GitHub Advanced Security (₹2K/month)
- **Error Tracking**: Sentry (₹5K/month)
- **Dev/Staging Environments**: Dedicated AWS environments (₹10K/month)

**Options**:
**A)** Add ₹20-30K/month for dev tools (Jira, Figma, GitHub, Sentry, dev/staging AWS)
**B)** No additional tooling budget (shadowmarket team has existing tools)
**C)** Include in infrastructure costs (merge into existing budget)

**User Answer**: **A) Add ₹20-30K for dev tools (Recommended)**

**Rationale**:
Even with an internal team, the LIVVLY project requires dedicated tools and environments:
- **Sprint Tracking**: Section 13 (REQ-IR-005) requires weekly milestone tracking and demo days - needs Jira/Linear
- **Design Handoff**: UI/UX collaboration requires Figma for design system and component library
- **Code Security**: GitHub Advanced Security for vulnerability scanning and dependency checks
- **Error Monitoring**: Sentry for production error tracking and performance monitoring
- **Isolated Environments**: Separate dev and staging environments to avoid impacting shadowmarket team's other projects

**Budget Allocation**: ₹25,000/month x 5 months = **₹1,25,000**

**Breakdown**:
| Tool | Monthly | 5-Month Total | Purpose |
|------|---------|---------------|---------|
| Jira/Linear | ₹5,000 | ₹25,000 | Sprint planning, burndown tracking (REQ-IR-009) |
| Figma Pro | ₹3,000 | ₹15,000 | Design collaboration, component library |
| GitHub Advanced | ₹2,000 | ₹10,000 | Security scanning, advanced CI/CD |
| Sentry | ₹5,000 | ₹25,000 | Error tracking, performance monitoring (REQ-TA-015) |
| Dev/Staging AWS | ₹10,000 | ₹50,000 | Isolated dev and staging environments |
| **Total** | **₹25,000** | **₹1,25,000** | |

**Impact on Requirements**:
- **REQ-COE-002**: Infrastructure budget updated to include ₹1,25,000 dev tools
- **Total Infrastructure**: ₹4,05,000 (AWS + Agora + Firebase + Redis + CDN + Dev Tools)

**Trade-offs**:
- **Cost**: +₹1.25L to budget (now ₹9.47L total)
- **Benefit**: Proper project management and monitoring infrastructure
- **Benefit**: Isolated environments prevent impacting other shadowmarket projects

---

### Question 5: Break-Even Timeline Expectations

**Question**:
With 15-25% platform share (vs PRD's 38.5%), revenue will be lower and break-even will extend beyond the PRD's Month 6 estimate. What's the acceptable runway before profitability?

**Analysis**:
- **Updated Revenue**: ₹7.93L by Month 6 (vs ₹13.27L in PRD)
- **Costs**: ₹9.47L (5-month development)
- **Deficit at Month 6**: ₹1.54L
- **Additional Revenue Needed**: ₹1.5-2L (approximately 1-2 months at Month 6 revenue rate)

**Options**:
**A)** 8-10 months acceptable (longer runway for creator-friendly model, sustainable growth)
**B)** Must break-even by Month 6 (aggressive user acquisition or higher monetization required)
**C)** 12+ months acceptable (focus on growth over profitability)

**User Answer**: **A) 8-10 months acceptable (Recommended)**

**Rationale**:
The user accepts a longer runway to profitability in exchange for building a sustainable, creator-friendly ecosystem. This strategic decision prioritizes:
- **Creator Loyalty**: 75-85% creator share is industry-leading (competitive with Patreon, OnlyFans)
- **Long-term Retention**: Creators who earn more stay longer, reducing churn
- **Platform Reputation**: "Creator-first" positioning attracts quality content creators
- **Sustainable Growth**: Lower short-term revenue, but healthier unit economics

**Break-Even Projection**:
| Month | Platform Revenue | Cumulative Revenue | Cumulative Costs | Net Position |
|-------|-----------------|-------------------|------------------|--------------|
| 1-5 | ₹3.78L | ₹3.78L | ₹9.47L | -₹5.69L |
| 6 | ₹4.14L | ₹7.92L | ₹9.47L | -₹1.55L |
| 7 | ₹5.00L (est) | ₹12.92L | ₹9.97L | +₹2.95L |
| 8 | ₹6.00L (est) | ₹18.92L | ₹10.47L | +₹8.45L |

**Assumptions for Months 7-8**:
- User growth continues: 75K (Month 7), 100K (Month 8)
- Paying user %: 8-10% (increases with engagement)
- Average spend: ₹500-600 (higher as users get comfortable)
- Platform share: 23% weighted average

**Funding Requirements**:
- **Initial Investment**: ₹10L for 5-month development
- **Runway Buffer**: ₹2-3L for Months 6-8 operations before break-even
- **Total Funding Needed**: **₹12-13L** to reach profitability

**Impact on Requirements**:
- **REQ-COE-007**: Break-even timeline set to 8-10 months (accepted by stakeholders)
- **Funding Ask**: ₹12-13L seed funding to cover development + runway to profitability

**Risk Mitigation**:
If break-even extends beyond Month 10:
1. **Revenue Optimization**: Introduce VIP subscriptions (₹299/799/month) in Month 6
2. **Cost Reduction**: Negotiate AWS reserved instances (20-30% savings)
3. **Bridge Funding**: Small fundraise (₹5-7L) to extend runway to Month 12

---

### Question 6: Marketing Budget Timing

**Question**:
The PRD shows marketing starting Month 3 and running for 4 months (₹85K/month = ₹3.4L total). With the 5-month MVP timeline, when should marketing budget be allocated?

**Consideration**:
- **Week 12 (end of Month 3)**: Phase 1 completion milestone - early beta recruitment
- **Week 17-20 (Month 5)**: Beta testing and launch phase - primary marketing push

**Options**:
**A)** Start Week 17 for launch (concentrate budget on beta + launch phase)
**B)** Start Week 12 for beta recruitment (allocate ₹50K early for quality beta testers)
**C)** Split budget: ₹50K Week 12 (beta), ₹2.5L Week 17 (launch)

**User Answer**: **A) Start Week 17 for launch (Recommended)**

**Rationale**:
User selected concentrated marketing push at launch phase (Weeks 17-20) rather than spreading budget earlier. This approach:
- **Maximizes Launch Impact**: ₹2L concentrated in 1 month > ₹85K spread over 4 months
- **Product Maturity**: Wait until product is polished (post-Phase 2 enhancements) before heavy promotion
- **Beta Recruitment**: Week 17 ads also serve beta recruitment (100-200 users)
- **Cost Efficiency**: Organic beta recruitment (employee network, early adopters) saves ₹50K

**Budget Allocation**: ₹2,00,000 (Month 5, Weeks 17-20)

**Campaign Breakdown**:
| Week | Campaign Focus | Budget | Expected Outcome |
|------|---------------|--------|------------------|
| 17 | Beta User Recruitment | ₹50,000 | 200 signups (50 creators, 150 users) |
| 18 | Influencer Seeding | ₹30,000 | 5-10 influencer partnerships, content creation |
| 19 | Pre-Launch PR | ₹30,000 | Press releases, media outreach, ASO optimization |
| 20 | Launch Campaign | ₹90,000 | App install ads (Google, Facebook), 2,000 installs |

**Channel Allocation**:
- Google Ads (UAC): ₹60,000 (30%)
- Facebook/Instagram Ads: ₹50,000 (25%)
- Influencer Marketing: ₹60,000 (30%)
- ASO & PR: ₹30,000 (15%)

**Impact on Requirements**:
- **REQ-COE-008**: Marketing budget set to ₹2L for Week 17-20 launch phase
- **Total Budget**: ₹9.47L (vs PRD's ₹17L) - marketing reduction from ₹3.4L to ₹2L saves ₹1.4L

**Trade-offs**:
- **Pro**: Concentrated budget creates bigger splash at launch
- **Pro**: Cost savings (₹1.4L) vs PRD's 4-month spread
- **Con**: No early marketing (Week 12) means smaller beta pool (rely on organic recruitment)
- **Con**: All marketing eggs in one basket (Week 17-20) - high risk if launch delayed

**Post-Launch Marketing**:
This budget covers only MVP launch (Month 5). Months 6+ require separate marketing budget:
- **Month 6-12**: ₹1-1.5L/month for sustained user acquisition
- **Focus**: Paid UA (Google Ads, Meta Ads), influencer campaigns, referral programs

---

## Gap Analysis Questions

### Question 7: Missing API Services Budget

**Question**:
The PRD budget doesn't include costs for services approved in previous sections:
1. **ClearTax API** for TDS automation (Section 12, REQ-LC-006): ~₹15-20K/year
2. **AWS Rekognition** for AI age verification (Section 5, REQ-FS-024): ~₹10K/month

Should these be added to the budget?

**Context**:
- **ClearTax API**: Automates quarterly 26Q TDS filing and annual 16A certificate generation. Cost: ₹1,500/month (₹18K/year) vs manual filing cost ₹40K/year (saves ₹22K/year)
- **AWS Rekognition**: Face detection API for age estimation during signup. Cost: ₹10K/month (assumes 4,000 signups/month @ ₹2.50 per detection)

**Options**:
**A)** Add both services (₹57,500 total for 5 months)
**B)** Add only ClearTax (defer AI age verification to post-launch)
**C)** Defer both to post-launch (launch with manual TDS and self-declaration age verification)

**User Answer**: **A) Add both services (Recommended)**

**Rationale**:
Both services are legally/strategically critical and should be included from MVP:

**ClearTax API**:
- **Legal Requirement**: Section 12 (REQ-LC-006) mandates automated TDS filing for compliance
- **Cost Savings**: ₹18K API cost vs ₹40K manual filing = ₹22K/year savings
- **Risk Mitigation**: Automated filing reduces compliance errors and penalties
- **Timeline**: Must be operational by Month 3 (first quarterly filing if creators cross ₹30K threshold)

**AWS Rekognition**:
- **Platform Safety**: Section 5 (REQ-FS-024) approved AI-enhanced age verification to reduce liability
- **User Experience**: Instant face-based age check (self-declaration + AI) is faster than manual document verification
- **Competitive Feature**: Other dating platforms (Tinder, Bumble) use AI age verification
- **Timeline**: Integrated in signup flow from Week 15 (Phase 2)

**Budget Impact**: +₹57,500

| Service | Monthly Cost | 5-Month Total | Notes |
|---------|-------------|---------------|-------|
| ClearTax API | ₹1,500 | ₹7,500 | Start Month 1, operational by Month 3 for Q4 filing |
| AWS Rekognition | ₹10,000 | ₹50,000 | Start Month 4 (Week 13), 4K signups/month @ ₹2.50 |
| **Total** | ₹11,500 | **₹57,500** | |

**Impact on Requirements**:
- **REQ-COE-003**: API services budget updated to ₹1,42,500 (was ₹85,000 in PRD)
- **Services Breakdown**: SMS (₹50K) + Karza (₹25K) + n8n (₹10K) + ClearTax (₹7.5K) + Rekognition (₹50K) = ₹1,42,500

**Trade-offs**:
- **Cost**: +₹57.5K to budget (now ₹9.47L)
- **Benefit**: Legal compliance (ClearTax) and platform safety (Rekognition) from Day 1
- **Alternative**: Defer to post-launch would save ₹57.5K but increase legal/safety risk

---

### Question 8: Translation Budget for 5 Languages

**Question**:
Section 13 (REQ-IR-003) approved "All 5 languages at launch" strategy (Tamil, Telugu, Kannada, Hindi, English). The PRD budget doesn't include professional translation costs. Should we add translation budget?

**Estimation**:
- **String Count**: Approximately 500 UI strings (buttons, labels, error messages, onboarding flow, legal disclosures, help text)
- **Languages**: 4 target languages (Tamil, Telugu, Kannada, Hindi) - English is source language
- **Cost per Language**: ₹15,000 for professional localization (₹30 per string)
- **Total**: ₹60,000

**Options**:
**A)** Add ₹60K professional translation budget (native speakers, cultural adaptation)
**B)** Use Google Translate + manual review (₹20K - machine translation with native review)
**C)** English + 1 language at launch (₹15K - defer other languages post-launch)

**User Answer**: **A) Add ₹60K translation budget (Recommended)**

**Rationale**:
Professional translation is critical for user trust and market positioning:

**Quality Requirement**:
- **Cultural Appropriateness**: Dating/relationship terminology is culturally sensitive (Tamil "காதல்", Telugu "ప్రేమ", Kannada "ಪ್ರೀತಿ" have nuanced meanings)
- **Legal Precision**: Terms of Service, Privacy Policy, Creator Agreement must be legally accurate in all languages
- **User Trust**: Poor translation erodes trust in a dating platform (privacy-sensitive product)
- **Competitive Positioning**: Section 2 targets "South India's #1 Voice Dating Platform" - requires native-quality localization

**Professional vs Machine Translation**:
| Approach | Cost | Quality | Risk |
|----------|------|---------|------|
| Professional (Native speakers) | ₹60,000 | High | Low (culturally appropriate, legally accurate) |
| Google Translate + Review | ₹20,000 | Medium | Medium (may miss cultural nuances, legal errors) |
| English + 1 language | ₹15,000 | High for 1 | High (market fragmentation, misses 80% of target users) |

**Deliverables**:
- Translated JSON files for Flutter i18n framework
- Glossary for domain-specific terms ("coins", "live room", "voice bio", "creator", "payout")
- Native speaker review and QA feedback (Week 17-18 beta)
- Ongoing translation support for new strings (Weeks 2-20)

**Budget Allocation**: ₹60,000 (one-time)

**Breakdown**:
| Language | String Count | Cost per String | Total Cost |
|----------|-------------|----------------|------------|
| Tamil | 500 | ₹30 | ₹15,000 |
| Telugu | 500 | ₹30 | ₹15,000 |
| Kannada | 500 | ₹30 | ₹15,000 |
| Hindi | 500 | ₹30 | ₹15,000 |
| **Total** | | | **₹60,000** |

**Impact on Requirements**:
- **REQ-COE-004**: Translation budget added as one-time cost (₹60,000)
- **Total One-Time Costs**: Legal (₹53,500) + Translation (₹60,000) = ₹1,13,500

**Timeline**:
- **Week 2**: Engage professional translators (Tamil, Telugu, Kannada, Hindi)
- **Week 4**: Initial 300-string batch completed (core UI strings)
- **Week 8**: Second batch (200 strings) - error messages, help text
- **Week 16**: Final batch - legal documents, onboarding flow
- **Week 17-18**: Native speaker QA during beta testing

**Trade-offs**:
- **Cost**: +₹60K to budget (now ₹9.47L total)
- **Quality**: High-quality localization builds trust and brand reputation
- **Market Coverage**: Full South India addressable market (80M+ Tamil/Telugu/Kannada speakers)

---

### Question 9: Agora Video Call Budget Increase

**Question**:
The PRD shows ₹20K/month Agora budget for audio-only calls (₹1.2L for 6 months). However, Section 5 (REQ-FS-015) approved video calls in Phase 2 (Weeks 13-16). Video calling costs 3-4x more than audio-only due to higher bandwidth requirements.

Should we increase the Agora budget to account for video support?

**Cost Comparison**:
- **Audio Call**: ₹1.50 per minute (Agora pricing)
- **Video Call (SD 480p)**: ₹3.50 per minute
- **Video Call (HD 720p)**: ₹5.00 per minute

**Options**:
**A)** Increase to ₹40K/month starting Month 4 (₹20K Months 1-3, ₹40K Months 4-5)
**B)** Keep ₹20K/month, defer video to post-launch (audio-only MVP)
**C)** Increase to ₹50K/month all 5 months (conservative estimate)

**User Answer**: **A) Increase to ₹40K/month starting Month 4 (Recommended)**

**Rationale**:
Align Agora budget with Section 5's phased video rollout:
- **Phase 1 (Weeks 1-12 = Months 1-3)**: Audio calls only - ₹20K/month adequate
- **Phase 2 (Weeks 13-16 = Month 4)**: Video calls introduced - costs increase
- **Phase 3 (Weeks 17-20 = Month 5)**: Beta testing with video - higher usage

**Updated Budget**: ₹1,40,000 (5 months)

| Period | Service Type | Monthly Cost | Total | Notes |
|--------|-------------|-------------|-------|-------|
| Months 1-3 | Audio only | ₹20,000 | ₹60,000 | 8,000 min/month @ ₹1.50 = ₹12K, buffer ₹8K |
| Month 4 | Audio + Video (beta) | ₹40,000 | ₹40,000 | 6K audio min (₹9K) + 5K video min (₹25K) + buffer ₹6K |
| Month 5 | Audio + Video (launch) | ₹40,000 | ₹40,000 | Same as Month 4, beta testing increases usage |
| **Total** | | | **₹1,40,000** | +₹20K vs PRD (₹1.2L for 6 months) |

**Assumptions**:
- **Video Adoption**: 30-40% of calls switch to video in Months 4-5 (users experiment with new feature)
- **Video Quality**: HD 720p @ ₹5/min (premium positioning)
- **Usage Growth**: Month 4: 11,000 total minutes, Month 5: 12,000 total minutes

**Impact on Requirements**:
- **REQ-COE-005**: Agora budget increased to ₹1,40,000 (₹20K x 3 + ₹40K x 2)
- **Infrastructure Total**: ₹4,05,000 (includes ₹1.4L Agora, ₹75K AWS, ₹25K Firebase, etc.)

**Trade-offs**:
- **Cost**: +₹20K vs PRD budget (₹1.4L vs ₹1.2L)
- **Feature Completeness**: Video support in MVP as approved in Section 5
- **User Experience**: Video calling is table-stakes for modern dating apps (competitive parity)
- **Risk**: If video adoption is lower (20%), Month 4-5 costs may be ₹30K instead of ₹40K (₹10K savings)

**Monitoring**:
- Track audio vs video minute usage in Agora dashboard
- Alert if cost per minute exceeds ₹6 (indicates wastage or quality issues)
- Optimize video bitrate if costs spike (720p → 480p reduces cost by 30%)

---

## Summary of Answers

| Question # | Topic | User Decision | Budget Impact |
|-----------|-------|---------------|---------------|
| Q1 | Team Costs | Remove (shadowmarket team internal) | -₹8,70,000 |
| Q2 | Platform Share | 15-25% (creator-friendly) | Revenue -40%, break-even Month 8-10 |
| Q3 | Budget Timeline | 5-month budget with full scope | Aligned with Section 13 |
| Q4 | Dev Tools | Add ₹25K/month dev tools | +₹1,25,000 |
| Q5 | Break-Even | 8-10 months acceptable | Funding: ₹12-13L |
| Q6 | Marketing Timing | Week 17-20 launch phase | ₹2L (vs ₹3.4L spread) |
| Q7 | API Services | Add ClearTax + Rekognition | +₹57,500 |
| Q8 | Translation | Add ₹60K professional translation | +₹60,000 |
| Q9 | Agora Video | Increase to ₹40K in Months 4-5 | +₹20,000 |

---

## Overall Impact Assessment

### Budget Transformation

**PRD Budget (6 months)**: ₹17,08,850
**Updated Budget (5 months)**: ₹9,47,100

**Net Change**: -₹7,61,750 (-44%)

**Major Changes**:
1. **Team Costs Removed**: -₹8,70,000 (shadowmarket team is internal)
2. **Infrastructure Increased**: +₹1,17,000 (dev tools + Agora video)
3. **Services Increased**: +₹40,500 (ClearTax + AWS Rekognition)
4. **Translation Added**: +₹60,000 (professional localization)
5. **Marketing Reduced**: -₹1,40,000 (concentrated in launch phase vs 4-month spread)
6. **Timeline Reduced**: 5 months vs 6 months (Month 5 includes full launch)

### Revenue Model Impact

**Platform Share**:
- PRD: 38.5% platform (61.5% creator)
- Updated: 23% weighted average (15-25% platform, 75-85% creator tiered)
- Revenue Impact: -40% in Months 1-6

**Break-Even**:
- PRD: Month 5-6
- Updated: Month 8-10
- Funding Requirement: ₹12-13L (₹9.5L development + ₹2.5L runway)

### Strategic Decisions

1. **Creator-First Model**: Prioritize long-term ecosystem health over short-term revenue
2. **Quality Localization**: Professional translation for all 5 languages at launch
3. **Comprehensive Tooling**: Invest in dev tools for proper project management
4. **Launch-Focused Marketing**: Concentrate budget for maximum launch impact
5. **Full-Feature MVP**: Include video, AI verification, TDS automation from launch

---

**Generated by**: PRD Breakdown Skill
**Section Status**: Completed
**Total Questions Answered**: 9
**Next Section**: 15-success-metrics

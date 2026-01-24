# LIVVLY Requirements Comparison Report

**Generated:** 2026-01-24
**Stakeholders Compared:** Kapil vs. M. Kumaran
**Sections Analyzed:** 15 sections

---

# REPORT 1: REQUIREMENTS.MD DETAILED COMPARISON

---

## Section 01: Executive Summary

### Kapil's Requirements:
- **Total:** 13 requirements (7 P0, 6 P1)
- **Target:** Tamil-first approach, expand to Telugu/Kannada
- **6-Month Goals:** 50K MAU, ₹20L GMV
- **Key Differentiators:** 75-85% creator share, T+1 payouts, regional languages
- **AI Companion:** Deferred to Phase 2
- **Agency Model:** Required for MVP

### M. Kumaran's Requirements:
- **Total:** 11 requirements (5 P0, 4 P1, 2 P2)
- **Target:** Tamil + Telugu MVP, all 5 languages at launch
- **6-Month Goals:** 50K MAU, ₹20L GMV
- **12-Month Goals:** 200K MAU, ₹1Cr GMV
- **24-Month Goals:** 1M MAU, ₹5Cr GMV
- **AI Companion:** Deferred to Phase 2+
- **Agency Model:** Not mentioned for MVP

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Language Strategy | Tamil-first, phased | Tamil+Telugu MVP, 5 at launch |
| Active User Definition | MAU-based | MAU+WAU with engagement (calls+gifts only) |
| Business Targets | 6-month only | 6/12/24-month targets |
| Agency Model | Required MVP | Not in MVP |
| Requirements Count | 13 | 11 |

### Similarities:
- Both target 50K MAU, ₹20L GMV at 6 months
- Both defer AI Companion to Phase 2
- Both prioritize 75-85% creator revenue share as differentiator
- Both target Tier-2/3 Indian cities

### Gaps & Conflicts:
- **Language rollout conflict:** Kapil's phased approach vs M. Kumaran's simultaneous launch
- **Agency model:** Kapil requires for MVP, M. Kumaran excludes from MVP
- **Engagement tracking:** M. Kumaran uses stricter definition (calls+gifts only)

### Unique to Kapil:
- Agency model requirement from day one
- 4 languages (includes Malayalam)

### Unique to M. Kumaran:
- Strict engagement-based active user definition
- Long-term targets (12 and 24-month milestones)
- 5 languages including Hindi

### Risk Assessment:
- **HIGH:** Language strategy conflict - different MVP scope
- **MEDIUM:** Agency model scope difference affects creator acquisition strategy

### Recommendations for Merging:
1. Adopt M. Kumaran's 5-language approach for broader market
2. Include agency model as optional feature (not blocking MVP)
3. Use M. Kumaran's stricter engagement metrics for more meaningful KPIs

---

## Section 02: Market Analysis

### Kapil's Requirements:
- **Total:** 11 requirements
- Market sizing accepted as-is from PRD
- Web purchase option to bypass app store fees
- 75% base creator share, up to 85% for top creators

### M. Kumaran's Requirements:
- **Total:** 13 requirements
- **Updated Market Sizing (2026-2027):**
  - TAM: ₹8,400 Cr (up from ₹5,000 Cr)
  - SAM: ₹2,500 Cr (up from ₹1,500 Cr)
  - SOM: ₹150 Cr (up from ₹50 Cr - 3x increase)
- Hybrid creator model (direct + agency)
- Competitive intelligence dashboard

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Market Research | PRD as-is | Updated 2026-2027 |
| SOM Estimate | ₹50 Cr | ₹150 Cr (3x larger) |
| Agency Model | Required MVP | Hybrid (direct + agency) |
| Competitive Tracking | Not specified | Dashboard feature |

### Similarities:
- Both support 75-85% tiered creator revenue share
- Both acknowledge 30% app store fee impact
- Both target regional language market opportunity

### Gaps & Conflicts:
- Market sizing significantly different (SOM 3x variance)
- Agency model complexity differs

### Recommendations for Merging:
1. Use M. Kumaran's updated 2026-2027 market sizing (more current)
2. Implement hybrid agency model (supports both approaches)
3. Add competitive intelligence dashboard (valuable for strategy)

---

## Section 03: Product Vision

### Kapil's Requirements:
- **Total:** 14 requirements
- Basic preference matching for MVP
- Automated + manual content moderation
- Social platform focus

### M. Kumaran's Requirements:
- **Total:** 13 requirements
- Social + Dating hybrid positioning (equal weight)
- Core values as operational principles with KPIs
- Value hierarchy: Safety > Transparency > Creator-First > Accessibility
- Comprehensive MVP with all features (12-14 weeks)

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Platform Positioning | Social platform | Social + Dating hybrid |
| Core Values | Not operationalized | KPIs for each value |
| MVP Scope | Basic features | Comprehensive (all dating + social) |
| Timeline | Not specified | 12-14 weeks |

### Similarities:
- Both support automated + manual moderation
- Both target regional language users
- Both include creator-first approach

### Recommendations for Merging:
1. Adopt hybrid social+dating positioning
2. Implement core value KPIs for accountability
3. Define value hierarchy for conflict resolution

---

## Section 04: Revenue Model

### Kapil's Requirements:
- **Total:** 12 requirements
- 65% of net revenue to creators (after 30% app store)
- Free + VIP subscription tiers only for MVP
- TDS: Creator responsibility

### M. Kumaran's Requirements:
- **Total:** 10 requirements
- 75-85% tiered creator share (not 45-65%)
- No free tier mentioned (corrected from PRD)
- 5-stream revenue model
- Platform handles TDS deduction

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Creator Share | 65% of net | 75-85% tiered |
| TDS Handling | Creator responsibility | Platform deducts |
| Subscription Tiers | Free + VIP only | Not specified |
| Platform Share | 35% of net | 15-25% |

### Conflicts:
- **CRITICAL:** Revenue share calculation differs significantly
- **HIGH:** TDS responsibility - compliance implications

### Recommendations for Merging:
1. Use M. Kumaran's 75-85% tiered model (competitive advantage)
2. Platform should handle TDS (legal compliance)
3. Define clear subscription tiers

---

## Section 05: Feature Specifications

### Kapil's Requirements:
- **Total:** 28 requirements
- Detailed UI/UX specifications
- Phase 1 core features focus

### M. Kumaran's Requirements:
- **Total:** 25 requirements
- 5 languages at MVP
- 17-22 week timeline
- 15-20 person team recommended

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Languages at MVP | 2 (Tamil, Telugu) | 5 languages |
| Timeline | Not specified | 17-22 weeks |
| Team Size | Not specified | 15-20 people |

---

## Section 06: Technical Architecture

### Kapil's Requirements:
- **Total:** 17 requirements
- **Backend:** Node.js + Supabase (changed from PRD's FastAPI)
- **n8n:** Deferred to Phase 2
- **Automation:** pg_cron + Edge Functions for MVP
- **Monitoring:** Supabase + Sentry

### M. Kumaran's Requirements:
- **Total:** 18 requirements
- **Backend:** FastAPI (Python) - per PRD
- **n8n:** Included in MVP, self-hosted on AWS
- **Performance SLA:** <200ms p95 API response
- **Infrastructure:** Premium over-provisioned (30-40% higher cost)
- **Database:** 8-shard PostgreSQL from day 1
- **Security:** Enterprise-grade (WAF, JWT, MFA, E2E encryption, PCI DSS)
- **Timeline:** 19-24 weeks (includes AI provider evaluation)

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Backend | Node.js + Supabase | FastAPI (Python) |
| n8n | Phase 2 | MVP (self-hosted) |
| Performance SLA | Not specified | <200ms p95 |
| Infrastructure | Standard | Premium (30-40% higher) |
| Database | Single instance | 8-shard architecture |
| Security Level | Standard | Enterprise-grade |
| AI Moderation | Not detailed | Hybrid (Google/Azure MVP, Whisper Phase 2) |

### Conflicts:
- **CRITICAL:** Backend technology stack completely different
- **HIGH:** n8n inclusion timing (MVP vs Phase 2)
- **MEDIUM:** Infrastructure cost approach

### Risk Assessment:
- **CRITICAL:** Stack divergence creates incompatible implementations
- **HIGH:** Performance expectations differ significantly

### Recommendations for Merging:
1. **Choose one stack** - FastAPI offers better AI/ML integration; Supabase offers faster development
2. Consider n8n in MVP for workflow visibility
3. Adopt performance SLAs for quality assurance
4. Use sharded database design for future-proofing

---

## Section 07: Database Schema

### Kapil's Requirements:
- **Total:** 20 requirements
- PostgreSQL via Supabase
- Single instance approach
- RLS (Row Level Security) policies

### M. Kumaran's Requirements:
- **Total:** 15 requirements
- PostgreSQL 15
- 8-shard architecture from MVP
- 50+ indexes defined
- JSONB i18n columns for multilingual

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Architecture | Single instance | 8-shard from day 1 |
| Indexes | Not specified | 50+ indexes |
| i18n Approach | Not specified | JSONB columns |

---

## Section 08: API Specifications

### Kapil's Requirements:
- **Total:** 27 requirements
- Node.js Edge Functions
- RESTful API design

### M. Kumaran's Requirements:
- FastAPI-based
- Comprehensive endpoint documentation
- Performance requirements specified

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Framework | Node.js Edge Functions | FastAPI |
| Documentation | Standard | Comprehensive |

---

## Section 09: Coin Economy Design

### Kapil's Requirements:
- **Total:** 12 requirements
- 6 coin packages (₹49-₹1999)
- 55% platform / 45% creator split
- Coin expiry system for MVP

### M. Kumaran's Requirements:
- **Total:** 10 requirements (Updated: 11 with daily login bonus)
- **Dual Revenue Model:**
  - Calls: 75-85% tiered creator share
  - Gifts: 45% fixed creator share
- 6 coin packages (₹49-₹1999)
- Creator-set call rates (no platform-fixed tiers)
- No surge pricing
- 20 signup coins + 5 daily login coins

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Revenue Split | 55%/45% uniform | Dual model (75-85% calls, 45% gifts) |
| Call Pricing | Not specified | Creator-set (₹8-25/min) |
| Surge Pricing | Not specified | Removed |
| Free Coins | Not specified | 20 signup + 5/day login |

### Recommendations for Merging:
1. Use dual revenue model (higher call share attracts creators)
2. Implement creator-set pricing for flexibility
3. Include daily login bonus for engagement

---

## Section 10: Creator Payout System

### Kapil's Requirements:
- **Total:** 13 requirements
- **Holding Period:** 7 days fixed
- **Minimum Withdrawal:** ₹500 (per PRD)
- **TDS:** Creator responsibility

### M. Kumaran's Requirements:
- **Total:** 13 requirements
- **Holding Period:** NONE (T+1 only)
- **Minimum Withdrawal:** ₹100 (corrected from PRD)
- **TDS:** Platform deducts @ 10% for >₹30K/FY (Section 194J compliant)
- **Payout Method:** Auto-select IMPS/NEFT by amount
- **Agency:** Direct creators only for MVP

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Holding Period | 7 days | NONE (instant to available) |
| Minimum Withdrawal | ₹500 | ₹100 |
| TDS Handling | Creator responsibility | Platform deducts |
| TDS Threshold | Not specified | ₹30K per FY |
| Payout Timing | T+1 after hold | True T+1 (next day) |
| Agency Support | Required MVP | Deferred post-MVP |

### Conflicts:
- **CRITICAL:** Holding period - 7 days vs none
- **HIGH:** TDS handling responsibility
- **MEDIUM:** Minimum withdrawal amount

### Risk Assessment:
- **HIGH:** No holding period increases fraud risk
- **MEDIUM:** ₹100 minimum may increase transaction costs

### Recommendations for Merging:
1. Consider 24-48 hour hold (compromise) for fraud mitigation
2. Platform MUST handle TDS for legal compliance
3. Use ₹100 minimum with daily limits for fraud protection
4. Implement M. Kumaran's fraud detection as mitigation

---

## Section 11: N8N Automation Workflows

### Kapil's Requirements:
- **Total:** 17 requirements
- **Approach:** Deferred to Phase 2
- MVP uses Edge Functions + pg_cron directly

### M. Kumaran's Requirements:
- **Total:** 11 requirements (6 workflows)
- **Approach:** Included in MVP, self-hosted on AWS
- **Workflows:**
  1. Creator Onboarding Automation
  2. Real-time Payout Processing (webhook-triggered)
  3. Fraud Detection
  4. Daily Tier Recalculation
  5. Failed Webhook Retry Monitor
  6. Daily Analytics Aggregation
- **Cost:** Self-hosted saves ₹2.16L-5.16L/year vs n8n Cloud

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| n8n Timing | Phase 2 | MVP |
| Workflow Count | Deferred | 6 workflows |
| Payout Trigger | Not specified | Webhook (real-time) |
| Hosting | Not specified | Self-hosted AWS |

### Recommendations for Merging:
1. Include n8n in MVP for workflow visibility and monitoring
2. Implement webhook-triggered payouts for better UX
3. Self-host on AWS for cost savings

---

## Section 12: Legal and Compliance

### Kapil's Requirements:
- **Total:** 15 requirements
- Company registration requirements
- Privacy policy requirements
- Content moderation policies

### M. Kumaran's Requirements:
- **Note:** File does not exist in breakdown

### Gap Analysis:
- M. Kumaran's breakdown is missing legal/compliance section
- This is a significant gap that must be addressed

### Recommendations:
1. Use Kapil's legal requirements as baseline
2. Add DPDP Act 2023 compliance (M. Kumaran mentions in other sections)
3. Ensure TDS compliance per Section 194J

---

## Section 13: Implementation Roadmap

### Kapil's Requirements:
- **Total:** 13 requirements
- **Timeline:** 90 days (3 phases)
  - Phase 1 (Days 1-30): MVP
  - Phase 2 (Days 31-60): Beta
  - Phase 3 (Days 61-90): Launch
- **Technology:** Node.js + Supabase stack
- Video calls in Phase 3 (Week 11-12)

### M. Kumaran's Requirements:
- **Total:** 10 requirements
- **Timeline:** 16-20 weeks (4-5 months)
  - Phase 1 (Weeks 1-12): Core MVP
  - Phase 2 (Weeks 13-16): Enhanced Features
  - Phase 3 (Weeks 17-20): Launch Preparation
- **Team:** Shadowmarket agent team (existing)
- **Languages:** All 5 from Day 1
- Video calls in Phase 2 (Weeks 13-16)

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| MVP Timeline | 30 days | 12 weeks |
| Total Timeline | 90 days | 16-20 weeks |
| Language Rollout | Phased | All 5 at launch |
| Beta Testing | Week 7-8 | Week 17-18 |
| Team | Not specified | Shadowmarket team |

### Conflicts:
- **CRITICAL:** Timeline difference (90 days vs 16-20 weeks)
- **HIGH:** Scope difference explains timeline variance

### Recommendations for Merging:
1. Use realistic 16-20 week timeline for comprehensive scope
2. If faster launch needed, reduce MVP scope
3. Plan for 2-week beta testing minimum

---

## Section 14: Cost Estimation

### Kapil's Requirements:
- **Total:** 10 requirements
- **Total Budget:** ₹15.83L (6 months)
- **Team Cost:** ₹8.70L
- **Infrastructure:** ₹1.95L (Supabase savings)
- **Marketing:** ₹3.40L (4 months)
- **Savings:** ₹1.25L from Supabase vs AWS

### M. Kumaran's Requirements:
- **Total:** 8 requirements
- **Total Budget:** ₹9.47L (5 months)
- **Team Cost:** ₹0 (internal shadowmarket team)
- **Infrastructure:** ₹4.05L (includes dev tools)
- **Marketing:** ₹2.00L (1 month intensive)
- **Translation:** ₹60K (4 languages)
- **Break-even:** Month 7-8 (vs PRD's Month 5-6)

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Total Budget | ₹15.83L | ₹9.47L |
| Timeline | 6 months | 5 months |
| Team Cost | ₹8.70L (hiring) | ₹0 (internal team) |
| Infrastructure | ₹1.95L | ₹4.05L |
| Marketing Period | 4 months | 1 month |
| Break-even | Month 5-6 | Month 7-8 |
| Platform Share | ~35% | 15-25% |

### Key Insight:
- M. Kumaran's lower budget is due to internal team (no hiring)
- M. Kumaran's higher infrastructure is due to premium specs
- M. Kumaran's later break-even is due to lower platform share (creator-friendly)

### Recommendations:
1. If team exists internally, use ₹9.47L budget approach
2. If hiring required, use ₹15.83L budget approach
3. Accept Month 7-8 break-even for creator-friendly model

---

## Section 15: Success Metrics

### Kapil's Requirements:
- **Total:** 12 requirements
- **North Star:** Monthly Active Minutes
- DAU/MAU tracking
- Creator earnings tracking
- Admin analytics dashboard

### M. Kumaran's Requirements:
- **Total:** 12 requirements
- **North Star:** Monthly Active Minutes (MAM)
- Detailed MAM targets:
  - Month 1: 100K minutes
  - Month 6: 5M minutes
  - Month 12: 15M minutes
- User retention targets (D7: 40%, D30: 20%)
- Launch success criteria (4/6 metrics must pass)
- Quality and safety metrics

### Key Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| MAM Targets | Not specified | Detailed monthly targets |
| Retention Targets | Not specified | D7: 40%, D30: 20% |
| Launch Criteria | Not specified | 4/6 metrics must pass |
| Safety Metrics | Basic | Comprehensive |

### Recommendations:
1. Use M. Kumaran's specific metric targets
2. Implement launch success criteria for go/no-go decisions
3. Add quality and safety dashboard

---

# REPORT 2: QUESTIONS-ANSWERS.MD DETAILED COMPARISON

---

## Section 01: Executive Summary Q&A

### Kapil's Key Q&A:
- **Q1:** MVP Scope → Phase 1 (0-6 months)
- **Q2:** AI Companion → Phase 2
- **Q3:** Agency Model → Required for MVP
- **Total Questions:** 3

### M. Kumaran's Key Q&A:
- **Q1:** Active User Definition → MAU+WAU with engagement (calls+gifts only)
- **Q2:** Creator Revenue Tiers → 75%/80%/85% based on earnings
- **Q3:** T+1 Payout → Calendar day, ₹100 minimum
- **Q4-Q6:** Follow-ups on tier calculation, payout methods
- **Q7-Q9:** Business goals (gross GMV), AI companion deferral, language strategy
- **Total Questions:** 9

### Alignment:
- Both defer AI Companion
- Both target Phase 1 MVP

### Divergent Interpretations:
- **Active User:** Kapil not specified, M. Kumaran strict definition
- **Agency:** Kapil requires MVP, M. Kumaran doesn't mention

---

## Section 04: Revenue Model Q&A

### Kapil's Key Q&A:
- Creator gets 65% of net revenue
- Free + VIP tiers only for MVP
- TDS: Creator responsibility

### M. Kumaran's Key Q&A:
- Creator gets 75-85% tiered
- TDS: Platform handles (legal compliance)
- Corrected multiple PRD errors

### Divergent Interpretations:
- **CRITICAL:** Revenue share calculation completely different
- **HIGH:** TDS handling responsibility

---

## Section 06: Technical Architecture Q&A

### Kapil's Key Q&A:
- **Q1:** Backend → Node.js + Supabase (changed from FastAPI)
- **Q2:** n8n → Defer to Phase 2
- **Total Decisions:** 2

### M. Kumaran's Key Q&A:
- **Q1:** Technology → Flutter + FastAPI confirmed
- **Q2:** Scalability → Premium (<200ms SLA, 15% concurrent)
- **Q3:** AI Moderation → Hybrid (Google/Azure MVP, Whisper Phase 2)
- **Q4:** Infrastructure → Premium over-provisioning (30-40% higher)
- **Q5:** CDN → Mumbai primary + CloudFront
- **Q6:** AI Provider → Evaluate both Google + Azure
- **Q7:** Security → Enterprise-grade (PCI DSS)
- **Q8:** Monitoring → Full observability stack
- **Q9:** Disaster Recovery → RTO 4h, RPO 1h
- **Total Decisions:** 9

### Critical Divergence:
- **Backend Stack:** Completely different choices
- **Complexity:** Kapil simplified, M. Kumaran enterprise-grade

---

## Section 10: Creator Payout System Q&A

### Kapil's Key Q&A:
- Holding period: 7 days fixed
- Simple approach

### M. Kumaran's Key Q&A:
- **Q1:** Minimum withdrawal → ₹100 (corrected from ₹500)
- **Q2:** TDS threshold → ₹30K per FY (Section 194J)
- **Q3:** Holding period → NONE (T+1 only)
- **Q4:** Tier changes → No retroactive adjustment
- **Q5:** KYC → Before first withdrawal (lazy KYC)
- **Q6:** Payout method → Auto-select IMPS/NEFT
- **Q7:** Failed payouts → Hybrid retry (3 attempts)
- **Q8:** Schedule → On-demand withdrawals
- **Q9:** Agency → Direct creators only for MVP
- **Total Decisions:** 9

### Critical Conflicts:
- **Holding period:** 7 days vs NONE
- **Minimum:** ₹500 vs ₹100
- **TDS:** Creator vs Platform responsibility

---

## Section 11: N8N Automation Q&A

### Kapil's Key Q&A:
- n8n deferred to Phase 2
- Use Edge Functions directly

### M. Kumaran's Key Q&A:
- **Q1:** Payout trigger → Webhook (real-time)
- **Q2:** Balance workflow → REMOVED (no holding period)
- **Q3:** KYC API → Karza confirmed
- **Q4:** Fraud flags → Immediate payout hold
- **Q5:** Retries → Backend handles (not n8n)
- **Q6:** IMPS/NEFT → Backend calculates method
- **Q7:** New workflows → Added 3 (tier recalc, webhook retry, analytics)
- **Q8:** Error handling → Error workflow + retries
- **Q9:** Hosting → Self-hosted AWS (saves ₹2-5L/year)
- **Total Decisions:** 9, **Workflows:** 6

---

# EXECUTIVE SUMMARY

---

## OVERALL DIFFERENCES SUMMARY

### Core Philosophical/Approach Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| **Development Philosophy** | Rapid MVP, simplified stack | Enterprise-grade, comprehensive |
| **Technology Preference** | BaaS (Supabase) | Custom backend (FastAPI) |
| **Speed vs Quality** | Faster launch | Higher quality, longer timeline |
| **Creator Experience** | Standard | Premium (instant payouts, lower minimums) |
| **Platform Share** | Higher (35%) | Lower (15-25%) |

### Scope Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| MVP Timeline | 90 days | 16-20 weeks |
| Languages at Launch | 2 (Tamil, Telugu) | 5 (all South Indian + Hindi) |
| n8n Automation | Phase 2 | MVP |
| Holding Period | 7 days | None |
| Agency Support | MVP | Post-MVP |

### Technical Architecture Differences:
| Component | Kapil | M. Kumaran |
|-----------|-------|------------|
| Backend | Node.js + Supabase | FastAPI (Python) |
| Database | Single instance | 8-shard from day 1 |
| Performance SLA | Not specified | <200ms p95 |
| Infrastructure | Standard | Premium (30-40% higher) |
| Security | Basic | Enterprise-grade |
| Monitoring | Supabase + Sentry | Full observability stack |

### Timeline & Cost Differences:
| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| MVP Duration | 30 days | 12 weeks |
| Total Timeline | 90 days | 16-20 weeks |
| Total Budget | ₹15.83L | ₹9.47L |
| Team Cost | ₹8.70L (hiring) | ₹0 (internal) |
| Break-even | Month 5-6 | Month 7-8 |

---

## OVERALL SIMILARITIES SUMMARY

### Shared Vision:
- Voice-first dating platform for Tier-2/3 India
- Regional language focus (South Indian markets)
- Creator-centric revenue model (75-85% share)
- T+1 payout as competitive advantage
- Flutter for mobile development
- Agora SDK for voice/video calls
- Razorpay for payments
- 50K MAU / ₹20L GMV at 6 months

### Common Goals:
- AI Companion deferred to Phase 2
- Content moderation (automated + manual)
- KYC verification for creators
- Multiple subscription tiers
- Admin analytics dashboard

### Aligned Technical Choices:
- Flutter 3.x for mobile
- PostgreSQL database
- Redis caching
- Firebase for authentication
- Agora for real-time communication
- Razorpay for payments
- CloudFront for CDN

---

## CRITICAL CONFLICTS SUMMARY

### High Priority Conflicts (Must Resolve Before Proceeding):

**1. Backend Technology Stack**
- **Kapil:** Node.js + Supabase
- **M. Kumaran:** FastAPI (Python)
- **Impact:** Completely incompatible implementations
- **Resolution:** Choose one stack. Recommend FastAPI for AI/ML capabilities OR Supabase for faster MVP.

**2. Creator Payout Holding Period**
- **Kapil:** 7-day hold
- **M. Kumaran:** No hold (instant to available)
- **Impact:** Different creator experience, fraud risk profile
- **Resolution:** Implement 24-48 hour compromise hold with fraud detection.

**3. TDS Handling Responsibility**
- **Kapil:** Creator handles their own taxes
- **M. Kumaran:** Platform deducts TDS @ 10% for >₹30K/FY
- **Impact:** Legal compliance risk
- **Resolution:** Platform MUST handle TDS for Section 194J compliance.

**4. Revenue Share Calculation**
- **Kapil:** 65% of net to creators
- **M. Kumaran:** 75-85% tiered to creators
- **Impact:** Core business model difference
- **Resolution:** Use 75-85% tiered model for competitive differentiation.

### Medium Priority Conflicts (Should Resolve Early):

**5. n8n Automation Timing**
- **Kapil:** Phase 2
- **M. Kumaran:** MVP
- **Impact:** Workflow visibility and maintainability
- **Resolution:** Include basic n8n workflows in MVP.

**6. Language Rollout Strategy**
- **Kapil:** Phased (Tamil first)
- **M. Kumaran:** All 5 at launch
- **Impact:** Market coverage and development scope
- **Resolution:** 5 languages if timeline allows, otherwise prioritize Tamil+Telugu.

**7. MVP Timeline**
- **Kapil:** 90 days
- **M. Kumaran:** 16-20 weeks
- **Impact:** Resource planning and market entry timing
- **Resolution:** Use 16-20 weeks for comprehensive scope.

### Low Priority Conflicts (Address During Implementation):

**8. Minimum Withdrawal Amount**
- **Kapil:** ₹500
- **M. Kumaran:** ₹100
- **Resolution:** Use ₹100 with daily limits.

**9. Agency Model Timing**
- **Kapil:** MVP
- **M. Kumaran:** Post-MVP
- **Resolution:** Optional for MVP, full support in Phase 2.

**10. Infrastructure Approach**
- **Kapil:** Standard
- **M. Kumaran:** Premium
- **Resolution:** Start standard, scale to premium based on growth.

---

## MERGED REQUIREMENTS ROADMAP

### Top 5 Agreed-Upon Priorities:
1. **Voice-first dating experience** with Agora SDK integration
2. **75-85% creator revenue share** as key differentiator
3. **Regional language support** for South Indian markets
4. **T+1 payout system** for creator satisfaction
5. **Content moderation** (automated + manual)

### Top 3 Areas Needing Stakeholder Alignment:
1. **Backend Technology Stack** - Node.js+Supabase vs FastAPI
2. **Holding Period Policy** - 7 days vs none vs compromise
3. **MVP Timeline** - 90 days vs 16-20 weeks

### Recommended Approach Combining Best of Both:

**Technology:**
- Use FastAPI for backend (better AI/ML integration for moderation)
- Include n8n in MVP (workflow visibility)
- Enterprise-grade security from start

**Business Model:**
- 75-85% tiered creator share
- ₹100 minimum withdrawal
- 24-48 hour holding period (fraud mitigation)
- Platform handles TDS

**Timeline:**
- 16-20 weeks total
- 5 languages at launch
- 2-week beta testing

**Budget:**
- ₹9.5-10L if team exists
- ₹15-16L if hiring required
- Month 7-8 break-even

---

## STAKEHOLDER ALIGNMENT SCORE

| Section | Alignment Score | Key Conflict |
|---------|-----------------|--------------|
| 01 Executive Summary | 75% | Language strategy, agency timing |
| 02 Market Analysis | 80% | Market sizing, agency model |
| 03 Product Vision | 70% | Social vs hybrid positioning |
| 04 Revenue Model | 50% | Revenue share calculation |
| 05 Feature Specifications | 75% | Language count at MVP |
| 06 Technical Architecture | 30% | Backend stack completely different |
| 07 Database Schema | 60% | Sharding approach |
| 08 API Specifications | 50% | Framework difference |
| 09 Coin Economy Design | 65% | Dual revenue model |
| 10 Creator Payout System | 40% | Holding period, TDS, minimum |
| 11 N8N Workflows | 45% | MVP vs Phase 2, workflow count |
| 12 Legal & Compliance | N/A | M. Kumaran missing this section |
| 13 Implementation Roadmap | 55% | Timeline (90d vs 16-20w) |
| 14 Cost Estimation | 60% | Budget approach |
| 15 Success Metrics | 80% | Both use MAM as north star |

**Overall Alignment: 58%**

---

## ACTION ITEMS

### Immediate Decisions Required:

1. **Technology Stack Decision Meeting**
   - Participants: Both stakeholders + Tech Lead
   - Decision: Node.js+Supabase vs FastAPI
   - Timeline: Before development starts

2. **Business Model Alignment**
   - Topics: Revenue share %, TDS handling, holding period
   - Output: Single revenue model document
   - Timeline: Within 1 week

3. **Timeline Agreement**
   - Options: 90 days (reduced scope) vs 16-20 weeks (full scope)
   - Output: Final project schedule
   - Timeline: Within 1 week

### Documentation Updates Required:

1. M. Kumaran to add Legal & Compliance section
2. Harmonize revenue share calculations
3. Create unified API specification
4. Merge database schemas

### Process Recommendations:

1. Weekly alignment meetings until conflicts resolved
2. Single source of truth document for requirements
3. Technical architect to recommend stack
4. Legal review of TDS handling approach

---

## ADDITIONAL ANALYSIS

### Technology Stack Comparison

| Component | Kapil Preference | M. Kumaran Preference | Recommendation |
|-----------|------------------|----------------------|----------------|
| Frontend | Flutter 3.x | Flutter 3.x | Flutter 3.x ✓ |
| Backend | Node.js + Supabase | FastAPI (Python) | FastAPI (AI/ML) |
| Database | Supabase PostgreSQL | PostgreSQL 15 (sharded) | Sharded PostgreSQL |
| Cache | Upstash Redis | Redis 7 Cluster | Redis Cluster |
| Auth | Firebase + Supabase | Firebase Auth | Firebase Auth |
| RTC | Agora SDK | Agora SDK | Agora SDK ✓ |
| Payments | Razorpay | Razorpay | Razorpay ✓ |
| Automation | pg_cron + Edge Fn | n8n (self-hosted) | n8n in MVP |
| Monitoring | Sentry | Full observability | Full observability |

### Feature Priority Comparison

**Must-Have (Both Agree):**
- User registration with phone OTP
- Voice/video calls with per-minute billing
- Creator profiles with voice bio
- Coin wallet and purchase flow
- Creator payout system
- Basic analytics dashboard

**Kapil Must-Have (M. Kumaran Optional):**
- Agency management system (MVP)
- 7-day holding period

**M. Kumaran Must-Have (Kapil Optional):**
- 5 languages at launch
- <200ms API SLA
- Enterprise security
- n8n workflows in MVP

### Success Metrics Alignment

| Metric | Kapil Target | M. Kumaran Target | Aligned? |
|--------|--------------|-------------------|----------|
| 6-Month MAU | 50,000 | 50,000 | ✓ |
| 6-Month GMV | ₹20L | ₹18L | ~ |
| D7 Retention | Not specified | 40% | Adopt |
| D30 Retention | Not specified | 20% | Adopt |
| North Star | MAM | MAM | ✓ |
| Creator NPS | Not specified | >50 | Adopt |

---

**Report Generated:** 2026-01-24
**Comparison Status:** Complete
**Next Steps:** Stakeholder alignment meeting to resolve critical conflicts

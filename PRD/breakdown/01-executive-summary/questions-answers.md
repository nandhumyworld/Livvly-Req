# Executive Summary - Questions & Answers

**Section ID:** 01-executive-summary
**Section Title:** Executive Summary
**Last Updated:** 2026-01-19
**Status:** Completed

---

## Question Session Overview

This document captures all clarifying questions asked during the breakdown of the Executive Summary section, along with the user's answers and the impact of those answers on requirement extraction.

**Total Questions Asked:** 9 (3 core + 3 follow-ups + 3 gap analysis)
**Session Duration:** ~20 minutes
**Requirements Generated:** 11

---

## Phase 1: Core Questions

### Q1: Active User Definition

**Question:**
The PRD mentions "50,000 active users" as a 6-month goal, but the term "active user" needs precise definition for requirement specification.

- What specific actions qualify a user as "active" within a measurement period?
- What is the measurement period (Daily Active Users, Weekly Active Users, Monthly Active Users)?
- Does "active" mean logged in, or does it require engagement (calls, gifts, room participation)?

**Answer:**
Both Monthly Active Users (MAU) and Weekly Active Users (WAU) with engagement:
- Users must log in AND perform at least one action (call, gift, room visit) to count as active
- Track both MAU and WAU metrics

**Impact on Requirements:**
- **REQ-ES-007:** Active User Metrics Definition created with strict engagement criteria
- **REQ-ES-008, 009, 010:** Business goals clarified to use MAU (not DAU or total registered users)
- Established that passive browsing does NOT count toward active user metrics

**Follow-up Questions Generated:** Q6 (Engagement Action Tracking)

---

### Q2: Creator Revenue Share Implementation

**Question:**
The PRD states "75-85% creator revenue share" as a differentiator, but this range needs clarification for implementation.

- What determines whether a creator gets 75% vs 85%? (Performance tier, engagement level, exclusivity?)
- Is this split calculated before or after platform costs (payment gateway fees, taxes)?
- Are there any conditions or thresholds that affect the revenue share percentage?

**Answer:**
Performance-based tiers:
- 75% for new creators
- 80% for mid-tier creators (₹50K+ per month)
- 85% for top performers (₹2L+ per month)
- Incentivizes growth and retention

**Impact on Requirements:**
- **REQ-ES-004:** Creator Revenue Share Model created with three-tier structure
- Established clear threshold amounts (₹50K and ₹2L monthly earnings)
- Defined that revenue share is a growth incentive mechanism
- Clarified this is THE primary competitive differentiator

**Follow-up Questions Generated:** Q4 (Performance Tier Tracking)

---

### Q3: T+1 Payout Mechanics

**Question:**
The PRD mentions "T+1 (Next day)" payout as a competitive advantage.

- What is the cutoff time for transactions to be included in T+1 payout? (e.g., 11:59 PM IST?)
- Does T+1 mean calendar day or business day? (What about weekends/holidays?)
- Are there any minimum threshold amounts before payout is processed?

**Answer:**
Next calendar day, 11:59 PM cutoff, ₹100 minimum:
- Transactions completed by 11:59 PM IST are paid the next calendar day (including weekends/holidays)
- Minimum payout threshold: ₹100
- Best user experience for creators

**Impact on Requirements:**
- **REQ-ES-005:** T+1 Payout System created with specific cutoff time
- Established that T+1 includes weekends/holidays (major competitive advantage)
- Defined minimum threshold to prevent micro-transaction processing costs
- Clarified this is calendar day, not business day

**Follow-up Questions Generated:** Q5 (Payout Processing for Weekends/Holidays)

---

## Phase 2: Follow-up Questions

### Q4: Performance Tier Tracking (Follow-up to Q2)

**Question:**
You mentioned performance-based revenue tiers with monthly earnings thresholds (₹50K and ₹2L).

- Should tier calculation be based on trailing 30 days or calendar month?
- When a creator crosses a threshold mid-month, does the new percentage apply immediately or from next month?
- If a creator's earnings drop below threshold in subsequent month, do they revert to lower tier?

**Answer:**
Trailing 30 days, immediate tier change:
- Calculate based on last 30 days of earnings
- New tier applies immediately when threshold is crossed (no waiting for next month)
- Most dynamic and fair approach
- If earnings drop below threshold, tier reverts immediately

**Impact on Requirements:**
- **REQ-ES-004:** Enhanced with trailing 30-day calculation method (not calendar month)
- Added acceptance criteria for immediate tier changes (no waiting period)
- Added acceptance criteria for tier reversion if earnings drop
- This creates a truly dynamic, real-time incentive system (more complex but fairer)

**Technical Implications:**
- Requires continuous monitoring of creator earnings
- Database queries must efficiently calculate rolling 30-day sums
- Creator dashboard must show real-time tier status and proximity to next tier

---

### Q5: Payout Processing for Weekends/Holidays (Follow-up to Q3)

**Question:**
You specified T+1 includes weekends/holidays for best creator experience.

- Which payment rails support weekend/holiday transfers? (UPI/IMPS 24x7, NEFT business days only)
- Should the system automatically route to appropriate payment method based on availability?
- What happens if creator's bank account is unavailable during weekend processing?

**Answer:**
Auto-route: UPI/IMPS 24x7, queue NEFT:
- Use UPI/IMPS for instant 24x7 transfers when available
- Queue NEFT-only accounts for next business day
- Best balance between T+1 promise and technical constraints

**Impact on Requirements:**
- **REQ-ES-005:** Enhanced with intelligent payment routing logic
- Added acceptance criteria for automatic payment method selection
- Added acceptance criteria for queuing NEFT transfers to next business day
- Added acceptance criteria for retry logic (3 attempts with 4-hour intervals)

**Technical Implications:**
- Payment gateway integration must support multiple rails (UPI/IMPS/NEFT)
- Creator profile must store preferred/available payment methods
- Notification system must inform creators of queued payouts (NEFT)
- Retry mechanism needed for failed transactions

---

### Q6: Engagement Action Tracking (Follow-up to Q1)

**Question:**
You defined active users as those with login AND engagement (call/gift/room visit).

- Should "room visit" require minimum duration (e.g., stayed 30+ seconds vs just opened)?
- Does browsing profiles or sending messages count as engagement, or only the three actions listed?
- For calls, does initiated-but-not-connected count, or must the call be successfully established?

**Answer:**
Multiple actions selected:
1. Completed voice call (connected) - Call must be successfully connected and establish audio (excludes failed/missed calls)
2. Gift sent (any amount) - Sent at least one gift to any creator (strong engagement signal and revenue action)

Note: Profile browsing, messaging, and room participation do NOT count as engagement for active user metrics.

**Impact on Requirements:**
- **REQ-ES-007:** Refined engagement actions to only 2 highly-qualified actions (not 3)
- Explicitly excluded room participation (which was in original Q1 answer)
- Explicitly excluded profile browsing and messaging
- Clarified that calls must be successfully connected (not just attempted)
- Gifts of any amount count (no minimum threshold)

**Strategic Rationale:**
This creates a stricter, more meaningful active user metric focused on:
1. **Revenue-generating actions** (gifts directly generate revenue)
2. **High-commitment actions** (completed calls show serious engagement)
3. **Avoiding vanity metrics** (passive browsing/messaging excluded)

**Technical Implications:**
- Call tracking must differentiate between initiated, ringing, connected, and completed states
- Gift transactions automatically trigger "active user" flag
- Analytics must track "engaged users" vs "browsing users" separately

---

## Phase 3: Gap Analysis Questions

### Q7: Business Goal Metrics Measurement

**Question:**
The PRD specifies business goals (50K users @ ₹20L GMV in 6 months), but doesn't clarify:

- Is the ₹20L GMV the gross amount users spend (before platform/creator split), or net revenue to platform?
- Should we track GMV as total transaction value or as monthly recurring value?
- Are the user targets (50K, 2L, 10L) based on MAU or total registered users?

**Answer:**
Gross GMV, MAU target:
- ₹20L GMV is total user spending (gross amount before platform/creator split)
- 50K target is MAU (Monthly Active Users with engagement as defined earlier)
- Track GMV as total transaction value
- Standard industry practice for measuring marketplace success

**Impact on Requirements:**
- **REQ-ES-008, 009, 010:** Business goals clarified to use gross GMV (not net platform revenue)
- Confirmed user targets are MAU (not total registered or DAU)
- Established GMV as "total transaction value" (coins + subscriptions + all revenue streams)
- Aligned metrics with standard marketplace measurement practices

**Why This Matters:**
- Gross GMV is a more impressive growth metric (larger numbers)
- Marketplace investors evaluate on GMV, not just platform take rate
- Separates platform success (GMV) from monetization efficiency (platform percentage)
- MAU is more meaningful than total registered users (eliminates dead accounts)

---

### Q8: AI Companion Implementation Scope

**Question:**
The PRD mentions "AI companion + Real creators" as a key differentiator, but the Executive Summary doesn't specify:

- Should AI companions be available from MVP/launch, or is this a future phase feature?
- Are AI companions meant to supplement real creators during low-traffic times, or as a separate service tier?
- This will affect sprint planning and technical architecture prioritization.

**Answer:**
Post-MVP, Phase 2+ after real creators proven:
- Launch MVP with real creators only
- Add AI companions in a later phase after validating the core business model
- This decision affects sprint planning - AI companions NOT included in initial MVP sprints
- Lower initial complexity, can gather traffic pattern data first

**Impact on Requirements:**
- **REQ-ES-006:** AI Companion Feature Roadmap created (explicitly scoped OUT of MVP)
- Removed AI companion from competitive differentiation tracking (REQ-ES-011)
- Simplified MVP scope and reduced initial development timeline
- Technical architecture must allow future AI integration without major refactoring

**Strategic Rationale:**
- Validate business model with real creators first (lower risk)
- Gather traffic pattern data to inform AI companion design
- Reduce MVP complexity and time-to-market
- Can use early user behavior to train AI models more effectively

**Impact on Sprint Planning:**
- AI companion work moves to Sprint 6+ (post-MVP)
- Frees up MVP sprints for core features
- Can prioritize real creator recruitment and onboarding
- Estimated 2-3 weeks saved on MVP timeline

---

### Q9: Regional Language Expansion Strategy

**Question:**
The PRD states "Tamil-first, expanding to Telugu/Kannada" but doesn't specify:

- Should the MVP support only Tamil, or should it be architected to easily add Telugu/Kannada post-launch?
- What is the priority order: Tamil → Telugu → Kannada, or should Telugu/Kannada launch together?
- This affects whether we build i18n infrastructure from day one or add it later.

**Initial Answer:**
Tamil/Telugu/Kannada/Malayalam (Custom answer - all four South Indian languages):
- This expands beyond the original PRD scope which only mentioned Tamil/Telugu/Kannada
- User wants to include Malayalam as well
- This significantly impacts the language strategy and requires building robust i18n infrastructure from day one

**Follow-up Clarification Question:**
Which rollout approach:
- Option A: All 4 languages at MVP launch
- Option B: Phased rollout (Tamil+Telugu first, then Kannada+Malayalam)
- Option C: Tamil-only MVP, rapid expansion

**Final Answer:**
Phased: Tamil+Telugu MVP, then Kannada+Malayalam:
- Phase 1 (MVP Launch): Tamil + Telugu (2 largest markets)
- Build robust i18n infrastructure from day one
- Phase 2 (Month 2-3 post-launch): Kannada + Malayalam
- Learn from initial rollout before expanding

**Impact on Requirements:**
- **REQ-ES-003:** Regional Language Support Strategy created with phased approach
- Scope expanded from 3 to 4 languages (Malayalam added)
- Established MVP launches with 2 languages (Tamil + Telugu)
- Requirement for robust i18n infrastructure from day one
- Month 2-3 expansion to Kannada + Malayalam

**Strategic Rationale:**
- Tamil Nadu (75M people) + Andhra Pradesh/Telangana (85M people) = 160M potential users at launch
- Karnataka (65M) + Kerala (35M) = 100M potential users in Phase 2
- Faster MVP launch (2-3 weeks earlier than all-4-languages approach)
- Easier initial creator recruitment (2 languages vs 4)
- Learn from Tamil/Telugu rollout to improve Kannada/Malayalam onboarding

**Technical Implications:**
- i18n framework must be architected from day one (not retrofitted)
- Content management system for translations needed early
- UI/UX must handle script differences (Telugu uses different script than Tamil/Malayalam/Kannada)
- Moderation rules and keyword filtering needed for all 4 languages eventually
- Creator onboarding materials needed in all 4 languages

**Impact on Sprint Planning:**
- Sprint 1-2: i18n framework implementation
- Sprint 2-4: Tamil + Telugu content, UI strings, moderation
- Sprint 6-7 (post-MVP): Kannada + Malayalam content and launch

---

## Summary of Key Decisions

### Strategic Decisions
1. **Language Strategy:** Phased rollout (Tamil+Telugu MVP, then Kannada+Malayalam) with 4th language (Malayalam) added to original scope
2. **AI Companions:** Deferred to post-MVP Phase 2+ to reduce initial complexity and validate business model first
3. **Creator Revenue Tiers:** Dynamic trailing 30-day calculation with immediate tier changes (no monthly waiting period)
4. **Active User Metrics:** Strict engagement-based definition (completed calls + gifts only, excludes passive browsing)

### Technical Decisions
1. **Payout Processing:** Intelligent routing (UPI/IMPS 24x7, queue NEFT) to deliver T+1 promise with weekend/holiday support
2. **i18n Infrastructure:** Built from day one to support easy language expansion
3. **Metrics Calculation:** GMV as gross transaction value (standard marketplace practice)

### Scope Changes from Original PRD
1. **Added:** Malayalam language support (expanding from 3 to 4 South Indian languages)
2. **Removed:** AI companion from MVP scope (moved to post-MVP Phase 2+)
3. **Clarified:** Phased language rollout instead of all-at-once

---

## Requirements Influenced by Q&A

| Requirement ID | Primary Question(s) | Key Insights from Answer |
|----------------|---------------------|--------------------------|
| REQ-ES-001 | N/A (from PRD) | Product definition clarified |
| REQ-ES-002 | N/A (from PRD) | Target market defined |
| REQ-ES-003 | Q9 + Follow-up | 4 languages phased (Tamil+Telugu first) |
| REQ-ES-004 | Q2, Q4 | 3-tier dynamic revenue share, trailing 30 days |
| REQ-ES-005 | Q3, Q5 | T+1 with intelligent routing, weekend support |
| REQ-ES-006 | Q8 | AI companions post-MVP Phase 2+ |
| REQ-ES-007 | Q1, Q6 | Strict engagement definition (calls+gifts only) |
| REQ-ES-008 | Q7 | 6-month goals: gross GMV, MAU basis |
| REQ-ES-009 | Q7 | 12-month goals: gross GMV, MAU basis |
| REQ-ES-010 | Q7 | 24-month goals: gross GMV, MAU basis |
| REQ-ES-011 | Multiple | Competitive differentiation tracking |

---

## Open Questions for Future Sections

These questions emerged during this session but belong to other sections:

1. **For Feature Specifications (Section 5):**
   - What specific i18n framework should be used? (React Native Localization vs i18next vs custom)
   - How should language switching work mid-session? (app restart vs dynamic reload)

2. **For Technical Architecture (Section 6):**
   - What caching strategy for i18n strings? (local storage vs API-driven)
   - How to handle mixed-language scenarios (user speaks Tamil, creator speaks Telugu)?

3. **For Creator Payout System (Section 10):**
   - Should tier status be visible to creators in real-time?
   - How to communicate proximity to next tier? (e.g., "₹5K away from 80% tier")

4. **For API Specifications (Section 8):**
   - What Razorpay API endpoints support UPI/IMPS/NEFT routing?
   - How to handle payout retry logic (webhook-based vs polling)?

5. **For Success Metrics (Section 15):**
   - What analytics tool stack for MAU/WAU tracking? (Mixpanel, Amplitude, custom)
   - How frequently should tier calculations run? (real-time vs hourly batch)

---

## Lessons Learned

### What Worked Well
1. **Progressive questioning:** Core → Follow-up → Gap analysis revealed layered insights
2. **Scope clarification early:** Catching language expansion (3→4 languages) in Executive Summary prevents late-stage surprises
3. **Strategic deferral:** Moving AI companions to post-MVP simplifies project and reduces risk

### Improvements for Next Sections
1. **Ask about implementation timeline earlier:** Language rollout question required follow-up clarification
2. **Probe on "range" values:** The "75-85%" range immediately triggered need for tier structure clarification
3. **Watch for competitive claims:** "T+1 vs T+3 to T+7" required deep dive into weekend/holiday handling

---

## Next Section Preview

**Section 2: Market Analysis** will likely require:
- Validation of competitor revenue share claims (50-60%)
- Validation of market size estimates (TAM, SAM, SOM)
- Research on regional language dating app landscape
- Clarification on how SWOT analysis informs feature prioritization

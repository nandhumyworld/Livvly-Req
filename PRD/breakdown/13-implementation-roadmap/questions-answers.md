# Section 13: Implementation Roadmap - Questions & Answers

**Section**: Implementation Roadmap
**Section ID**: 13-implementation-roadmap
**Generated**: 2026-01-24
**Total Questions**: 3

---

## Question 1: MVP Timeline Reconciliation

**Question**:
The PRD shows a 30-day MVP timeline broken into 4 weeks (Week 1: Foundation, Week 2: Core Features, Week 3: Communication, Week 4: Monetization). However, the approved scope from previous sections has expanded significantly:

- **Language Support**: 5 languages (Tamil, Telugu, Kannada, Hindi, English) vs. PRD's implied 2
- **Video Support**: Video calls approved for Phase 2 (Section 5) vs. PRD's audio-only
- **AI Features**: Age verification with AWS Rekognition, content moderation
- **Live Rooms**: Multi-user audio rooms functionality
- **Automation**: 6 n8n workflows vs. PRD's 3
- **Compliance**: Comprehensive KYC (Karza API), TDS automation (ClearTax API)

Section 2 (Market Analysis) estimated 22-29 weeks for comprehensive scope. What is the realistic MVP timeline given the approved feature set?

**Options Provided**:
1. Keep 30-day aggressive timeline (high risk, requires scope reduction)
2. Update to 12-16 weeks (moderate scope, faster market entry)
3. **Update to 16-20 weeks (Recommended)** - Comprehensive scope with quality buffer
4. Update to 22-29 weeks (matches Section 2 estimate, conservative)

**User Answer**:
**Option 3: Update to 16-20 weeks (Recommended)**

**Rationale**:
The 16-20 week timeline provides:
- Sufficient time for 5-language implementation from Day 1
- Quality buffer for complex integrations (Agora SDK, Razorpay X, Karza API)
- Adequate testing window (2 weeks beta testing in Weeks 17-18)
- Competitive advantage vs. Section 2's 22-29 week estimate
- Realistic for shadowmarket agent team capacity

**Impact on Requirements**:
- **REQ-IR-001**: Official timeline updated to 16-20 weeks (not 30 days)
- **REQ-IR-002**: Phased approach defined (Phase 1: Weeks 1-12, Phase 2: Weeks 13-16, Phase 3: Weeks 17-20)
- **REQ-IR-006**: Phase 1 completion criteria set for Week 12 milestone
- **REQ-IR-007**: Beta testing scheduled for Weeks 17-18 (2 weeks)
- **REQ-IR-008**: Launch readiness checklist due Week 20

**Trade-offs Accepted**:
- Longer time-to-market vs. 30-day aggressive plan
- Higher development cost (4-5 months team engagement vs. 1 month)
- Benefit: Comprehensive feature set, lower technical debt, higher quality at launch

---

## Question 2: Team Structure and Capacity

**Question**:
The PRD doesn't specify team size or structure for the 16-20 week MVP timeline. What is the development team composition?

**Context**:
Typical team for this scope:
- 2-3 Flutter developers (mobile app)
- 2 Backend developers (FastAPI, PostgreSQL)
- 1 DevOps engineer (AWS infrastructure, n8n setup)
- 1 QA engineer (manual + automation testing)
- 1 Product Owner / Project Manager
- Total: 7-8 full-time resources

**Options Provided**:
1. Hire new dedicated team (7-8 FTEs, ₹35-50L/month cost)
2. **Use existing shadowmarket agent team (Recommended)** - Leverage existing resources
3. Hybrid: Core team + contractors for specialized tasks
4. Outsource to agency (fixed cost, less control)

**User Answer**:
**"we have our shadowmarket agent team"**

**Interpretation**:
User confirmed existing "shadowmarket agent team" will execute the project. No new hiring required.

**Follow-up Assumptions** (to be validated):
- Team has Flutter and Python/FastAPI expertise
- Team is available for 16-20 week commitment
- Team capacity is sufficient for estimated workload
- Team has worked together before (established velocity)

**Impact on Requirements**:
- **REQ-IR-004**: Shadowmarket agent team structure formalized with roles and sprint capacity
- **REQ-IR-009**: Sprint velocity tracking from Week 1 to establish baseline
- **Cost Impact**: No hiring cost, but opportunity cost of team allocation to this project

**Risk Mitigation**:
- If team lacks specific expertise (e.g., Agora SDK, Razorpay X), budget 1-2 weeks upskilling in Phase 1
- If team capacity is lower than expected, timeline may extend to upper bound (20 weeks vs. 16 weeks)

---

## Question 3: Language Rollout Strategy

**Question**:
The approved scope includes 5 languages (Tamil, Telugu, Kannada, Hindi, English). Should language support be:

**Option A**: Rolled out incrementally
- Launch 1: Tamil + English (Week 16)
- Launch 2: Telugu + Kannada (Week 18)
- Launch 3: Hindi (Week 20)
- Benefit: Faster initial launch, focused QA
- Risk: Market fragmentation, user confusion

**Option B**: All 5 languages at launch
- All languages from Day 1 of development
- i18n infrastructure built in Week 1
- Translation workflow parallel to development
- Benefit: Single launch event, full market coverage
- Risk: Higher complexity, translation quality variability

**Options Provided**:
1. Incremental rollout (Tamil + English first, others later)
2. **All 5 languages at launch (Recommended)** - Single launch, full market coverage
3. English-only MVP, add languages post-launch

**User Answer**:
**Option 2: All 5 languages at launch**

**Rationale**:
User confirmed preference for "All 5 languages at launch" strategy. This ensures:
- Single unified launch event (no market fragmentation)
- Full South Indian market coverage from Day 1
- Avoids user confusion about language availability
- Aligns with Section 2 (Market Analysis) positioning as "South India's #1 Voice Dating Platform"

**Impact on Requirements**:
- **REQ-IR-003**: Multi-language implementation strategy requires i18n infrastructure from Week 1
- **REQ-DB-002**: All JSONB i18n_content columns must be built from database schema design phase
- **REQ-FS-001**: Language switcher UI component required in all screens from Day 1
- **Translation Workflow**: Professional translators engaged by Week 2 for continuous translation
- **QA Testing**: All test cases must cover all 5 languages (5x testing effort)

**Implementation Approach**:
1. **Week 1**: Flutter i18n framework setup, English as source language
2. **Week 2**: Engage professional translators for Tamil, Telugu, Kannada, Hindi
3. **Week 3-20**: Continuous translation workflow (new UI strings → English master → translate to 4 languages)
4. **Week 17-18**: Beta testing includes native speakers for each language (translation quality feedback)
5. **Week 19**: Final translation quality review before launch

**Budget Impact**:
- Translation cost: ₹50K-80K for ~500 UI strings across 4 languages
- QA cost: 5x testing effort for language coverage (factor into sprint velocity)

**Risk Mitigation**:
- Use Flutter's built-in i18n (intl package) for robust framework
- Maintain English as single source of truth (all translations derived from English)
- Native speaker QA review in Week 17-18 beta to catch cultural/contextual translation errors

---

## Questions Summary

| Question # | Topic | User Decision | Impact |
|-----------|-------|---------------|--------|
| Q1 | MVP Timeline | 16-20 weeks (Recommended) | REQ-IR-001, REQ-IR-002, REQ-IR-006, REQ-IR-007, REQ-IR-008 |
| Q2 | Team Structure | Shadowmarket agent team | REQ-IR-004, REQ-IR-009 |
| Q3 | Language Rollout | All 5 languages at launch | REQ-IR-003, REQ-DB-002, REQ-FS-001 |

---

## Overall Impact Assessment

### Timeline Impact
- **Original PRD**: 30 days (4 weeks)
- **Approved Timeline**: 16-20 weeks
- **Justification**: Expanded scope (5 languages, video calls, AI moderation, live rooms, comprehensive compliance) requires realistic timeline. 16-20 weeks positions competitively vs. Section 2's 22-29 week estimate while ensuring quality.

### Resource Impact
- **Team**: Existing shadowmarket agent team (no new hiring)
- **Budget**: Translation (₹50K-80K), testing (5x language coverage), infrastructure (AWS, APIs)
- **Opportunity Cost**: Team allocated to LIVVLY for 4-5 months

### Scope Clarifications
- **Languages**: All 5 from Day 1 (Tamil, Telugu, Kannada, Hindi, English)
- **Features**: Comprehensive Phase 1 (audio calls, coin economy, payouts) + Phase 2 (video calls, live rooms, AI) + Phase 3 (beta, launch)
- **Quality**: 2 weeks beta testing, launch readiness checklist, security audit

### Risk Mitigation
- **Timeline Risk**: 4-week buffer (16-20 weeks allows flexibility)
- **Quality Risk**: Phase 1 quality gate at Week 12, beta testing Weeks 17-18
- **Team Risk**: Sprint velocity tracking from Week 1 to detect capacity issues early
- **Language Risk**: Professional translators + native speaker QA review

---

## Next Steps

1. **Finalize Team Structure** (Week 1 Day 1):
   - Document shadowmarket agent team roles (Frontend Lead, Backend Lead, DevOps, QA, Product Owner)
   - Establish sprint capacity and velocity baseline
   - Set up communication channels (daily standup, weekly demo, retrospectives)

2. **Set Up i18n Infrastructure** (Week 1):
   - Flutter intl package integration
   - JSONB i18n_content database schema
   - Translation workflow documentation

3. **Engage Translators** (Week 2):
   - Source professional translators for Tamil, Telugu, Kannada, Hindi
   - Establish translation turnaround SLA (48 hours for new strings)
   - Create glossary for domain-specific terms ("coins", "live room", "voice bio")

4. **Weekly Milestone Tracking** (Week 1-20):
   - Set up progress dashboard (Jira/Linear)
   - Define weekly deliverables for Weeks 1-20
   - Schedule demo days every 2 weeks (Fridays 3 PM)

---

**Generated by**: PRD Breakdown Skill
**Section Status**: Completed
**Total Questions Answered**: 3
**Next Section**: 14-cost-estimation

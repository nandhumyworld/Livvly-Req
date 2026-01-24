# Section 15: Success Metrics - Requirements

**Section**: Success Metrics
**Section ID**: 15-success-metrics
**Generated**: 2026-01-24
**Status**: Completed

---

## Requirements Overview

This section defines the key performance indicators (KPIs), success metrics, monitoring dashboards, and launch criteria for the LIVVLY Voice Dating App. These metrics provide quantitative measures of product success, user engagement, creator ecosystem health, and business performance.

**Total Requirements**: 12
**Priority Breakdown**:
- P0 (Critical): 5
- P1 (High): 5
- P2 (Medium): 2

---

## Functional Requirements

### REQ-SM-001: North Star Metric - Monthly Active Minutes (MAM)
**ID**: REQ-SM-001
**Priority**: P0 (Critical)
**Type**: Measurement
**Status**: Approved

**Description**:
The primary success metric (North Star Metric) is **Monthly Active Minutes (MAM)**, defined as the total number of minutes users spend in voice calls, video calls, and live rooms per calendar month.

**Rationale**:
MAM directly correlates with the three pillars of platform success:
1. **User Engagement**: More minutes = higher engagement and retention
2. **Revenue**: Per-minute billing means more minutes = more GMV and platform revenue
3. **Creator Earnings**: More minutes = higher creator payouts and ecosystem health

**MAM Targets (Aggressive Growth)**:

| Month | MAM Target | Calculation Basis | Notes |
|-------|-----------|------------------|-------|
| 1 | 100,000 | 200 DAU x 2 calls/day x 20 min x 30 days | Post-launch Week 20, early adopters, high engagement |
| 2 | 250,000 | 500 DAU x 2 calls/day x 20 min x 30 days | 2.5x growth, word-of-mouth |
| 3 | 1,000,000 | 2,000 DAU x 2 calls/day x 20 min x 30 days | 4x growth, marketing campaigns, video calls launched |
| 4 | 2,000,000 | 4,000 DAU x 2.5 calls/day x 20 min x 30 days | Live rooms feature increases call frequency |
| 5 | 3,500,000 | 7,000 DAU x 2.5 calls/day x 20 min x 30 days | Viral growth phase |
| 6 | 5,000,000 | 10,000 DAU x 2.5 calls/day x 20 min x 30 days | Stable growth, established user base |
| 12 | 15,000,000 | 50,000 DAU x 2 calls/day x 15 min x 30 days | Year 1 target, larger base but lower per-user engagement |

**Measurement Method**:
- Track call duration: `SUM(call_sessions.duration_seconds) / 60` for voice and video calls
- Track room participation: `SUM(room_participants.duration_seconds) / 60` for live rooms
- Aggregate monthly: `SELECT SUM(minutes) FROM (calls + rooms) WHERE month = CURRENT_MONTH`

**Acceptance Criteria**:
- [ ] MAM tracking implemented in analytics dashboard (Week 19)
- [ ] Daily MAM reports sent to team (automated n8n workflow)
- [ ] MAM prominently displayed on main monitoring dashboard
- [ ] MAM alerts configured: Yellow if <80% of target, Red if <60% of target
- [ ] Monthly MAM review in stakeholder meetings

**Dependencies**:
- REQ-FS-007 (Call duration tracking)
- REQ-FS-016 (Live rooms participation tracking)
- REQ-AW-006 (Daily analytics aggregation n8n workflow)

**Notes**:
- MAM is a leading indicator of revenue (1M MAM ≈ ₹5-7L GMV based on ₹5-7 per minute average spend)
- If MAM targets are missed by >20%, trigger investigation: Call quality issues? Creator availability? User acquisition problems?

---

### REQ-SM-002: User Acquisition and Growth Metrics
**ID**: REQ-SM-002
**Priority**: P0 (Critical)
**Type**: Measurement
**Status**: Approved

**Description**:
Track user acquisition and growth metrics to measure platform adoption and scale: Total Downloads, Daily Active Users (DAU), Monthly Active Users (MAU), and DAU/MAU ratio.

**Targets**:

| Metric | Month 1 | Month 3 | Month 6 | Month 12 |
|--------|---------|---------|---------|----------|
| **Total Downloads** | 2,000 | 15,000 | 75,000 | 3,00,000 |
| **DAU** (Daily Active Users) | 200 | 2,000 | 10,000 | 50,000 |
| **MAU** (Monthly Active Users) | 1,000 | 8,000 | 50,000 | 2,00,000 |
| **DAU/MAU Ratio** | 20% | 25% | 20% | 25% |

**Definitions**:
- **Total Downloads**: Cumulative app installs from Google Play + App Store
- **DAU**: Unique users who open the app and perform any action (view profile, call, message) in a 24-hour period
- **MAU**: Unique users who perform any action in a 30-day rolling window
- **DAU/MAU Ratio**: Measures "stickiness" - higher % means users return more frequently

**Acceptance Criteria**:
- [ ] Firebase Analytics configured to track downloads, DAU, MAU (Week 1)
- [ ] DAU/MAU ratio calculated daily and graphed on dashboard
- [ ] Weekly user growth rate tracked: (This Week DAU - Last Week DAU) / Last Week DAU
- [ ] Alerts configured: Yellow if DAU growth <5% week-over-week, Red if negative growth
- [ ] Month 1 target: 2,000 downloads and 200 DAU achieved by Day 30 post-launch

**Dependencies**:
- REQ-TA-013 (Firebase Analytics integration)
- REQ-SM-009 (Monitoring dashboard implementation)

**Notes**:
- DAU/MAU ratio of 20-25% is healthy for social apps (means users return 6-7 days per month on average)
- Month 12 target of 3L downloads aligns with Section 2 market analysis (1-2% of South India addressable market)

---

### REQ-SM-003: Engagement Metrics
**ID**: REQ-SM-003
**Priority**: P1 (High)
**Type**: Measurement
**Status**: Approved

**Description**:
Track user engagement depth to measure product stickiness and session quality: Average Session Duration, Sessions per User per Day, Calls per Active User, Messages per Active User, and Room Participation Rate.

**Targets**:

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| **Avg Session Duration** | > 15 minutes | AVG(session_end_time - session_start_time) |
| **Sessions per User per Day** | > 2 | COUNT(sessions) / COUNT(DISTINCT users) per day |
| **Calls per Active User** | > 1/day | COUNT(call_sessions) / COUNT(active_users) per day |
| **Messages per Active User** | > 10/day | COUNT(messages) / COUNT(active_users) per day |
| **Room Participation Rate** | > 30% | COUNT(users_in_rooms) / COUNT(DAU) per day |

**Rationale**:
- **Session Duration**: 15+ minutes indicates users are deeply engaged (browsing profiles, listening to bios, making calls)
- **Sessions per Day**: 2+ sessions shows users return multiple times (morning/evening usage pattern)
- **Calls per User**: 1+ call/day is the core engagement loop (listening to creators, making connections)
- **Messages**: 10+ messages/day shows active conversation and relationship building
- **Room Participation**: 30%+ shows live rooms feature is sticky and driving community

**Acceptance Criteria**:
- [ ] All engagement metrics tracked in PostgreSQL analytics tables
- [ ] Daily aggregation n8n workflow calculates metrics (REQ-AW-006)
- [ ] Engagement dashboard shows 7-day trend for each metric
- [ ] Alerts configured: Yellow if any metric drops below target for 3 consecutive days
- [ ] Weekly engagement report sent to team with insights (highest/lowest engagement days, feature usage breakdown)

**Dependencies**:
- REQ-DB-009 (Session tracking in database)
- REQ-FS-007 (Call tracking)
- REQ-FS-011 (Messaging system)
- REQ-FS-016 (Live rooms)
- REQ-AW-006 (Daily analytics aggregation)

**Notes**:
- If engagement metrics drop, investigate: App bugs? Call quality issues? Creator availability? Seasonal patterns?
- High engagement (15-min sessions, 2+ sessions/day) is a strong predictor of retention and monetization

---

### REQ-SM-004: Revenue Metrics
**ID**: REQ-SM-004
**Priority**: P0 (Critical)
**Type**: Measurement
**Status**: Approved

**Description**:
Track revenue and monetization metrics to measure business performance: GMV (Gross Merchandise Value), Paying Users %, ARPU (Average Revenue Per User), and ARPPU (Average Revenue Per Paying User).

**Targets**:

| Metric | Month 1 | Month 3 | Month 6 | Month 12 |
|--------|---------|---------|---------|----------|
| **GMV** | ₹16,000 | ₹1,92,000 | ₹18,00,000 | ₹1,00,00,000 |
| **Paying Users %** | 5% | 8% | 8% | 10% |
| **ARPU (All Users)** | ₹16 | ₹24 | ₹36 | ₹50 |
| **ARPPU (Paying Users)** | ₹200 | ₹300 | ₹450 | ₹500 |

**Definitions**:
- **GMV**: Total value of all transactions (coin purchases, call spending, gifts) before platform commission
- **Paying Users %**: (Paying Users / MAU) x 100 - percentage of monthly active users who made any purchase
- **ARPU**: GMV / MAU - average revenue across all users (includes non-payers)
- **ARPPU**: GMV / Paying Users - average revenue from users who paid

**Progressive Monetization Strategy** (drives 5% → 8% → 10% paying user growth):
- **Month 1 (5% paying)**: New users are experimental, trying platform with free coins
- **Month 3 (8% paying)**: Trust built, users understand value, comfortable making purchases
- **Month 6-9 (8% stable)**: Core user base monetizing consistently
- **Month 12 (10% paying)**: VIP subscriptions launched, habitual users, higher spending

**Acceptance Criteria**:
- [ ] Revenue tracking implemented in transactions table
- [ ] Daily GMV calculated and graphed (7-day rolling average)
- [ ] Paying user % tracked weekly (cohort analysis: Week 1 users vs Week 4 users)
- [ ] ARPU and ARPPU calculated monthly and compared to targets
- [ ] Revenue alerts: Yellow if GMV <80% of target, Red if <60%
- [ ] Monthly revenue report sent to stakeholders with breakdown (coin purchases, call revenue, gifts, subscriptions)

**Dependencies**:
- REQ-DB-007 (Transactions table)
- REQ-API-013 (Coin purchase endpoint tracking)
- REQ-API-014 (Call billing tracking)
- REQ-COE-006 (Revenue projections from Section 14)

**Notes**:
- Month 1 GMV (₹16K) is conservative - beta users may spend more if product is compelling
- ARPPU growth from ₹200 (M1) to ₹500 (M12) shows increasing user lifetime value
- If paying user % stagnates at 5%, investigate: Pricing too high? Free coins too generous? Monetization friction?

---

### REQ-SM-005: Creator Ecosystem Metrics
**ID**: REQ-SM-005
**Priority**: P0 (Critical)
**Type**: Measurement
**Status**: Approved

**Description**:
Track creator supply-side metrics to measure ecosystem health: Total Creators, Active Creators (weekly), Average Creator Earning, and Top Creator Earning.

**Targets**:

| Metric | Month 1 | Month 3 | Month 6 | Month 12 |
|--------|---------|---------|---------|----------|
| **Total Creators** (registered) | 20 | 100 | 500 | 2,000 |
| **Active Creators** (weekly earnings >₹500) | 10 | 60 | 300 | 1,200 |
| **Avg Creator Earning** (monthly) | ₹2,000 | ₹5,000 | ₹10,000 | ₹15,000 |
| **Top Creator Earning** (monthly) | ₹10,000 | ₹30,000 | ₹1,00,000 | ₹3,00,000 |

**Creator Recruitment Strategy** (Week 12-16):
- **Target**: Recruit 20 initial creators before beta launch (Week 17)
- **Qualification Criteria**:
  - Age 21+ (mature, responsible creators)
  - Good voice/personality (verified in interview call)
  - 10+ hours/week availability (committed creators)
  - KYC verified (PAN, bank account)
- **Incentives**:
  - ₹500 bonus coins for first 20 creators
  - Featured placement in app (top of home feed for first month)
  - 85% revenue share tier (vs 75% for new creators)
  - Early access to beta features (video calls, live rooms)

**Active Creator Definition**:
- Earned >₹500 in a calendar week (approximately 3-5 hours of calls @ ₹8-25/min)
- This filters out inactive/dormant creators from metrics

**Acceptance Criteria**:
- [ ] Creator recruitment campaign launched Week 12 (target: 20 creators by Week 16)
- [ ] Creator onboarding workflow documented (KYC, profile setup, training on platform features)
- [ ] Creator dashboard shows real-time earnings, call stats, tier status
- [ ] Weekly creator report: Active creators, avg earning, churn rate, new creator signups
- [ ] Creator support channel set up (creators@livvly.com, Telegram group)
- [ ] Top 10 creators recognized monthly (leaderboard, bonus incentives)

**Dependencies**:
- REQ-IR-007 (Beta testing program - creator recruitment timing)
- REQ-CP-* (Creator payout system requirements)
- REQ-FS-019 (Creator dashboard)

**Notes**:
- Month 1 avg earning (₹2K) assumes 10 active creators earning consistently
- Top creator earning (₹3L/month by M12) creates aspirational goal for other creators
- If active creator % drops below 50% (e.g., 500 total, <250 active), investigate: Call demand insufficient? Creator experience issues?

---

### REQ-SM-006: User Retention Metrics
**ID**: REQ-SM-006
**Priority**: P1 (High)
**Type**: Measurement
**Status**: Approved

**Description**:
Track user retention rates to measure product stickiness and long-term engagement: D7 Retention (7-day), D30 Retention (30-day), and Cohort Retention Analysis.

**Targets (Industry Standard for Social Apps)**:

| Retention Metric | Target | Definition |
|-----------------|--------|------------|
| **D7 Retention** | 40% | % of users who return to app 7 days after first install |
| **D30 Retention** | 20% | % of users who return to app 30 days after first install |
| **D90 Retention** | 10% | % of users who return to app 90 days after first install |

**Calculation Example**:
- 100 users install app on Day 0
- 40 users return on Day 7 → D7 Retention = 40%
- 20 users return on Day 30 → D30 Retention = 20%

**Rationale**:
- **D7 40%**: Typical for social/dating apps. 4 out of 10 new users return after first week if product is compelling.
- **D30 20%**: 2 out of 10 users become "core users" who stick around for a month. These are high-value, likely to monetize.
- **D90 10%**: 1 out of 10 users become long-term users. These drive majority of revenue and engagement.

**Cohort Analysis**:
Track retention by weekly cohorts to identify trends:
- Week 1 Cohort: Users acquired Week 20 (launch week)
- Week 2 Cohort: Users acquired Week 21
- Compare cohorts: Is Week 2 retention better than Week 1? (indicates product improvements)

**Acceptance Criteria**:
- [ ] Retention tracking implemented in Firebase Analytics (cohort analysis)
- [ ] D7, D30, D90 retention calculated for each weekly cohort
- [ ] Retention dashboard shows cohort retention curves (graph of Day 0, D1, D3, D7, D14, D30)
- [ ] Alerts configured: Yellow if D7 <35%, Red if D7 <30%
- [ ] Monthly retention report identifies: Best/worst cohorts, retention drivers (features used by retained users vs churned users)

**Dependencies**:
- REQ-TA-013 (Firebase Analytics integration)
- REQ-SM-007 (Churn analysis to understand why users leave)

**Notes**:
- If D7 retention is <30%, indicates serious product-market fit issues (investigate: Onboarding too complex? Call quality poor? Not enough creators?)
- Retained users (D30+) are 5-10x more likely to become paying users than churned users

---

### REQ-SM-007: Churn and Attrition Metrics
**ID**: REQ-SM-007
**Priority**: P1 (High)
**Type**: Measurement
**Status**: Approved

**Description**:
Track user and creator churn rates to identify attrition patterns and reasons for leaving the platform.

**Definitions**:
- **User Churn**: % of MAU from previous month who did not return this month
- **Creator Churn**: % of active creators from previous month who did not earn >₹500 this month
- **Churn Cohort Analysis**: Why did users/creators leave? (survey churned users, analyze behavior before churn)

**Targets**:

| Metric | Target | Definition |
|--------|--------|------------|
| **Monthly User Churn** | <25% | % of last month's MAU who didn't return this month |
| **Creator Churn (30-day)** | <15% | % of last month's active creators who didn't earn >₹500 this month |
| **Voluntary Churn** | <20% | Users who explicitly deleted account or uninstalled |
| **Involuntary Churn** | <5% | Users banned for policy violations |

**Churn Reduction Strategies**:
- **Win-back campaigns**: Email/push notifications to users who haven't opened app in 7 days
- **Creator support**: Reach out to creators whose earnings dropped 50%+ in a month
- **Exit surveys**: Ask churned users why they left (too expensive? not enough matches? technical issues?)

**Acceptance Criteria**:
- [ ] Churn calculation automated: `(Last Month MAU - This Month Returning Users) / Last Month MAU`
- [ ] Churn reasons categorized: Price, Quality, Matches, Technical, Safety
- [ ] Weekly churn report identifies: High-churn cohorts, common churn patterns
- [ ] Win-back n8n workflow sends notifications to 7-day inactive users
- [ ] Creator retention program launched (monthly check-ins with creators earning <₹2K)

**Dependencies**:
- REQ-SM-006 (Retention metrics)
- REQ-AW-* (N8n workflows for win-back campaigns)

**Notes**:
- 25% monthly churn is normal for new apps (75% retention month-over-month)
- High creator churn (>20%) is red flag - indicates earnings too low, not enough demand for calls

---

### REQ-SM-008: Quality and Safety Metrics
**ID**: REQ-SM-008
**Priority**: P0 (Critical)
**Type**: Measurement
**Status**: Approved

**Description**:
Track platform health, user safety, and content quality metrics to ensure LIVVLY remains a safe, high-quality dating platform.

**Safety Dashboard Metrics**:

| Metric | Target | Definition | Escalation Threshold |
|--------|--------|------------|---------------------|
| **Report Rate** | <2% | % of users who submitted reports (harassment, fake profiles, etc.) | >5% (Red alert) |
| **Block Rate** | <5% | % of users who blocked another user | >10% (Yellow alert) |
| **Call Quality Score** | >4.0/5 | Avg user rating after calls (1-5 stars) | <3.5 (Red alert) |
| **Moderation Response Time** | <2 hours | Avg time from report submission to human review | >4 hours (Yellow alert) |
| **Safety Incidents (Serious)** | 0 | Serious incidents (threats, harassment escalation, legal issues) | 1+ (Red alert, immediate escalation) |
| **Fake Profile Rate** | <1% | % of profiles flagged as fake/spam by AI moderation | >3% (Yellow alert) |

**Safety Incident Categories**:
- **Serious**: Physical threats, extortion, doxxing, child safety issues → Immediate escalation to legal team
- **Moderate**: Sexual harassment, hate speech, scams → Review within 2 hours, account suspension
- **Minor**: Spam, fake profiles, minor guideline violations → Review within 24 hours, warning or removal

**Acceptance Criteria**:
- [ ] Safety dashboard implemented with real-time metrics
- [ ] Report submission flow in app (REQ-FS-023: Safety features)
- [ ] Moderation queue for human review (Grievance Officer from REQ-LC-008)
- [ ] Automated alerts: Email + SMS to Grievance Officer if serious incident reported
- [ ] Weekly safety report: Total reports, resolution time, incident breakdown, repeat offenders
- [ ] Monthly safety review: Trends, policy updates, moderation training needs

**Dependencies**:
- REQ-FS-023 (Safety features: Report, block, emergency contact)
- REQ-LC-008 (Grievance Officer appointment and 72h response SLA)
- REQ-FS-024 (AI moderation for fake profiles and content filtering)

**Notes**:
- Safety is critical for dating platforms - even 1 serious incident can damage brand reputation
- Call quality score <4.0 indicates technical issues (Agora latency, audio quality) or bad creator experiences

---

### REQ-SM-009: Real-Time Monitoring Dashboard
**ID**: REQ-SM-009
**Priority**: P1 (High)
**Type**: Implementation
**Status**: Approved

**Description**:
Implement a real-time monitoring dashboard (admin panel) that displays all critical metrics in a single view for daily operations and decision-making.

**Dashboard Layout** (inspired by PRD's ASCII mockup):

**Top Row - Key Metrics (4 Cards)**:
- DAU: Current count, % change vs yesterday
- GMV: Today's GMV, % change vs last week
- Active Calls: Current concurrent calls, peak today
- Active Creators: Creators online now, total creators

**Second Row - 7-Day Revenue Trend** (Line Graph):
- Daily GMV for past 7 days
- Hover shows: Date, GMV, Top creator, Top spender

**Third Row - Leaderboards** (2 Tables):
- **Top 10 Creators** (This Month): Creator name, earnings, calls completed, avg rating
- **Top 10 Spenders** (This Month): User ID (anonymized), total spend, calls made, favorite creators

**Fourth Row - Alerts and Health** (2 Panels):
- **Active Alerts**: Red/Yellow alerts from all metric thresholds (e.g., "D7 Retention: 32% (Target: 40%)")
- **System Health**: API uptime, database status, Agora status, Redis status

**Acceptance Criteria**:
- [ ] Dashboard implemented in admin panel (separate from user-facing app)
- [ ] All metrics refresh every 60 seconds (real-time updates)
- [ ] Dashboard accessible only to authorized team members (role-based access)
- [ ] Mobile-responsive design (viewable on tablets/phones for on-the-go monitoring)
- [ ] Export functionality: Download metrics as CSV for offline analysis
- [ ] Custom date range selector (Today, Last 7 Days, Last 30 Days, Custom)

**Dependencies**:
- REQ-SM-001 to REQ-SM-008 (all metrics to be displayed)
- REQ-TA-015 (Monitoring and alerting infrastructure)
- REQ-AW-006 (Daily analytics aggregation)

**Notes**:
- Dashboard is for internal team use only (shadowmarket team, stakeholders)
- Consider using tools like Metabase, Grafana, or custom Next.js admin panel

---

### REQ-SM-010: Launch Success Criteria (Week 20)
**ID**: REQ-SM-010
**Priority**: P1 (High)
**Type**: Evaluation
**Status**: Approved

**Description**:
Define clear success criteria to evaluate if the MVP launch at Week 20 achieved product-market fit and justifies continued investment.

**Multi-Metric Success Criteria**:

| Metric | Success Target | Measurement Period | Rationale |
|--------|---------------|-------------------|-----------|
| **Downloads** | ≥2,000 | Week 20 (launch week) | Validates marketing effectiveness and initial interest |
| **D7 Retention** | ≥40% | Week 21 (7 days post-launch) | Validates product stickiness and user value |
| **Paid Transactions** | ≥100 | Week 20-21 (first 2 weeks) | Validates monetization and willingness to pay |
| **Bug Report Rate** | <3% | Week 20-21 | <3% of users reporting bugs = quality launch |
| **Active Creators** | ≥15 | Week 20-21 | At least 15 of 20 seed creators actively taking calls |
| **Net Promoter Score (NPS)** | ≥50 | Week 21 survey | NPS ≥50 = users likely to recommend app to friends |

**Success Outcome**: If **≥4 out of 6 metrics** hit targets → **Successful MVP Launch**
- Proceed to Month 2 with continued development and marketing
- Allocate Month 6+ budget for scaling

**Partial Success Outcome**: If **2-3 out of 6 metrics** hit targets → **Mixed Results**
- Conduct user research to identify issues (survey churned users, interview power users)
- Iterate on product based on feedback
- Consider pivoting features or repositioning

**Failure Outcome**: If **<2 metrics** hit targets → **Failed Launch**
- Critical product-market fit issues
- Pause new feature development
- Deep dive analysis: Wrong target market? Poor execution? Technical issues?
- Decision point: Pivot, iterate, or shut down

**Acceptance Criteria**:
- [ ] Launch week (Week 20) metrics tracked hourly
- [ ] Week 21 post-launch review meeting scheduled (entire team + stakeholders)
- [ ] Post-launch survey sent to all Week 20 signups (NPS + feedback questions)
- [ ] Success criteria scorecard created and shared with stakeholders
- [ ] Go/No-Go decision made by end of Week 21 (continue vs pivot)

**Dependencies**:
- REQ-IR-008 (Launch readiness checklist from Section 13)
- All metrics in REQ-SM-001 to REQ-SM-008

**Notes**:
- Week 20 is "make or break" moment - success criteria determines future of project
- Even if launch is "Partial Success", learnings are valuable for iteration

---

### REQ-SM-011: Monthly Active Minutes (MAM) Breakdown and Analysis
**ID**: REQ-SM-011
**Priority**: P2 (Medium)
**Type**: Analysis
**Status**: Approved

**Description**:
Break down Monthly Active Minutes (MAM) by call type (voice, video, live rooms) and user segment to understand engagement patterns and optimize for MAM growth.

**MAM Breakdown by Call Type**:

| Call Type | Expected % of MAM (Month 6) | Avg Duration | Monetization |
|-----------|---------------------------|-------------|-------------|
| **Voice Calls (1-on-1)** | 50-60% | 15-20 min | High (₹8-25/min) |
| **Video Calls (1-on-1)** | 20-30% | 10-15 min | Higher (₹15-40/min) |
| **Live Rooms (group)** | 10-20% | 30-60 min | Lower (free or subscription) |

**MAM by User Segment**:
- **Power Users** (top 10% by MAM): Contribute 40-50% of total MAM
- **Regular Users** (next 30%): Contribute 30-40% of total MAM
- **Casual Users** (bottom 60%): Contribute 10-20% of total MAM

**Analysis Questions**:
- Which call type drives highest MAM per user?
- Which user segment has highest retention? (hypothesis: power users have 70%+ D30 retention)
- Which time of day has peak MAM? (optimize creator availability for peak hours)
- Which language has highest MAM per user? (optimize marketing for high-engagement languages)

**Acceptance Criteria**:
- [ ] MAM breakdown query implemented: `SELECT call_type, SUM(duration_minutes) FROM calls GROUP BY call_type`
- [ ] User segmentation by MAM: Power (top 10%), Regular (30%), Casual (60%)
- [ ] Weekly MAM analysis report: Breakdown by call type, time of day, language, user segment
- [ ] MAM optimization strategies identified (e.g., "Video calls have 2x ARPPU but only 20% of MAM → promote video calling feature")

**Dependencies**:
- REQ-SM-001 (MAM tracking)
- REQ-DB-007 (Call sessions table with call_type and duration)

**Notes**:
- Understanding MAM breakdown helps prioritize features (e.g., if live rooms contribute <5% MAM, deprioritize room features)
- Power users are high-value - focus retention efforts on top 10% of users by MAM

---

### REQ-SM-012: Automated Reporting and Alerting
**ID**: REQ-SM-012
**Priority**: P2 (Medium)
**Type**: Process
**Status**: Approved

**Description**:
Implement automated reporting and alerting workflows to ensure team is informed of metric changes, anomalies, and threshold violations.

**Daily Reports** (Automated n8n Workflow):
- **Morning Report** (9 AM): Yesterday's DAU, GMV, MAM, active creators, top creator earnings
- Sent to: shadowmarket team email + Slack channel

**Weekly Reports** (Every Monday 10 AM):
- **Weekly Performance Summary**:
  - User growth: New downloads, DAU trend, retention (D7 for last week's cohort)
  - Revenue: GMV, paying user %, ARPU, ARPPU
  - Creator ecosystem: New creators, active creators, avg earnings
  - Engagement: MAM, call count, session duration
- Sent to: shadowmarket team + stakeholders

**Monthly Reports** (1st of each month):
- **Monthly Business Review**:
  - All KPIs vs targets (color-coded: Green if met, Yellow if 80-99%, Red if <80%)
  - Cohort retention analysis
  - Creator earnings distribution (top 10%, median, bottom 10%)
  - Revenue breakdown (coin purchases, call revenue, gifts, subscriptions)
  - Safety report (incidents, moderation stats)
- Sent to: Leadership team + investors

**Real-Time Alerts** (n8n + Slack/Email):
- **Red Alerts** (immediate notification):
  - D7 retention <30%
  - Daily GMV <60% of target
  - Call quality score <3.5
  - Serious safety incident reported
  - System downtime >5 minutes
- **Yellow Alerts** (notification within 1 hour):
  - D7 retention 30-35%
  - Daily GMV 60-80% of target
  - Creator churn >20% this month
  - Bug report rate >3%

**Acceptance Criteria**:
- [ ] N8n workflows configured for daily, weekly, monthly reports
- [ ] Email templates created for each report type
- [ ] Slack integration for alerts (#livvly-alerts channel)
- [ ] Alert thresholds configured and tested
- [ ] Report opt-in/opt-out mechanism (team members can subscribe to specific reports)

**Dependencies**:
- REQ-AW-006 (Daily analytics aggregation n8n workflow)
- REQ-SM-009 (Monitoring dashboard - source of metrics)
- All metric requirements (REQ-SM-001 to REQ-SM-008)

**Notes**:
- Automated reporting saves 5-10 hours/week of manual metric compilation
- Real-time alerts enable fast response to critical issues (e.g., system downtime, safety incidents)

---

## Requirements Summary Table

| Requirement ID | Title | Priority | Type | Status |
|---------------|-------|----------|------|--------|
| REQ-SM-001 | North Star Metric - Monthly Active Minutes (MAM) | P0 | Measurement | Approved |
| REQ-SM-002 | User Acquisition and Growth Metrics | P0 | Measurement | Approved |
| REQ-SM-003 | Engagement Metrics | P1 | Measurement | Approved |
| REQ-SM-004 | Revenue Metrics | P0 | Measurement | Approved |
| REQ-SM-005 | Creator Ecosystem Metrics | P0 | Measurement | Approved |
| REQ-SM-006 | User Retention Metrics | P1 | Measurement | Approved |
| REQ-SM-007 | Churn and Attrition Metrics | P1 | Measurement | Approved |
| REQ-SM-008 | Quality and Safety Metrics | P0 | Measurement | Approved |
| REQ-SM-009 | Real-Time Monitoring Dashboard | P1 | Implementation | Approved |
| REQ-SM-010 | Launch Success Criteria (Week 20) | P1 | Evaluation | Approved |
| REQ-SM-011 | MAM Breakdown and Analysis | P2 | Analysis | Approved |
| REQ-SM-012 | Automated Reporting and Alerting | P2 | Process | Approved |

---

## Cross-References

**Related Sections**:
- Section 02: Market Analysis (user growth targets, market penetration)
- Section 05: Feature Specifications (engagement features that drive MAM)
- Section 10: Creator Payout System (creator earnings metrics)
- Section 11: N8N Automation Workflows (REQ-AW-006: Daily analytics aggregation)
- Section 13: Implementation Roadmap (Week 12-16 creator recruitment, Week 20 launch)
- Section 14: Cost Estimation (revenue projections, break-even timeline)

**External Dependencies**:
- Firebase Analytics (DAU, MAU, retention tracking)
- Sentry (error tracking, bug report rate)
- Agora SDK (call quality metrics)
- N8n (automated reporting workflows)
- Admin dashboard tool (Metabase, Grafana, or custom)

---

## Notes

**Critical Success Metrics Hierarchy**:
1. **North Star**: Monthly Active Minutes (MAM) - drives everything
2. **User Growth**: DAU, MAU, downloads - measures scale
3. **Engagement**: Session duration, calls per user - measures stickiness
4. **Monetization**: GMV, paying user %, ARPU/ARPPU - measures revenue
5. **Creator Health**: Active creators, avg earnings - measures supply-side
6. **Retention**: D7, D30 - measures product-market fit
7. **Safety**: Report rate, moderation response time - measures platform health

**Data-Driven Decision Making**:
- All product decisions should reference metrics (e.g., "Should we add feature X?" → "Will it increase MAM? Will it improve D7 retention?")
- Weekly metric reviews to identify trends and course-correct quickly
- A/B testing for major features to measure impact on metrics

**Week 20 Launch is Critical**:
- Success criteria (REQ-SM-010) determines fate of project
- If launch succeeds (4+ out of 6 metrics hit targets), proceed with confidence
- If launch fails (<2 metrics hit), deep investigation and potential pivot required

---

**Generated by**: PRD Breakdown Skill
**Section Status**: Completed
**Next Section**: 09-coin-economy-design (remaining section)

# Requirements: Implementation Roadmap

**Section**: 13-implementation-roadmap
**Source Lines**: 2706-2825
**Generated**: 2026-01-20
**Status**: Complete

---

> **Note**: PRD references FastAPI and n8n, but actual stack is Node.js + Supabase per DD-TA-001 and DD-TA-005.

---

## Phase 1: MVP (Days 1-30)

### REQ-IR-001: Week 1 - Foundation Setup
**Priority**: P0 (Critical)
**Type**: Implementation

**Description**: Complete project foundation including Flutter setup, Supabase backend, and basic auth flow.

**Rationale**: Foundation must be solid before feature development.

**Acceptance Criteria**:
- Day 1-2: Flutter project setup with folder structure
- Day 1-2: Supabase project creation and configuration
- Day 3-4: Database schema implementation (per Section 07)
- Day 3-4: RLS policies for core tables
- Day 5-6: Firebase Auth + Supabase integration
- Day 7: Basic user registration flow working end-to-end
- Environment configs: dev, staging, prod

**Dependencies**: None
**Related Design Decisions**: DD-TA-001, DD-TA-002, DD-FS-002

**Notes**: Stack: Flutter + Node.js/Supabase (not FastAPI per PRD).

---

### REQ-IR-002: Week 2 - Core Features
**Priority**: P0 (Critical)
**Type**: Implementation

**Description**: Implement user profiles and payment/wallet infrastructure.

**Rationale**: Core user journey and monetization foundation.

**Acceptance Criteria**:
- Day 8-9: Profile creation UI and API
- Day 8-9: Profile editing with image upload
- Day 10-11: Coin wallet database and APIs
- Day 10-11: Razorpay integration (Edge Function)
- Day 12-13: End-to-end recharge flow
- Day 12-13: Payment webhook handling
- Day 14: Integration testing and bug fixes

**Dependencies**: REQ-IR-001
**Related Design Decisions**: DD-FS-006, DD-API-004

**Notes**: Include both Razorpay and Play Billing preparation.

---

### REQ-IR-003: Week 3 - Communication Features
**Priority**: P0 (Critical)
**Type**: Implementation

**Description**: Implement Agora-based audio calling with coin billing.

**Rationale**: Core value proposition - voice calls between users and creators.

**Acceptance Criteria**:
- Day 15-16: Agora SDK integration in Flutter
- Day 15-16: Agora token generation Edge Function
- Day 17-18: 1-to-1 audio call UI and flow
- Day 17-18: Call state management
- Day 19-20: Call billing logic (end-of-call)
- Day 19-20: Coin deduction and diamond credit
- Day 21: Push notification integration (FCM)

**Dependencies**: REQ-IR-002
**Related Design Decisions**: DD-FS-005, DD-API-002, DD-API-005

**Notes**: Video calls deferred to Week 11-12 per roadmap.

---

### REQ-IR-004: Week 4 - Monetization Features
**Priority**: P0 (Critical)
**Type**: Implementation

**Description**: Implement gift system and basic creator dashboard.

**Rationale**: Complete monetization loop and creator visibility.

**Acceptance Criteria**:
- Day 22-23: Gift catalog UI and selection
- Day 22-23: Gift sending API with transaction logging
- Day 24-25: Creator dashboard home screen
- Day 24-25: Earnings summary (today, week, total)
- Day 26-27: Diamond wallet implementation
- Day 26-27: Pending vs available balance display
- Day 28-30: Full testing cycle and polish

**Dependencies**: REQ-IR-003
**Related Design Decisions**: DD-DB-002, DD-DB-003

**Notes**: Advanced creator analytics deferred to Phase 2.

---

### REQ-IR-005: MVP Deliverables Checklist
**Priority**: P0 (Critical)
**Type**: Milestone

**Description**: Define MVP completion criteria for Day 30 checkpoint.

**Rationale**: Clear success criteria for MVP milestone.

**Acceptance Criteria**:
- [ ] Android APK signed for internal testing
- [ ] User registration and phone verification
- [ ] Profile creation with photo upload
- [ ] Coin purchase via Razorpay (min ₹49 package)
- [ ] 1-to-1 audio calls working
- [ ] Call billing deducting coins correctly
- [ ] Gift sending between users
- [ ] Creator earnings visible in dashboard
- [ ] Basic push notifications
- [ ] No critical bugs blocking core flows

**Dependencies**: REQ-IR-001 through REQ-IR-004
**Related Design Decisions**: None

**Notes**: Internal testing only; not Play Store.

---

## Phase 2: Beta Launch (Days 31-60)

### REQ-IR-006: Week 5-6 - Polish & KYC
**Priority**: P0 (Critical)
**Type**: Implementation

**Description**: UI/UX improvements and full KYC/payout system.

**Rationale**: Production-ready quality and creator payment enablement.

**Acceptance Criteria**:
- UI/UX polish based on internal feedback
- Performance optimization (load times < 2s)
- KYC submission flow in app
- PAN verification API integration (Karza/Digio)
- Bank account verification (penny drop)
- Withdrawal request flow
- Name matching validation
- KYC status tracking UI

**Dependencies**: REQ-IR-005
**Related Design Decisions**: DD-TA-005

**Notes**: Direct Edge Function automation (not n8n).

---

### REQ-IR-007: Week 7-8 - Beta Testing
**Priority**: P0 (Critical)
**Type**: Implementation

**Description**: Soft launch with limited users and creators.

**Rationale**: Real-world validation before public launch.

**Acceptance Criteria**:
- Play Store internal testing track published
- 100 beta users onboarded
- 10 creators onboarded and verified
- Feedback collection mechanism (in-app)
- Bug tracking and prioritization
- Daily payout automation running (pg_cron)
- Balance settlement automation running
- Fraud detection rules active

**Dependencies**: REQ-IR-006
**Related Design Decisions**: DD-TA-005

**Notes**: Creator recruitment via personal outreach initially.

---

### REQ-IR-008: Beta Deliverables Checklist
**Priority**: P0 (Critical)
**Type**: Milestone

**Description**: Define beta completion criteria for Day 60 checkpoint.

**Rationale**: Clear success criteria for beta milestone.

**Acceptance Criteria**:
- [ ] Play Store internal testing track live
- [ ] KYC system fully functional
- [ ] At least one successful payout completed
- [ ] 100+ active beta users
- [ ] 10+ verified creators
- [ ] Automated workflows operational
- [ ] User feedback incorporated
- [ ] Critical bugs resolved

**Dependencies**: REQ-IR-006, REQ-IR-007
**Related Design Decisions**: None

**Notes**: Go/no-go decision for public launch.

---

## Phase 3: Public Launch (Days 61-90)

### REQ-IR-009: Week 9-10 - Launch Preparation
**Priority**: P0 (Critical)
**Type**: Implementation

**Description**: Prepare for public Play Store launch with marketing.

**Rationale**: Market entry preparation.

**Acceptance Criteria**:
- App Store Optimization (ASO) completed
- Play Store listing created (screenshots, video, description)
- Creator recruitment campaign launched
- Influencer partnerships identified (Tamil creators)
- Legal compliance final check
- Privacy policy and ToS in app
- Rating/review prompt implemented
- Crashlytics/analytics verified

**Dependencies**: REQ-IR-008
**Related Design Decisions**: None

**Notes**: Tamil Nadu market focus initially.

---

### REQ-IR-010: Week 11-12 - Launch & Scale
**Priority**: P0 (Critical)
**Type**: Implementation

**Description**: Execute public launch and add video calls.

**Rationale**: Market launch and feature expansion.

**Acceptance Criteria**:
- Public Play Store production release
- Performance marketing execution (₹50K budget)
- Daily metrics monitoring dashboard
- Scaling infrastructure as needed
- Video call feature implementation
- Video call billing (1.5x audio rate)
- Target: 10,000 downloads
- Target: 50+ active creators
- Target: ₹5L+ monthly GMV

**Dependencies**: REQ-IR-009
**Related Design Decisions**: DD-FS-005

**Notes**: Video calls added in this phase per roadmap.

---

### REQ-IR-011: Launch Deliverables Checklist
**Priority**: P0 (Critical)
**Type**: Milestone

**Description**: Define public launch success criteria for Day 90 checkpoint.

**Rationale**: Clear success criteria for launch milestone.

**Acceptance Criteria**:
- [ ] Public Play Store listing live
- [ ] 10,000+ total downloads
- [ ] 1,000+ DAU
- [ ] 50+ verified creators
- [ ] ₹5L+ monthly GMV (coin purchases)
- [ ] Video calls feature live
- [ ] App rating > 4.0
- [ ] Crash-free rate > 99%

**Dependencies**: REQ-IR-010
**Related Design Decisions**: None

**Notes**: KPIs for Series A readiness.

---

## Phase Tracking Requirements

### REQ-IR-012: Progress Tracking System
**Priority**: P1 (High)
**Type**: Process

**Description**: Implement tracking for roadmap progress.

**Rationale**: Visibility into development progress for stakeholders.

**Acceptance Criteria**:
- Weekly status updates documented
- Blocker tracking and escalation
- Milestone dates tracked vs actual
- Resource allocation visibility
- Demo recordings for major features
- Decision log maintained

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Can use Notion, Linear, or similar tool.

---

### REQ-IR-013: Risk Management
**Priority**: P1 (High)
**Type**: Process

**Description**: Identify and mitigate roadmap risks.

**Rationale**: Proactive risk management prevents delays.

**Acceptance Criteria**:
- Risk register maintained
- Key risks identified:
  - Payment gateway approval delays
  - Agora SDK integration complexity
  - Creator acquisition
  - Play Store review rejection
- Mitigation plans documented
- Weekly risk review

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Update risk register weekly.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-IR-001 | Week 1 - Foundation Setup | P0 | Implementation |
| REQ-IR-002 | Week 2 - Core Features | P0 | Implementation |
| REQ-IR-003 | Week 3 - Communication Features | P0 | Implementation |
| REQ-IR-004 | Week 4 - Monetization Features | P0 | Implementation |
| REQ-IR-005 | MVP Deliverables Checklist | P0 | Milestone |
| REQ-IR-006 | Week 5-6 - Polish & KYC | P0 | Implementation |
| REQ-IR-007 | Week 7-8 - Beta Testing | P0 | Implementation |
| REQ-IR-008 | Beta Deliverables Checklist | P0 | Milestone |
| REQ-IR-009 | Week 9-10 - Launch Preparation | P0 | Implementation |
| REQ-IR-010 | Week 11-12 - Launch & Scale | P0 | Implementation |
| REQ-IR-011 | Launch Deliverables Checklist | P0 | Milestone |
| REQ-IR-012 | Progress Tracking System | P1 | Process |
| REQ-IR-013 | Risk Management | P1 | Process |

**Total Requirements**: 13
**P0 (Critical)**: 11
**P1 (High)**: 2

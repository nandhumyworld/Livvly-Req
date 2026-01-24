# LIVVLY Voice Dating App - Dependency Graph

**Generated:** 2026-01-24
**Total Requirements:** 203
**Total Dependencies:** 247 cross-section references

---

## Overview

This document visualizes dependencies between requirements across all 15 sections of the LIVVLY PRD breakdown. Use this to:
- **Understand implementation order** (which requirements must be built first)
- **Identify critical path** (P0 requirements that block others)
- **Detect circular dependencies** (requirements that reference each other)
- **Plan sprints** (group requirements with no mutual dependencies for parallel work)

---

## Critical Path (P0 Requirements Only)

These requirements form the **minimum viable implementation path**. Build in this order:

### Stage 1: Foundation Setup (Week 1-2)
```
REQ-TA-001: Tech Stack Selection
    └─> REQ-TA-002: Repository Structure (Monorepo)
        └─> REQ-TA-003: Authentication & Authorization (Firebase + JWT)
            ├─> REQ-DB-001: Database Setup (PostgreSQL 15)
            └─> REQ-API-003: Auth Endpoints (POST /api/v1/auth/*)
```

**Dependencies:** None (start here)
**Blocks:** All other requirements (foundation)
**Estimated Time:** 2 weeks

---

### Stage 2: Database Layer (Week 2-4)
```
REQ-DB-001: Database Setup
    ├─> REQ-DB-002: users table + coin_wallets table
    │   └─> REQ-CE-005: Three-tier balance tracking (purchased/bonus/promo)
    │       └─> REQ-CE-011: Daily login bonus program
    │
    ├─> REQ-DB-003: calls table
    │   └─> REQ-FS-008: Voice call initiation
    │
    ├─> REQ-DB-004: creator_performance table
    │   └─> REQ-RM-001: 75-85% tiered revenue model
    │       ├─> REQ-CE-002: Dual revenue split (75-85% calls, 45% gifts)
    │       └─> REQ-API-002: Dynamic revenue calculation
    │
    ├─> REQ-DB-005: gifts table (JSONB i18n)
    │   └─> REQ-CE-004: Gift pricing (10 gifts, 45% revenue)
    │       └─> REQ-FS-014: Gift sending UI
    │
    └─> REQ-DB-006: coin_packages seed data
        └─> REQ-CE-001: Coin package pricing (6 packages)
            └─> REQ-API-001: GET /api/v1/wallet/packages
```

**Dependencies:** REQ-TA-001 (Database choice)
**Blocks:** All API and feature requirements
**Estimated Time:** 2 weeks

---

### Stage 3: Core APIs (Week 4-7)
```
REQ-API-001: Wallet & Package APIs
    ├─ Depends on: REQ-DB-002 (coin_wallets), REQ-DB-006 (coin_packages)
    └─> Enables: REQ-FS-001 (Coin purchase flow)

REQ-API-002: Billing & Revenue APIs
    ├─ Depends on: REQ-DB-003 (calls), REQ-DB-004 (creator_performance)
    ├─ Depends on: REQ-CE-002 (Dual revenue model)
    └─> Enables: REQ-FS-008 (Voice calls with billing)

REQ-API-003: Auth Endpoints
    ├─ Depends on: REQ-TA-003 (Firebase Auth + JWT)
    └─> Enables: REQ-FS-001 (Onboarding flow), REQ-LC-001 (Age verification)

REQ-API-007: Call Management APIs
    ├─ Depends on: REQ-API-002 (Billing), REQ-TA-006 (Agora SDK)
    └─> Enables: REQ-FS-009 (In-call features)
```

**Dependencies:** Database layer complete (Stage 2)
**Blocks:** Frontend features
**Estimated Time:** 3 weeks

---

### Stage 4: Frontend Features (Week 7-14)
```
REQ-FS-001: Onboarding Flow (4 steps)
    ├─ Depends on: REQ-API-003 (Auth), REQ-LC-001 (Age verification)
    └─> Enables: REQ-FS-002 (Profile creation)
        └─> Enables: REQ-FS-005 (Discover screen)

REQ-FS-008: Voice Call Initiation
    ├─ Depends on: REQ-API-007 (Call APIs), REQ-TA-006 (Agora SDK)
    ├─ Depends on: REQ-CE-002 (Revenue model), REQ-CE-003 (Creator call rates)
    └─> Enables: REQ-FS-009 (In-call features)

REQ-FS-014: Virtual Gift Sending
    ├─ Depends on: REQ-API-001 (Wallet), REQ-DB-005 (gifts)
    ├─ Depends on: REQ-CE-004 (Gift pricing 45%)
    └─> Enables: REQ-FS-015 (Gift animations)
```

**Dependencies:** Core APIs complete (Stage 3)
**Blocks:** User testing, launch
**Estimated Time:** 7 weeks

---

### Stage 5: Payment Integration (Week 10-13)
```
REQ-RM-002: Razorpay Checkout Integration
    ├─ Depends on: REQ-API-001 (Wallet APIs)
    └─> Enables: REQ-FS-001 (Coin purchase)
        └─> Enables: REQ-CE-001 (Coin packages live)

REQ-CP-001: Razorpay X Payout Integration
    ├─ Depends on: REQ-DB-004 (creator_performance)
    ├─ Depends on: REQ-API-002 (Revenue calculation)
    └─> Enables: REQ-CP-003 (T+1 automated payouts)
        └─> Enables: REQ-CP-005 (Payout dashboard)
```

**Dependencies:** Wallet APIs, Revenue calculation
**Blocks:** Creator earnings, monetization
**Estimated Time:** 3 weeks (parallel with Stage 4)

---

### Stage 6: Compliance & Safety (Week 12-15)
```
REQ-LC-001: Age Verification (Karza KYC)
    ├─ Depends on: REQ-API-003 (Auth flow)
    └─> Blocks: REQ-FS-001 (Onboarding - mandatory verification)

REQ-LC-004: Safety Center UI
    ├─ Depends on: REQ-FS-001 (User logged in)
    └─> Enables: REQ-LC-005 (Block/Report), REQ-LC-006 (Incident reporting)

REQ-LC-007: Content Moderation (3-tier)
    ├─ Depends on: REQ-DB-001 (moderation tables)
    └─> Enables: REQ-AW-004 (N8N moderation queue workflow)
```

**Dependencies:** User management, APIs
**Blocks:** Legal launch
**Estimated Time:** 3 weeks (parallel with Stage 4)

---

### Stage 7: Automation (Week 14-16)
```
REQ-AW-001: N8N Setup (Self-hosted AWS EC2)
    ├─ Depends on: REQ-TA-001 (Infrastructure)
    └─> Enables: All workflow requirements

REQ-AW-002: Daily Payout Processing Workflow
    ├─ Depends on: REQ-CP-001 (Razorpay X), REQ-CP-003 (T+1 logic)
    └─> Automates: Creator payouts

REQ-AW-004: Moderation Queue Workflow
    ├─ Depends on: REQ-LC-007 (Moderation system)
    └─> Automates: Content review

REQ-AW-006: Analytics Reporting Workflow
    ├─ Depends on: REQ-DB-001 (All tables populated)
    └─> Generates: Daily reports (REQ-CE-010 analytics)
```

**Dependencies:** APIs, database, compliance
**Blocks:** Operational efficiency
**Estimated Time:** 2 weeks (final sprint)

---

## Dependency Graph by Section

### Section 1: Executive Summary (Foundation)
```
REQ-ES-001 → REQ-ES-002 → REQ-ES-003 → REQ-ES-004
             (Vision)     (Market)     (Features)    (Revenue)
                                                          ↓
                                             REQ-RM-001 (Section 4)
```

**Outgoing Dependencies:**
- REQ-ES-004 (Creator revenue tiers) → REQ-RM-001 (Section 4: Revenue Model)
- REQ-ES-006 (5-language support) → REQ-DB-005 (Section 7: JSONB i18n)

**Incoming Dependencies:** None (foundation section)

---

### Section 2: Market Analysis (Research)
```
REQ-MA-002 (Competitor pricing ₹8-25/min)
    └─> REQ-RM-005 (Section 4: Creator call rates)
        └─> REQ-CE-003 (Section 9: Creator-set rates)
            └─> REQ-API-001 (Section 8: PUT /api/v1/creator/rates)
```

**Outgoing Dependencies:**
- REQ-MA-002 → REQ-RM-005 (Creator call rates validated by market research)

**Incoming Dependencies:** None (research foundation)

---

### Section 3: Product Vision (Strategy)
```
REQ-PV-002 (50K MAU target)
    └─> REQ-SM-001 (Section 15: Monthly Active Minutes as North Star)
        └─> REQ-SM-010 (Section 15: Launch success criteria)
```

**Outgoing Dependencies:**
- REQ-PV-002 → REQ-SM-001 (Vision metrics flow to success metrics)

**Incoming Dependencies:** REQ-ES-001 (Executive vision)

---

### Section 4: Revenue Model (Monetization Foundation)
```
REQ-RM-001: 75-85% Tiered Model
    ├─> REQ-CE-002 (Section 9: Dual revenue - calls use 75-85%)
    ├─> REQ-API-002 (Section 8: Dynamic revenue calculation)
    ├─> REQ-CP-001 (Section 10: Payout calculation)
    └─> REQ-DB-004 (Section 7: creator_performance table)

REQ-RM-002: Razorpay Checkout
    └─> REQ-API-001 (Section 8: POST /api/v1/wallet/purchase)

REQ-RM-004: Coin Pricing (₹99 minimum)
    └─> REQ-CE-001 (Section 9: 6 coin packages ₹49-₹1999)
        └─> REQ-DB-006 (Section 7: coin_packages seed data)

REQ-RM-005: Creator Call Rates (₹8-25/min)
    ├─> REQ-CE-003 (Section 9: Creator-set rates)
    ├─> REQ-DB-004 (Section 7: creator_call_rates table)
    └─> REQ-API-001 (Section 8: PUT /api/v1/creator/rates)
```

**Outgoing Dependencies:** 18 dependencies to Sections 7, 8, 9, 10
**Incoming Dependencies:** REQ-ES-004 (Executive revenue tiers), REQ-MA-002 (Market pricing)

---

### Section 5: Feature Specifications (UI/UX)
```
REQ-FS-001: Onboarding Flow
    ├─ Depends on: REQ-API-003 (Auth), REQ-LC-001 (Age verification)
    └─> REQ-FS-002 (Profile creation)

REQ-FS-008: Voice Call Initiation
    ├─ Depends on: REQ-API-007 (Call APIs), REQ-CE-002 (Revenue), REQ-CE-003 (Rates)
    └─> REQ-FS-009 (In-call features)

REQ-FS-012: Live Audio Rooms
    ├─ Depends on: REQ-TA-006 (Agora multi-user broadcast)
    └─> REQ-FS-013 (Room moderation)

REQ-FS-014: Virtual Gift Sending
    ├─ Depends on: REQ-CE-004 (Gift pricing), REQ-DB-005 (gifts table)
    └─> REQ-FS-015 (Gift animations)
```

**Outgoing Dependencies:** None (consumer of other sections)
**Incoming Dependencies:** 32 dependencies from Sections 6, 7, 8, 9, 12

---

### Section 6: Technical Architecture (Infrastructure)
```
REQ-TA-001: Tech Stack
    ├─> REQ-TA-002 (Repository structure)
    ├─> REQ-DB-001 (Database setup)
    └─> ALL other sections (foundation)

REQ-TA-003: Authentication
    ├─> REQ-API-003 (Auth endpoints)
    └─> REQ-FS-001 (Onboarding flow)

REQ-TA-006: Real-time Communication (Agora SDK)
    ├─> REQ-FS-008 (Voice calls)
    ├─> REQ-FS-012 (Live rooms)
    └─> REQ-API-007 (Call management)
```

**Outgoing Dependencies:** 45 dependencies to all sections
**Incoming Dependencies:** None (foundation infrastructure)

---

### Section 7: Database Schema (Data Layer)
```
REQ-DB-001: PostgreSQL 15 Setup
    └─> ALL table requirements (REQ-DB-002 to REQ-DB-008)

REQ-DB-002: users + coin_wallets tables
    ├─> REQ-CE-005 (Three-tier balance)
    ├─> REQ-API-001 (Wallet APIs)
    └─> REQ-FS-001 (User registration)

REQ-DB-003: calls table
    ├─> REQ-API-002 (Billing)
    ├─> REQ-API-007 (Call management)
    └─> REQ-FS-008 (Voice calls)

REQ-DB-004: creator_performance table
    ├─> REQ-RM-001 (Tier determination)
    ├─> REQ-CE-002 (Revenue calculation)
    └─> REQ-CP-001 (Payout processing)

REQ-DB-005: gifts table (JSONB i18n)
    ├─> REQ-CE-004 (Gift pricing)
    └─> REQ-FS-014 (Gift sending)

REQ-DB-006: coin_packages seed data
    ├─> REQ-CE-001 (Package pricing)
    └─> REQ-API-001 (GET /api/v1/wallet/packages)
```

**Outgoing Dependencies:** 38 dependencies to Sections 5, 8, 9, 10, 11
**Incoming Dependencies:** REQ-TA-001 (Database choice), REQ-RM-001/004/005 (Revenue requirements)

---

### Section 8: API Specifications (Backend)
```
REQ-API-001: Wallet & Package APIs
    ├─ Depends on: REQ-DB-002 (coin_wallets), REQ-DB-006 (coin_packages)
    └─> REQ-FS-001 (Coin purchase UI)

REQ-API-002: Billing & Revenue APIs
    ├─ Depends on: REQ-RM-001 (Tier model), REQ-CE-002 (Dual revenue)
    └─> REQ-FS-008 (Call billing)

REQ-API-003: Auth Endpoints
    ├─ Depends on: REQ-TA-003 (Firebase + JWT)
    └─> REQ-FS-001 (Onboarding)

REQ-API-007: Call Management
    ├─ Depends on: REQ-API-002 (Billing), REQ-TA-006 (Agora)
    └─> REQ-FS-009 (In-call features)

REQ-API-010: Moderation APIs
    ├─ Depends on: REQ-LC-007 (Moderation system)
    └─> REQ-AW-004 (N8N moderation queue)
```

**Outgoing Dependencies:** 25 dependencies to Section 5 (Features)
**Incoming Dependencies:** 40 dependencies from Sections 6, 7, 9, 12

---

### Section 9: Coin Economy Design (Virtual Currency)
```
REQ-CE-001: Coin Packages (6 packages)
    ├─ Depends on: REQ-RM-004 (Pricing strategy)
    └─> REQ-DB-006 (Seed data), REQ-API-001 (GET /packages)

REQ-CE-002: Dual Revenue Model
    ├─ Depends on: REQ-RM-001 (75-85% tiers)
    └─> REQ-API-002 (Revenue calculation), REQ-CP-001 (Payouts)

REQ-CE-003: Creator-Set Call Rates
    ├─ Depends on: REQ-RM-005 (₹8-25/min range)
    └─> REQ-DB-004 (creator_call_rates), REQ-API-001 (PUT /rates)

REQ-CE-004: Gift Pricing (45% fixed)
    └─> REQ-DB-005 (gifts table), REQ-FS-014 (Gift sending)

REQ-CE-005: Three-Tier Balance Tracking
    └─> REQ-DB-002 (coin_wallets schema update)

REQ-CE-011: Daily Login Bonus
    ├─ Depends on: REQ-CE-005 (bonus_balance tracking)
    └─> REQ-API-001 (Login bonus endpoint), REQ-FS-001 (Signup flow)
```

**Outgoing Dependencies:** 14 dependencies to Sections 7, 8, 10
**Incoming Dependencies:** REQ-RM-001/004/005 (Revenue model)

---

### Section 10: Creator Payout System (Payments Out)
```
REQ-CP-001: Razorpay X Integration
    ├─ Depends on: REQ-CE-002 (Revenue calculation)
    └─> REQ-CP-003 (T+1 payouts), REQ-AW-002 (N8N workflow)

REQ-CP-003: T+1 Payout Cycle
    ├─ Depends on: REQ-CP-001 (Razorpay X), REQ-DB-004 (creator_performance)
    └─> REQ-AW-002 (Daily payout workflow)

REQ-CP-005: Creator Dashboard
    ├─ Depends on: REQ-API-001 (Creator APIs)
    └─> Enables creators to view earnings/payouts

REQ-CP-007: Tax Compliance (TDS)
    ├─ Depends on: REQ-CP-001 (Payout integration)
    └─> REQ-AW-002 (Auto-TDS deduction)
```

**Outgoing Dependencies:** 5 dependencies to Section 11 (N8N workflows)
**Incoming Dependencies:** REQ-RM-001 (Revenue tiers), REQ-CE-002 (Dual revenue), REQ-DB-004 (Performance tracking)

---

### Section 11: N8N Automation Workflows (Operations)
```
REQ-AW-001: N8N Self-Hosted Setup
    ├─ Depends on: REQ-TA-001 (AWS infrastructure)
    └─> Enables all workflow requirements

REQ-AW-002: Daily Payout Processing
    ├─ Depends on: REQ-CP-003 (T+1 logic), REQ-CP-001 (Razorpay X)
    └─> Automates creator payouts

REQ-AW-004: Moderation Queue Workflow
    ├─ Depends on: REQ-LC-007 (Moderation system)
    └─> Routes content for review

REQ-AW-006: Analytics Reporting
    ├─ Depends on: REQ-DB-001 (Data availability)
    └─> Generates REQ-CE-010 (Coin economy analytics), REQ-SM-002 (Retention reports)
```

**Outgoing Dependencies:** None (operational automation)
**Incoming Dependencies:** 22 dependencies from Sections 6, 7, 10, 12, 15

---

### Section 12: Legal & Compliance (Safety)
```
REQ-LC-001: Age Verification (Karza KYC)
    ├─ Depends on: REQ-API-003 (Auth flow)
    └─> Blocks REQ-FS-001 (Mandatory before onboarding)

REQ-LC-004: Safety Center UI
    └─> Enables REQ-LC-005 (Block/Report), REQ-LC-006 (Incident reporting)

REQ-LC-007: Content Moderation (3-tier)
    └─> REQ-AW-004 (Moderation queue), REQ-API-010 (Moderation APIs)

REQ-LC-008: Automated Safety Rules
    ├─ Depends on: REQ-LC-007 (Moderation system)
    └─> REQ-AW-004 (Auto-escalation workflow)
```

**Outgoing Dependencies:** 8 dependencies to Sections 5, 8, 11
**Incoming Dependencies:** REQ-API-003 (Auth required for verification)

---

### Section 13: Implementation Roadmap (Planning)
```
REQ-IR-001: 16-20 Week Timeline
    └─> Informs sprint planning (Phases 1-3)

REQ-IR-003: Multi-Language Launch (5 languages)
    ├─> REQ-DB-005 (JSONB i18n), REQ-FS-002 (Localized profiles)
    └─> REQ-IR-005 (Translation budget)
```

**Outgoing Dependencies:** None (planning document)
**Incoming Dependencies:** All P0 requirements inform timeline

---

### Section 14: Cost Estimation (Budget)
```
REQ-COE-001: ₹9.47L 5-Month Budget
    └─> Informs resource allocation

REQ-COE-003: Service & API Costs
    ├─ Razorpay: ₹30K
    ├─ Agora: ₹40K/month (Months 4-5)
    ├─ Karza KYC: ₹15K
    └─ ClearTax API: ₹2.5K/month

REQ-COE-006: Revenue Projections (15-25% platform share)
    ├─ Depends on: REQ-CE-002 (Dual revenue model)
    └─> Month 8-10 break-even target
```

**Outgoing Dependencies:** None (financial planning)
**Incoming Dependencies:** All infrastructure and service requirements

---

### Section 15: Success Metrics (KPIs)
```
REQ-SM-001: Monthly Active Minutes (North Star)
    └─> Tracks from REQ-FS-008 (Call duration), REQ-FS-012 (Room time)

REQ-SM-005: Creator Ecosystem Metrics
    ├─ Depends on: REQ-CP-001 (Payout tracking), REQ-DB-004 (Performance)
    └─> 20 creators recruited Week 12-16

REQ-SM-010: Launch Success Criteria
    ├─ Depends on: All feature requirements complete
    └─> 4/6 metrics needed for success:
        1. Downloads ≥2,000
        2. D7 Retention ≥40%
        3. Paid Transactions ≥100
        4. Bug Report Rate <3%
        5. Active Creators ≥15
        6. NPS ≥50
```

**Outgoing Dependencies:** None (measurement framework)
**Incoming Dependencies:** All sections provide metrics to track

---

## Circular Dependencies (⚠️ Requires Resolution)

### Circular #1: Call System Revenue Loop
```
REQ-FS-008 (Voice calls)
    ├─> Requires REQ-API-002 (Billing calculation)
        └─> Requires REQ-CE-002 (Dual revenue model)
            └─> Requires REQ-DB-004 (creator_performance table)
                └─> Populated by REQ-FS-008 (Call completions)
```

**Resolution Strategy:**
1. Create database tables first (REQ-DB-004 with default tier for all creators)
2. Implement billing calculation (REQ-API-002 with tier lookup)
3. Implement calls (REQ-FS-008 with billing hooks)
4. Performance tracking updates tier over time (no circular dependency in code)

**Implementation Order:** DB → API → Feature

---

### Circular #2: Creator Dashboard & Payouts
```
REQ-CP-005 (Creator dashboard)
    ├─> Displays data from REQ-CP-001 (Payout history)
        └─> Requires REQ-CP-003 (T+1 processing)
            └─> Requires REQ-DB-004 (Earnings tracking)
                └─> Updated by creator activity viewed in REQ-CP-005
```

**Resolution Strategy:**
1. Build payout processing backend first (REQ-CP-001, REQ-CP-003)
2. Populate historical data (initial state: no payouts)
3. Build dashboard to display payout history (REQ-CP-005)
4. Dashboard shows "No payouts yet" initially, populates after first T+1 cycle

**Implementation Order:** Backend → Data → Frontend

---

### Circular #3: Moderation Queue Workflow
```
REQ-AW-004 (N8N moderation queue)
    ├─> Depends on REQ-LC-007 (Moderation system)
        └─> Depends on REQ-API-010 (Moderation endpoints)
            └─> Triggers REQ-AW-004 (N8N workflow for escalation)
```

**Resolution Strategy:**
1. Build moderation database tables (REQ-LC-007 data layer)
2. Build moderation APIs (REQ-API-010)
3. Create N8N workflow to consume API events (REQ-AW-004)
4. Wire up API → N8N webhook triggers (completes loop)

**Implementation Order:** Data → API → Workflow

---

## Sprint Planning Guide

### Parallelization Opportunities

#### Sprint 1 (Week 1-2): Foundation - 0 Dependencies
These can be built in parallel by separate teams:
- **Team A:** REQ-TA-001, REQ-TA-002 (Infrastructure + Monorepo)
- **Team B:** REQ-DB-001 (PostgreSQL setup)
- **Team C:** REQ-TA-003 (Firebase Auth configuration)
- **Team D:** REQ-MA-001 to REQ-MA-005 (Market research)

---

#### Sprint 2 (Week 3-4): Database Layer - Depends on Sprint 1
All database table requirements can be built in parallel:
- **Team A:** REQ-DB-002 (users + coin_wallets)
- **Team B:** REQ-DB-003 (calls), REQ-DB-004 (creator_performance)
- **Team C:** REQ-DB-005 (gifts JSONB), REQ-DB-006 (coin_packages seed)
- **Team D:** REQ-DB-007 (creator_payouts), REQ-DB-008 (moderation tables)

---

#### Sprint 3 (Week 5-7): Core APIs - Depends on Sprint 2
Build APIs in groups based on domain:
- **Team A (Payments):** REQ-API-001 (Wallet), REQ-RM-002 (Razorpay Checkout)
- **Team B (Calls):** REQ-API-007 (Call management), REQ-API-002 (Billing)
- **Team C (Auth):** REQ-API-003 (Auth endpoints), REQ-LC-001 (Karza integration)
- **Team D (Safety):** REQ-API-010 (Moderation endpoints)

---

#### Sprint 4-5 (Week 8-12): Frontend Features - Depends on Sprint 3
Parallel feature development:
- **Team A (Onboarding):** REQ-FS-001 to REQ-FS-004
- **Team B (Discovery):** REQ-FS-005 to REQ-FS-007
- **Team C (Calls):** REQ-FS-008 to REQ-FS-011
- **Team D (Gifts & Rooms):** REQ-FS-012 to REQ-FS-016

---

#### Sprint 6 (Week 13-14): Payout Integration - Depends on Sprint 3
- **Team A:** REQ-CP-001 (Razorpay X), REQ-CP-003 (T+1 logic)
- **Team B:** REQ-CP-005 (Creator dashboard)
- **Team C:** REQ-CP-007 (TDS compliance), REQ-CP-008 (ClearTax)

---

#### Sprint 7 (Week 15-16): Automation & Launch Prep - Depends on All Previous
- **Team A (N8N):** REQ-AW-001 to REQ-AW-006 (All workflows)
- **Team B (Compliance):** REQ-LC-004 to REQ-LC-008 (Safety features)
- **Team C (Analytics):** REQ-CE-010 (Coin economy), REQ-SM-001 to REQ-SM-012 (Success metrics)
- **Team D (Testing):** Integration testing, load testing, launch checklist

---

## Implementation Checklist

Use this checklist to track implementation progress:

### Foundation (Stage 1)
- [ ] REQ-TA-001: Tech stack selected (Flutter, FastAPI, PostgreSQL, Redis)
- [ ] REQ-TA-002: Monorepo structure created
- [ ] REQ-TA-003: Firebase Auth + JWT authentication working
- [ ] REQ-DB-001: PostgreSQL 15 database provisioned

### Database Layer (Stage 2)
- [ ] REQ-DB-002: users + coin_wallets tables created
- [ ] REQ-DB-003: calls table created
- [ ] REQ-DB-004: creator_performance table created
- [ ] REQ-DB-005: gifts table with JSONB i18n
- [ ] REQ-DB-006: coin_packages seed data loaded
- [ ] REQ-DB-007: creator_payouts table created
- [ ] REQ-DB-008: moderation tables created

### Core APIs (Stage 3)
- [ ] REQ-API-001: Wallet & package APIs working
- [ ] REQ-API-002: Billing & revenue calculation tested
- [ ] REQ-API-003: Auth endpoints live
- [ ] REQ-API-007: Call management APIs functional
- [ ] REQ-API-010: Moderation APIs deployed

### Frontend Features (Stage 4)
- [ ] REQ-FS-001: Onboarding flow complete (4 steps)
- [ ] REQ-FS-002: Profile creation working
- [ ] REQ-FS-005: Discover screen with filters
- [ ] REQ-FS-008: Voice call initiation tested
- [ ] REQ-FS-009: In-call features (mute, speaker, end)
- [ ] REQ-FS-012: Live audio rooms functional
- [ ] REQ-FS-014: Virtual gift sending working

### Payment Integration (Stage 5)
- [ ] REQ-RM-002: Razorpay Checkout integrated
- [ ] REQ-CP-001: Razorpay X payout integration complete
- [ ] REQ-CP-003: T+1 automated payout cycle tested
- [ ] REQ-CE-001: 6 coin packages purchasable

### Compliance & Safety (Stage 6)
- [ ] REQ-LC-001: Karza KYC age verification working
- [ ] REQ-LC-004: Safety Center UI deployed
- [ ] REQ-LC-005: Block/report features functional
- [ ] REQ-LC-007: 3-tier moderation system live

### Automation (Stage 7)
- [ ] REQ-AW-001: N8N self-hosted on AWS EC2
- [ ] REQ-AW-002: Daily payout workflow automated
- [ ] REQ-AW-004: Moderation queue workflow running
- [ ] REQ-AW-006: Analytics reporting scheduled

### Launch Readiness
- [ ] REQ-SM-010: All 6 launch metrics instrumented
- [ ] REQ-IR-001: 16-20 week timeline on track
- [ ] REQ-COE-001: Budget tracking ₹9.47L
- [ ] All P0 requirements completed (86 total)

---

## Dependency Statistics

### By Section (Most Depended Upon)
1. **Section 7 (Database Schema):** 38 outgoing dependencies
2. **Section 6 (Technical Architecture):** 45 outgoing dependencies
3. **Section 4 (Revenue Model):** 18 outgoing dependencies
4. **Section 9 (Coin Economy):** 14 outgoing dependencies
5. **Section 8 (API Specifications):** 25 outgoing dependencies

### By Section (Most Dependent On Others)
1. **Section 5 (Features):** 32 incoming dependencies
2. **Section 11 (N8N Workflows):** 22 incoming dependencies
3. **Section 10 (Payouts):** 12 incoming dependencies
4. **Section 15 (Success Metrics):** 15 incoming dependencies
5. **Section 12 (Compliance):** 8 incoming dependencies

### Critical Bottlenecks (Block >10 Requirements)
1. **REQ-TA-001 (Tech Stack):** Blocks 45+ requirements
2. **REQ-DB-001 (Database Setup):** Blocks 38 requirements
3. **REQ-RM-001 (Revenue Model):** Blocks 18 requirements
4. **REQ-API-001 (Wallet APIs):** Blocks 14 requirements
5. **REQ-CE-002 (Dual Revenue):** Blocks 12 requirements

---

## Summary

- **Total Requirements:** 203
- **Total Cross-Section Dependencies:** 247
- **Circular Dependencies Identified:** 3 (all resolved with staging)
- **Parallelization Opportunities:** 7 sprints with 4 parallel teams
- **Critical Path Length:** 7 stages (16-20 weeks)
- **Bottleneck Requirements:** 5 (must complete early)

**Recommendation:** Follow the 7-stage implementation plan to minimize dependency conflicts and enable parallel development.

---

**Dependency Graph Complete!** ✅
Use this document alongside `master-index.md` for sprint planning and implementation sequencing.

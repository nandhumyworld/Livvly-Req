# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **requirements comparison repository** for the LIVVLY app development project - a voice-first social and dating application targeting Indian Tier-2/3 cities. It contains parallel requirement breakdowns from two stakeholders (Kapil and M. Kumaran) for comparison and alignment purposes.

## Repository Structure

```
compare/
├── comparison-prompt.md    # Template for performing requirements comparison
├── kapil/
│   └── breakdown/          # Kapil's requirements (15 sections)
│       ├── 01-executive-summary/
│       ├── 02-market-analysis/
│       ├── ...
│       └── 15-success-metrics/
└── mkumaran/
    └── breakdown/          # M. Kumaran's requirements (15 sections)
        └── (same structure)
```

Each section contains:
- `requirements.md` - Main requirements with IDs, priorities (P0/P1/P2), acceptance criteria
- `questions-answers.md` - Supporting Q&A and clarifications
- Some sections include `design-decisions.md` or `research-notes.md`

## Section Topics (01-15)

1. Executive Summary - Product definition, targets, differentiators
2. Market Analysis - Target market, competitors
3. Product Vision - Core features and UX
4. Revenue Model - Monetization strategy
5. Feature Specifications - Detailed feature requirements
6. Technical Architecture - Tech stack, infrastructure
7. Database Schema - Data models
8. API Specifications - Endpoints and contracts
9. Coin Economy Design - Virtual currency system
10. Creator Payout System - Earnings and withdrawals
11. n8n Automation Workflows - Automation pipelines
12. Legal and Compliance - Regulatory requirements
13. Implementation Roadmap - Timeline and phases
14. Cost Estimation - Budget projections
15. Success Metrics - KPIs and tracking

## Key Technical Decisions (Notable Differences)

| Aspect | Kapil | M. Kumaran |
|--------|-------|------------|
| Backend | Node.js + Supabase | FastAPI (Python) |
| n8n | Deferred to Phase 2 | Included in MVP |
| Languages at MVP | Tamil first, then Telugu | Tamil + Telugu together |
| AI Moderation | Not detailed | Parallel evaluation (Google/Azure) + Whisper migration |
| Infrastructure | Standard | Premium over-provisioned |

## Working with This Repository

### Comparison Analysis
Follow the template in `comparison-prompt.md` to produce:
- Report 1: Requirements.md comparison across all 15 sections
- Report 2: Questions-Answers.md comparison across all 15 sections
- Executive Summary with alignment scores

### Requirement ID Conventions
- Format: `REQ-{SECTION}-{NUMBER}` (e.g., `REQ-ES-001`, `REQ-TA-007`)
- Sections: ES (Executive Summary), MA (Market), PV (Product Vision), RM (Revenue), FS (Features), TA (Technical), DB (Database), API, CE (Coin Economy), CP (Creator Payout), AW (Automation Workflows), LC (Legal), IR (Implementation Roadmap), CO (Cost), SM (Success Metrics)

### Priority Levels
- **P0**: Critical - Must have for MVP
- **P1**: High - Important but can be phased
- **P2**: Medium - Nice to have / Post-MVP

## LIVVLY Product Context

- **Target**: Tier-2/3 Indian cities (Tamil Nadu, Andhra Pradesh, Karnataka, Kerala)
- **Core Features**: Voice/video calls, live audio rooms, virtual gifting
- **Key Differentiators**: 75-85% creator revenue share, T+1 payouts, regional language support
- **Tech Stack**: Flutter mobile, PostgreSQL, Redis, Agora SDK, Razorpay
- **Target Metrics**: 50K MAU by Month 6, 200K by Month 12, 1M by Month 24

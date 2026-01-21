# Requirements: Market Analysis

**Section**: 02-market-analysis
**Source Lines**: 63-120
**Generated**: 2026-01-19
**Status**: Complete

---

## Revenue Stream Requirements

### REQ-MA-001: Multi-Stream Revenue Model
**Priority**: P0 (Critical)
**Type**: Business

**Description**: The platform must support multiple revenue streams matching or exceeding competitor models: In-App Purchases (coins), Paid Calls, Virtual Gifts, VIP Subscriptions, and Ads.

**Rationale**: Competitor analysis shows diversified revenue streams are essential for voice social apps. Single-stream dependence creates business risk.

**Acceptance Criteria**:
- All 5 revenue streams are implemented and trackable
- Revenue dashboard shows breakdown by stream
- Each stream contributes to total GMV calculations

**Dependencies**: REQ-CE-xxx (Coin Economy), REQ-RM-xxx (Revenue Model)
**Related Design Decisions**: None

**Notes**: Target contribution mix - Coins 60-70%, Paid Calls 15-20%, Gifts 10-15%, VIP 5-10%, Ads 2-5%.

---

### REQ-MA-002: In-App Purchase as Primary Revenue
**Priority**: P0 (Critical)
**Type**: Business

**Description**: In-app coin purchases must be the primary revenue driver, targeting 60-70% of total revenue.

**Rationale**: Competitor analysis confirms virtual currency purchases are the dominant monetization method in voice social apps.

**Acceptance Criteria**:
- Coin purchase flow is optimized for conversion
- Multiple coin packages available at various price points
- Purchase analytics track conversion rates and ARPU

**Dependencies**: REQ-CE-xxx (Coin packages)
**Related Design Decisions**: None

**Notes**: Coin pricing must account for app store fees while remaining competitive.

---

### REQ-MA-003: Web Purchase Option
**Priority**: P1 (High)
**Type**: Functional

**Description**: Users must be able to purchase coins via a web portal at discounted prices, bypassing app store fees.

**Rationale**: 30% app store fee significantly impacts margins. Web purchases allow better pricing for users and higher margins for the platform.

**Acceptance Criteria**:
- Web purchase portal is accessible via deep link from app
- Web purchases offer at least 15-20% better value than in-app purchases
- Coin balance syncs between web and app purchases in real-time
- Web portal supports same payment methods as target market (UPI, cards, wallets)

**Dependencies**: REQ-API-xxx (Web purchase API)
**Related Design Decisions**: None

**Notes**: Must comply with Google Play policies regarding alternative payment mentions.

---

## Creator Revenue Requirements

### REQ-MA-004: Tiered Creator Revenue Share
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Creators must receive 75% base revenue share, with top performers earning up to 85% based on performance tiers.

**Rationale**: Higher creator share than competitors (45-60%) is a key differentiator for creator acquisition and retention.

**Acceptance Criteria**:
- New creators start at 75% revenue share
- Performance tier system is documented and visible to creators
- Tier progression criteria are clearly defined
- Top tier creators receive up to 85% share
- Revenue share percentage is shown in creator dashboard

**Dependencies**: REQ-CP-xxx (Creator Payout)
**Related Design Decisions**: None

**Notes**: Tier criteria may include: total earnings, call hours, ratings, retention rate.

---

### REQ-MA-005: Creator Share Visibility
**Priority**: P1 (High)
**Type**: Functional

**Description**: Creators must see their current tier, revenue share percentage, and progress toward the next tier in their dashboard.

**Rationale**: Transparency on earnings potential drives creator motivation and trust.

**Acceptance Criteria**:
- Dashboard shows current tier name and revenue share %
- Progress bar shows distance to next tier
- Tier requirements are documented in help section
- Historical tier changes are logged

**Dependencies**: REQ-MA-004 (Tiered system)
**Related Design Decisions**: None

**Notes**: Consider gamification elements (badges, milestones) to encourage tier progression.

---

## Market Positioning Requirements

### REQ-MA-006: Regional Language Market Focus
**Priority**: P0 (Critical)
**Type**: Business

**Description**: The platform must establish dominance in the regional language voice social market, targeting the ₹50 Cr SOM (Serviceable Obtainable Market).

**Rationale**: Regional language focus is underserved by Hindi/English-focused competitors, creating a defensible niche.

**Acceptance Criteria**:
- Marketing materials prioritize regional language positioning
- App store listing optimized for regional language keywords
- Regional language content is prominently featured
- Analytics track user acquisition by language preference

**Dependencies**: REQ-ES-004 (Tamil-first)
**Related Design Decisions**: None

**Notes**: Phase 1 focus on Tamil Nadu market before Telugu/Kannada expansion.

---

### REQ-MA-007: Competitive Payout Speed Advantage
**Priority**: P0 (Critical)
**Type**: Business

**Description**: The platform must maintain T+1 (next day) payout speed as a competitive advantage over competitors offering T+3 to T+7.

**Rationale**: SWOT analysis identifies faster payouts as a key strength for creator acquisition.

**Acceptance Criteria**:
- Payout requests submitted before 6 PM are processed next business day
- Payout speed is prominently communicated in creator marketing
- System tracks and reports actual payout times
- 95% of payouts meet T+1 SLA

**Dependencies**: REQ-CP-xxx (Payout system)
**Related Design Decisions**: None

**Notes**: Requires working capital reserves to enable fast payouts before revenue is collected.

---

## Risk Mitigation Requirements

### REQ-MA-008: App Store Policy Compliance
**Priority**: P0 (Critical)
**Type**: Non-Functional

**Description**: The platform must maintain compliance with Google Play and Apple App Store policies to avoid removal or suspension.

**Rationale**: SWOT identifies platform policy changes as a key threat. Non-compliance could result in business-ending app removal.

**Acceptance Criteria**:
- App complies with current Google Play content policies
- In-app purchase implementation follows platform guidelines
- Web purchase promotion (if any) complies with platform rules
- Regular policy audits are scheduled quarterly

**Dependencies**: REQ-LC-xxx (Legal Compliance)
**Related Design Decisions**: None

**Notes**: Pay special attention to policies around virtual gifting, dating content, and payment alternatives.

---

### REQ-MA-009: Regulatory Change Preparedness
**Priority**: P2 (Medium)
**Type**: Non-Functional

**Description**: The system architecture must be flexible enough to adapt to potential regulatory changes in India's digital economy.

**Rationale**: SWOT identifies regulatory changes as a threat. Modular design enables faster compliance adaptation.

**Acceptance Criteria**:
- Payment processing is abstracted to allow provider changes
- KYC/identity verification can be upgraded without major refactoring
- Content moderation system can be tuned to stricter standards if required
- Data storage complies with data localization requirements

**Dependencies**: REQ-TA-xxx (Technical Architecture)
**Related Design Decisions**: None

**Notes**: Monitor RBI guidelines on virtual currencies and IT Ministry rules on social platforms.

---

### REQ-MA-010: Economic Downturn Resilience
**Priority**: P2 (Medium)
**Type**: Business

**Description**: The platform should support multiple price tiers and free engagement options to maintain user activity during economic downturns.

**Rationale**: SWOT identifies economic downturn as a threat affecting discretionary spend on entertainment.

**Acceptance Criteria**:
- Free features are available for users who cannot or choose not to spend
- Low-cost coin packages (under ₹50) are available
- Free users can still engage with some features (limited rooms, profiles)
- Conversion funnel tracks free-to-paid user journey

**Dependencies**: REQ-CE-xxx (Coin packages)
**Related Design Decisions**: None

**Notes**: Balance free features to encourage conversion without cannibalizing paid features.

---

## Competitor Intelligence Requirements

### REQ-MA-011: Competitive Monitoring System
**Priority**: P2 (Medium)
**Type**: Functional

**Description**: The platform should track key competitor metrics (pricing, features, creator rates) to maintain competitive positioning.

**Rationale**: Market analysis shows tight competition; staying informed enables rapid response to competitor moves.

**Acceptance Criteria**:
- Quarterly competitor analysis reports are produced
- Key competitor pricing is documented and tracked
- New competitor feature launches are logged
- Creator poaching risk is monitored (unusual churn patterns)

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: This may be a manual process initially, automated later if volume justifies.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-MA-001 | Multi-Stream Revenue Model | P0 | Business |
| REQ-MA-002 | In-App Purchase as Primary Revenue | P0 | Business |
| REQ-MA-003 | Web Purchase Option | P1 | Functional |
| REQ-MA-004 | Tiered Creator Revenue Share (75-85%) | P0 | Business |
| REQ-MA-005 | Creator Share Visibility | P1 | Functional |
| REQ-MA-006 | Regional Language Market Focus | P0 | Business |
| REQ-MA-007 | Competitive Payout Speed (T+1) | P0 | Business |
| REQ-MA-008 | App Store Policy Compliance | P0 | Non-Functional |
| REQ-MA-009 | Regulatory Change Preparedness | P2 | Non-Functional |
| REQ-MA-010 | Economic Downturn Resilience | P2 | Business |
| REQ-MA-011 | Competitive Monitoring System | P2 | Functional |

**Total Requirements**: 11
**P0 (Critical)**: 6
**P1 (High)**: 2
**P2 (Medium)**: 3

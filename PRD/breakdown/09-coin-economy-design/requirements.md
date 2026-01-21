# Requirements: Coin Economy Design

**Section**: 09-coin-economy-design
**Source Lines**: 1928-2002
**Generated**: 2026-01-20
**Status**: Complete

---

## Coin Package Requirements

### REQ-CE-001: Coin Package Catalog
**Priority**: P0 (Critical)
**Type**: Business

**Description**: The platform must offer 6 tiered coin packages with increasing value/discount for larger purchases.

**Rationale**: Tiered pricing incentivizes larger purchases while providing entry-level options.

**Acceptance Criteria**:
- 6 packages available: Starter (₹49), Popular (₹99), Value (₹299), Best Seller (₹499), Premium (₹999), VIP (₹1999)
- Each package shows: price, base coins, bonus coins, total coins
- Per-coin cost decreases with larger packages (₹0.98 to ₹0.71)
- "Popular" and "Best Seller" packages highlighted in UI
- Packages can be enabled/disabled by admin

**Dependencies**: REQ-DB-018
**Related Design Decisions**: None

**Notes**: Bonus coins have shorter expiry than purchased coins.

---

### REQ-CE-002: Bonus Coin Structure
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Each coin package must include bonus coins that increase percentage-wise with package size.

**Rationale**: Bonus coins encourage larger purchases and provide perceived value.

**Acceptance Criteria**:
- Starter: 10% bonus (5 coins)
- Popular: 16% bonus (15 coins)
- Value: 25% bonus (70 coins)
- Best Seller: 30% bonus (140 coins)
- Premium: 44% bonus (400 coins)
- VIP: 60% bonus (1050 coins)
- Bonus coins tracked separately for expiry purposes

**Dependencies**: REQ-CE-001
**Related Design Decisions**: None

**Notes**: Bonus percentage displayed prominently in purchase UI.

---

## Call Pricing Requirements

### REQ-CE-003: Base Call Rates
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Call rates must vary by creator type (Regular, Popular, Verified, Celebrity) and call type (audio/video).

**Rationale**: Tiered pricing rewards top creators and reflects their value to users.

**Acceptance Criteria**:
- Regular creators: 5 coins/min audio, 8 coins/min video
- Popular creators (100+ followers): 8 coins/min audio, 12 coins/min video
- Verified creators (KYC done): 12 coins/min audio, 18 coins/min video
- Celebrity creators (1000+ followers): 20 coins/min audio, 30 coins/min video
- Video rates ~1.5x audio rates
- Rates displayed before call initiation

**Dependencies**: REQ-DB-019
**Related Design Decisions**: None

**Notes**: Creator type determined by follower count and verification status.

---

### REQ-CE-004: Peak Hour Pricing
**Priority**: P1 (High)
**Type**: Business

**Description**: All call rates must have 1.5x multiplier during peak hours (10 PM - 2 AM IST).

**Rationale**: Peak pricing increases revenue during high-demand periods and manages call volume.

**Acceptance Criteria**:
- Peak hours: 22:00 - 02:00 IST
- All base rates multiplied by 1.5 during peak
- Peak status clearly indicated in UI
- Users warned when entering peak hours
- Rate change notifications if call spans peak boundary

**Dependencies**: REQ-CE-003
**Related Design Decisions**: None

**Notes**: Consider timezone for users outside India.

---

## Gift Pricing Requirements

### REQ-CE-005: Gift Pricing Tiers
**Priority**: P0 (Critical)
**Type**: Business

**Description**: Virtual gifts must be organized into 4 categories (Basic, Premium, Luxury, Exclusive) with prices ranging from 10 to 10,000 coins.

**Rationale**: Wide price range accommodates different spending levels and occasions.

**Acceptance Criteria**:
- Basic: 10-50 coins (Rose, Heart, Coffee)
- Premium: 100-200 coins (Teddy Bear, Bouquet, Diamond)
- Luxury: 500-1000 coins (Crown, Sports Car)
- Exclusive: 5000-10000 coins (Private Jet, Castle)
- Each gift has icon, animation (optional), Tamil name
- Gift catalog sortable by category and price

**Dependencies**: REQ-DB-009
**Related Design Decisions**: None

**Notes**: Event/seasonal gifts can be added to any category.

---

### REQ-CE-006: Revenue Split Formula
**Priority**: P0 (Critical)
**Type**: Business

**Description**: When users spend coins, revenue must be split 55% platform / 45% creator.

**Rationale**: 45% creator share (higher than competitors) is key differentiator for creator acquisition.

**Acceptance Criteria**:
- Platform retains 55% of coin value (₹0.55 per coin)
- Creator receives 45% as diamonds (₹0.45 per coin)
- Formula: Creator Diamonds = Coins × ₹1 × 0.45
- Split applies equally to calls, gifts, and room contributions
- Split percentage configurable by admin (for future adjustments)

**Dependencies**: REQ-DB-004, REQ-DB-005
**Related Design Decisions**: DD-DB-003

**Notes**: This is net split; app store fees already factored into coin purchase price.

---

## Coin Lifecycle Requirements

### REQ-CE-007: Coin Expiry System
**Priority**: P1 (High)
**Type**: Technical

**Description**: Coins must have expiry rules based on type and account activity.

**Rationale**: Coin expiry ensures revenue recognition and prevents indefinite liability.

**Acceptance Criteria**:
- Purchased coins: Expire after 365 days of account inactivity
- Bonus coins: Expire after 90 days from grant date
- Promotional coins: Expire after 30 days from grant date
- Expiry tracked at batch level (not individual coin)
- FIFO consumption (oldest coins used first)
- User notification 7 days and 1 day before expiry
- Expiry job runs daily

**Dependencies**: REQ-DB-004
**Related Design Decisions**: None

**Notes**: Stakeholder confirmed full expiry implementation for MVP.

---

### REQ-CE-008: Coin Type Tracking
**Priority**: P1 (High)
**Type**: Technical

**Description**: System must track coin types (purchased, bonus, promotional) separately for expiry and reporting.

**Rationale**: Different coin types have different expiry rules and accounting treatment.

**Acceptance Criteria**:
- Each coin batch has type: purchased, bonus, promo
- Each batch has granted_at and expires_at timestamps
- Balance queries sum unexpired batches
- Spending deducts from oldest expiring batch first
- Reports show breakdown by coin type
- Admin can grant promotional coins with custom expiry

**Dependencies**: REQ-CE-007
**Related Design Decisions**: None

**Notes**: May need separate coin_batches table or JSONB tracking.

---

### REQ-CE-009: Refund Policy
**Priority**: P1 (High)
**Type**: Business

**Description**: Coin refunds must be limited to technical issues within 7 days, unused coins only.

**Rationale**: Clear refund policy prevents abuse while addressing legitimate issues.

**Acceptance Criteria**:
- Refunds only for technical issues (payment errors, service failures)
- Request window: 7 days from purchase
- Only unused coins eligible (no partial refunds on spent coins)
- Refund processed to original payment method
- Bonus coins forfeited on refund
- Admin approval required for refunds
- Refund reason logged for audit

**Dependencies**: REQ-API-008
**Related Design Decisions**: None

**Notes**: App store purchases follow store's refund policy.

---

## Display Requirements

### REQ-CE-010: Call Cost Display
**Priority**: P0 (Critical)
**Type**: UX

**Description**: Users must see call cost information before and during calls.

**Rationale**: Transparency builds trust and helps users manage spending.

**Acceptance Criteria**:
- Show rate per minute before call starts
- Show if peak pricing applies
- Show estimated minutes based on current balance
- During call: show elapsed time and coins spent (optional)
- Low balance warning at 1 minute remaining
- Auto-disconnect option when balance depleted

**Dependencies**: REQ-API-010, REQ-CE-003
**Related Design Decisions**: DD-API-002

**Notes**: Consider "do not disturb" option for cost displays.

---

### REQ-CE-011: Balance Display
**Priority**: P0 (Critical)
**Type**: UX

**Description**: User's coin balance must be visible throughout the app with breakdown available.

**Rationale**: Users need to know their balance to make spending decisions.

**Acceptance Criteria**:
- Total balance in header/navigation
- Tap to see breakdown: purchased, bonus, promotional
- Show expiring coins prominently (if expiring within 7 days)
- Quick access to recharge from balance display
- Real-time balance updates after transactions

**Dependencies**: REQ-API-006, REQ-CE-008
**Related Design Decisions**: None

**Notes**: Consider color coding for expiring coins.

---

### REQ-CE-012: Gift Value Display
**Priority**: P0 (Critical)
**Type**: UX

**Description**: Gifts must show both coin cost and creator earning value.

**Rationale**: Showing creator earnings encourages gifting by demonstrating direct impact.

**Acceptance Criteria**:
- Each gift shows: name, icon, coin price, creator earns amount
- Creator earning shown as "Creator earns ₹X"
- Gift selection sorted by popularity, then price
- Combo/bulk gift option for popular gifts
- Animation preview available for premium gifts

**Dependencies**: REQ-DB-009, REQ-CE-005
**Related Design Decisions**: None

**Notes**: Consider Tamil language toggle for gift names.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-CE-001 | Coin Package Catalog | P0 | Business |
| REQ-CE-002 | Bonus Coin Structure | P0 | Business |
| REQ-CE-003 | Base Call Rates | P0 | Business |
| REQ-CE-004 | Peak Hour Pricing | P1 | Business |
| REQ-CE-005 | Gift Pricing Tiers | P0 | Business |
| REQ-CE-006 | Revenue Split Formula | P0 | Business |
| REQ-CE-007 | Coin Expiry System | P1 | Technical |
| REQ-CE-008 | Coin Type Tracking | P1 | Technical |
| REQ-CE-009 | Refund Policy | P1 | Business |
| REQ-CE-010 | Call Cost Display | P0 | UX |
| REQ-CE-011 | Balance Display | P0 | UX |
| REQ-CE-012 | Gift Value Display | P0 | UX |

**Total Requirements**: 12
**P0 (Critical)**: 8
**P1 (High)**: 4

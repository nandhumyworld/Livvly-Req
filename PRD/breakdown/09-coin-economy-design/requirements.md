# Section 9: Coin Economy Design - Requirements

**Section:** Coin Economy Design
**Total Requirements:** 10
**Priority Breakdown:** P0: 6 | P1: 3 | P2: 1
**Last Updated:** 2026-01-24

**CRITICAL UPDATES:**
- Dual revenue model: 75-85% tiered for calls, 45% for gifts
- 6 coin packages (restored ₹49 package per user decision)
- 10 gifts (PRD catalog, removed previous mid-tier additions)
- Daily login bonus added (5 coins/day)

---

## REQ-CE-001: Coin Package Pricing Structure

**Priority:** P0
**Type:** Business Requirement
**Status:** Approved

**Description:**
Implement 6 coin packages (₹49 - ₹1999) with progressive bonus incentives per PRD Section 9.1, including entry-level ₹49 package for trial users.

**Specifications:**

**Coin Packages:**

| Package | Price (₹) | Base Coins | Bonus | Total | Per Coin | Discount |
|---------|-----------|------------|-------|-------|----------|----------|
| Starter | ₹49 | 45 | +5 | 50 | ₹0.98 | 2% |
| Popular ⭐ | ₹99 | 90 | +10 | 100 | ₹0.99 | 1% |
| Value | ₹299 | 270 | +30 | 300 | ₹1.00 | 0% |
| Best Seller 🔥 | ₹499 | 450 | +50 | 500 | ₹1.00 | 0% |
| Premium | ₹999 | 900 | +100 | 1000 | ₹1.00 | 0% |
| VIP | ₹1999 | 1800 | +200 | 2000 | ₹1.00 | 0% |

**Note:** Updated to match PRD Section 9.1 per user decision (Q4: "Keep PRD packages")

**Database Implementation:**
```sql
-- coin_packages table (from REQ-DB-006)
INSERT INTO coin_packages (name, price_inr, base_coins, bonus_coins, sort_order, is_active) VALUES
('Starter', 49.00, 45, 5, 1, true),
('Popular', 99.00, 90, 10, 2, true),
('Value', 299.00, 270, 30, 3, true),
('Best Seller', 499.00, 450, 50, 4, true),
('Premium', 999.00, 900, 100, 5, true),
('VIP', 1999.00, 1800, 200, 6, true);
```

**Bonus Calculation Formula:**
```
Bonus = Base Coins × Bonus Percentage

Examples (from PRD):
- ₹49: 10% bonus (45 + 5 = 50)
- ₹99: 10% bonus (90 + 10 = 100)
- ₹1999: 10% bonus (1800 + 200 = 2000)
```

**Package Recommendations:**
- **Starter (₹49)**: Entry-level, trial package for new users
- **Popular (₹99)**: Default selection, first-time purchasers
- **Value (₹299)**: Good balance of price and value
- **Best Seller (₹499)**: Recommended badge, most popular
- **Premium (₹999)**: High-value users, serious daters
- **VIP (₹1999)**: Whales, top spenders

**Rationale:**
₹49 entry package lowers barrier to entry for trial users. Consistent 10% bonus across all packages simplifies pricing. Matches PRD Section 9.1 exactly per user decision.

**Acceptance Criteria:**
- [ ] 6 coin packages (₹49, ₹99, ₹299, ₹499, ₹999, ₹1999)
- [ ] ₹49 Starter package included (entry-level trial)
- [ ] Bonus coins calculated correctly (10% bonus for all)
- [ ] coin_packages seed data matches PRD structure
- [ ] Mobile app shows badges (⭐ Popular, 🔥 Best Seller)
- [ ] Per-coin pricing displayed for transparency

**Dependencies:**
- REQ-RM-004 (Coin pricing from Section 4)
- REQ-DB-006 (Seed data corrections)
- REQ-API-001 (GET /api/v1/wallet/packages endpoint)

**Design Decisions:**
- **DD-CE-001**: ₹99 minimum chosen over ₹49 for higher transaction value
- **DD-CE-002**: 5 packages chosen for optimal choice architecture (not too few, not overwhelming)

**Notes:**
- PRD showed 6 packages including ₹49; removed per user decision
- Bonus percentages increase with package price to incentivize larger purchases
- Package names and badges optimized for conversion (Popular, Best Seller)

**Testing Requirements:**
- Test package purchase flow for all 5 packages
- Verify bonus coins credited correctly
- Test package display with badges in mobile app

---

## REQ-CE-002: Dual Revenue Split Model (CRITICAL UPDATE)

**Priority:** P0
**Type:** Financial Requirement
**Status:** Approved

**Description:**
Implement DUAL revenue model: 75-85% tiered creator share for calls/rooms, 45% fixed creator share for gifts only. Balances creator earnings on calls with platform sustainability on gift monetization.

**Specifications:**

**Dual Revenue Model:**

**For Calls & Rooms (75-85% Tiered Model):**
```
User Spends X Coins on Call/Room
    │
    ▼
Gross Amount = X × ₹1
    │
    ▼
Query creator_performance table → Determine Tier
    │
    ▼
Tier 1 (75%): <₹50K monthly → Creator = ₹0.75X, Platform = ₹0.25X
Tier 2 (80%): ₹50K-200K monthly → Creator = ₹0.80X, Platform = ₹0.20X
Tier 3 (85%): ₹200K+ monthly → Creator = ₹0.85X, Platform = ₹0.15X
```

**For Gifts (45% Fixed Model - Per PRD Section 9.4):**
```
User Sends Gift Worth X Coins
    │
    ▼
Gross Amount = X × ₹1
    │
    ▼
Fixed 45% Creator Share (NO tier variation)
    │
    ▼
Creator Earnings = ₹0.45X
Platform Revenue = ₹0.55X
```

**Example Calculations:**

**Example 1: Call Revenue (Tier 1 Creator - 75% Share)**
```
User calls for 10 minutes at ₹12/min:
- Coins charged: 120 coins
- Gross: ₹120 (120 coins × ₹1)
- Creator earns: ₹90 (75% × ₹120)
- Platform keeps: ₹30 (25% × ₹120)
```

**Example 2: Gift Revenue (Any Creator - 45% Fixed Share)**
```
User sends 100-coin Rose gift:
- Gross: ₹100 (100 coins × ₹1)
- Creator earns: ₹45 (45% × ₹100) ← Fixed rate for gifts
- Platform keeps: ₹55 (55% × ₹100)

User sends 500-coin Crown gift:
- Gross: ₹500
- Creator earns: ₹225 (45% × ₹500)
- Platform keeps: ₹275 (55% × ₹500)
```

**Example 3: Mixed Revenue (Tier 3 Creator - 85% for calls)**
```
Same day earnings:
- Calls: 1000 coins → ₹850 creator (85% tier)
- Gifts: 500 coins → ₹225 creator (45% fixed)
- Total creator: ₹1075 from ₹1500 gross (71.7% blended rate)
```

**Implementation:**
```python
def calculate_creator_earnings(
    creator_id: UUID,
    coins_spent: Decimal,
    transaction_type: str,  # 'call', 'gift', or 'room'
    db: Session
) -> Tuple[Decimal, Decimal, Decimal]:
    """
    Calculate creator earnings using DUAL revenue model:
    - Calls/Rooms: 75-85% tiered
    - Gifts: 45% fixed
    """
    COIN_TO_INR = Decimal("1.0")  # 1 coin = ₹1 when spent
    GIFT_CREATOR_SHARE = Decimal("0.45")  # Fixed 45% for gifts

    gross_amount = coins_spent * COIN_TO_INR

    # Gifts use fixed 45% (no tier variation)
    if transaction_type == "gift":
        creator_earnings = gross_amount * GIFT_CREATOR_SHARE
        platform_earnings = gross_amount - creator_earnings
        return creator_earnings, platform_earnings, GIFT_CREATOR_SHARE

    # Calls and Rooms use 75-85% tiered model
    performance = db.query(CreatorPerformance).filter(
        CreatorPerformance.user_id == creator_id
    ).first()

    # Determine tier for calls/rooms
    if not performance:
        tier_percent = Decimal("0.75")  # New creator: Tier 1
    elif performance.total_earnings_30d >= Decimal("200000.00"):
        tier_percent = Decimal("0.85")  # Tier 3: Top 5%
    elif performance.total_earnings_30d >= Decimal("50000.00"):
        tier_percent = Decimal("0.80")  # Tier 2: Top 20%
    else:
        tier_percent = Decimal("0.75")  # Tier 1: Default

    creator_earnings = gross_amount * tier_percent
    platform_earnings = gross_amount - creator_earnings

    return creator_earnings, platform_earnings, tier_percent
```

**Revenue Split by Transaction Type:**
1. **Calls:** Coins charged per minute → Revenue split 75-85% (tiered by creator performance)
2. **Gifts:** Gift coin price → Revenue split 45% fixed (all creators equal)
3. **Room Gifts:** Room gift coins → Revenue split 75-85% (tiered, to room owner)

**Rationale:**
Dual revenue model balances creator economics with platform sustainability. Calls/rooms use 75-85% tiered model to reward high-performing creators. Gifts use fixed 45% to improve platform margins on impulse purchases while maintaining PRD Section 9.4 gift economics.

**Acceptance Criteria:**
- [ ] Calls use 75-85% tiered calculation based on creator performance
- [ ] Gifts use fixed 45% calculation (all creators equal)
- [ ] Room gifts use 75-85% tiered calculation (to room owner)
- [ ] Gift "Creator Earns" values recalculated with 45% (see REQ-CE-004)
- [ ] Call billing uses tiered revenue split
- [ ] Transaction type ('call', 'gift', 'room') determines revenue model
- [ ] Financial projections use blended rate (weighted by call vs gift volume)

**Dependencies:**
- REQ-RM-001 (75-85% tiered model from Section 4)
- REQ-ES-004 (Creator revenue tiers from Section 1)
- REQ-API-002 (Dynamic revenue calculation from Section 8)
- REQ-DB-004 (creator_performance table)

**Design Decisions:**
- **DD-CE-003**: 75-85% tiered model chosen over PRD's 45% for creator retention
- **DD-CE-004**: Same calculation applies to all coin spending (calls, gifts, rooms) for consistency

**Notes:**
- This is the SAME 45% error we fixed in Section 8 API Specifications
- PRD Section 9.4 must be treated as ERROR and corrected in implementation
- All gift pricing calculations in REQ-CE-004 use corrected 75-85% formula

**Testing Requirements:**
- Unit test: Verify tier determination (75%, 80%, 85%)
- Integration test: End-to-end call billing with tiered revenue
- Integration test: Gift sending with tiered revenue
- Compare against PRD's WRONG 45% calculation to verify fix

---

## REQ-CE-003: Creator-Set Call Rates (₹8-25/min)

**Priority:** P0
**Type:** Business Requirement
**Status:** Approved

**Description:**
Implement creator-set call rates (₹8-25/min for audio, ₹12-35/min for video) with NO fixed tier rates and NO peak hour pricing. Creators control their own pricing 24/7.

**Specifications:**

**PRD Section 9.2 ERROR:**
```
❌ WRONG: Fixed rates by user type
- Regular: 5 coins/min (audio), 8 coins/min (video)
- Popular: 8 coins/min, 12 coins/min
- Verified: 12 coins/min, 18 coins/min
- Celebrity: 20 coins/min, 30 coins/min
- Peak Hour Pricing: 1.5x multiplier (10 PM - 2 AM IST)
```

**CORRECTED MODEL (Approved in REQ-RM-005):**
```
✓ CORRECT: Creator-set rates
- Audio: ₹8-25 per minute (creator chooses)
- Video: ₹12-35 per minute (creator chooses)
- NO fixed tiers by user type
- NO peak hour pricing (rate is fixed 24/7)
```

**Default Rates (Suggested):**
```
New creators (no rate set):
- Audio: ₹10/min (default)
- Video: ₹15/min (default)

Creators can customize via API:
- PUT /api/v1/creator/rates
```

**Database Implementation:**
```sql
-- creator_call_rates table (from REQ-DB-004)
CREATE TABLE creator_call_rates (
    user_id UUID PRIMARY KEY REFERENCES users(id),
    audio_rate_per_minute DECIMAL(10,2) NOT NULL CHECK (audio_rate_per_minute BETWEEN 8.00 AND 25.00),
    video_rate_per_minute DECIMAL(10,2) NOT NULL CHECK (video_rate_per_minute BETWEEN 12.00 AND 35.00),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Example data
INSERT INTO creator_call_rates (user_id, audio_rate_per_minute, video_rate_per_minute) VALUES
('creator-uuid-1', 10.00, 15.00),  -- Default rates
('creator-uuid-2', 15.00, 22.00),  -- Mid-tier
('creator-uuid-3', 25.00, 35.00);  -- Premium
```

**Rate Discovery (Caller View):**
```
Call rate displayed BEFORE call starts:
- "Priya: ₹12/min audio, ₹18/min video"
- "Rahul: ₹25/min audio, ₹35/min video"

Caller sees exact per-minute cost before initiating call
```

**Billing Calculation:**
```python
def calculate_call_cost(call: Call, db: Session) -> Decimal:
    """Calculate call cost using creator-set rates"""

    # Get creator's custom rates
    creator_rates = db.query(CreatorCallRates).filter(
        CreatorCallRates.user_id == call.receiver_id
    ).first()

    # Use custom rates or defaults
    if creator_rates:
        rate_per_minute = (
            creator_rates.audio_rate_per_minute if call.call_type == "audio"
            else creator_rates.video_rate_per_minute
        )
    else:
        # Default rates for new creators
        rate_per_minute = Decimal("10.00") if call.call_type == "audio" else Decimal("15.00")

    # Calculate cost (round up to minute)
    minutes = -(-call.duration_seconds // 60)  # Ceiling division
    coins_spent = Decimal(str(minutes)) * rate_per_minute

    return coins_spent
```

**NO Peak Hour Pricing:**
- Creator rate is fixed 24/7
- If creator wants higher peak rates, they set higher base rate
- Simplifies pricing UX (predictable for callers)

**Rationale:**
PRD Section 9.2 shows fixed tier rates, but REQ-RM-005 (approved) specifies creator-set rates. Creator autonomy increases satisfaction and enables competitive pricing. Already implemented in Sections 7 (database) and 8 (API).

**Acceptance Criteria:**
- [ ] Creators can set audio rate (₹8-25/min)
- [ ] Creators can set video rate (₹12-35/min)
- [ ] Default rates applied if creator hasn't set custom rates
- [ ] Caller sees creator's rate BEFORE starting call
- [ ] NO fixed tier rates (Regular/Popular/Verified/Celebrity)
- [ ] NO peak hour pricing multiplier
- [ ] creator_call_rates table enforces min/max constraints
- [ ] PUT /api/v1/creator/rates endpoint working (REQ-API-001)

**Dependencies:**
- REQ-RM-005 (Creator call rates from Section 4)
- REQ-DB-004 (creator_call_rates table from Section 7)
- REQ-API-001 (Creator dashboard APIs from Section 8)

**Design Decisions:**
- **DD-CE-005**: Creator-set rates chosen over fixed tiers for creator autonomy
- **DD-CE-006**: No peak pricing chosen for predictable caller UX
- **DD-CE-007**: ₹8-25 range chosen based on market research (₹8 = entry, ₹25 = premium)

**Notes:**
- PRD Section 9.2 fixed tier rates IGNORED per user decision
- Peak hour pricing (1.5x) REMOVED per user decision (Q6)
- Creators control pricing, not platform-imposed tiers

**Testing Requirements:**
- Test creator sets custom rates via API
- Test default rates applied for new creators
- Test rate constraints enforced (₹8-25, ₹12-35)
- Test caller sees accurate rate before call
- Verify NO peak hour multiplier applied

---

## REQ-CE-004: Gift Pricing with 45% Fixed Revenue Share

**Priority:** P0
**Type:** Business Requirement
**Status:** Approved

**Description:**
Implement 10 gift tiers (10-10,000 coins) per PRD Section 9.3 with 45% fixed creator revenue share (simplified model, no tier variation for gifts).

**Specifications:**

**Gift Pricing (Per PRD Section 9.3 - 45% Fixed Creator Share):**

| Gift | Coins | Creator Earns (45%) | Platform (55%) | Category |
|------|-------|--------------------|--------------|---------|
| 🌹 Rose | 10 | ₹4.50 | ₹5.50 | Basic |
| ❤️ Heart | 20 | ₹9.00 | ₹11.00 | Basic |
| ☕ Coffee | 50 | ₹22.50 | ₹27.50 | Basic |
| 🧸 Teddy Bear | 100 | ₹45.00 | ₹55.00 | Premium |
| 💐 Bouquet | 150 | ₹67.50 | ₹82.50 | Premium |
| 💎 Diamond | 200 | ₹90.00 | ₹110.00 | Premium |
| 👑 Crown | 500 | ₹225.00 | ₹275.00 | Luxury |
| 🏎️ Sports Car | 1000 | ₹450.00 | ₹550.00 | Luxury |
| ✈️ Private Jet | 5000 | ₹2,250.00 | ₹2,750.00 | Exclusive |
| 🏰 Castle | 10000 | ₹4,500.00 | ₹5,500.00 | Exclusive |

**Total: 10 gifts** (per PRD Section 9.3, matches user decision Q5: "Keep PRD gift catalog")

**Gift Categories:**
1. **Basic (10-50 coins):** Entry-level, frequent gifts (Rose, Heart, Coffee)
2. **Premium (100-200 coins):** Mid-tier, special occasions (Teddy, Bouquet, Diamond)
3. **Luxury (500-1000 coins):** High-value, status symbols (Crown, Sports Car)
4. **Exclusive (5000-10000 coins):** Ultra-premium, whales only (Private Jet, Castle)

**Revenue Calculation Example:**
```python
# Rose gift (10 coins) sent to any creator (45% fixed)
coins_spent = 10
gross_amount = 10 × ₹1 = ₹10
creator_earnings = ₹10 × 0.45 = ₹4.50  # Fixed 45% for all gifts
platform_earnings = ₹10 × 0.55 = ₹5.50

# Castle gift (10000 coins) sent to any creator (45% fixed)
coins_spent = 10000
gross_amount = 10000 × ₹1 = ₹10,000
creator_earnings = ₹10,000 × 0.45 = ₹4,500  # Fixed 45% (NO tier variation)
platform_earnings = ₹10,000 × 0.55 = ₹5,500
```

**Database Implementation:**
```sql
-- gifts table seed data (45% fixed creator share)
INSERT INTO gifts (names, descriptions, icon_url, coin_price, diamond_value, category, sort_order) VALUES
-- Basic
('{"en":"Rose","ta":"ரோஜா","te":"రోజా","kn":"ಗುಲಾಬಿ","hi":"गुलाब"}', '{"en":"Symbol of love"}', '/gifts/rose.png', 10, 4.50, 'basic', 1),
('{"en":"Heart","ta":"இதയம்","te":"హృదయం","kn":"ಹೃದಯ","hi":"दिल"}', '{"en":"From my heart"}', '/gifts/heart.png', 20, 9.00, 'basic', 2),
('{"en":"Coffee","ta":"காபி","te":"కాఫీ","kn":"ಕಾಫಿ","hi":"कॉफी"}', '{"en":"Let\'s chat"}', '/gifts/coffee.png', 50, 22.50, 'basic', 3),

-- Premium
('{"en":"Teddy Bear"}', '{"en":"Cute and cuddly"}', '/gifts/teddy.png', 100, 45.00, 'premium', 4),
('{"en":"Bouquet"}', '{"en":"Beautiful flowers"}', '/gifts/bouquet.png', 150, 67.50, 'premium', 5),
('{"en":"Diamond"}', '{"en":"Forever precious"}', '/gifts/diamond.png', 200, 90.00, 'premium', 6),

-- Luxury
('{"en":"Crown"}', '{"en":"You\'re royalty"}', '/gifts/crown.png', 500, 225.00, 'luxury', 7),
('{"en":"Sports Car"}', '{"en":"Fast and furious"}', '/gifts/car.png', 1000, 450.00, 'luxury', 8),

-- Exclusive
('{"en":"Private Jet"}', '{"en":"Fly away together"}', '/gifts/jet.png', 5000, 2250.00, 'exclusive', 9),
('{"en":"Castle"}', '{"en":"Build a kingdom"}', '/gifts/castle.png', 10000, 4500.00, 'exclusive', 10);
```

**Note:** `diamond_value` column stores creator earnings at 45% fixed rate (all creators earn same percentage on gifts)

**Rationale:**
Simplified gift revenue model using fixed 45% creator share (per PRD Section 9.4) regardless of creator tier. Easier to understand for users, better platform margins on impulse purchases. Matches PRD gift catalog exactly (10 gifts).

**Acceptance Criteria:**
- [ ] 10 gifts implemented per PRD Section 9.3
- [ ] All "Creator Earns" values calculated with 45% fixed rate
- [ ] NO tier variation for gifts (all creators earn 45%)
- [ ] Gift categories: Basic (3), Premium (3), Luxury (2), Exclusive (2)
- [ ] gifts table seed data matches 45% creator share
- [ ] Mobile app displays all 10 gifts with localized names (5 languages)
- [ ] Gift sending uses fixed 45% revenue split (see REQ-CE-002)

**Dependencies:**
- REQ-CE-002 (Corrected revenue formula)
- REQ-DB-005 (JSONB i18n for gift names)
- REQ-API-001 (GET /api/v1/gifts endpoint)

**Design Decisions:**
- **DD-CE-008**: 13 gifts chosen (vs PRD's 10) to fill mid-tier gap
- **DD-CE-009**: Mid-tier at 250/300/400 chosen for psychological pricing
- **DD-CE-010**: Gift categories aligned with coin package tiers

**Notes:**
- PRD showed 10 gifts; user requested 13 with mid-tier additions
- All revenue calculations corrected from 45% to 75-85%
- Gift icons and animations TBD by design team

**Testing Requirements:**
- Test all 13 gifts can be sent
- Verify correct revenue split (75-85% based on creator tier)
- Test gift catalog displays with localized names
- Test mid-tier gifts (250, 300, 400) appear in catalog

---

## REQ-CE-005: Three-Tier Coin Balance Tracking

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement separate balance fields (purchased_balance, bonus_balance, promo_balance) in coin_wallets table to track coin expiry independently: purchased (365d inactivity), bonus (90d), promo (30d).

**Specifications:**

**Database Schema Update:**
```sql
-- coin_wallets table modification
ALTER TABLE coin_wallets
    ADD COLUMN purchased_balance INTEGER NOT NULL DEFAULT 0,
    ADD COLUMN bonus_balance INTEGER NOT NULL DEFAULT 0,
    ADD COLUMN promo_balance INTEGER NOT NULL DEFAULT 0,
    ADD COLUMN last_activity_at TIMESTAMPTZ;  -- For 365d inactivity tracking

-- Migrate existing balance to purchased_balance
UPDATE coin_wallets SET purchased_balance = balance WHERE balance > 0;

-- Drop old balance column (after migration)
ALTER TABLE coin_wallets DROP COLUMN balance;

-- Computed total balance (virtual column or view)
CREATE VIEW coin_wallet_balances AS
SELECT
    user_id,
    purchased_balance,
    bonus_balance,
    promo_balance,
    (purchased_balance + bonus_balance + promo_balance) AS total_balance,
    total_purchased,
    total_spent,
    last_activity_at,
    created_at,
    updated_at
FROM coin_wallets;
```

**Expiry Rules:**

| Balance Type | Expiry Condition | Tracking Field |
|--------------|------------------|----------------|
| purchased_balance | 365 days of account inactivity | last_activity_at |
| bonus_balance | 90 days from credit date | bonus_expires_at (per transaction) |
| promo_balance | 30 days from credit date | promo_expires_at (per transaction) |

**Coin Spending Priority (FIFO by expiry):**
```
1. Promo coins (expire soonest: 30d)
2. Bonus coins (expire next: 90d)
3. Purchased coins (expire last: 365d inactivity)
```

**Coin Credit Logic:**
```python
def credit_coins_from_package(user_id: UUID, package: CoinPackage, db: Session):
    """Credit purchased + bonus coins with separate tracking"""

    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == user_id).first()

    # Credit purchased coins (365d inactivity expiry)
    wallet.purchased_balance += package.base_coins

    # Credit bonus coins (90d expiry)
    wallet.bonus_balance += package.bonus_coins

    # Create transaction record
    transaction = CoinTransaction(
        user_id=user_id,
        transaction_type="purchase",
        purchased_coins=package.base_coins,
        bonus_coins=package.bonus_coins,
        bonus_expires_at=datetime.utcnow() + timedelta(days=90),
        amount_inr=package.price_inr,
        status="completed"
    )
    db.add(transaction)
    db.commit()
```

**Coin Debit Logic (Spending):**
```python
def debit_coins(user_id: UUID, coins_needed: int, db: Session) -> bool:
    """Debit coins with priority: promo → bonus → purchased"""

    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == user_id).first()

    # Check total balance
    total = wallet.promo_balance + wallet.bonus_balance + wallet.purchased_balance
    if total < coins_needed:
        return False  # Insufficient balance

    remaining = coins_needed

    # 1. Deduct from promo first (expires soonest)
    if wallet.promo_balance > 0:
        deduct = min(wallet.promo_balance, remaining)
        wallet.promo_balance -= deduct
        remaining -= deduct

    # 2. Deduct from bonus next
    if remaining > 0 and wallet.bonus_balance > 0:
        deduct = min(wallet.bonus_balance, remaining)
        wallet.bonus_balance -= deduct
        remaining -= deduct

    # 3. Deduct from purchased last
    if remaining > 0:
        wallet.purchased_balance -= remaining

    wallet.total_spent += coins_needed
    wallet.last_activity_at = datetime.utcnow()  # Reset inactivity timer
    db.commit()

    return True
```

**Expiry Enforcement (Daily Cron Job):**
```python
def expire_coins_daily():
    """Run daily at 00:00 UTC to expire coins"""

    db = SessionLocal()
    now = datetime.utcnow()

    # 1. Expire bonus coins (>90 days old)
    expired_bonus = db.execute("""
        UPDATE coin_wallets
        SET bonus_balance = 0
        WHERE user_id IN (
            SELECT user_id FROM coin_transactions
            WHERE bonus_expires_at < :now AND bonus_coins > 0
        )
    """, {"now": now})

    # 2. Expire promo coins (>30 days old)
    expired_promo = db.execute("""
        UPDATE coin_wallets
        SET promo_balance = 0
        WHERE user_id IN (
            SELECT user_id FROM coin_transactions
            WHERE promo_expires_at < :now AND promo_coins > 0
        )
    """, {"now": now})

    # 3. Expire purchased coins (365 days inactivity)
    inactive_threshold = now - timedelta(days=365)
    expired_purchased = db.execute("""
        UPDATE coin_wallets
        SET purchased_balance = 0
        WHERE last_activity_at < :threshold OR last_activity_at IS NULL
    """, {"threshold": inactive_threshold})

    db.commit()

    logger.info(f"Expired coins: {expired_bonus + expired_promo + expired_purchased} users")
```

**User-Facing Balance Display:**
```json
{
  "total_balance": 1500,
  "breakdown": {
    "purchased": 1000,
    "bonus": 400,
    "promo": 100
  },
  "expiry_warnings": [
    {
      "type": "promo",
      "amount": 100,
      "expires_in_days": 5
    },
    {
      "type": "bonus",
      "amount": 400,
      "expires_in_days": 30
    }
  ]
}
```

**Rationale:**
Separate balance fields enable accurate expiry tracking per coin type. Users see exactly which coins expire when, improving transparency. FIFO spending prioritizes expiring coins first, reducing waste.

**Acceptance Criteria:**
- [ ] coin_wallets table has purchased_balance, bonus_balance, promo_balance fields
- [ ] Coin spending follows priority: promo → bonus → purchased
- [ ] Daily cron job expires coins correctly (30d, 90d, 365d)
- [ ] Mobile app shows balance breakdown (purchased/bonus/promo)
- [ ] Expiry warnings displayed when coins near expiration
- [ ] last_activity_at updated on any coin spending (resets 365d timer)
- [ ] Migration script moves existing balance to purchased_balance

**Dependencies:**
- REQ-DB-002 (coin_wallets table structure)
- REQ-API-001 (GET /api/v1/wallet/balance endpoint)

**Design Decisions:**
- **DD-CE-011**: Separate balance fields chosen over transaction-level expiry for simpler queries
- **DD-CE-012**: FIFO spending by expiry chosen to minimize coin waste
- **DD-CE-013**: Daily cron job chosen over real-time expiry for performance

**Notes:**
- 365-day expiry is for account inactivity (no coin spending), not from purchase date
- Bonus and promo coins expire from credit date (fixed 90d/30d)
- Users warned 7 days before expiry via push notification

**Testing Requirements:**
- Test coin credit separates purchased vs bonus
- Test coin spending follows FIFO priority
- Test expiry cron job expires coins correctly
- Test inactivity timer resets on spending
- Test balance breakdown displayed in mobile app

---

## REQ-CE-006: 1 Coin = ₹1 Spending Standard

**Priority:** P0
**Type:** Business Requirement
**Status:** Approved

**Description:**
Enforce 1 coin = ₹1 conversion rate for ALL coin spending (calls, gifts, rooms), regardless of user's purchase price per coin (₹0.71 - ₹0.98). Standard virtual currency model.

**Specifications:**

**Conversion Standard:**
```
When coins are SPENT:
- 100 coins spent = ₹100 gross revenue
- Creator receives ₹75-85 (based on tier)
- Platform keeps ₹15-25 (based on tier)

Regardless of what user paid per coin:
- VIP package: User paid ₹0.71/coin
- Popular package: User paid ₹0.90/coin
→ Both spend 100 coins = ₹100 gross (same value)
```

**Purchase vs Spending:**

| User Action | Per-Coin Value | Example |
|-------------|----------------|---------|
| **Purchase** (varies by package) | ₹0.71 - ₹0.98 | User buys VIP: ₹1999 ÷ 2800 coins = ₹0.71/coin |
| **Spending** (fixed) | ₹1.00 | User spends 100 coins = ₹100 gross revenue |

**Why This Model:**
1. **Simplicity:** Fixed spending value, easy mental math for users
2. **Fairness:** All users' coins have equal spending power
3. **Standard practice:** All virtual currency platforms use this model (Roblox Robux, Fortnite V-Bucks, etc.)
4. **Profit margin:** Platform profit = purchase discount + platform revenue share

**Platform Economics:**

**Example 1: VIP Package User (Best Margin)**
```
User purchases VIP:
- Pays: ₹1999
- Receives: 2800 coins (₹0.71/coin purchase price)

User spends 1000 coins on calls:
- Gross value: 1000 × ₹1 = ₹1000
- Creator earns (75%): ₹750
- Platform earns (25%): ₹250

Platform total revenue:
- From purchase: ₹1999 (user paid)
- Minus creator share: ₹750 (on 1000 coins spent)
- Net: ₹1249 on 1000 coins spent
- Remaining: 1800 coins × ₹1 = ₹1800 potential revenue
```

**Example 2: Popular Package User (Lower Margin)**
```
User purchases Popular:
- Pays: ₹99
- Receives: 110 coins (₹0.90/coin purchase price)

User spends 100 coins on gift:
- Gross value: 100 × ₹1 = ₹100
- Creator earns (75%): ₹75
- Platform earns (25%): ₹25

Platform total revenue:
- From purchase: ₹99 (user paid)
- Minus creator share: ₹75 (on 100 coins spent)
- Net: ₹24 on 100 coins spent
- Remaining: 10 bonus coins × ₹1 = ₹10 potential revenue
```

**Implementation:**
```python
# Constants
COIN_TO_INR_SPENDING = Decimal("1.0")  # Fixed: 1 coin = ₹1 when spent

# Call billing
def calculate_call_cost(duration_seconds: int, rate_per_minute: Decimal) -> Decimal:
    """Calculate call cost in coins"""
    minutes = -(-duration_seconds // 60)  # Round up
    coins = Decimal(str(minutes)) * rate_per_minute
    # coins directly = rupees (no conversion needed, 1:1 ratio)
    return coins

# Gift pricing
def get_gift_gross_value(gift: Gift) -> Decimal:
    """Get gift's gross value in rupees"""
    return gift.coin_price * COIN_TO_INR_SPENDING  # 1 coin = ₹1

# Revenue calculation
def calculate_revenue(coins_spent: Decimal, creator_tier: Decimal) -> Tuple[Decimal, Decimal]:
    """Calculate creator and platform earnings"""
    gross_inr = coins_spent * COIN_TO_INR_SPENDING  # 100 coins = ₹100
    creator_earnings = gross_inr * creator_tier      # ₹75-85
    platform_earnings = gross_inr - creator_earnings  # ₹15-25
    return creator_earnings, platform_earnings
```

**Rationale:**
Standard virtual currency model used by all major platforms. Simplifies user mental math (100 coins = ₹100 value). Platform profits from package discounts (₹0.71-0.98 purchase vs ₹1.00 spending) plus revenue share (15-25%).

**Acceptance Criteria:**
- [ ] All coin spending uses 1 coin = ₹1 conversion (calls, gifts, rooms)
- [ ] User's purchase price per coin (₹0.71-0.98) does NOT affect spending value
- [ ] Documentation clearly states: "1 coin = ₹1 when spending"
- [ ] Gift pricing displays coin cost and rupee value (10 coins = ₹10 value)
- [ ] Call rates displayed in coins/minute (₹10/min = 10 coins/min)
- [ ] Revenue calculations use ₹1 per coin for gross amount

**Dependencies:**
- REQ-CE-001 (Coin packages with variable purchase price)
- REQ-CE-002 (Revenue split formula)
- REQ-CE-003 (Creator call rates)
- REQ-CE-004 (Gift pricing)

**Design Decisions:**
- **DD-CE-014**: 1 coin = ₹1 spending chosen for simplicity (standard virtual currency model)
- **DD-CE-015**: Purchase price variance (₹0.71-0.98) creates platform profit margin

**Notes:**
- This confirms PRD Section 9.4 assumption (1 coin = ₹1 for spending)
- Platform profit = purchase discount + revenue share
- VIP package buyers get best coin value (₹0.71/coin) but spend at ₹1/coin

**Testing Requirements:**
- Test 100 coins spent = ₹100 gross value (regardless of package)
- Test creator earnings = gross × tier_percent (75-85%)
- Test platform earnings = gross - creator_earnings
- Verify purchase price doesn't affect spending value

---

## REQ-CE-007: Bonus Coins Equal Revenue Share

**Priority:** P1
**Type:** Business Requirement
**Status:** Approved

**Description:**
Bonus coins receive the SAME 75-85% creator revenue share as purchased coins when spent. Platform absorbs bonus coin cost as marketing expense, not creator penalty.

**Specifications:**

**Revenue Share Policy:**
```
Purchased Coins:
- User spends 100 purchased coins
- Creator earns: ₹75-85 (based on tier)
- Platform earns: ₹15-25

Bonus Coins:
- User spends 100 bonus coins
- Creator earns: ₹75-85 (SAME as purchased coins)
- Platform earns: ₹15-25 (SAME as purchased coins)

NO DISTINCTION in revenue calculation
```

**Why Same Revenue Share:**
1. **Fair to creators:** Bonus is platform's marketing cost, not creator's problem
2. **Simple implementation:** No need to track coin source when spending
3. **Creator retention:** Ensures creators don't lose earnings on bonus-heavy users
4. **Competitive advantage:** Other platforms often penalize creators on bonus coins

**Platform Economics:**

**Example: VIP Package (₹1999 = 2800 coins)**
```
Purchase breakdown:
- Base coins: 1750 (user paid ₹1999)
- Bonus coins: 1050 (platform marketing cost)
- Platform upfront revenue: ₹1999

User spends all 2800 coins on calls (Tier 1 creator, 75%):
- Gross value: 2800 × ₹1 = ₹2800
- Creator earns: ₹2800 × 0.75 = ₹2100
- Platform earns: ₹2800 × 0.25 = ₹700

Platform net:
- Revenue: ₹1999 (upfront) + ₹700 (share) = ₹2699
- Creator payout: ₹2100
- Net profit: ₹599 (₹2699 - ₹2100)

Even with 60% bonus, platform profits ₹599 on ₹1999 spent
```

**Alternative Model (Rejected):**
```
❌ Lower revenue share for bonus coins (e.g., 50% instead of 75-85%)

Problems:
1. Complex tracking: Need to track which coins (purchased vs bonus) are spent
2. Unfair to creators: Penalizes creators for platform's marketing decisions
3. Creator dissatisfaction: Top creators would avoid platform
4. Implementation complexity: Spending logic becomes complicated
```

**Implementation:**
```python
def calculate_creator_earnings(
    creator_id: UUID,
    coins_spent: Decimal,
    db: Session
) -> Tuple[Decimal, Decimal, Decimal]:
    """
    Calculate creator earnings - NO distinction between purchased and bonus coins

    Bonus coins are platform marketing cost, not creator penalty
    """
    COIN_TO_INR = Decimal("1.0")

    # Query creator tier (75%, 80%, or 85%)
    performance = db.query(CreatorPerformance).filter(...).first()
    tier_percent = determine_tier(performance)

    # Calculate earnings (bonus and purchased coins treated equally)
    gross_amount = coins_spent * COIN_TO_INR
    creator_earnings = gross_amount * tier_percent
    platform_earnings = gross_amount - creator_earnings

    # NO TRACKING of coin source (purchased vs bonus)
    return creator_earnings, platform_earnings, tier_percent
```

**Coin Spending (No Source Tracking):**
```python
def debit_coins(user_id: UUID, coins_needed: int, db: Session):
    """
    Debit coins - priority is by expiry (promo → bonus → purchased)
    but revenue share is SAME regardless of source
    """
    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == user_id).first()

    # Debit with priority (REQ-CE-005)
    # ... deduct from promo_balance, bonus_balance, purchased_balance ...

    # Create transaction
    transaction = CoinTransaction(
        user_id=user_id,
        transaction_type="spend",
        coins_spent=coins_needed,
        # NO field for "purchased_coins_spent" vs "bonus_coins_spent"
        # Revenue calculation doesn't care about source
    )
    db.add(transaction)
```

**Rationale:**
Bonus coins are marketing incentive to drive purchases. Penalizing creators on bonus coins would reduce creator earnings, hurting retention. Platform still profits from package pricing (₹0.71-0.98/coin purchase vs ₹1/coin spending).

**Acceptance Criteria:**
- [ ] Bonus coins receive 75-85% creator share (same as purchased coins)
- [ ] NO tracking of coin source (purchased vs bonus) during spending
- [ ] Revenue calculation treats all coins equally
- [ ] Platform absorbs bonus cost as marketing expense
- [ ] Financial projections account for bonus coin cost
- [ ] Creators see consistent earnings per coin spent (no bonus penalty)

**Dependencies:**
- REQ-CE-002 (Revenue split formula)
- REQ-CE-005 (Three-tier balance tracking)
- REQ-CE-006 (1 coin = ₹1 spending standard)

**Design Decisions:**
- **DD-CE-016**: Same revenue share chosen for creator fairness and retention
- **DD-CE-017**: No source tracking chosen for implementation simplicity

**Notes:**
- Bonus is platform's marketing cost (like cashback/rewards)
- Even with 60% bonus (VIP package), platform still profits
- Competitive advantage: Fair to creators vs platforms that penalize bonus spending

**Testing Requirements:**
- Test bonus coins earn creator 75-85% (same as purchased coins)
- Test no distinction in revenue calculation based on coin source
- Verify platform can still profit with 60% bonus packages

---

## REQ-CE-008: Technical Refunds Only Policy

**Priority:** P1
**Type:** Business Requirement
**Status:** Approved

**Description:**
Implement strict refund policy: Refunds only within 7 days for verified technical issues preventing coin use. NO refunds for user regret or buyer's remorse.

**Specifications:**

**Refund Eligibility Criteria:**

| Scenario | Refundable? | Reason |
|----------|-------------|--------|
| Payment succeeded, coins NOT credited | ✅ YES | Technical issue |
| App crashed during purchase | ✅ YES | Technical issue |
| User regrets purchase | ❌ NO | User decision |
| User spent all coins | ❌ NO | Coins already used |
| User spent 50%, wants refund for unused 50% | ❌ NO | Partial usage not refundable |
| Payment failed, no coins credited | ✅ YES (Auto-refund) | Failed transaction |
| Account suspended, coins inaccessible | ✅ YES | Technical/policy issue |

**Refund Process:**
```
1. User submits support ticket via app
   ├─ Category: "Payment Issues"
   └─ Subcategory: "Request Refund"

2. Support agent reviews ticket
   ├─ Check: Was payment successful? (Razorpay transaction status)
   ├─ Check: Were coins credited to wallet? (coin_transactions table)
   ├─ Check: Have any coins been spent? (total_spent)
   └─ Check: Is issue within 7 days? (created_at timestamp)

3. Verification
   ├─ If technical issue verified (coins not credited, app error logs)
   │   └─ Approve refund
   ├─ If user spent coins already
   │   └─ Deny refund (coins used, no longer unused)
   └─ If user regret (no technical issue)
       └─ Deny refund (policy: no refunds for user decisions)

4. Refund Execution (if approved)
   ├─ Razorpay refund initiated (razorpay.refund.create())
   ├─ Update coin_transactions status = "refunded"
   ├─ Deduct coins from wallet (if credited)
   └─ Log refund in audit trail
```

**Database Tracking:**
```sql
-- coin_transactions table
CREATE TABLE coin_transactions (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    transaction_type VARCHAR(50),  -- 'purchase', 'refund'
    razorpay_payment_id VARCHAR(255),
    razorpay_refund_id VARCHAR(255),  -- Populated on refund
    status VARCHAR(50),  -- 'completed', 'refunded', 'failed'
    refund_reason TEXT,  -- Technical issue description
    refund_approved_by UUID REFERENCES users(id),  -- Admin who approved
    refund_approved_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ
);
```

**Support Ticket Template:**
```
Title: Refund Request - [User Name] - [Transaction ID]

Details:
- User ID: [UUID]
- Transaction ID: [coin_transaction.id]
- Razorpay Payment ID: [razorpay_payment_id]
- Amount: ₹[amount_inr]
- Coins Expected: [coins_purchased]
- Purchase Date: [created_at]
- Days Since Purchase: [X] days

Issue:
[User's description of technical issue]

Verification:
☐ Payment successful in Razorpay? (Yes/No)
☐ Coins credited to wallet? (Yes/No)
☐ Coins spent by user? [X] coins spent
☐ Within 7-day window? (Yes/No)
☐ Technical issue verified? (Yes/No)

Logs:
[Relevant app error logs, if any]

Decision:
☐ APPROVE REFUND - Technical issue verified, within 7 days, coins unused
☐ DENY REFUND - [Reason: coins spent / no technical issue / outside 7-day window / user regret]

Approved By: [Admin Name]
Refund ID: [razorpay_refund_id]
```

**Refund Implementation:**
```python
async def process_refund_request(transaction_id: UUID, admin_id: UUID, db: Session):
    """Process refund request with strict verification"""

    transaction = db.query(CoinTransaction).filter(
        CoinTransaction.id == transaction_id
    ).first()

    # Verification checks
    if not transaction:
        raise HTTPException(404, detail="Transaction not found")

    # Check 7-day window
    days_since_purchase = (datetime.utcnow() - transaction.created_at).days
    if days_since_purchase > 7:
        raise HTTPException(400, detail="Refund window expired (>7 days)")

    # Check if coins spent
    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == transaction.user_id).first()
    coins_at_purchase = transaction.coins_purchased + transaction.bonus_coins
    # If user spent ANY coins from this purchase, deny refund
    # (Note: This is simplified; real implementation needs granular tracking)

    # Initiate Razorpay refund
    razorpay_refund = razorpay_client.payment.refund(
        transaction.razorpay_payment_id,
        {
            "amount": int(transaction.amount_inr * 100),  # Paisa
            "notes": {
                "reason": "Technical issue - coins not credited",
                "transaction_id": str(transaction_id)
            }
        }
    )

    # Update transaction
    transaction.status = "refunded"
    transaction.razorpay_refund_id = razorpay_refund["id"]
    transaction.refund_approved_by = admin_id
    transaction.refund_approved_at = datetime.utcnow()
    transaction.refund_reason = "Technical issue verified by support"

    # Deduct coins from wallet (if credited)
    wallet.purchased_balance -= transaction.coins_purchased
    wallet.bonus_balance -= transaction.bonus_coins

    db.commit()

    # Send notification to user
    send_push_notification(
        transaction.user_id,
        "Refund Processed",
        f"Your refund of ₹{transaction.amount_inr} has been initiated. It will reflect in your account within 5-7 business days."
    )
```

**Rationale:**
Strict refund policy prevents abuse (user buys coins, uses them, requests refund). Technical-only refunds protect platform from losses while ensuring genuine technical issues are resolved fairly.

**Acceptance Criteria:**
- [ ] Refunds only approved for verified technical issues
- [ ] 7-day window enforced from purchase date
- [ ] NO refunds if any coins spent from purchase
- [ ] Support ticket workflow implemented
- [ ] Razorpay refund API integration working
- [ ] coin_transactions table tracks refund status
- [ ] Admin approval required for all refunds
- [ ] Refund audit trail maintained

**Dependencies:**
- REQ-RM-002 (Razorpay integration)
- REQ-DB-002 (coin_transactions table)

**Design Decisions:**
- **DD-CE-018**: Technical issues only chosen to prevent abuse
- **DD-CE-019**: 7-day window chosen as standard refund period
- **DD-CE-020**: Admin approval required for accountability

**Notes:**
- Auto-refunds for failed payments (Razorpay automatic)
- Manual review required for all other refund requests
- Refund time: 5-7 business days (Razorpay standard)

**Testing Requirements:**
- Test refund approved for technical issue within 7 days
- Test refund denied if coins spent
- Test refund denied if >7 days
- Test refund denied for user regret
- Test Razorpay refund API integration

---

## REQ-CE-009: No Peak Hour Pricing

**Priority:** P2
**Type:** Business Requirement
**Status:** Approved

**Description:**
Remove peak hour pricing multiplier (PRD's 1.5x during 10 PM - 2 AM IST). Creator-set rates are fixed 24/7 for predictable caller UX. Creators can set higher base rates if desired.

**Specifications:**

**PRD Section 9.2 Peak Hour Pricing (REMOVED):**
```
❌ REMOVED: Peak Hour Pricing (10 PM - 2 AM IST)
- All rates × 1.5
- Regular: 8 coins/min audio, 12 video (vs 5/8 non-peak)
- Verified: 18 coins/min audio, 27 video (vs 12/18 non-peak)
```

**APPROVED MODEL: Fixed 24/7 Rates**
```
✓ Creator sets rate once, applies 24/7:
- Priya: ₹12/min audio, ₹18/min video (ALL HOURS)
- Rahul: ₹25/min audio, ₹35/min video (ALL HOURS)

No time-based multipliers
No surprise price changes
```

**Why No Peak Pricing:**

1. **Creator Control:** If creator wants higher peak earnings, they set higher base rate
2. **Predictable UX:** Callers see consistent rate anytime (no confusion)
3. **Simpler Implementation:** No time-based rate calculation logic
4. **International Users:** IST peak hours (10 PM - 2 AM) don't align with global users

**Implementation:**
```python
def get_call_rate(creator_id: UUID, call_type: str, db: Session) -> Decimal:
    """Get creator's call rate - NO TIME-BASED MULTIPLIER"""

    creator_rates = db.query(CreatorCallRates).filter(
        CreatorCallRates.user_id == creator_id
    ).first()

    if creator_rates:
        rate = (
            creator_rates.audio_rate_per_minute if call_type == "audio"
            else creator_rates.video_rate_per_minute
        )
    else:
        # Default rates
        rate = Decimal("10.00") if call_type == "audio" else Decimal("15.00")

    # NO PEAK HOUR CHECK
    # current_hour = datetime.now(timezone.utc).hour
    # if 14 <= current_hour < 18:  # 10 PM - 2 AM IST (REMOVED)
    #     rate *= Decimal("1.5")

    return rate  # Fixed rate 24/7
```

**Caller Experience:**
```
Priya's Profile:
- Audio: ₹12/min
- Video: ₹18/min
- Rate is same 24/7 (no surprises)

User initiates call at 11 PM IST:
- Charged: ₹12/min audio (NOT ₹18/min with peak multiplier)
- Predictable billing
```

**Creator Strategy:**
```
If creator wants higher peak earnings:
- Option 1: Set higher base rate (₹20/min audio instead of ₹12/min)
- Option 2: Manually update rate during peak hours via API
  - Set ₹15/min at 10 PM
  - Set ₹10/min at 2 AM
  - (Manual control, not automatic multiplier)
```

**Rationale:**
Peak hour pricing adds complexity and confusion. Creator-set rates give control. If creator wants higher peak rates, they can adjust manually. Simplified UX outweighs potential peak hour revenue gains.

**Acceptance Criteria:**
- [ ] NO time-based rate multipliers in code
- [ ] Creator rate is fixed 24/7 (no automatic changes)
- [ ] Caller sees consistent rate regardless of call time
- [ ] PRD's peak hour pricing (1.5x) NOT implemented
- [ ] Creators can manually update rates anytime via API (optional)
- [ ] Documentation states: "Creator rates are fixed 24/7"

**Dependencies:**
- REQ-CE-003 (Creator-set call rates)
- REQ-API-001 (PUT /api/v1/creator/rates endpoint)

**Design Decisions:**
- **DD-CE-021**: No peak pricing chosen for predictable UX
- **DD-CE-022**: Creator manual rate control chosen over automatic multipliers

**Notes:**
- PRD Section 9.2 peak hour pricing (1.5x) explicitly removed
- Creators have full control: can set high rate if want peak earnings
- Simplifies billing logic (no time zone conversions)

**Testing Requirements:**
- Test call rate is same at 10 AM and 11 PM (no peak multiplier)
- Test caller charged consistent rate regardless of time
- Verify NO peak hour logic in call billing code

---

## REQ-CE-010: Coin Economy Analytics & Monitoring

**Priority:** P1
**Type:** Operational Requirement
**Status:** Approved

**Description:**
Implement comprehensive analytics and monitoring for coin economy health: package conversions, coin velocity, expiry rates, creator earnings distribution, and revenue projections.

**Specifications:**

**Key Metrics to Track:**

**1. Package Conversion Metrics:**
```sql
-- Daily package sales
SELECT
    package_name,
    COUNT(*) as purchases,
    SUM(amount_inr) as revenue,
    SUM(coins_purchased) as coins_sold,
    SUM(bonus_coins) as bonus_given
FROM coin_transactions
WHERE transaction_type = 'purchase'
    AND created_at >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY package_name
ORDER BY revenue DESC;

-- Best seller: Popular (₹99) or Best Seller (₹499)?
-- Avg order value (AOV)
```

**2. Coin Velocity (How fast coins are spent):**
```sql
-- Days from purchase to full spending
SELECT
    user_id,
    AVG(EXTRACT(EPOCH FROM (spent_at - purchased_at)) / 86400) as avg_days_to_spend
FROM (
    SELECT
        user_id,
        created_at as purchased_at,
        LAG(created_at) OVER (PARTITION BY user_id ORDER BY created_at) as spent_at
    FROM coin_transactions
    WHERE transaction_type IN ('purchase', 'spend')
) subquery
GROUP BY user_id;

-- Target: <7 days for healthy economy (users spending quickly)
```

**3. Coin Expiry Rate:**
```sql
-- Coins expired vs purchased (monthly)
SELECT
    DATE_TRUNC('month', created_at) as month,
    SUM(CASE WHEN transaction_type = 'purchase' THEN coins_purchased + bonus_coins ELSE 0 END) as coins_credited,
    SUM(CASE WHEN transaction_type = 'expiry' THEN coins_expired ELSE 0 END) as coins_expired,
    ROUND(100.0 * SUM(CASE WHEN transaction_type = 'expiry' THEN coins_expired ELSE 0 END) /
          NULLIF(SUM(CASE WHEN transaction_type = 'purchase' THEN coins_purchased + bonus_coins ELSE 0 END), 0), 2) as expiry_rate_pct
FROM coin_transactions
GROUP BY month
ORDER BY month DESC;

-- Target: <5% expiry rate (most coins spent before expiry)
```

**4. Creator Earnings Distribution:**
```sql
-- Revenue by creator tier
SELECT
    CASE
        WHEN total_earnings_30d >= 200000 THEN 'Tier 3 (85%)'
        WHEN total_earnings_30d >= 50000 THEN 'Tier 2 (80%)'
        ELSE 'Tier 1 (75%)'
    END as tier,
    COUNT(*) as creators,
    SUM(total_earnings_30d) as total_earnings,
    AVG(total_earnings_30d) as avg_earnings,
    MAX(total_earnings_30d) as top_earner
FROM creator_performance
WHERE total_earnings_30d > 0
GROUP BY tier
ORDER BY tier DESC;

-- Target: 5% in Tier 3, 20% in Tier 2 (top performers)
```

**5. Coin Flow Analysis:**
```sql
-- Where coins are spent (calls vs gifts vs rooms)
SELECT
    spending_type,  -- 'call', 'gift', 'room_gift'
    COUNT(*) as transactions,
    SUM(coins_spent) as total_coins,
    ROUND(100.0 * SUM(coins_spent) / SUM(SUM(coins_spent)) OVER (), 2) as pct_of_spending
FROM coin_transactions
WHERE transaction_type = 'spend'
    AND created_at >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY spending_type
ORDER BY total_coins DESC;

-- Expected: 60% calls, 30% gifts, 10% rooms (hypothesis)
```

**Analytics Dashboard (Admin):**
```
Coin Economy Health Dashboard
─────────────────────────────────────────

📊 Today's Snapshot:
- Coins Purchased: 45,230 (₹4.2M revenue)
- Coins Spent: 38,910 (86% utilization)
- Avg Package: ₹412 (Value package popular)
- Active Users: 3,240 (with coin balance)

💰 Revenue Breakdown:
- Platform Share (15-25%): ₹780K
- Creator Payouts (75-85%): ₹3.42M
- Net Margin: 18.6%

⏱️ Coin Velocity:
- Avg Days to Spend: 4.2 days ✅ (healthy)
- Fastest spenders: <1 day (25% of users)
- Slowest spenders: >30 days (5% of users)

⚠️ Expiry Alerts:
- Bonus coins expiring (next 7d): 12,450 coins (₹12.4K value)
- Promo coins expiring (next 7d): 3,200 coins
- Users to notify: 340

🎁 Spending Patterns:
- Calls: 58% (₹2.4M)
- Gifts: 32% (₹1.3M)
- Room Gifts: 10% (₹410K)

🏆 Top Performers:
- Top Creator: ₹450K (30d, Tier 3)
- Top Spender: 25,000 coins/month
- Top Package: Best Seller (₹499) - 42% of revenue
```

**Alerting Rules:**
```python
# Alert: Low coin velocity (>14 days avg)
if avg_days_to_spend > 14:
    send_alert("Low coin velocity - users not spending coins fast")

# Alert: High expiry rate (>10%)
if expiry_rate_pct > 10:
    send_alert("High coin expiry - consider reminder campaigns")

# Alert: Imbalanced tier distribution (>50% Tier 1)
if tier_1_creators_pct > 50:
    send_alert("Too many Tier 1 creators - incentivize creator growth")

# Alert: Negative platform margin
if platform_margin_pct < 10:
    send_alert("CRITICAL: Low platform margin - review bonus/revenue split")
```

**N8N Workflow Integration:**
```
Daily Coin Economy Report (8 AM IST):
1. Query all metrics (packages, velocity, expiry, creators)
2. Generate dashboard summary
3. Send email to CEO/CFO with key metrics
4. Trigger alerts if thresholds breached
5. Export to Google Sheets for historical tracking
```

**Rationale:**
Coin economy analytics enable data-driven decisions: adjust package pricing, optimize bonus percentages, identify creator growth opportunities, and ensure healthy coin circulation.

**Acceptance Criteria:**
- [ ] Package conversion metrics tracked (sales, revenue, AOV)
- [ ] Coin velocity calculated (days from purchase to spend)
- [ ] Expiry rate monitored (<5% target)
- [ ] Creator earnings distribution analyzed (tier breakdown)
- [ ] Coin flow analysis (calls vs gifts vs rooms)
- [ ] Admin dashboard displays real-time metrics
- [ ] N8N daily report sent to leadership
- [ ] Alerts triggered for unhealthy metrics (low velocity, high expiry)

**Dependencies:**
- REQ-DB-002 (coin_transactions table)
- REQ-DB-004 (creator_performance table)
- REQ-TA-008 (Monitoring & Logging from Section 6)
- REQ-AW-001 (N8N workflows - Section 11)

**Design Decisions:**
- **DD-CE-023**: Daily reporting chosen for leadership visibility
- **DD-CE-024**: <7 days coin velocity target chosen for healthy economy
- **DD-CE-025**: <5% expiry rate target chosen to minimize waste

**Notes:**
- Analytics inform future coin economy optimizations
- Historical data enables trend analysis (seasonality, growth)
- Metrics tracked in Prometheus + CloudWatch + Google Sheets

**Testing Requirements:**
- Test all SQL queries return correct metrics
- Test dashboard displays accurate real-time data
- Test N8N daily report sends successfully
- Test alerts trigger at correct thresholds

---

## REQ-CE-011: Daily Login Bonus Program

**Priority:** P2
**Type:** Engagement Requirement
**Status:** Approved

**Description:**
Implement daily login bonus reward system: 20 coins at signup (one-time) + 5 bonus coins per day for users who log in daily. Encourages daily engagement and reduces initial coin purchase friction.

**Specifications:**

**Signup Bonus (One-Time):**
```
New user creates account:
  ├─ Credit 20 bonus coins to coin_wallets.bonus_balance
  ├─ Bonus expires in 90 days (standard bonus expiry)
  └─ Tracked in coin_transactions with type="signup_bonus"
```

**Daily Login Bonus:**
```
User opens app each day:
  ├─ Check last_login_bonus_at timestamp
  ├─ If >24 hours ago OR NULL:
  │   ├─ Credit 5 bonus coins to bonus_balance
  │   ├─ Update last_login_bonus_at = NOW()
  │   ├─ Track in coin_transactions with type="daily_login_bonus"
  │   └─ Show notification: "Daily Login Bonus: +5 coins!"
  └─ If <24 hours ago:
      └─ No bonus (already claimed today)
```

**Database Schema:**
```sql
-- coin_wallets table addition
ALTER TABLE coin_wallets
    ADD COLUMN last_login_bonus_at TIMESTAMPTZ;  -- Track last daily bonus claim

-- coin_transactions types
-- 'signup_bonus' → 20 coins at signup
-- 'daily_login_bonus' → 5 coins per day
```

**Timing Rules:**
- **Daily reset:** Based on user's last claim (24-hour rolling window, NOT midnight)
- **Signup timing:** Immediately after account creation (before onboarding complete)
- **Streak tracking:** Optional future enhancement (not MVP)

**Expiry:**
- Signup bonus (20 coins): 90-day expiry (standard bonus expiry)
- Daily login bonus (5 coins/day): 90-day expiry from credit date

**UI/UX:**
```
Signup flow:
  Step 1: Account created
  Step 2: [MODAL] "Welcome! Here's 20 coins to get started! 🎉"
  Step 3: Continue to onboarding

Daily login:
  - Check on app open (background)
  - If eligible: Show toast notification "+5 coins! Daily Login Bonus 🎁"
  - Coin balance updates automatically
```

**Implementation:**
```python
async def credit_signup_bonus(user_id: UUID, db: Session):
    """Credit 20 coins signup bonus (one-time)"""

    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == user_id).first()

    # Credit signup bonus
    wallet.bonus_balance += 20

    # Create transaction record
    transaction = CoinTransaction(
        user_id=user_id,
        transaction_type="signup_bonus",
        bonus_coins=20,
        bonus_expires_at=datetime.utcnow() + timedelta(days=90),
        status="completed"
    )
    db.add(transaction)
    db.commit()

    return {"bonus_credited": 20, "message": "Welcome bonus added!"}


async def check_daily_login_bonus(user_id: UUID, db: Session):
    """Check and credit daily login bonus if eligible"""

    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == user_id).first()

    # Check if 24+ hours since last claim
    now = datetime.utcnow()
    if wallet.last_login_bonus_at:
        hours_since = (now - wallet.last_login_bonus_at).total_seconds() / 3600
        if hours_since < 24:
            return {"eligible": False, "next_bonus_in_hours": round(24 - hours_since, 1)}

    # Credit daily bonus
    wallet.bonus_balance += 5
    wallet.last_login_bonus_at = now

    # Create transaction record
    transaction = CoinTransaction(
        user_id=user_id,
        transaction_type="daily_login_bonus",
        bonus_coins=5,
        bonus_expires_at=now + timedelta(days=90),
        status="completed"
    )
    db.add(transaction)
    db.commit()

    return {"eligible": True, "bonus_credited": 5, "message": "+5 coins! Daily Login Bonus 🎁"}
```

**Trigger Points:**
- **Signup bonus:** POST /api/v1/auth/register (after account creation)
- **Daily login bonus:** Mobile app startup (check on app open)

**Rationale:**
Signup bonus reduces initial friction (users can try features before purchasing). Daily login bonus drives DAU/retention. Combined with low ₹49 package, creates strong onboarding funnel. Per user decision Q7: "keep option 1 and also add daily login bonus of 5 coins/day only if they login".

**Acceptance Criteria:**
- [ ] New users receive 20 bonus coins at signup (one-time)
- [ ] Users receive 5 bonus coins per day on login (24-hour cooldown)
- [ ] last_login_bonus_at timestamp tracked in coin_wallets
- [ ] Signup bonus transaction created with type="signup_bonus"
- [ ] Daily login bonus transaction created with type="daily_login_bonus"
- [ ] Both bonuses expire after 90 days (standard bonus expiry)
- [ ] Modal shown on signup: "Welcome! Here's 20 coins"
- [ ] Toast notification on daily login: "+5 coins! Daily Login Bonus"
- [ ] 24-hour cooldown enforced (no double claims)

**Dependencies:**
- REQ-CE-005 (Three-tier balance tracking with bonus_balance)
- REQ-DB-002 (coin_transactions table)
- REQ-API-001 (Wallet APIs)

**Design Decisions:**
- **DD-CE-026**: 20 coins signup chosen for trial (2 calls at ₹10/min)
- **DD-CE-027**: 5 coins daily chosen for engagement without abuse
- **DD-CE-028**: 24-hour rolling window chosen over midnight reset (simpler, no timezone issues)

**Notes:**
- Signup bonus: One-time only (checked by not creating duplicate signup_bonus transactions)
- Daily bonus: Unlimited (users can claim every day indefinitely)
- Bonus coins subject to 90-day expiry (per REQ-CE-005)
- Future enhancement: Streak bonuses (7-day streak = extra coins)

**Testing Requirements:**
- Test signup bonus credited once at registration
- Test daily login bonus credited after 24+ hours
- Test daily login bonus NOT credited within 24 hours
- Test bonus coins expire after 90 days
- Test transaction records created correctly
- Test UI notifications shown (modal + toast)

---

## Summary

**Total Requirements:** 11
**Priority Breakdown:**
- **P0 (Critical):** 6 requirements
  - REQ-CE-001: Coin packages (₹49-1999, 6 packages including entry-level ₹49)
  - REQ-CE-002: Dual revenue model (75-85% for calls, 45% for gifts)
  - REQ-CE-003: Creator-set call rates (₹8-25/min)
  - REQ-CE-004: Gift pricing (10 gifts with 45% fixed revenue)
  - REQ-CE-005: Three-tier balance tracking (purchased/bonus/promo)
  - REQ-CE-006: 1 coin = ₹1 spending standard

- **P1 (High):** 3 requirements
  - REQ-CE-007: Bonus coins equal revenue share
  - REQ-CE-008: Technical refunds only policy
  - REQ-CE-010: Coin economy analytics

- **P2 (Medium):** 2 requirements
  - REQ-CE-009: No peak hour pricing
  - REQ-CE-011: Daily login bonus program (20 signup + 5/day)

**Key Achievements:**
1. **Dual revenue model:** Balanced approach with 75-85% for calls, 45% for gifts
2. **Entry-level access:** ₹49 package + 20 signup bonus + 5 coins/day login bonus
3. **Simplified pricing:** No peak hour multipliers, fixed creator rates 24/7
4. **Fair bonus policy:** Creators earn same revenue share on bonus coins
5. **Comprehensive tracking:** Three-tier balance system for accurate expiry
6. **Engagement incentives:** Daily login rewards drive DAU/retention

**Timeline Impact:** +0 weeks (coin economy design, no implementation delay)
**Updated MVP Timeline:** 16-20 weeks (unchanged from Section 13)

---

**Next Steps:**
1. Update .metadata.json to mark Section 9 as completed
2. Update CHANGELOG.md with Section 9 entry
3. Present Section 9 demo for user approval
4. Continue to Section 10: Creator Payout System

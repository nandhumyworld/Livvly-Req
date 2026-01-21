# Requirements: Database Schema

**Section**: 07-database-schema
**Source Lines**: 658-1139
**Generated**: 2026-01-20
**Status**: Complete

---

## Core Database Requirements

### REQ-DB-001: PostgreSQL via Supabase
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: All persistent data must be stored in PostgreSQL database via Supabase with proper schema design supporting all application entities.

**Rationale**: PostgreSQL provides ACID compliance essential for financial transactions, with Supabase handling managed infrastructure.

**Acceptance Criteria**:
- PostgreSQL database provisioned on Supabase
- All tables from schema created with proper data types
- Foreign key constraints enforced
- Connection pooling configured
- Database migrations tracked in version control

**Dependencies**: REQ-TA-002, REQ-TA-004
**Related Design Decisions**: DD-TA-001, DD-TA-002

**Notes**: Use Supabase CLI for migration management.

---

### REQ-DB-002: Row Level Security (RLS)
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: All database tables must have Row Level Security enabled with appropriate policies for user, creator, and admin roles.

**Rationale**: RLS provides defense-in-depth, preventing unauthorized data access even if application code has vulnerabilities.

**Acceptance Criteria**:
- RLS enabled on ALL tables
- Users can only access their own data (read/write)
- Creators can access their call/gift earnings data
- Admins use service role key to bypass RLS
- Public tables (coin_packages, gifts, subscription_plans) have read-only public policies
- RLS policies tested for each user role

**Dependencies**: REQ-TA-002
**Related Design Decisions**: DD-DB-004, DD-TA-002

**Notes**: Test RLS policies thoroughly before production deployment.

---

### REQ-DB-003: Soft Delete Pattern
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: User-related and financial tables must implement soft delete pattern with deleted_at timestamp instead of hard deletes.

**Rationale**: Soft deletes preserve audit trail for compliance (GST, TDS), dispute resolution, and data recovery.

**Acceptance Criteria**:
- deleted_at TIMESTAMP NULL column on: users, transactions, calls, gift_transactions, rooms, withdrawals, reports
- Database views for active records: active_users, active_transactions, etc.
- RLS policies include `deleted_at IS NULL` condition
- Soft delete triggers replace CASCADE delete behavior
- Archive job for old soft-deleted records (>1 year)

**Dependencies**: REQ-DB-001
**Related Design Decisions**: DD-DB-001

**Notes**: Stakeholder decision changed from PRD's CASCADE delete approach.

---

## Wallet System Requirements

### REQ-DB-004: User Coin Wallet
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Each user must have a coin wallet tracking balance, total purchased, total spent, and total gifted coins.

**Rationale**: Coins are the spending currency; wallet tracks all coin movements for reconciliation.

**Acceptance Criteria**:
- coin_wallets table created per schema
- One wallet per user (UNIQUE constraint on user_id)
- Balance CHECK constraint (>= 0) prevents negative balance
- Wallet created automatically on user registration
- Balance updated atomically with transactions
- Audit columns: total_purchased, total_spent, total_gifted

**Dependencies**: REQ-DB-001, REQ-DB-006
**Related Design Decisions**: DD-DB-002

**Notes**: Use database triggers to maintain balance consistency.

---

### REQ-DB-005: Creator Diamond Wallet
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Creators must have a diamond wallet tracking available balance, pending balance, total earned, and total withdrawn.

**Rationale**: Diamonds represent real-money earnings; separate wallet ensures clear financial tracking for payouts.

**Acceptance Criteria**:
- diamond_wallets table created per schema
- One wallet per creator (UNIQUE constraint on user_id)
- Balance CHECK constraints (>= 0) on available and pending
- Wallet created when user becomes creator (lazy creation)
- Pending balance used for settlement period
- Balance moves: pending -> available after settlement

**Dependencies**: REQ-DB-001, REQ-DB-006
**Related Design Decisions**: DD-DB-002

**Notes**: Diamond wallet only needed for creators (is_creator = true).

---

### REQ-DB-006: Unified Transaction Ledger
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: All monetary transactions must be recorded in a single transactions table with type differentiation.

**Rationale**: Unified ledger simplifies financial reporting, reconciliation, and audit requirements.

**Acceptance Criteria**:
- transactions table with type enum: recharge, spend_call, spend_gift, spend_room, earn_call, earn_gift, earn_room, withdraw, refund, bonus, subscription
- Supports both INR amounts, coins, and diamonds
- Reference linking (reference_type, reference_id) to source entities
- Payment gateway tracking (payment_gateway, payment_id)
- Status tracking: pending, completed, failed, refunded
- JSONB metadata for extensibility
- Indexes on user_id, type, created_at

**Dependencies**: REQ-DB-001
**Related Design Decisions**: DD-DB-003

**Notes**: Consider table partitioning by month for performance at scale.

---

## Communication Tables Requirements

### REQ-DB-007: Calls Table
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: All voice and video calls must be tracked with full lifecycle data including timing, billing, and status.

**Rationale**: Call data is essential for billing, creator earnings, and dispute resolution.

**Acceptance Criteria**:
- calls table with caller_id, receiver_id foreign keys
- call_type enum: audio, video
- Timing fields: initiated_at, started_at, ended_at, duration_seconds
- Billing fields: rate_per_minute, coins_spent, diamonds_earned
- Status enum: initiated, ringing, ongoing, completed, missed, rejected, failed
- Agora channel reference for SDK integration
- Indexes on caller_id, receiver_id, status

**Dependencies**: REQ-DB-001, REQ-TA-006
**Related Design Decisions**: DD-DB-005

**Notes**: duration_seconds calculated on call end.

---

### REQ-DB-008: Live Rooms Tables
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Live room data must be tracked including room metadata, participants, and room-level statistics.

**Rationale**: Rooms are a core feature; need complete tracking for moderation, analytics, and creator earnings.

**Acceptance Criteria**:
- rooms table with host_id, title, description, tags
- Settings: max_speakers, is_private, password
- Status enum: live, ended, suspended
- Stats: current_viewers, total_viewers, total_gifts_value
- Timing: started_at, ended_at
- Agora channel reference
- room_participants table with role enum: host, co_host, speaker, audience
- Participant stats: gifts_sent, gifts_received per session

**Dependencies**: REQ-DB-001, REQ-TA-006
**Related Design Decisions**: DD-DB-005, DD-FS-004

**Notes**: Room participant records created on join, updated on leave.

---

## Gift System Requirements

### REQ-DB-009: Gift Catalog Table
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Gift catalog must store all available virtual gifts with pricing, categorization, and localization.

**Rationale**: Gifts are primary revenue driver; catalog supports multiple categories and Tamil localization.

**Acceptance Criteria**:
- gifts table with name, name_tamil for localization
- Visual assets: icon_url, animation_url
- Pricing: coin_price, diamond_value (creator earning)
- Category enum: basic, premium, luxury, exclusive, event
- Sort order and is_active for catalog management
- Seed data from PRD (Rose, Heart, Coffee, Teddy Bear, etc.)

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: Diamond value = ~45% of coin price (after platform take).

---

### REQ-DB-010: Gift Transactions Table
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: All gift sends must be tracked with sender, receiver, gift details, and context information.

**Rationale**: Gift transactions drive creator earnings; need complete tracking for revenue calculations.

**Acceptance Criteria**:
- gift_transactions table linking sender, receiver, gift
- Quantity support for multiple gifts
- Financial fields: coins_spent, diamonds_earned
- Context tracking: context_type (call, room, profile, chat), context_id
- Indexes on sender_id, receiver_id for earnings queries

**Dependencies**: REQ-DB-001, REQ-DB-009
**Related Design Decisions**: None

**Notes**: Context helps attribute gifts to specific interactions.

---

## Creator & Compliance Requirements

### REQ-DB-011: Creator KYC Table
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Creator KYC information must be stored for identity verification and payout processing.

**Rationale**: KYC required for regulatory compliance and secure payouts.

**Acceptance Criteria**:
- creator_kyc table with PAN details: pan_number, pan_name, pan_verified
- Bank details: account_number, ifsc, bank_name, branch, bank_verified
- UPI ID support as alternative payout method
- Status enum: pending, submitted, verified, rejected
- Rejection reason tracking
- Verification timestamps
- One KYC record per creator (UNIQUE on user_id)

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: PAN required for TDS compliance on payouts.

---

### REQ-DB-012: Data Encryption at Rest
**Priority**: P0 (Critical)
**Type**: Non-Functional

**Description**: All database data must be encrypted at rest using Supabase's default encryption.

**Rationale**: PII protection (PAN, bank details) requires encryption; Supabase provides this transparently.

**Acceptance Criteria**:
- Supabase project encryption enabled
- Data encrypted at rest automatically
- Encryption key managed by Supabase
- No application-level encryption needed for MVP
- Verify encryption status in Supabase dashboard

**Dependencies**: REQ-TA-002
**Related Design Decisions**: None

**Notes**: Stakeholder decision - use Supabase default encryption for simplicity.

---

### REQ-DB-013: Withdrawals Table
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Creator withdrawal requests must be tracked with full payout lifecycle including TDS calculations.

**Rationale**: Withdrawals involve real money; complete tracking needed for reconciliation and compliance.

**Acceptance Criteria**:
- withdrawals table with user_id, amounts (gross, TDS, net)
- Payout method enum: bank_transfer, upi
- Reference to creator_kyc for bank details
- Status enum: pending, processing, completed, failed, cancelled
- Failure reason tracking
- Payout gateway reference (Razorpay)
- Timing: requested_at, processed_at, completed_at
- Indexes on user_id, status

**Dependencies**: REQ-DB-001, REQ-DB-011
**Related Design Decisions**: None

**Notes**: TDS calculated at withdrawal time per compliance requirements.

---

## Subscription Requirements

### REQ-DB-014: Subscription Plans Table
**Priority**: P1 (High)
**Type**: Technical

**Description**: Subscription plan catalog must store available VIP/Premium plans with features and pricing.

**Rationale**: Subscription revenue stream requires plan catalog management.

**Acceptance Criteria**:
- subscription_plans table with name, price_inr, duration_days
- Features stored as JSONB for flexibility
- Coin discount percent for subscriber benefits
- is_active flag for plan management
- Seed data: VIP (199/30d), Premium (499/30d), Creator+ (999/30d)

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: MVP includes Free + VIP only per stakeholder decision; Premium/Creator+ deferred.

---

### REQ-DB-015: User Subscriptions Table
**Priority**: P1 (High)
**Type**: Technical

**Description**: Active user subscriptions must be tracked with expiration and payment details.

**Rationale**: Need to track who has active subscriptions for feature access control.

**Acceptance Criteria**:
- user_subscriptions table linking user to plan
- Timing: started_at, expires_at
- Payment tracking: payment_id, amount_paid
- Status enum: active, expired, cancelled
- RLS policy for users to see only their subscriptions

**Dependencies**: REQ-DB-001, REQ-DB-014
**Related Design Decisions**: None

**Notes**: Check expires_at for subscription validity.

---

## Moderation Requirements

### REQ-DB-016: Reports Table
**Priority**: P1 (High)
**Type**: Technical

**Description**: User reports must be tracked for moderation workflow with context and resolution.

**Rationale**: Trust and safety requires report tracking and resolution workflow.

**Acceptance Criteria**:
- reports table with reporter_id, reported_user_id
- Reason and description fields
- Context tracking: context_type, context_id (link to call/room/message)
- Status enum: pending, reviewed, action_taken, dismissed
- Resolution: reviewed_by, reviewed_at, action_taken
- Soft delete compatible (track historical reports)

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: Reports feed into moderation dashboard.

---

### REQ-DB-017: User Blocks Table
**Priority**: P1 (High)
**Type**: Technical

**Description**: User block relationships must be stored for filtering blocked users from interactions.

**Rationale**: Users must be able to block others to prevent unwanted contact.

**Acceptance Criteria**:
- user_blocks table with blocker_id, blocked_id
- UNIQUE constraint prevents duplicate blocks
- Used in matching/call filtering queries
- Bidirectional blocking recommended (if A blocks B, B also can't contact A)
- Unblock removes record

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: Query blocked users efficiently in user listing/matching.

---

## Configuration Data Requirements

### REQ-DB-018: Coin Packages Table
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Coin purchase packages must be stored with pricing, bonus structure, and display configuration.

**Rationale**: Coin packages are primary revenue; need configurable catalog.

**Acceptance Criteria**:
- coin_packages table per schema
- Pricing: price_inr, coins, bonus_coins, bonus_percent
- Display: is_popular flag, sort_order
- is_active for package management
- Seed data from PRD (Starter 49, Popular 99, Value 299, etc.)
- Public read access (no RLS restriction)

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: Packages shown in purchase UI.

---

### REQ-DB-019: Call Rates Table
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: Dynamic call rates must be stored per user type and call type with peak pricing support.

**Rationale**: Different creator tiers have different rates; peak pricing increases revenue.

**Acceptance Criteria**:
- call_rates table with user_type (regular, popular, verified, celebrity)
- call_type (audio, video)
- base_rate in coins per minute
- Peak pricing: peak_multiplier, peak_start_hour, peak_end_hour
- is_active for rate management
- Seed data from PRD (regular audio: 5, regular video: 8, etc.)

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: Peak hours default 22:00-02:00 (late night).

---

## Indexing Requirements

### REQ-DB-020: Query Performance Indexes
**Priority**: P1 (High)
**Type**: Non-Functional

**Description**: Database must have appropriate indexes for common query patterns to ensure performance.

**Rationale**: Proper indexing ensures responsive app experience even at scale.

**Acceptance Criteria**:
- users: phone, is_creator, (location_city, location_state)
- transactions: user_id, type, created_at
- calls: caller_id, receiver_id, status
- gift_transactions: sender_id, receiver_id
- room_participants: room_id
- withdrawals: user_id, status
- All foreign keys indexed
- Composite indexes for common query patterns

**Dependencies**: REQ-DB-001
**Related Design Decisions**: DD-TA-002

**Notes**: Monitor slow queries and add indexes as needed.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-DB-001 | PostgreSQL via Supabase | P0 | Technical |
| REQ-DB-002 | Row Level Security (RLS) | P0 | Technical |
| REQ-DB-003 | Soft Delete Pattern | P0 | Technical |
| REQ-DB-004 | User Coin Wallet | P0 | Technical |
| REQ-DB-005 | Creator Diamond Wallet | P0 | Technical |
| REQ-DB-006 | Unified Transaction Ledger | P0 | Technical |
| REQ-DB-007 | Calls Table | P0 | Technical |
| REQ-DB-008 | Live Rooms Tables | P0 | Technical |
| REQ-DB-009 | Gift Catalog Table | P0 | Technical |
| REQ-DB-010 | Gift Transactions Table | P0 | Technical |
| REQ-DB-011 | Creator KYC Table | P0 | Technical |
| REQ-DB-012 | Data Encryption at Rest | P0 | Non-Functional |
| REQ-DB-013 | Withdrawals Table | P0 | Technical |
| REQ-DB-014 | Subscription Plans Table | P1 | Technical |
| REQ-DB-015 | User Subscriptions Table | P1 | Technical |
| REQ-DB-016 | Reports Table | P1 | Technical |
| REQ-DB-017 | User Blocks Table | P1 | Technical |
| REQ-DB-018 | Coin Packages Table | P0 | Technical |
| REQ-DB-019 | Call Rates Table | P0 | Technical |
| REQ-DB-020 | Query Performance Indexes | P1 | Non-Functional |

**Total Requirements**: 20
**P0 (Critical)**: 15
**P1 (High)**: 5

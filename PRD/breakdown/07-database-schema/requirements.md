# Section 7: Database Schema - Requirements

**Section:** Database Schema
**Total Requirements:** 15
**Priority Breakdown:** P0: 8 | P1: 5 | P2: 2
**Last Updated:** 2026-01-20

---

## REQ-DB-001: 8-Shard Logical Partitioning Architecture

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement 8-logical-shard architecture using PostgreSQL table inheritance to support growth from MVP (10K users) to 1M+ users with user_id as sharding key.

**Specifications:**

**Sharding Strategy:**
- **MVP**: 8 logical shards using PostgreSQL table inheritance
- **Shard Routing**: `shard_id = hash(user_id) % 8` (computed in application layer)
- **Parent Tables**: Abstract tables with no data (users, calls, transactions, etc.)
- **Child Tables**: 8 child tables per parent (users_shard_0 through users_shard_7)

**Migration Path:**
- **MVP (20-26 weeks)**: 8 logical shards on single PostgreSQL instance
- **200K users (Month 12)**: Migrate to 8 physical databases (one per shard)
- **1M users (Year 2)**: Split each physical database into 4 shards (32 total)

**Sharded Tables (13 tables):**
- users, coin_wallets, diamond_wallets
- calls, call_rates (if creator-specific)
- transactions, gift_transactions
- rooms, room_participants
- reports, user_blocks
- matching_preferences, user_verification

**Shared Tables (9 tables - Reference Data):**
- coin_packages, gifts, subscription_plans
- creator_kyc, withdrawals
- notifications (global), ai_moderation_transcripts (searchable across shards)
- i18n_metadata, creator_performance (aggregate stats)

**Shard Distribution:**
- Hash-based distribution ensures ~12.5% of users per shard
- Acceptable imbalance: ±2% (1.5M users = 175K-200K per shard)

**Cross-Shard Operations:**
- Avoid cross-shard joins (denormalize data if needed)
- Admin dashboards use scatter-gather (query all shards, merge in application)
- Shared reference data accessible from all shards

**Rationale:**
8 logical shards provide better distribution than standard 4 shards, reducing hotspots and enabling smoother migration to physical shards. Table inheritance enables logical sharding without application changes.

**Acceptance Criteria:**
- [ ] Parent-child table structure created for all 13 sharded tables
- [ ] Application routing logic routes queries by user_id to correct shard
- [ ] Load testing validates even distribution across 8 shards (±2%)
- [ ] Migration scripts documented for 8 physical databases (200K users)
- [ ] Cross-shard query patterns identified and documented
- [ ] Denormalization strategy defined for critical cross-shard data

**Dependencies:**
- REQ-TA-010 (Database Sharding Strategy from Section 6)
- REQ-DB-002 (Core Table Structures)

**Design Decisions:**
- **DD-DB-001**: 8 shards chosen over 4 for better distribution and scalability
- **DD-DB-002**: Table inheritance chosen over declarative partitioning for MVP flexibility

**Notes:**
- More complex than standard 4-shard approach but significantly better scalability
- Application must implement consistent hashing for user_id
- Physical shard migration at 200K users (Month 12) requires careful planning

**Testing Requirements:**
- Shard distribution testing (verify even user distribution)
- Cross-shard query performance testing
- Failover testing (single shard unavailable)
- Migration simulation (logical → physical shards)

---

## REQ-DB-002: Core Table Structures with TIMESTAMPTZ and DECIMAL

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Define all 22 core database tables with timezone-aware timestamps (TIMESTAMPTZ), precise decimal types for financial data, and JSONB for i18n content.

**Specifications:**

**Data Type Standards:**
- **Timestamps**: `TIMESTAMPTZ` (store in UTC, convert to IST in application)
- **Coins**: `DECIMAL(12,2)` (allows fractional coins for promotions)
- **Money (Rupees)**: `DECIMAL(12,2)` (paise precision)
- **Percentages**: `DECIMAL(5,2)` (e.g., 75.00% for revenue share)
- **i18n Content**: `JSONB` (multi-language names, descriptions)

**Database Timezone:**
- Set database timezone to UTC: `ALTER DATABASE livvly SET timezone TO 'UTC'`
- Application handles UTC ↔ IST conversion

**Primary Keys:**
- All tables use `UUID` primary keys: `id UUID PRIMARY KEY DEFAULT gen_random_uuid()`
- Enables distributed ID generation without collisions

**Soft Deletes:**
- Critical tables (users, transactions, calls) use soft deletes
- Add `deleted_at TIMESTAMPTZ` column, exclude in queries with `WHERE deleted_at IS NULL`

**Audit Timestamps:**
- All tables include `created_at TIMESTAMPTZ DEFAULT NOW()`
- Mutable tables include `updated_at TIMESTAMPTZ DEFAULT NOW()`

**Table Categories:**

**1. Users & Authentication (2 tables):**
- users, user_interests

**2. Wallet System (4 tables):**
- coin_wallets, diamond_wallets, coin_packages, transactions

**3. Calls System (3 tables):**
- calls, call_rates, creator_call_rates (NEW)

**4. Gifts System (2 tables):**
- gifts, gift_transactions

**5. Live Rooms System (2 tables):**
- rooms, room_participants

**6. Creator KYC & Payouts (2 tables):**
- creator_kyc, withdrawals

**7. Subscriptions (2 tables):**
- subscription_plans, user_subscriptions

**8. Moderation & Reports (2 tables):**
- reports, user_blocks

**9. New Tables - Missing Features (6 tables):**
- matching_preferences, ai_moderation_transcripts, referrals, notifications, user_verification, creator_performance

**10. Internationalization (1 table):**
- i18n_metadata

**Total: 22 tables**

**Rationale:**
TIMESTAMPTZ ensures timezone correctness for T+1 payouts and call billing. DECIMAL types prevent rounding errors in financial calculations. JSONB enables efficient multi-language content storage.

**Acceptance Criteria:**
- [ ] All 22 tables defined in SQL migration scripts
- [ ] All timestamp columns use TIMESTAMPTZ (not TIMESTAMP)
- [ ] All financial columns use DECIMAL(12,2) (not INTEGER)
- [ ] Database timezone set to UTC
- [ ] Soft delete columns added to critical tables
- [ ] Audit timestamps (created_at, updated_at) on all tables
- [ ] Foreign key constraints defined with appropriate CASCADE behavior
- [ ] CHECK constraints defined for enum-like columns

**Dependencies:**
- REQ-DB-001 (Sharding Architecture - defines which tables are sharded)
- REQ-DB-003 (Aggressive Index Strategy)

**Design Decisions:**
- **DD-DB-003**: TIMESTAMPTZ chosen over TIMESTAMP for global compatibility
- **DD-DB-004**: DECIMAL chosen over INTEGER for financial precision
- **DD-DB-005**: UUID chosen over BIGSERIAL for distributed ID generation

**Notes:**
- TIMESTAMPTZ adds 8 bytes per column but critical for correctness
- DECIMAL adds minimal overhead vs. INTEGER
- All PRD TIMESTAMP columns must be changed to TIMESTAMPTZ

**Testing Requirements:**
- Timezone conversion testing (UTC storage, IST display)
- Financial precision testing (revenue share calculations)
- Soft delete query testing (exclude deleted records)
- Foreign key cascade testing

---

## REQ-DB-003: Aggressive Index Strategy for <200ms SLA

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement comprehensive indexing strategy with 50+ indexes including composite, partial, covering, GIN, full-text, and expression indexes to meet <200ms p95 API latency.

**Specifications:**

**Index Types:**

**1. Basic B-tree Indexes (Foreign Keys & Lookups):**
```sql
CREATE INDEX idx_users_phone ON users(phone);
CREATE INDEX idx_calls_caller ON calls(caller_id);
CREATE INDEX idx_calls_receiver ON calls(receiver_id);
CREATE INDEX idx_transactions_user ON transactions(user_id);
```

**2. Composite Indexes (Multi-Column Queries):**
```sql
-- Calls by receiver, status, and time (call history endpoint)
CREATE INDEX idx_calls_composite ON calls(receiver_id, status, created_at DESC);

-- Transactions by user, type, and time (wallet history endpoint)
CREATE INDEX idx_transactions_composite ON transactions(user_id, type, created_at DESC);

-- Room participants by room and role (room management)
CREATE INDEX idx_room_participants_composite ON room_participants(room_id, role, joined_at DESC);
```

**3. Partial Indexes (Filtered Queries):**
```sql
-- Active creators only (creator search)
CREATE INDEX idx_users_active_creators ON users(is_creator) WHERE is_active = TRUE AND deleted_at IS NULL;

-- Pending withdrawals (admin dashboard)
CREATE INDEX idx_withdrawals_pending ON withdrawals(status, requested_at DESC) WHERE status = 'pending';

-- Live rooms only (room discovery)
CREATE INDEX idx_rooms_live ON rooms(status, started_at DESC) WHERE status = 'live';
```

**4. Covering Indexes (Include Clause - Avoid Table Lookups):**
```sql
-- User phone lookup with profile data
CREATE INDEX idx_users_phone_covering ON users(phone) INCLUDE (display_name, profile_photo, is_creator);

-- Creator earnings lookup with KYC status
CREATE INDEX idx_diamond_wallets_covering ON diamond_wallets(user_id) INCLUDE (available_balance, pending_balance);
```

**5. GIN Indexes (JSONB Columns):**
```sql
-- Gift names (multi-language search)
CREATE INDEX idx_gifts_names_gin ON gifts USING GIN (names);

-- Transaction metadata (flexible queries)
CREATE INDEX idx_transactions_metadata_gin ON transactions USING GIN (metadata);

-- Subscription plan features (feature-based search)
CREATE INDEX idx_subscription_features_gin ON subscription_plans USING GIN (features);
```

**6. Full-Text Search (pg_trgm Extension):**
```sql
-- Enable pg_trgm extension
CREATE EXTENSION IF NOT EXISTS pg_trgm;

-- Room title search (fuzzy matching)
CREATE INDEX idx_rooms_title_trgm ON rooms USING GIN (title gin_trgm_ops);

-- User bio search
CREATE INDEX idx_users_bio_trgm ON users USING GIN (bio gin_trgm_ops);
```

**7. Expression Indexes (Computed Values):**
```sql
-- Age calculation from DOB (matching queries)
CREATE INDEX idx_users_age ON users(DATE_PART('year', AGE(dob)));

-- Lowercase phone for case-insensitive lookup
CREATE INDEX idx_users_phone_lower ON users(LOWER(phone));
```

**Index Maintenance:**
- **REINDEX**: Scheduled weekly during low-traffic hours (3-5 AM IST)
- **VACUUM ANALYZE**: Daily at 2 AM IST
- **Monitoring**: pg_stat_user_indexes view tracks index usage
- **Unused Indexes**: Quarterly review, drop indexes with <1% usage

**Index Statistics:**
- **Total Indexes**: 50+ across 22 tables
- **Storage Overhead**: ~30-40% of table size (acceptable for premium performance)
- **Query Performance**: 70-90% of queries use index scans (not seq scans)

**Rationale:**
Aggressive indexing is critical to meet <200ms SLA with 15% concurrent users (7,500 concurrent at Month 6). Composite and covering indexes eliminate most table lookups.

**Acceptance Criteria:**
- [ ] All 50+ indexes created in migration scripts
- [ ] pg_trgm extension enabled for full-text search
- [ ] Index maintenance scheduled (REINDEX weekly, VACUUM daily)
- [ ] EXPLAIN ANALYZE shows index usage for all critical queries
- [ ] Query performance testing validates <200ms p95 latency
- [ ] Index usage monitoring dashboard created (Grafana)
- [ ] Documentation of which endpoints use which indexes

**Dependencies:**
- REQ-DB-002 (Core Table Structures)
- REQ-TA-002 (Performance Targets - <200ms SLA)
- REQ-TA-008 (Full Observability Stack - index monitoring)

**Design Decisions:**
- **DD-DB-006**: Aggressive indexing chosen to prioritize read performance over write performance
- **DD-DB-007**: Covering indexes chosen to eliminate table lookups for hot queries
- **DD-DB-008**: GIN indexes chosen for JSONB (better performance than B-tree)

**Notes:**
- Index storage overhead is 30-40% of table size but necessary for <200ms SLA
- Write performance may decrease 10-15% due to index maintenance (acceptable)
- Index monitoring critical to identify unused indexes

**Testing Requirements:**
- EXPLAIN ANALYZE for all API endpoints (verify index usage)
- Query performance testing under load (7,500 concurrent users)
- Index maintenance testing (REINDEX, VACUUM schedules)
- Unused index identification (pg_stat_user_indexes)

---

## REQ-DB-004: Missing Tables - Matching, Moderation, Referrals, Notifications, Verification, Performance

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Add 6 missing tables required by features approved in Sections 1-5: matching preferences, AI moderation transcripts, referral system, notifications, user verification, and creator performance tracking.

**Specifications:**

**1. Matching Preferences Table (REQ-FS-012):**
```sql
CREATE TABLE matching_preferences (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE,

    -- Age range
    min_age INTEGER CHECK (min_age >= 18 AND min_age <= 100),
    max_age INTEGER CHECK (max_age >= 18 AND max_age <= 100),

    -- Location
    preferred_cities VARCHAR(50)[],
    preferred_states VARCHAR(50)[],
    max_distance_km INTEGER, -- For location-based matching

    -- Interests
    preferred_interests VARCHAR(50)[],

    -- Creator preferences
    creator_only BOOLEAN DEFAULT FALSE,
    verified_only BOOLEAN DEFAULT FALSE,

    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_matching_preferences_user ON matching_preferences(user_id);
```

**2. AI Moderation Transcripts Table (REQ-TA-004, REQ-FS-018):**
```sql
CREATE TABLE ai_moderation_transcripts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    -- Source
    source_type VARCHAR(20) CHECK (source_type IN ('call', 'room', 'voice_message')),
    source_id UUID, -- call_id, room_id, or message_id
    user_id UUID REFERENCES users(id),

    -- Transcription
    language VARCHAR(10), -- ta, te, kn, hi, en
    transcript TEXT NOT NULL,
    confidence DECIMAL(5,2), -- 0.00-100.00

    -- Moderation
    flagged BOOLEAN DEFAULT FALSE,
    violation_type VARCHAR(50), -- profanity, harassment, spam, etc.
    violation_keywords VARCHAR(50)[],

    -- AI Provider
    ai_provider VARCHAR(20), -- google, azure, whisper

    -- Status
    review_status VARCHAR(20) DEFAULT 'pending' CHECK (review_status IN (
        'pending', 'reviewed', 'action_taken', 'false_positive'
    )),
    reviewed_by UUID REFERENCES users(id),
    reviewed_at TIMESTAMPTZ,

    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_ai_transcripts_source ON ai_moderation_transcripts(source_type, source_id);
CREATE INDEX idx_ai_transcripts_flagged ON ai_moderation_transcripts(flagged, review_status, created_at DESC);
CREATE INDEX idx_ai_transcripts_user ON ai_moderation_transcripts(user_id);
```

**3. Referrals Table (REQ-FS-024):**
```sql
CREATE TABLE referrals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    -- Referrer & Referee
    referrer_id UUID REFERENCES users(id),
    referee_id UUID REFERENCES users(id),
    referral_code VARCHAR(20) UNIQUE NOT NULL,

    -- Type
    referral_type VARCHAR(20) CHECK (referral_type IN (
        'user_signup', 'creator_signup'
    )),

    -- Status
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN (
        'pending', 'completed', 'rewarded', 'expired'
    )),

    -- Rewards
    referrer_reward_coins INTEGER DEFAULT 0,
    referee_reward_coins INTEGER DEFAULT 0,
    rewarded_at TIMESTAMPTZ,

    -- Expiration
    expires_at TIMESTAMPTZ,

    created_at TIMESTAMPTZ DEFAULT NOW(),

    UNIQUE(referrer_id, referee_id)
);

CREATE INDEX idx_referrals_referrer ON referrals(referrer_id);
CREATE INDEX idx_referrals_code ON referrals(referral_code);
CREATE INDEX idx_referrals_status ON referrals(status, created_at DESC);
```

**4. Notifications Table (Firebase FCM History):**
```sql
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),

    -- Content
    type VARCHAR(50) NOT NULL, -- call_request, new_message, gift_received, creator_online
    title VARCHAR(100) NOT NULL,
    body TEXT NOT NULL,

    -- Localization
    language VARCHAR(10), -- Notification language

    -- Data payload
    data JSONB, -- Additional data for app navigation

    -- FCM
    fcm_token VARCHAR(200),
    fcm_message_id VARCHAR(100),

    -- Status
    status VARCHAR(20) DEFAULT 'sent' CHECK (status IN (
        'sent', 'delivered', 'clicked', 'dismissed', 'failed'
    )),

    -- Timing
    sent_at TIMESTAMPTZ DEFAULT NOW(),
    delivered_at TIMESTAMPTZ,
    clicked_at TIMESTAMPTZ,

    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_notifications_user ON notifications(user_id, created_at DESC);
CREATE INDEX idx_notifications_type ON notifications(type, created_at DESC);
```

**5. User Verification Table (REQ-FS-020):**
```sql
CREATE TABLE user_verification (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE,

    -- Phone Verification (Tier 1)
    phone_verified BOOLEAN DEFAULT FALSE,
    phone_verified_at TIMESTAMPTZ,

    -- Selfie Verification (Tier 2)
    selfie_url TEXT,
    selfie_verified BOOLEAN DEFAULT FALSE,
    selfie_verified_at TIMESTAMPTZ,

    -- ID Verification (Tier 3)
    id_type VARCHAR(20), -- aadhar, pan, driving_license, passport
    id_number_hash VARCHAR(64), -- SHA-256 hash (never store plain ID)
    id_document_url TEXT,
    id_verified BOOLEAN DEFAULT FALSE,
    id_verified_at TIMESTAMPTZ,

    -- Video Verification (Tier 4 - Optional)
    video_url TEXT,
    video_verified BOOLEAN DEFAULT FALSE,
    video_verified_at TIMESTAMPTZ,

    -- Overall Status
    verification_tier INTEGER DEFAULT 1 CHECK (verification_tier BETWEEN 1 AND 4),
    verification_status VARCHAR(20) DEFAULT 'pending' CHECK (verification_status IN (
        'pending', 'verified', 'rejected', 'expired'
    )),

    -- Rejection
    rejection_reason TEXT,
    rejected_at TIMESTAMPTZ,

    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_user_verification_user ON user_verification(user_id);
CREATE INDEX idx_user_verification_status ON user_verification(verification_status, verification_tier);
```

**6. Creator Performance Table (REQ-ES-004 - Trailing 30-Day Stats):**
```sql
CREATE TABLE creator_performance (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE,

    -- Trailing 30 days statistics
    period_start TIMESTAMPTZ NOT NULL,
    period_end TIMESTAMPTZ NOT NULL,

    -- Earnings
    total_earnings_30d DECIMAL(12,2) DEFAULT 0,
    total_calls_30d INTEGER DEFAULT 0,
    total_gifts_30d INTEGER DEFAULT 0,
    total_call_minutes_30d INTEGER DEFAULT 0,

    -- Engagement
    avg_rating_30d DECIMAL(3,2), -- 0.00-5.00
    total_ratings_30d INTEGER DEFAULT 0,
    completion_rate_30d DECIMAL(5,2), -- Percentage of calls completed (not dropped)

    -- Revenue Tier
    revenue_tier VARCHAR(20) CHECK (revenue_tier IN (
        'tier_1_75', 'tier_2_80', 'tier_3_85'
    )),
    revenue_share_percent DECIMAL(5,2), -- 75.00, 80.00, or 85.00

    -- Calculated at
    calculated_at TIMESTAMPTZ DEFAULT NOW(),
    next_calculation_at TIMESTAMPTZ,

    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_creator_performance_user ON creator_performance(user_id);
CREATE INDEX idx_creator_performance_tier ON creator_performance(revenue_tier);
CREATE INDEX idx_creator_performance_next_calc ON creator_performance(next_calculation_at);
```

**Rationale:**
These 6 tables are critical for features approved in Sections 1-5. Missing tables would block implementation of matching algorithm, AI moderation compliance, referral rewards, notification history, tiered verification, and creator revenue tiers.

**Acceptance Criteria:**
- [ ] All 6 tables created in migration scripts
- [ ] Foreign key constraints defined
- [ ] Indexes created for common query patterns
- [ ] Seed data prepared for testing
- [ ] Documentation of table purpose and relationships
- [ ] API endpoints designed to use these tables (Section 8)

**Dependencies:**
- REQ-FS-012 (Matching Algorithm)
- REQ-FS-018 (AI Moderation)
- REQ-FS-024 (Referral System)
- REQ-FS-020 (User Verification)
- REQ-ES-004 (Creator Revenue Tiers)

**Design Decisions:**
- **DD-DB-009**: Separate tables chosen over JSONB for structured, queryable data
- **DD-DB-010**: ID numbers hashed (SHA-256) for privacy compliance
- **DD-DB-011**: Creator performance cached in table (avoid expensive 30-day aggregations)

**Notes:**
- ai_moderation_transcripts will grow large (1M+ rows at scale) - needs partitioning by created_at
- creator_performance calculated daily by N8N workflow (REQ-AW-*)
- user_verification tier determines badge display in UI

**Testing Requirements:**
- Matching algorithm testing with preferences
- AI moderation workflow testing (flagged content review)
- Referral reward calculation testing
- Notification delivery tracking
- Verification tier upgrade testing
- Creator performance calculation (30-day rolling window)

---

## REQ-DB-005: JSONB i18n Columns for Multi-Language Content

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Add JSONB columns to all user-facing content tables to support 5 languages (Tamil, Telugu, Kannada, Hindi, English) with fast lookups and GIN indexing.

**Specifications:**

**Tables Requiring i18n:**

**1. Gifts Table:**
```sql
ALTER TABLE gifts ADD COLUMN names JSONB;
ALTER TABLE gifts ADD COLUMN descriptions JSONB;

-- Example data:
-- names: {"en": "Rose", "ta": "ரோஜா", "te": "గులాబీ", "kn": "ಗುಲಾಬಿ", "hi": "गुलाब"}

-- GIN index for fast lookups
CREATE INDEX idx_gifts_names_gin ON gifts USING GIN (names);
```

**2. Coin Packages Table:**
```sql
ALTER TABLE coin_packages ADD COLUMN names JSONB;
ALTER TABLE coin_packages ADD COLUMN descriptions JSONB;

-- Example data:
-- names: {"en": "Popular Pack", "ta": "பிரபல பேக்", "te": "ప్రసిద్ధ ప్యాక్", ...}
-- descriptions: {"en": "Best value for money", "ta": "பணத்திற்கு சிறந்த மதிப்பு", ...}
```

**3. Subscription Plans Table:**
```sql
ALTER TABLE subscription_plans ADD COLUMN names JSONB;
ALTER TABLE subscription_plans ADD COLUMN feature_descriptions JSONB;

-- Example data:
-- names: {"en": "VIP Plan", "ta": "VIP திட்டம்", ...}
-- feature_descriptions: {"en": {"unlimited_calls": "Unlimited audio calls"}, "ta": {...}}
```

**4. Error Messages / System Messages (New Table):**
```sql
CREATE TABLE system_messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    message_key VARCHAR(100) UNIQUE NOT NULL, -- error_insufficient_coins
    messages JSONB NOT NULL,
    category VARCHAR(50), -- error, warning, info, success
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Example data:
-- messages: {"en": "Insufficient coins", "ta": "போதிய நாணயங்கள் இல்லை", ...}

CREATE INDEX idx_system_messages_key ON system_messages(message_key);
```

**5. i18n Metadata Table (Language Settings):**
```sql
CREATE TABLE i18n_metadata (
    language VARCHAR(10) PRIMARY KEY, -- en, ta, te, kn, hi
    display_name VARCHAR(50) NOT NULL, -- "English", "தமிழ்", "తెలుగు", ...
    native_name VARCHAR(50) NOT NULL, -- "English", "Tamil", "Telugu", ...
    locale VARCHAR(10) NOT NULL, -- en-IN, ta-IN, te-IN, kn-IN, hi-IN
    date_format VARCHAR(20) DEFAULT 'DD/MM/YYYY',
    time_format VARCHAR(20) DEFAULT 'HH:mm',
    currency_symbol VARCHAR(5) DEFAULT '₹',
    decimal_separator VARCHAR(1) DEFAULT '.',
    thousand_separator VARCHAR(1) DEFAULT ',',
    is_rtl BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Seed data
INSERT INTO i18n_metadata (language, display_name, native_name, locale, sort_order) VALUES
('en', 'English', 'English', 'en-IN', 1),
('ta', 'தமிழ்', 'Tamil', 'ta-IN', 2),
('te', 'తెలుగు', 'Telugu', 'te-IN', 3),
('kn', 'ಕನ್ನಡ', 'Kannada', 'kn-IN', 4),
('hi', 'हिन्दी', 'Hindi', 'hi-IN', 5);
```

**JSONB Query Pattern:**
```sql
-- Get gift name in Tamil
SELECT names->>'ta' AS name_tamil FROM gifts WHERE id = '...';

-- Get all gifts with Tamil names
SELECT id, names->>'ta' AS name FROM gifts WHERE names ? 'ta';

-- Search gifts by name in any language (GIN index)
SELECT * FROM gifts WHERE names @> '{"en": "Rose"}';
```

**Application Layer:**
- API endpoint accepts `Accept-Language` header (e.g., `Accept-Language: ta`)
- Backend returns localized content based on header
- Default to English if language not found

**Rationale:**
JSONB provides fast single-row lookups (no joins) with GIN indexing for search. Adding new languages requires only data insertion (no schema changes). Meets <200ms SLA requirement.

**Acceptance Criteria:**
- [ ] JSONB columns added to gifts, coin_packages, subscription_plans
- [ ] system_messages table created with i18n content
- [ ] i18n_metadata table created with 5 language settings
- [ ] GIN indexes created on all JSONB columns
- [ ] Seed data includes translations for all 5 languages
- [ ] API endpoints return localized content based on Accept-Language header
- [ ] Default to English if language key missing in JSONB

**Dependencies:**
- REQ-FS-001 (5 Languages at MVP)
- REQ-DB-003 (GIN Indexes)

**Design Decisions:**
- **DD-DB-012**: JSONB chosen over separate translation tables for performance
- **DD-DB-013**: GIN indexes chosen for JSONB search capability
- **DD-DB-014**: Accept-Language header chosen for API localization

**Notes:**
- JSONB adds ~100-200 bytes per row (minimal overhead)
- GIN indexes add ~20-30% storage overhead but necessary for search
- Translation content managed via admin API (not hardcoded)

**Testing Requirements:**
- JSONB query performance testing
- GIN index search testing
- Localization testing for all 5 languages
- Missing translation fallback testing (defaults to English)

---

## REQ-DB-006: Corrected Seed Data - Coin Packages, Call Rates, Gift Catalog

**Priority:** P0
**Type:** Data Requirement
**Status:** Approved

**Description:**
Update seed data to match approved requirements from Sections 4 and 5: corrected coin packages (REQ-RM-004), creator-set call rates (REQ-FS-014), and expanded gift catalog (15 gifts).

**Specifications:**

**1. Coin Packages (REQ-RM-004):**
```sql
-- Remove PRD packages, use approved packages
INSERT INTO coin_packages (name, price_inr, coins, bonus_coins, bonus_percent, is_popular, sort_order, names, descriptions) VALUES
(
    'Starter', 99, 100, 10, 10, TRUE, 1,
    '{"en": "Starter Pack", "ta": "தொடக்க பேக்", "te": "స్టార్టర్ ప్యాక్", "kn": "ಸ್ಟಾರ್ಟರ್ ಪ್ಯಾಕ್", "hi": "स्टार्टर पैक"}'::jsonb,
    '{"en": "Perfect to get started", "ta": "தொடங்க சிறந்தது", "te": "ప్రారంభించడానికి మంచిది", "kn": "ಪ್ರಾರಂಭಿಸಲು ಉತ್ತಮ", "hi": "शुरू करने के लिए बढ़िया"}'::jsonb
),
(
    'Popular', 299, 300, 50, 17, TRUE, 2,
    '{"en": "Popular Pack", "ta": "பிரபல பேக்", "te": "ప్రసిద్ధ ప్యాక్", "kn": "ಜನಪ್ರಿಯ ಪ್ಯಾಕ್", "hi": "लोकप्रिय पैक"}'::jsonb,
    '{"en": "Most popular choice", "ta": "மிகவும் பிரபலமான தேர்வு", "te": "అత్యంత ప్రజాదరణ పొందిన ఎంపిక", "kn": "ಅತ್ಯಂತ ಜನಪ್ರಿಯ ಆಯ್ಕೆ", "hi": "सबसे लोकप्रिय विकल्प"}'::jsonb
),
(
    'Value', 499, 500, 100, 20, FALSE, 3,
    '{"en": "Value Pack", "ta": "மதிப்பு பேக்", "te": "వాల్యూ ప్యాక్", "kn": "ಮೌಲ್ಯ ಪ್ಯಾಕ್", "hi": "वैल्यू पैक"}'::jsonb,
    '{"en": "Great value for money", "ta": "பணத்திற்கு சிறந்த மதிப்பு", "te": "డబ్బుకు గొప్ప విలువ", "kn": "ಹಣಕ್ಕೆ ಉತ್ತಮ ಮೌಲ್ಯ", "hi": "पैसे की अच्छी कीमत"}'::jsonb
),
(
    'Premium', 999, 1000, 300, 30, TRUE, 4,
    '{"en": "Premium Pack", "ta": "பிரீமியம் பேக்", "te": "ప్రీమియం ప్యాక్", "kn": "ಪ್ರೀಮಿಯಂ ಪ್ಯಾಕ್", "hi": "प्रीमियम पैक"}'::jsonb,
    '{"en": "Best value with maximum bonus", "ta": "அதிகபட்ச போனஸுடன் சிறந்த மதிப்பு", "te": "గరిష్ట బోనస్‌తో ఉత్తమ విలువ", "kn": "ಗರಿಷ್ಠ ಬೋನಸ್‌ನೊಂದಿಗೆ ಉತ್ತಮ ಮೌಲ್ಯ", "hi": "अधिकतम बोनस के साथ सर्वोत्तम मूल्य"}'::jsonb
);
```

**2. Creator Call Rates (NEW Table - REQ-FS-014):**
```sql
-- Baseline call rates (suggested)
-- Keep call_rates table from PRD as baseline

-- Creator custom rates (NEW)
CREATE TABLE creator_call_rates (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    creator_id UUID REFERENCES users(id) ON DELETE CASCADE,

    -- Rates (₹8-25/min range)
    audio_rate_per_minute DECIMAL(10,2) NOT NULL CHECK (audio_rate_per_minute BETWEEN 8.00 AND 25.00),
    video_rate_per_minute DECIMAL(10,2) NOT NULL CHECK (video_rate_per_minute BETWEEN 8.00 AND 25.00),

    -- Peak hours (optional multiplier)
    peak_multiplier DECIMAL(3,2) DEFAULT 1.0,
    peak_start_hour INTEGER DEFAULT 22,
    peak_end_hour INTEGER DEFAULT 2,

    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),

    UNIQUE(creator_id)
);

CREATE INDEX idx_creator_call_rates_creator ON creator_call_rates(creator_id);

-- Baseline rates (fallback if creator hasn't set rates)
INSERT INTO call_rates (user_type, call_type, base_rate, peak_multiplier) VALUES
('regular', 'audio', 8.00, 1.5),
('regular', 'video', 12.00, 1.5),
('verified', 'audio', 12.00, 1.5),
('verified', 'video', 18.00, 1.5);
```

**3. Gift Catalog (15 Gifts - Compromise):**
```sql
-- Budget Tier (5 gifts: ₹10-50)
INSERT INTO gifts (name, icon_url, coin_price, diamond_value, category, sort_order, names) VALUES
('Rose', '/gifts/rose.png', 10, 7.50, 'basic', 1,
 '{"en": "Rose", "ta": "ரோஜா", "te": "గులాబీ", "kn": "ಗುಲಾಬಿ", "hi": "गुलाब"}'::jsonb),
('Heart', '/gifts/heart.png', 20, 15.00, 'basic', 2,
 '{"en": "Heart", "ta": "இதயம்", "te": "హృదయం", "kn": "ಹೃದಯ", "hi": "दिल"}'::jsonb),
('Coffee', '/gifts/coffee.png', 30, 22.50, 'basic', 3,
 '{"en": "Coffee", "ta": "காபி", "te": "కాఫీ", "kn": "ಕಾಫಿ", "hi": "कॉफ़ी"}'::jsonb),
('Smile', '/gifts/smile.png', 40, 30.00, 'basic', 4,
 '{"en": "Smile", "ta": "சிரிப்பு", "te": "చిరునవ్వు", "kn": "ನಗು", "hi": "मुस्कान"}'::jsonb),
('Wave', '/gifts/wave.png', 50, 37.50, 'basic', 5,
 '{"en": "Wave", "ta": "அலை", "te": "వేవ్", "kn": "ತರಂಗ", "hi": "लहर"}'::jsonb),

-- Mid Tier (6 gifts: ₹100-500)
('Cake', '/gifts/cake.png', 100, 75.00, 'premium', 6,
 '{"en": "Cake", "ta": "கேக்", "te": "కేక్", "kn": "ಕೇಕ್", "hi": "केक"}'::jsonb),
('Champagne', '/gifts/champagne.png', 150, 112.50, 'premium', 7,
 '{"en": "Champagne", "ta": "ஷாம்பெயின்", "te": "షాంపైన్", "kn": "ಶಾಂಪೇನ್", "hi": "शैंपेन"}'::jsonb),
('Teddy', '/gifts/teddy.png', 200, 150.00, 'premium', 8,
 '{"en": "Teddy Bear", "ta": "டெடி பேர்", "te": "టెడ్డీ బేర్", "kn": "ಟೆಡ್ಡಿ ಬೇರ್", "hi": "टेडी बियर"}'::jsonb),
('Chocolate', '/gifts/chocolate.png', 250, 187.50, 'premium', 9,
 '{"en": "Chocolate", "ta": "சாக்லேட்", "te": "చాక్లెట్", "kn": "ಚಾಕೊಲೇಟ್", "hi": "चॉकलेट"}'::jsonb),
('Bouquet', '/gifts/bouquet.png', 350, 262.50, 'premium', 10,
 '{"en": "Bouquet", "ta": "பூங்கொத்து", "te": "బొకే", "kn": "ಪುಷ್ಪಗುಚ್ಛ", "hi": "गुलदस्ता"}'::jsonb),
('Kiss', '/gifts/kiss.png', 500, 375.00, 'premium', 11,
 '{"en": "Kiss", "ta": "முத்தம்", "te": "ముద్దు", "kn": "ಮುತ್ತು", "hi": "चुंबन"}'::jsonb),

-- Premium Tier (4 gifts: ₹1000-10000)
('Diamond Ring', '/gifts/ring.png', 1000, 750.00, 'luxury', 12,
 '{"en": "Diamond Ring", "ta": "வைர மோதிரம்", "te": "వజ్రపు ఉంగరం", "kn": "ವಜ್ರದ ಉಂಗುರ", "hi": "हीरे की अंगूठी"}'::jsonb),
('Crown', '/gifts/crown.png', 2500, 1875.00, 'luxury', 13,
 '{"en": "Crown", "ta": "கிரீடம்", "te": "కిరీటం", "kn": "ಕಿರೀಟ", "hi": "मुकुट"}'::jsonb),
('Luxury Car', '/gifts/car.png', 5000, 3750.00, 'exclusive', 14,
 '{"en": "Luxury Car", "ta": "ஆடம்பர கார்", "te": "లగ్జరీ కారు", "kn": "ಐಷಾರಾಮಿ ಕಾರು", "hi": "लग्जरी कार"}'::jsonb),
('Gold Jewelry', '/gifts/jewelry.png', 10000, 7500.00, 'exclusive', 15,
 '{"en": "Gold Jewelry", "ta": "தங்க நகை", "te": "బంగారు ఆభరణాలు", "kn": "ಚಿನ್ನದ ಆಭರಣ", "hi": "सोने के आभूषण"}'::jsonb);
```

**Diamond Conversion:**
- 1 coin = ₹1 (user purchase price)
- Diamond value = Coin price × 0.75 (75% revenue share)
- Example: Rose (10 coins) → Creator earns 7.50 diamonds = ₹7.50

**Rationale:**
Corrected seed data maintains consistency with approved requirements. Creator_call_rates table enables creator-set pricing while baseline rates provide defaults.

**Acceptance Criteria:**
- [ ] Coin packages match REQ-RM-004 (₹99=110, ₹299=350, ₹499=600, ₹999=1,300)
- [ ] Creator_call_rates table created
- [ ] Gift catalog expanded to 15 gifts with 3 tiers
- [ ] All seed data includes JSONB i18n content (5 languages)
- [ ] Diamond conversion formula validated (75% revenue share)

**Dependencies:**
- REQ-RM-004 (Coin Packages)
- REQ-FS-014 (Creator-Set Call Rates)
- REQ-FS-016 (Gift Catalog)
- REQ-DB-005 (JSONB i18n)

**Design Decisions:**
- **DD-DB-015**: 4 coin packages chosen (simplified from PRD's 6 packages)
- **DD-DB-016**: 15 gifts chosen as compromise between 9 (PRD) and 20+ (requirement)
- **DD-DB-017**: Creator_call_rates table separates custom rates from baseline rates

**Notes:**
- Remaining 5+ gifts can be added post-MVP based on usage analytics
- Baseline call rates used if creator hasn't set custom rates
- All prices in coins (application handles ₹ conversion for display)

**Testing Requirements:**
- Coin package purchase testing (verify bonus calculations)
- Creator rate-setting testing (₹8-25/min validation)
- Gift sending testing (verify diamond conversion)
- i18n content display testing (all 5 languages)

---

## REQ-DB-007: Alembic Migration Framework Integration

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Integrate Alembic database migration framework with FastAPI project to manage schema evolution safely across 20-26 week MVP timeline.

**Specifications:**

**Alembic Setup:**
```bash
# Install Alembic
pip install alembic

# Initialize Alembic in FastAPI project
alembic init alembic

# Configure alembic.ini with PostgreSQL connection
sqlalchemy.url = postgresql://user:pass@localhost/livvly
```

**Migration File Structure:**
```
project_root/
├── alembic/
│   ├── versions/
│   │   ├── 001_initial_schema.py
│   │   ├── 002_add_sharding_parents.py
│   │   ├── 003_create_child_shards.py
│   │   ├── 004_add_missing_tables.py
│   │   ├── 005_add_i18n_jsonb.py
│   │   ├── 006_create_indexes.py
│   │   ├── 007_update_seed_data.py
│   │   ├── 008_add_creator_call_rates.py
│   │   └── ...
│   ├── env.py (Alembic configuration)
│   └── script.py.mako (migration template)
├── alembic.ini (Alembic settings)
└── app/
    ├── models/ (SQLAlchemy models)
    └── database.py (DB connection)
```

**Migration Versioning:**
- Sequential numbering: `001`, `002`, `003`, ...
- Naming convention: `{revision}_description.py`
- Example: `001_initial_schema.py`, `002_add_sharding_parents.py`

**Migration Content (Both Directions):**
```python
"""Add matching_preferences table

Revision ID: 004
Revises: 003
Create Date: 2026-01-20

"""
from alembic import op
import sqlalchemy as sa

# revision identifiers
revision = '004'
down_revision = '003'
branch_labels = None
depends_on = None

def upgrade():
    """Upgrade database schema"""
    op.create_table(
        'matching_preferences',
        sa.Column('id', sa.UUID(), primary_key=True),
        sa.Column('user_id', sa.UUID(), nullable=False),
        # ... all columns
    )
    op.create_index('idx_matching_preferences_user', 'matching_preferences', ['user_id'])

def downgrade():
    """Rollback database schema"""
    op.drop_index('idx_matching_preferences_user')
    op.drop_table('matching_preferences')
```

**Migration Workflow:**

**Development:**
```bash
# Create new migration
alembic revision -m "Add AI moderation table"

# Apply migration (upgrade to latest)
alembic upgrade head

# Rollback one version
alembic downgrade -1

# Show current version
alembic current
```

**CI/CD Integration:**
```yaml
# GitHub Actions workflow
- name: Run database migrations
  run: |
    alembic upgrade head
  env:
    DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

**Environment-Specific Migration:**
- **Dev/Staging**: Automated migrations on deploy (CI/CD runs `alembic upgrade head`)
- **Production**: Manual DBA review + approval → Automated apply via CI/CD
- **Rollback**: Manual decision (run `alembic downgrade`)

**Migration Best Practices:**
- Test migrations on staging before production
- Backup database before production migration
- Create rollback script for every migration
- Document breaking changes in migration comments
- Keep migrations small and focused (one change per migration)

**Rationale:**
Alembic provides safe, version-controlled schema evolution with automatic rollback. Integration with FastAPI/SQLAlchemy simplifies model-to-schema synchronization.

**Acceptance Criteria:**
- [ ] Alembic installed and initialized in FastAPI project
- [ ] alembic.ini configured with database connection
- [ ] Migration file structure created (versions/ directory)
- [ ] Initial schema migration (001_initial_schema.py) created and tested
- [ ] CI/CD workflow includes `alembic upgrade head` step
- [ ] Migration rollback tested (downgrade function works)
- [ ] Documentation written for creating new migrations

**Dependencies:**
- REQ-TA-001 (FastAPI Backend)
- REQ-TA-016 (CI/CD Pipeline with GitHub Actions)
- REQ-DB-002 (Core Table Structures)

**Design Decisions:**
- **DD-DB-018**: Alembic chosen over Flyway for Python/FastAPI integration
- **DD-DB-019**: Sequential numbering chosen over timestamps for clarity
- **DD-DB-020**: Manual production approval chosen to prevent accidental destructive migrations

**Notes:**
- Alembic autogenerate can create migrations from SQLAlchemy models: `alembic revision --autogenerate -m "message"`
- Production migrations should be reviewed by DBA before apply
- Rollback strategy: downgrade function must reverse upgrade completely

**Testing Requirements:**
- Migration upgrade testing (apply all migrations)
- Migration rollback testing (downgrade all migrations)
- CI/CD migration automation testing
- Production migration dry-run testing

---

## REQ-DB-008: Foreign Key Constraints and Cascade Behavior

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Define comprehensive foreign key constraints with appropriate CASCADE behavior to maintain referential integrity across 22 tables.

**Specifications:**

**CASCADE Behavior Guidelines:**

**ON DELETE CASCADE (Delete Child Records):**
- User deletion → Delete user's wallets, preferences, verification, performance
- Room deletion → Delete room_participants
- Example: `user_id UUID REFERENCES users(id) ON DELETE CASCADE`

**ON DELETE SET NULL (Keep Child, Remove Reference):**
- Reviewer deletion (moderation) → Keep reports, set reviewed_by to NULL
- Example: `reviewed_by UUID REFERENCES users(id) ON DELETE SET NULL`

**ON DELETE RESTRICT (Prevent Deletion):**
- Gift catalog → Prevent deletion if gift_transactions exist
- Coin package → Prevent deletion if transactions exist
- Example: `gift_id UUID REFERENCES gifts(id) ON DELETE RESTRICT`

**Foreign Key Definitions:**

**Users Table (No FK - Root Table)**

**Coin Wallets Table:**
```sql
CREATE TABLE coin_wallets (
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE, -- Delete wallet if user deleted
    -- ...
);
```

**Diamond Wallets Table:**
```sql
CREATE TABLE diamond_wallets (
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE, -- Delete wallet if user deleted
    -- ...
);
```

**Transactions Table:**
```sql
CREATE TABLE transactions (
    user_id UUID REFERENCES users(id) ON DELETE SET NULL, -- Keep transaction history
    -- ...
);
```

**Calls Table:**
```sql
CREATE TABLE calls (
    caller_id UUID REFERENCES users(id) ON DELETE SET NULL, -- Keep call records
    receiver_id UUID REFERENCES users(id) ON DELETE SET NULL,
    -- ...
);
```

**Gift Transactions Table:**
```sql
CREATE TABLE gift_transactions (
    sender_id UUID REFERENCES users(id) ON DELETE SET NULL, -- Keep gift history
    receiver_id UUID REFERENCES users(id) ON DELETE SET NULL,
    gift_id UUID REFERENCES gifts(id) ON DELETE RESTRICT, -- Prevent gift deletion if used
    -- ...
);
```

**Rooms Table:**
```sql
CREATE TABLE rooms (
    host_id UUID REFERENCES users(id) ON DELETE SET NULL, -- Keep room records
    -- ...
);
```

**Room Participants Table:**
```sql
CREATE TABLE room_participants (
    room_id UUID REFERENCES rooms(id) ON DELETE CASCADE, -- Delete participants if room deleted
    user_id UUID REFERENCES users(id) ON DELETE CASCADE, -- Delete participation if user deleted
    -- ...
);
```

**Creator KYC Table:**
```sql
CREATE TABLE creator_kyc (
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE, -- Delete KYC if user deleted
    -- ...
);
```

**Withdrawals Table:**
```sql
CREATE TABLE withdrawals (
    user_id UUID REFERENCES users(id) ON DELETE SET NULL, -- Keep withdrawal history
    bank_account_id UUID REFERENCES creator_kyc(id) ON DELETE SET NULL, -- Keep record if KYC deleted
    -- ...
);
```

**Reports Table:**
```sql
CREATE TABLE reports (
    reporter_id UUID REFERENCES users(id) ON DELETE SET NULL, -- Keep report if reporter deleted
    reported_user_id UUID REFERENCES users(id) ON DELETE SET NULL,
    reviewed_by UUID REFERENCES users(id) ON DELETE SET NULL,
    -- ...
);
```

**User Blocks Table:**
```sql
CREATE TABLE user_blocks (
    blocker_id UUID REFERENCES users(id) ON DELETE CASCADE, -- Delete block if blocker deleted
    blocked_id UUID REFERENCES users(id) ON DELETE CASCADE, -- Delete block if blocked user deleted
    -- ...
);
```

**Referrals Table:**
```sql
CREATE TABLE referrals (
    referrer_id UUID REFERENCES users(id) ON DELETE SET NULL, -- Keep referral history
    referee_id UUID REFERENCES users(id) ON DELETE SET NULL,
    -- ...
);
```

**User Verification Table:**
```sql
CREATE TABLE user_verification (
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE, -- Delete verification if user deleted
    -- ...
);
```

**Creator Performance Table:**
```sql
CREATE TABLE creator_performance (
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE, -- Delete performance if user deleted
    -- ...
);
```

**AI Moderation Transcripts Table:**
```sql
CREATE TABLE ai_moderation_transcripts (
    user_id UUID REFERENCES users(id) ON DELETE SET NULL, -- Keep transcript for compliance
    reviewed_by UUID REFERENCES users(id) ON DELETE SET NULL,
    -- ...
);
```

**Notifications Table:**
```sql
CREATE TABLE notifications (
    user_id UUID REFERENCES users(id) ON DELETE CASCADE, -- Delete notifications if user deleted
    -- ...
);
```

**Matching Preferences Table:**
```sql
CREATE TABLE matching_preferences (
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE, -- Delete preferences if user deleted
    -- ...
);
```

**User Subscriptions Table:**
```sql
CREATE TABLE user_subscriptions (
    user_id UUID REFERENCES users(id) ON DELETE CASCADE, -- Delete subscription if user deleted
    plan_id UUID REFERENCES subscription_plans(id) ON DELETE RESTRICT, -- Prevent plan deletion if subscriptions exist
    -- ...
);
```

**Foreign Key Indexing:**
- All foreign key columns automatically indexed by PostgreSQL
- Additional composite indexes created for common query patterns (REQ-DB-003)

**Rationale:**
Proper CASCADE behavior ensures data integrity and prevents orphaned records. Financial/compliance records (transactions, transcripts) use SET NULL to preserve history even if users deleted.

**Acceptance Criteria:**
- [ ] All foreign key constraints defined in migration scripts
- [ ] CASCADE behavior documented in schema comments
- [ ] Foreign key constraint testing (insert, update, delete operations)
- [ ] Orphaned record prevention validated
- [ ] Financial record preservation validated (SET NULL behavior)

**Dependencies:**
- REQ-DB-002 (Core Table Structures)
- REQ-DB-007 (Alembic Migrations)

**Design Decisions:**
- **DD-DB-021**: ON DELETE CASCADE for user-specific data (wallets, preferences)
- **DD-DB-022**: ON DELETE SET NULL for historical/compliance data (transactions, transcripts)
- **DD-DB-023**: ON DELETE RESTRICT for reference data (gifts, coin packages)

**Notes:**
- SET NULL requires nullable foreign key columns
- CASCADE can trigger multiple levels of deletions (be careful with user deletion)
- Test CASCADE behavior thoroughly to avoid accidental data loss

**Testing Requirements:**
- User deletion cascade testing (verify wallets, preferences deleted)
- Gift deletion restriction testing (prevent if transactions exist)
- Historical data preservation testing (SET NULL behavior)

---

## REQ-DB-009: Soft Delete Implementation for Critical Tables

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement soft delete pattern for critical tables (users, transactions, calls) to prevent accidental data loss and enable data recovery.

**Specifications:**

**Soft Delete Pattern:**
- Add `deleted_at TIMESTAMPTZ` column to critical tables
- Deleted records have `deleted_at` set to deletion timestamp
- Active records have `deleted_at = NULL`
- All queries exclude deleted records: `WHERE deleted_at IS NULL`

**Tables Requiring Soft Deletes:**

**1. Users Table:**
```sql
ALTER TABLE users ADD COLUMN deleted_at TIMESTAMPTZ;

-- Soft delete user
UPDATE users SET deleted_at = NOW() WHERE id = '...';

-- Restore user
UPDATE users SET deleted_at = NULL WHERE id = '...';

-- Query active users only
SELECT * FROM users WHERE deleted_at IS NULL;

-- Index for performance
CREATE INDEX idx_users_deleted ON users(deleted_at) WHERE deleted_at IS NULL;
```

**2. Transactions Table (Financial Records):**
```sql
ALTER TABLE transactions ADD COLUMN deleted_at TIMESTAMPTZ;

-- Soft delete transaction (rare, only for errors)
UPDATE transactions SET deleted_at = NOW() WHERE id = '...';

-- Query active transactions
SELECT * FROM transactions WHERE deleted_at IS NULL;
```

**3. Calls Table (Historical Records):**
```sql
ALTER TABLE calls ADD COLUMN deleted_at TIMESTAMPTZ;

-- Soft delete call (user requests deletion)
UPDATE calls SET deleted_at = NOW() WHERE id = '...';

-- Query active calls
SELECT * FROM calls WHERE deleted_at IS NULL;
```

**4. Gift Transactions Table:**
```sql
ALTER TABLE gift_transactions ADD COLUMN deleted_at TIMESTAMPTZ;
```

**5. Rooms Table:**
```sql
ALTER TABLE rooms ADD COLUMN deleted_at TIMESTAMPTZ;
```

**6. Reports Table (Compliance):**
```sql
ALTER TABLE reports ADD COLUMN deleted_at TIMESTAMPTZ;
```

**Application Layer Handling:**
- Default query filter: `WHERE deleted_at IS NULL`
- SQLAlchemy query example:
  ```python
  query = session.query(User).filter(User.deleted_at == None)
  ```
- Admin endpoints can query deleted records if needed

**Permanent Deletion (Optional):**
- Scheduled job deletes records older than 90 days: `WHERE deleted_at < NOW() - INTERVAL '90 days'`
- GDPR compliance: permanent deletion on user request

**Rationale:**
Soft deletes prevent accidental data loss for financial and compliance-critical records. Enables data recovery and audit trails.

**Acceptance Criteria:**
- [ ] deleted_at column added to 6 critical tables
- [ ] Partial indexes created for deleted_at IS NULL
- [ ] Application queries exclude deleted records by default
- [ ] Soft delete API endpoints implemented (mark as deleted, restore)
- [ ] Admin endpoints can view/restore deleted records
- [ ] Scheduled job for permanent deletion (90+ days old)

**Dependencies:**
- REQ-DB-002 (Core Table Structures)
- REQ-DB-007 (Alembic Migrations)

**Design Decisions:**
- **DD-DB-024**: Soft delete chosen over hard delete for critical tables
- **DD-DB-025**: 90-day retention chosen for permanent deletion

**Notes:**
- Soft deletes add complexity to queries (must filter deleted_at)
- Partial indexes ensure performance (only indexes active records)
- Permanent deletion job must be carefully tested

**Testing Requirements:**
- Soft delete testing (verify records marked deleted, not removed)
- Query filtering testing (verify deleted records excluded)
- Restore testing (verify deleted_at set to NULL)
- Permanent deletion testing (verify 90-day cleanup)

---

## REQ-DB-010: Database Performance Monitoring and Maintenance

**Priority:** P1
**Type:** Operational Requirement
**Status:** Approved

**Description:**
Implement automated database performance monitoring, index maintenance, and vacuum schedules to sustain <200ms SLA.

**Specifications:**

**Performance Monitoring Views:**
```sql
-- Monitor index usage
SELECT
    schemaname,
    tablename,
    indexname,
    idx_scan AS index_scans,
    idx_tup_read AS tuples_read,
    idx_tup_fetch AS tuples_fetched
FROM pg_stat_user_indexes
WHERE idx_scan = 0 -- Unused indexes
ORDER BY schemaname, tablename;

-- Monitor slow queries
SELECT
    queryid,
    query,
    calls,
    total_exec_time,
    mean_exec_time,
    stddev_exec_time
FROM pg_stat_statements
WHERE mean_exec_time > 200 -- Queries slower than 200ms
ORDER BY mean_exec_time DESC;

-- Monitor table bloat
SELECT
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size,
    n_live_tup AS live_tuples,
    n_dead_tup AS dead_tuples
FROM pg_stat_user_tables
WHERE n_dead_tup > 1000
ORDER BY n_dead_tup DESC;
```

**Automated Maintenance Schedules:**

**1. VACUUM ANALYZE (Daily at 2 AM IST):**
```sql
-- Cron job (pg_cron extension)
SELECT cron.schedule('vacuum-analyze', '0 2 * * *', 'VACUUM ANALYZE;');

-- Or manual script
VACUUM ANALYZE users;
VACUUM ANALYZE transactions;
VACUUM ANALYZE calls;
-- ... all tables
```

**2. REINDEX (Weekly on Sunday 3 AM IST):**
```sql
-- Reindex all indexes (rebuilds to remove bloat)
SELECT cron.schedule('reindex-weekly', '0 3 * * 0', 'REINDEX DATABASE livvly;');
```

**3. Unused Index Cleanup (Quarterly):**
```sql
-- Script to identify and drop unused indexes (manual review required)
SELECT
    'DROP INDEX ' || schemaname || '.' || indexname || ';' AS drop_statement
FROM pg_stat_user_indexes
WHERE idx_scan < 100 -- Used less than 100 times
    AND idx_tup_read = 0
ORDER BY schemaname, tablename;
```

**4. Table Partitioning (Large Tables):**
```sql
-- Partition ai_moderation_transcripts by created_at (monthly partitions)
CREATE TABLE ai_moderation_transcripts_2026_01 PARTITION OF ai_moderation_transcripts
    FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');

CREATE TABLE ai_moderation_transcripts_2026_02 PARTITION OF ai_moderation_transcripts
    FOR VALUES FROM ('2026-02-01') TO ('2026-03-01');

-- Auto-create partitions via script (monthly)
```

**Grafana Dashboards (REQ-TA-008):**
- Database connections (active, idle, max)
- Query latency (p50, p95, p99)
- Index hit ratio (target >95%)
- Cache hit ratio (target >95%)
- Table bloat (dead tuples)
- Slow query log (>200ms queries)

**Alerts:**
- Index hit ratio <95% → Slack warning
- Slow query count >10/min → PagerDuty critical
- Table bloat >50% → Slack warning
- Connection pool exhaustion → PagerDuty critical

**Rationale:**
Automated maintenance prevents database degradation over time. Monitoring ensures early detection of performance issues before they impact users.

**Acceptance Criteria:**
- [ ] pg_stat_statements extension enabled
- [ ] pg_cron extension installed (or equivalent scheduled jobs)
- [ ] Daily VACUUM ANALYZE scheduled
- [ ] Weekly REINDEX scheduled
- [ ] Grafana dashboards created for database metrics
- [ ] Alerts configured for critical thresholds
- [ ] Quarterly unused index review process documented

**Dependencies:**
- REQ-DB-003 (Aggressive Index Strategy)
- REQ-TA-008 (Full Observability Stack)

**Design Decisions:**
- **DD-DB-026**: Daily VACUUM chosen to prevent table bloat
- **DD-DB-027**: Weekly REINDEX chosen to rebuild indexes (removes bloat)
- **DD-DB-028**: Quarterly unused index review (manual) prevents over-indexing

**Notes:**
- VACUUM ANALYZE updates statistics for query planner
- REINDEX locks tables briefly (schedule during low-traffic hours)
- pg_cron requires PostgreSQL superuser privileges

**Testing Requirements:**
- Maintenance schedule testing (verify jobs run successfully)
- Alert testing (trigger thresholds, verify notifications)
- Performance impact testing (VACUUM and REINDEX during load)

---

## REQ-DB-011: Connection Pooling with PgBouncer

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Deploy PgBouncer connection pooler to manage 1000+ concurrent database connections efficiently and prevent connection exhaustion.

**Specifications:**

**PgBouncer Configuration:**
```ini
[databases]
livvly = host=localhost port=5432 dbname=livvly

[pgbouncer]
listen_addr = 0.0.0.0
listen_port = 6432
auth_type = md5
auth_file = /etc/pgbouncer/userlist.txt

# Pool settings
pool_mode = transaction  # Transaction-level pooling (best for FastAPI)
max_client_conn = 10000  # Max client connections (from API servers)
default_pool_size = 100  # Connections per database
reserve_pool_size = 20   # Reserved connections
reserve_pool_timeout = 3 # Seconds
max_db_connections = 200 # Total database connections

# Timeouts
server_idle_timeout = 600     # Close idle server connections after 10 min
server_lifetime = 3600        # Recycle connections after 1 hour
server_connect_timeout = 15   # Connection timeout
query_timeout = 60            # Query timeout (abort slow queries)

# Logging
log_connections = 1
log_disconnections = 1
log_pooler_errors = 1
```

**Connection String (FastAPI):**
```python
# Connect to PgBouncer (not directly to PostgreSQL)
DATABASE_URL = "postgresql://user:pass@pgbouncer:6432/livvly"

# SQLAlchemy engine with connection pooling
engine = create_engine(
    DATABASE_URL,
    pool_size=20,        # API server connection pool (each server)
    max_overflow=10,     # Additional connections if needed
    pool_pre_ping=True,  # Verify connections before use
    pool_recycle=3600    # Recycle connections after 1 hour
)
```

**Architecture:**
```
[10 FastAPI Servers] → [PgBouncer] → [PostgreSQL]
  20 conns each          Max 200       Max 200
  (200 total)            pooled        connections
```

**PgBouncer Benefits:**
- **Connection Reuse**: Shares database connections across requests
- **Connection Limits**: Prevents connection exhaustion (max 200 to PostgreSQL)
- **Timeout Protection**: Aborts slow queries automatically
- **Connection Health**: Automatically closes idle/broken connections

**Monitoring:**
```sql
-- PgBouncer admin console
psql -p 6432 -U pgbouncer pgbouncer

-- Show pool statistics
SHOW POOLS;

-- Show active clients
SHOW CLIENTS;

-- Show server connections
SHOW SERVERS;
```

**Grafana Metrics (REQ-TA-008):**
- PgBouncer client connections (used/available)
- Server connections (used/available)
- Wait time (queue time for connections)
- Transaction duration (avg, p95)

**Rationale:**
PgBouncer enables 10 API servers (20 connections each = 200) to share 100-200 PostgreSQL connections efficiently. Critical for meeting <200ms SLA with 7,500 concurrent users.

**Acceptance Criteria:**
- [ ] PgBouncer installed and configured
- [ ] Transaction-level pooling enabled
- [ ] Max 200 database connections configured
- [ ] FastAPI connects to PgBouncer (not direct PostgreSQL)
- [ ] Connection pool monitoring in Grafana
- [ ] Alert if pool exhaustion occurs (wait time >1s)
- [ ] Documentation for scaling (add more API servers → increase pool size)

**Dependencies:**
- REQ-TA-003 (Premium Infrastructure - 10+ API servers)
- REQ-DB-002 (Database Configuration)
- REQ-TA-008 (Observability - connection monitoring)

**Design Decisions:**
- **DD-DB-029**: Transaction-level pooling chosen (best for FastAPI stateless requests)
- **DD-DB-030**: Max 200 DB connections chosen based on PostgreSQL performance limits

**Notes:**
- Transaction-level pooling suitable for stateless APIs (not for long transactions)
- Each API server has its own SQLAlchemy pool (20 conns) → PgBouncer pools to DB
- PgBouncer adds <1ms latency (negligible)

**Testing Requirements:**
- Connection pool exhaustion testing (simulate 10,000 concurrent clients)
- Query timeout testing (verify 60s timeout aborts slow queries)
- PgBouncer failover testing (restart PgBouncer, verify reconnections)

---

## REQ-DB-012: Database Backup Verification and Restoration Testing

**Priority:** P2
**Type:** Operational Requirement
**Status:** Approved

**Description:**
Implement automated backup verification and quarterly restoration drills to ensure RTO 4h/RPO 1h disaster recovery targets (REQ-TA-009).

**Specifications:**

**Backup Verification (Daily):**
```bash
#!/bin/bash
# Verify latest backup is restorable

LATEST_BACKUP=$(aws s3 ls s3://livvly-backups/postgres/ | sort | tail -n 1 | awk '{print $4}')

# Download backup
aws s3 cp s3://livvly-backups/postgres/$LATEST_BACKUP /tmp/backup.sql.gz

# Decompress
gunzip /tmp/backup.sql.gz

# Restore to test database
psql -h test-db -U postgres -d livvly_test < /tmp/backup.sql

# Verify row counts match production
PROD_USERS=$(psql -h prod-db -U postgres -d livvly -t -c "SELECT COUNT(*) FROM users WHERE deleted_at IS NULL;")
TEST_USERS=$(psql -h test-db -U postgres -d livvly_test -t -c "SELECT COUNT(*) FROM users WHERE deleted_at IS NULL;")

if [ "$PROD_USERS" -eq "$TEST_USERS" ]; then
    echo "Backup verification PASSED"
    # Send success notification
else
    echo "Backup verification FAILED"
    # Send alert to PagerDuty
fi

# Cleanup
psql -h test-db -U postgres -c "DROP DATABASE livvly_test;"
```

**Restoration Drill (Quarterly):**
```bash
#!/bin/bash
# Quarterly disaster recovery drill

# 1. Simulate failure (create new RDS instance)
aws rds create-db-instance \
    --db-instance-identifier livvly-dr-drill \
    --db-instance-class db.r6i.2xlarge \
    --engine postgres \
    --master-username postgres \
    --master-user-password $PASSWORD

# 2. Restore from latest backup
LATEST_BACKUP=$(aws s3 ls s3://livvly-backups/postgres/ | sort | tail -n 1 | awk '{print $4}')
aws s3 cp s3://livvly-backups/postgres/$LATEST_BACKUP /tmp/backup.sql.gz
gunzip /tmp/backup.sql.gz
psql -h livvly-dr-drill.xxx.rds.amazonaws.com -U postgres -d livvly < /tmp/backup.sql

# 3. Verify data integrity
# ... run verification queries ...

# 4. Measure RTO (should be <4 hours)
RESTORE_TIME=$(calculate_time_elapsed)
if [ "$RESTORE_TIME" -lt 14400 ]; then  # 4 hours = 14400 seconds
    echo "RTO target MET: $RESTORE_TIME seconds"
else
    echo "RTO target MISSED: $RESTORE_TIME seconds"
fi

# 5. Cleanup
aws rds delete-db-instance --db-instance-identifier livvly-dr-drill
```

**Point-in-Time Recovery Testing:**
```bash
# Restore to specific timestamp (within 30-day window)
aws rds restore-db-instance-to-point-in-time \
    --source-db-instance-identifier livvly-prod \
    --target-db-instance-identifier livvly-pitr-test \
    --restore-time 2026-01-20T10:30:00Z
```

**Backup Monitoring (Grafana):**
- Last successful backup timestamp
- Backup size trend
- Backup verification status (pass/fail)
- Cross-region replication lag (Mumbai → Singapore)

**Alerts:**
- Backup verification failure → PagerDuty critical
- Backup older than 2 hours → Slack warning
- Cross-region replication lag >15 minutes → Slack warning

**Rationale:**
Regular backup verification ensures recoverability. Quarterly drills validate RTO 4h target and train team on restoration procedures.

**Acceptance Criteria:**
- [ ] Daily backup verification script operational
- [ ] Quarterly restoration drill scheduled (calendar reminder)
- [ ] RTO measurement automated (target <4 hours)
- [ ] Backup verification monitoring in Grafana
- [ ] Alerts configured for backup failures
- [ ] Restoration runbook documented with step-by-step procedures

**Dependencies:**
- REQ-TA-009 (Disaster Recovery Strategy)
- REQ-TA-008 (Observability - backup monitoring)

**Design Decisions:**
- **DD-DB-031**: Daily backup verification ensures recoverability without waiting for disaster
- **DD-DB-032**: Quarterly drills ensure team readiness and validate RTO

**Notes:**
- Backup verification adds ~30 minutes to daily maintenance window
- Restoration drills should be during low-traffic hours (weekends)
- Document all drill results for audit compliance

**Testing Requirements:**
- Backup verification testing (verify script works)
- Restoration drill testing (simulate full disaster recovery)
- RTO measurement testing (verify <4 hours)

---

## REQ-DB-013: Schema Documentation and ERD Generation

**Priority:** P2
**Type:** Documentation Requirement
**Status:** Approved

**Description:**
Generate and maintain comprehensive database schema documentation and Entity-Relationship Diagrams (ERD) for 22 tables.

**Specifications:**

**ERD Generation Tool:**
- **SchemaSpy**: Generates HTML documentation with interactive ERD
- **DBeaver**: Visual ERD editor for manual diagramming
- **dbdiagram.io**: Online ERD tool with DBML syntax

**SchemaSpy Example:**
```bash
# Install SchemaSpy
wget https://github.com/schemaspy/schemaspy/releases/download/v6.1.0/schemaspy-6.1.0.jar

# Generate documentation
java -jar schemaspy-6.1.0.jar \
    -t pgsql \
    -host localhost \
    -port 5432 \
    -db livvly \
    -u postgres \
    -p password \
    -o ./schema-docs

# Open ./schema-docs/index.html in browser
```

**Schema Comments (PostgreSQL):**
```sql
-- Add table comments
COMMENT ON TABLE users IS 'Core user table with authentication and profile data. Sharded by user_id.';
COMMENT ON COLUMN users.is_creator IS 'Flag indicating if user has creator privileges (can receive calls/gifts)';
COMMENT ON COLUMN users.verification_tier IS 'Tiered verification level (1=phone, 2=selfie, 3=ID, 4=video)';

-- Add index comments
COMMENT ON INDEX idx_users_phone IS 'Unique index for phone-based authentication lookups';
COMMENT ON INDEX idx_users_active_creators IS 'Partial index for active creator search (excludes deleted/inactive)';
```

**Documentation Structure:**
```
schema-docs/
├── index.html (Overview)
├── tables/
│   ├── users.html
│   ├── calls.html
│   ├── transactions.html
│   └── ... (all 22 tables)
├── diagrams/
│   ├── relationships.png (Full ERD)
│   ├── users-wallet.png (User wallet subsystem)
│   ├── calls-gifts.png (Calls and gifts subsystem)
│   └── moderation.png (Moderation subsystem)
├── constraints/
│   ├── foreign_keys.html
│   ├── unique_constraints.html
│   └── check_constraints.html
└── indexes/
    └── all_indexes.html
```

**ERD Sections:**
1. **Users & Authentication**: users, user_interests, user_verification
2. **Wallet System**: coin_wallets, diamond_wallets, coin_packages, transactions
3. **Calls System**: calls, call_rates, creator_call_rates
4. **Gifts System**: gifts, gift_transactions
5. **Live Rooms**: rooms, room_participants
6. **Creator KYC & Payouts**: creator_kyc, withdrawals, creator_performance
7. **Subscriptions**: subscription_plans, user_subscriptions
8. **Moderation**: reports, user_blocks, ai_moderation_transcripts
9. **Features**: matching_preferences, referrals, notifications
10. **i18n**: i18n_metadata, system_messages

**Maintenance:**
- Regenerate documentation after each schema migration
- Include in CI/CD: `alembic upgrade head && schemaspy generate`
- Host documentation on internal wiki or S3 static site

**Rationale:**
Comprehensive schema documentation accelerates onboarding and reduces errors. ERD visualizations help developers understand relationships.

**Acceptance Criteria:**
- [ ] SchemaSpy (or equivalent) installed
- [ ] Initial schema documentation generated
- [ ] ERD diagrams created for all 22 tables
- [ ] Table and column comments added to PostgreSQL
- [ ] Documentation hosted and accessible to team
- [ ] CI/CD regenerates documentation on schema changes
- [ ] README with instructions for viewing documentation

**Dependencies:**
- REQ-DB-002 (Core Table Structures)
- REQ-DB-007 (Alembic Migrations)

**Design Decisions:**
- **DD-DB-033**: SchemaSpy chosen for automated documentation generation
- **DD-DB-034**: Schema comments stored in PostgreSQL (single source of truth)

**Notes:**
- Documentation generation adds ~5 minutes to migration process
- ERD should be included in onboarding materials for new developers
- Update documentation wiki link in project README

**Testing Requirements:**
- Documentation generation testing (verify HTML output)
- ERD accuracy validation (verify all relationships shown)
- Schema comment completeness check (all tables and critical columns commented)

---

## SUMMARY

**Total Requirements:** 15
**Priority Breakdown:**
- **P0 (Critical):** 8 requirements (Sharding, core tables, indexes, missing tables, i18n, seed data, performance monitoring, foreign keys)
- **P1 (High):** 5 requirements (Alembic, foreign keys, soft deletes, monitoring, PgBouncer)
- **P2 (Medium):** 2 requirements (Backup verification, documentation)

**Key Technical Decisions:**
1. **Sharding**: 8 logical shards using table inheritance (MVP) → 8 physical databases (200K users) → 32 shards (1M users)
2. **Indexes**: Aggressive strategy with 50+ indexes (composite, partial, covering, GIN, full-text)
3. **Data Types**: TIMESTAMPTZ (UTC) for timestamps, DECIMAL(12,2) for financial precision
4. **Missing Tables**: 6 new tables added (matching, moderation, referrals, notifications, verification, performance)
5. **i18n**: JSONB columns for multi-language content (5 languages)
6. **Seed Data**: Corrected coin packages, 15-gift catalog, creator_call_rates table
7. **Migrations**: Alembic with sequential versioning
8. **Connection Pooling**: PgBouncer with max 200 database connections

**Timeline Impact:**
- **Original MVP**: 19-24 weeks
- **Comprehensive Schema Addition**: +1-2 weeks
- **Updated MVP**: 20-26 weeks

**Database Statistics:**
- **Total Tables**: 22 (15 base + 7 new)
- **Sharded Tables**: 13 tables
- **Shared Tables**: 9 tables (reference data)
- **Total Indexes**: 50+ (aggressive indexing strategy)
- **Total Foreign Keys**: 30+ relationships
- **Storage Overhead**: 30-40% for indexes (acceptable for <200ms SLA)

**Dependencies Created:**
- Section 8 (API Specifications): Must implement endpoints for all 22 tables
- Section 11 (N8N Workflows): Must handle creator_call_rates, moderation transcripts, creator_performance calculations
- Section 14 (Cost Estimation): Must include 8-shard database costs, index storage costs, PgBouncer costs

**Next Section:** Section 8 - API Specifications (lines 1140-1927, 788 lines - largest remaining section)

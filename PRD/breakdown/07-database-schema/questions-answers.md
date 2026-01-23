# Section 7: Database Schema - Questions & Answers

**Section:** Database Schema
**Date:** 2026-01-20
**Status:** Completed
**Questions Asked:** 9
**Critical Decisions:** 8-shard architecture, TIMESTAMPTZ (UTC), DECIMAL precision, JSONB i18n

---

## PHASE 1: CORE QUESTIONS

### Q1: SHARDING IMPLEMENTATION STRATEGY

**Context:** REQ-TA-010 requires sharding-ready architecture with user_id as sharding key. Current schema uses UUID primary keys but doesn't show explicit sharding implementation.

**Question:** How should we implement logical sharding in the schema?

**Options Presented:**
- Option A: Hash-Based Partitioning (PostgreSQL Native)
- Option B: Application-Level Sharding
- Option C: Hybrid (Logical Partitions Now, Physical Shards Later)

**Answer:** **Hybrid (Logical partitions MVP, physical shards later)**

**Implementation Plan:**
- **MVP (20-26 weeks)**: PostgreSQL table inheritance for logical shards
- **Parent tables**: users, calls, wallet_transactions, etc. (abstract, no data)
- **Child tables**: users_shard_0, users_shard_1, users_shard_2, users_shard_3 (inherit from parent)
- **Shard routing**: Application computes shard_id = hash(user_id) % number_of_shards
- **Migration at 200K users**: Convert to separate physical PostgreSQL databases (separate servers)
- **Scalability**: Each physical database can be further partitioned (16-32 total shards at 1M users)

**Impact:** Requires parent-child table structure in schema. Application must route all queries by user_id.

---

### Q2: INDEX OPTIMIZATION FOR <200MS SLA

**Context:** Schema has basic indexes, but REQ-TA-002 requires <200ms p95 latency for all user-facing endpoints.

**Question:** What level of index optimization is needed for MVP?

**Options Presented:**
- Option A: Basic Indexes Only (Current PRD)
- Option B: Comprehensive Indexes (Performance-Optimized)
- Option C: Aggressive Index Strategy (Premium Performance)

**Answer:** **Aggressive index strategy (Premium)**

**Index Strategy:**
- **Composite indexes** for common query patterns (multi-column indexes)
  - Example: `idx_calls_composite ON calls(receiver_id, status, created_at DESC)`
- **Partial indexes** for filtered queries (WHERE clause conditions)
  - Example: `idx_users_active_creators ON users(is_creator) WHERE is_active = TRUE`
- **Covering indexes** with INCLUDE clause (avoid table lookups)
  - Example: `idx_users_phone_covering ON users(phone) INCLUDE (display_name, profile_photo)`
- **GIN indexes** for JSONB columns (metadata, features, i18n names)
- **Full-text search** indexes using pg_trgm extension (room titles, user bios)
- **Expression indexes** for computed values (age from DOB, revenue tier from earnings)
- **Index maintenance**: Automated REINDEX and VACUUM ANALYZE schedules

**Impact:** Most aggressive index strategy to meet <200ms SLA. Increased storage for indexes, but necessary for premium performance.

---

### Q3: MISSING TABLES AND SCHEMA GAPS

**Context:** Several missing tables required by earlier sections.

**Question:** Should we add all missing tables to the schema now, or defer some to post-MVP?

**Missing Tables Identified:**
1. Matching/Preferences: User preferences for matching algorithm (REQ-FS-012)
2. AI Moderation Transcripts: Speech-to-text transcripts for compliance (REQ-TA-004, REQ-FS-018)
3. Referral System: Track referrals for users and creators (REQ-FS-024)
4. Notifications: Push notification history (Firebase FCM)
5. User Verification: Selfie/ID verification data (REQ-FS-020)
6. Creator Performance: Trailing 30-day stats for revenue tiers (REQ-ES-004)

**Options Presented:**
- Option A: Add All Missing Tables to MVP Schema
- Option B: Add Only Critical Tables (Matching, Moderation, Referrals)
- Option C: Minimal MVP Schema (No New Tables)

**Answer:** **Add all missing tables to MVP**

**New Tables to Add:**
1. **matching_preferences**: Store user preferences for matching algorithm
2. **ai_moderation_transcripts**: Store speech-to-text transcripts for compliance
3. **referrals**: Track user and creator referral program
4. **notifications**: Push notification history (Firebase FCM)
5. **user_verification**: Selfie/ID verification data (tiered badges)
6. **creator_performance**: Trailing 30-day stats for revenue tier calculation

**Timeline Impact:** +1-2 weeks for comprehensive schema design and migration scripts

**UPDATED MVP TIMELINE: 20-26 weeks** (was 19-24 weeks)

**Justification:** Complete schema coverage for all features, proper data structure from start, easier to maintain and optimize.

**Impact:** Comprehensive database design supporting all approved features from Sections 1-6.

---

## PHASE 2: FOLLOW-UP QUESTIONS

### Q4: SHARDING MIGRATION MECHANICS

**Context:** Hybrid sharding chosen. Need to define specific implementation details.

**Question:** Which tables should be sharded, how to handle cross-shard queries, and what about shard rebalancing?

**Answer:** **Over-provision: 8 logical shards for better distribution**

**Sharding Strategy:**
- **8 logical shards at MVP** (instead of typical 4)
- More even distribution of users across shards
- Better load balancing from start
- Shard routing: `shard_id = hash(user_id) % 8`
- **Migration path**:
  - MVP: 8 logical shards (table inheritance)
  - 200K users: 8 physical databases (one per shard)
  - 1M users: 32 physical shards (each of 8 shards splits into 4)

**Tables to Shard:**
- Users, coin_wallets, diamond_wallets, calls, transactions, gift_transactions, rooms, room_participants, reports, user_blocks
- **Shared tables** (reference data): coin_packages, call_rates, subscription_plans, gifts

**Cross-Shard Queries:**
- Avoid through denormalization where possible
- Admin dashboards can use application-level scatter-gather if needed

**Shard Rebalancing:**
- Accept minor imbalance (hash distribution is generally even)
- 8 shards provide better initial distribution than 4

**Impact:** More complex than standard 4-shard approach but provides better distribution and scalability. Application routing logic must support 8 shards.

---

### Q5: TIMESTAMP AND TIMEZONE HANDLING

**Context:** Schema uses TIMESTAMP without timezone specification. Need to define timezone handling for India-focused app.

**Question:** Should we use timezone-aware timestamps, what default timezone, and how to handle historical data?

**Answer:** **TIMESTAMPTZ (UTC) - Best practice**

**Timestamp Strategy:**
- **Data type**: `TIMESTAMPTZ` for all timestamp columns
- **Storage**: All times stored in UTC
- **Database timezone**: Set to UTC
- **Application layer**: Convert to IST (Asia/Kolkata) when displaying to users
- **Benefits**:
  - Handles daylight saving time automatically
  - Supports future global expansion
  - Industry best practice
  - No ambiguity in time calculations

**Critical for:**
- T+1 payout calculations (11:59 PM IST cutoff)
- Call billing (per-minute rates)
- Creator revenue tier calculations (trailing 30 days)
- Analytics and reporting

**Impact:** All TIMESTAMP columns in PRD schema must be changed to TIMESTAMPTZ. Application layer must handle UTC ↔ IST conversion.

---

### Q6: DATA TYPE CONSISTENCY AND PRECISION

**Context:** Schema has data type inconsistencies (INTEGER for coins, DECIMAL for diamonds).

**Question:** Should we standardize data types for financial accuracy?

**Answer:** **DECIMAL for both coins and money**

**Data Type Standards:**
- **Coins**: `DECIMAL(12,2)` (allows fractional coins for promotions, bonuses)
- **Money (Rupees)**: `DECIMAL(12,2)` (two decimal places for paise precision)
- **Percentages**: `DECIMAL(5,2)` (e.g., 75.00% for revenue share)
- **Call rates**: `DECIMAL(10,2)` (coins per minute, allows fractional rates)
- **Gift values**: `DECIMAL(10,2)` (consistent with money)

**Benefits:**
- No rounding errors in calculations
- Precise revenue share calculations (75-85% of net)
- Allows promotional pricing (0.5 coins for trial, 99.99 rupees)
- Financial accuracy for ₹2-3 Cr GMV platform

**Storage impact:** Minimal (DECIMAL is efficient in PostgreSQL)

**Impact:** All INTEGER columns for coins, diamonds, and monetary values must be changed to DECIMAL. Critical for financial accuracy.

---

## PHASE 3: GAP ANALYSIS QUESTIONS

### Q7: SEED DATA CORRECTIONS AND COMPLETENESS

**Context:** PRD seed data conflicts with approved requirements from Sections 4 and 5.

**Conflicts Identified:**
1. Coin packages don't match REQ-RM-004
2. Fixed call rates conflict with creator-set rates (REQ-FS-014)
3. Only 9 gifts vs. 20+ gift catalog (REQ-FS-016)

**Question:** Should we correct the seed data to match approved requirements?

**Answer:** **Hybrid: Critical corrections only**

**Seed Data Updates:**

**1. Coin Packages - Update to match REQ-RM-004:**
- ₹99 = 110 coins (10% bonus)
- ₹299 = 350 coins (17% bonus)
- ₹499 = 600 coins (20% bonus)
- ₹999 = 1,300 coins (30% bonus)
- Remove ₹49 package from PRD

**2. Call Rates - Hybrid approach:**
- Keep call_rates table as baseline/suggested rates
- Add creator_call_rates table for creator-set custom rates (REQ-FS-014)
- Creators can use baseline or set their own (₹8-25/min range)

**3. Gift Catalog - Compromise:**
- Expand from 9 to 15 gifts (not full 20+)
- **Budget tier (5 gifts)**: Rose, Heart, Coffee, Smile, Wave
- **Mid tier (6 gifts)**: Cake, Champagne, Teddy, Chocolate, Bouquet, Kiss
- **Premium tier (4 gifts)**: Diamond Ring, Crown, Luxury Car, Gold Jewelry
- Can add remaining 5+ gifts post-MVP based on usage

**Impact:** Maintains consistency with approved requirements while keeping MVP timeline reasonable. Creator_call_rates table must be added to schema.

---

### Q8: MULTI-LANGUAGE DATA STRUCTURE

**Context:** 5 languages approved at MVP (Tamil, Telugu, Kannada, Hindi, English) but schema shows limited i18n support.

**Question:** How should we handle multi-language data in the schema?

**Options Presented:**
- Option A: Separate Translation Tables (Normalized)
- Option B: JSONB Translation Columns (Denormalized)
- Option C: Columns Per Language (Simple)

**Answer:** **Hybrid: JSONB for content, tables for metadata**

**i18n Strategy:**

**User-facing content (names, descriptions)**: JSONB columns
```sql
ALTER TABLE gifts ADD COLUMN names JSONB;
-- {"en": "Rose", "ta": "ரோஜா", "te": "గులాబీ", "kn": "ಗುಲಾಬಿ", "hi": "गुलाब"}

ALTER TABLE coin_packages ADD COLUMN descriptions JSONB;
ALTER TABLE subscription_plans ADD COLUMN feature_lists JSONB;
```
- Fast lookups (<200ms SLA)
- GIN indexable for search
- Easy to add/update translations

**Structured metadata**: Separate i18n_metadata table
```sql
CREATE TABLE i18n_metadata (
    language VARCHAR(10) PRIMARY KEY,
    display_name VARCHAR(50),
    locale VARCHAR(10),
    date_format VARCHAR(20),
    currency_symbol VARCHAR(5),
    is_rtl BOOLEAN
);
```
- Locale settings, date formats, currency symbols per language

**Impact:** JSONB approach provides performance (single row lookup) with structure (metadata table). All user-facing content tables need JSONB columns added.

---

### Q9: DATABASE MIGRATION AND VERSION CONTROL

**Context:** 20-26 week MVP with comprehensive schema (15+ base tables, 6 new tables, 8 logical shards).

**Question:** Which migration tool, strategy, versioning, and rollback approach?

**Answer:** **Alembic with automated CI/CD**

**Migration Strategy:**
- **Tool**: Alembic (Python, integrates with FastAPI + SQLAlchemy)
- **Versioning**: Sequential numbering (001_initial.py, 002_add_sharding.py, etc.)
- **Automation**:
  - **Dev/Staging**: Automated migrations on deploy (CI/CD)
  - **Production**: Manual review + approval by DBA, then automated apply
- **Rollback**: Automatic transaction-based rollback on failure
- **Migration files**: Both upgrade() and downgrade() functions

**File Structure:**
```
alembic/
  versions/
    001_initial_schema.py
    002_add_logical_shards.py
    003_add_missing_tables.py
    004_add_i18n_jsonb.py
    005_update_seed_data.py
```

**Impact:** Safe schema evolution across 20-26 weeks. Alembic must be integrated into FastAPI project and CI/CD pipeline.

---

## SUMMARY OF DECISIONS

### Critical Technical Decisions
1. **Sharding**: 8 logical shards (table inheritance) at MVP → 8 physical databases at 200K users → 32 shards at 1M users
2. **Indexes**: Aggressive strategy (composite, partial, covering, GIN, full-text, expression)
3. **Missing Tables**: Add all 6 missing tables (matching, moderation, referrals, notifications, verification, creator performance)
4. **Timestamps**: TIMESTAMPTZ (UTC) for all timestamp columns
5. **Data Types**: DECIMAL(12,2) for coins and money (financial precision)
6. **Seed Data**: Update coin packages to REQ-RM-004, add creator_call_rates table, expand gifts to 15
7. **i18n**: JSONB columns for translations, separate metadata table
8. **Migrations**: Alembic with sequential versioning, automated dev/staging, manual production

### Timeline Impact
- **Original MVP**: 19-24 weeks
- **Comprehensive Schema Addition**: +1-2 weeks
- **Updated MVP**: 20-26 weeks

### Schema Complexity
- **Base Tables (PRD)**: 15 tables
- **New Tables**: 7 tables (6 missing features + creator_call_rates)
- **Total Tables**: 22 tables
- **Sharding**: 8 logical shards (13 sharded tables, 9 shared tables)
- **Indexes**: 50+ indexes (basic + composite + partial + covering + GIN + full-text)

### Dependencies Created
- Section 8 (API Specifications): Must implement endpoints for new tables (matching, referrals, verification)
- Section 11 (N8N Workflows): Must handle creator_call_rates updates, moderation transcripts
- Section 14 (Cost Estimation): Must include database costs (8-shard infrastructure, index storage)

---

**Next Steps:**
- Extract database schema requirements with all decisions incorporated
- Define complete table structures (22 tables with sharding, indexes, i18n)
- Specify Alembic migration plan
- Update seed data to match approved requirements

---

# 📱 LIVVLY - Voice Dating & Social App
## Product Requirements Document (PRD)

**Version:** 1.0  
**Date:** January 2026  
**Author:** Shadow Market  
**Status:** Draft  

---

## 📋 Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Market Analysis](#2-market-analysis)
3. [Product Vision](#3-product-vision)
4. [Revenue Model](#4-revenue-model)
5. [Feature Specifications](#5-feature-specifications)
6. [Technical Architecture](#6-technical-architecture)
7. [Database Schema](#7-database-schema)
8. [API Specifications](#8-api-specifications)
9. [Coin Economy Design](#9-coin-economy-design)
10. [Creator Payout System](#10-creator-payout-system)
11. [N8N Automation Workflows](#11-n8n-automation-workflows)
12. [Legal & Compliance](#12-legal--compliance)
13. [Implementation Roadmap](#13-implementation-roadmap)
14. [Cost Estimation](#14-cost-estimation)
15. [Success Metrics](#15-success-metrics)

---

## 1. Executive Summary

### 1.1 Product Overview

LIVVLY is a voice-first social and dating application targeting the Indian market, specifically Tier-2 and Tier-3 cities. The app enables users to connect through voice and video calls, live audio rooms, and virtual gifting, creating an engaging social experience with continuous revenue streams.

### 1.2 Key Differentiators

| Feature | LIVVLY | Competitors (FRND/Tango) |
|---------|--------|--------------------------|
| Creator Revenue Share | 75-85% | 50-60% |
| Regional Language Support | Tamil-first, expanding to Telugu/Kannada | Hindi/English focused |
| AI Integration | AI companion + Real creators | Real creators only |
| Payout Speed | T+1 (Next day) | T+3 to T+7 |

### 1.3 Target Market

- **Primary:** Users aged 18-35 in Tamil Nadu, Andhra Pradesh, Karnataka
- **Secondary:** Indian diaspora seeking regional language connections
- **User Personas:**
  - Lonely professionals seeking companionship
  - Users looking for friendship/dating
  - Content creators seeking income

### 1.4 Business Goals

- **6 Months:** 50,000 active users, ₹20L monthly GMV
- **12 Months:** 2,00,000 active users, ₹1Cr monthly GMV
- **24 Months:** 10,00,000 active users, ₹5Cr monthly GMV

---

## 2. Market Analysis

### 2.1 Competitor Revenue Analysis

Based on research of FRND, Connecto, Dost, and Tango apps:

#### Revenue Streams Identified:

| Revenue Stream | Description | Contribution |
|----------------|-------------|--------------|
| In-App Purchases (Coins) | Users buy virtual currency | 60-70% |
| Paid Calls | Per-minute voice/video calls | 15-20% |
| Virtual Gifts | Gifts during calls/rooms | 10-15% |
| Subscriptions (VIP) | Monthly premium access | 5-10% |
| Ads | Rewarded video ads | 2-5% |

#### Money Flow Example (₹500 User Spend):

```
User Spends ₹500
    │
    ├── Google Play (30%) ────────────► ₹150
    │
    └── Net Revenue (70%) ────────────► ₹350
            │
            ├── Platform (55%) ───────► ₹193
            │
            └── Creator Pool (45%) ───► ₹157
                    │
                    ├── Agency (25%) ─► ₹39
                    │
                    └── Creator (75%) ► ₹118
```

### 2.2 Market Size (India)

- **Total Addressable Market (TAM):** ₹5,000 Cr (Dating + Social apps)
- **Serviceable Addressable Market (SAM):** ₹1,500 Cr (Voice-based social)
- **Serviceable Obtainable Market (SOM):** ₹50 Cr (Regional language focus)

### 2.3 SWOT Analysis

| Strengths | Weaknesses |
|-----------|------------|
| Higher creator payout (75-85%) | New entrant, no brand recognition |
| Regional language focus | Limited initial creator base |
| AI + Human hybrid model | Higher development complexity |
| Strong technical foundation | Requires significant marketing spend |

| Opportunities | Threats |
|---------------|---------|
| Underserved Tier-2/3 market | Established competitors |
| Growing creator economy | Regulatory changes |
| AI companionship demand | Platform policy changes (Google/Apple) |
| Regional content explosion | Economic downturn affecting discretionary spend |

---

## 3. Product Vision

### 3.1 Vision Statement

> "To become India's leading regional-language voice social platform, empowering creators to earn sustainably while providing users with meaningful connections."

### 3.2 Mission

- Connect people through voice in their native language
- Provide creators with the highest revenue share in the industry
- Build safe, moderated spaces for genuine interactions

### 3.3 Core Values

1. **Creator-First:** 75-85% revenue share
2. **Safety:** Robust moderation and KYC
3. **Accessibility:** Regional language support
4. **Transparency:** Clear earnings and spending visibility

### 3.4 User Journey

```
┌─────────────────────────────────────────────────────────────────┐
│                        USER JOURNEY                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  DISCOVERY        ONBOARDING       ENGAGEMENT       MONETIZATION │
│      │                │                │                │        │
│      ▼                ▼                ▼                ▼        │
│  ┌───────┐       ┌───────┐       ┌───────┐       ┌───────┐      │
│  │ Ads/  │       │ Phone │       │ Browse│       │ Buy   │      │
│  │ ASO/  │──────►│ OTP   │──────►│ Rooms │──────►│ Coins │      │
│  │ Refer │       │ Setup │       │ Match │       │ Spend │      │
│  └───────┘       └───────┘       │ Call  │       │ Gift  │      │
│                                  └───────┘       └───────┘      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 4. Revenue Model

### 4.1 Revenue Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    REVENUE STREAMS                           │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   COINS      │  │  SUBSCRIPTIONS│  │    ADS       │       │
│  │              │  │              │  │              │       │
│  │ - Recharge   │  │ - VIP Pass   │  │ - Rewarded   │       │
│  │ - Gifts      │  │ - Creator+   │  │ - Banner     │       │
│  │ - Calls      │  │ - Ad-Free    │  │ - Native     │       │
│  │              │  │              │  │              │       │
│  │   (70%)      │  │    (25%)     │  │    (5%)      │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 4.2 Revenue Split Model

#### Standard Split (Recommended):

| Receiver | Percentage | From ₹500 Spend |
|----------|------------|-----------------|
| Google Play | 30% | ₹150 |
| Platform (LIVVLY) | 38.5% (55% of net) | ₹193 |
| Creator | 31.5% (45% of net) | ₹157 |
| **Total** | **100%** | **₹500** |

#### With Agency Model:

| Receiver | Percentage | From ₹500 Spend |
|----------|------------|-----------------|
| Google Play | 30% | ₹150 |
| Platform (LIVVLY) | 38.5% | ₹193 |
| Agency | 7.9% | ₹39 |
| Creator | 23.6% | ₹118 |
| **Total** | **100%** | **₹500** |

### 4.3 FRND-Style High Creator Share Model

For competitive advantage, LIVVLY offers higher creator share:

| Receiver | Percentage | From ₹500 Spend |
|----------|------------|-----------------|
| Google Play | 30% | ₹150 |
| Platform (LIVVLY) | 24.5% (35% of net) | ₹122.50 |
| Creator (Gross) | 45.5% (65% of net) | ₹227.50 |
| TDS (10%) | 4.55% | ₹22.75 |
| Creator (Net) | 40.95% | ₹204.75 |

### 4.4 Subscription Tiers

| Plan | Price | Features |
|------|-------|----------|
| **Free** | ₹0 | 5 free calls/day, limited rooms |
| **VIP** | ₹199/month | Unlimited calls, 20% coin discount, VIP badge |
| **Premium** | ₹499/month | All VIP + Priority matching, 30% coin discount |
| **Creator+** | ₹999/month | Analytics, promotion tools, 40% coin discount |

---

## 5. Feature Specifications

### 5.1 App Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      APP NAVIGATION                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │  HOME    │  │ DISCOVER │  │  WALLET  │  │ PROFILE  │        │
│  │          │  │          │  │          │  │          │        │
│  │ - Feed   │  │ - Search │  │ - Coins  │  │ - Settings│       │
│  │ - Live   │  │ - Filter │  │ - History│  │ - KYC    │        │
│  │ - Match  │  │ - Tags   │  │ - Recharge│ │ - Earnings│       │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘        │
│                                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │  ROOMS   │  │  CALLS   │  │  CHAT    │  │ CREATOR  │        │
│  │          │  │          │  │          │  │DASHBOARD │        │
│  │ - Live   │  │ - Audio  │  │ - 1-to-1 │  │ - Earn   │        │
│  │ - Join   │  │ - Video  │  │ - Group  │  │ - Stats  │        │
│  │ - Host   │  │ - History│  │ - Media  │  │ - Payout │        │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 5.2 Screen Specifications

#### 5.2.1 Onboarding Flow

```
SCREEN: Onboarding
├── Splash Screen (2 seconds)
│   └── App logo + tagline animation
│
├── Phone Authentication
│   ├── Input: Phone number (10 digits)
│   ├── Action: Send OTP (via Firebase/MSG91)
│   ├── Input: 6-digit OTP
│   └── Validation: Auto-verify or manual entry
│
├── Profile Setup
│   ├── Input: Display Name (3-20 characters)
│   ├── Input: Gender (Male/Female/Other)
│   ├── Input: Date of Birth (18+ validation)
│   ├── Input: Profile Photo (mandatory, face detection)
│   ├── Input: Bio (optional, 150 characters max)
│   └── Input: Location (auto-detect or manual)
│
├── Interest Selection
│   ├── Categories: Music, Movies, Gaming, Sports, Travel, etc.
│   ├── Minimum: 3 interests required
│   └── Maximum: 10 interests
│
└── Language Preference
    ├── Primary: Tamil, Telugu, Kannada, Hindi, English
    └── Secondary: Optional
```

#### 5.2.2 Home Screen

```
SCREEN: Home
├── Header
│   ├── App Logo (left)
│   ├── Coin Balance (right, tappable → Wallet)
│   └── Notification Bell (right)
│
├── Top Banner Carousel
│   ├── Promotions (auto-scroll)
│   ├── Events
│   └── New Features
│
├── Live Rooms Section
│   ├── Title: "🔴 Live Now"
│   ├── Horizontal Scroll Cards
│   │   ├── Host Photo (circular)
│   │   ├── Room Name
│   │   ├── Viewer Count
│   │   └── Tags (2 max)
│   └── "See All" button
│
├── Recommended Profiles Grid
│   ├── Title: "People You May Like"
│   ├── 2-column grid
│   │   ├── Profile Photo
│   │   ├── Name, Age
│   │   ├── Online Status (green dot)
│   │   └── Quick Call Button
│   └── Infinite scroll pagination
│
├── Floating Action Button
│   └── Quick Match (random pairing)
│
└── Bottom Navigation
    ├── Home (active)
    ├── Discover
    ├── Rooms (center, prominent)
    ├── Chat
    └── Profile
```

#### 5.2.3 Live Voice Room

```
SCREEN: Live Voice Room
├── Room Header
│   ├── Room Title
│   ├── Room Tags
│   ├── Viewer Count
│   ├── Share Button
│   └── Close Button (X)
│
├── Host Section
│   ├── Host Seat (large, center)
│   │   ├── Profile Photo
│   │   ├── Name + Badge
│   │   ├── Speaking Indicator (animated)
│   │   └── Mute Status
│   └── Co-Host Seats (8 max)
│       └── Same layout as host, smaller
│
├── Audience Section
│   ├── Audience Avatars (scrollable)
│   ├── Count: "123 listening"
│   └── "Raise Hand" queue indicator
│
├── Gift Animation Overlay
│   └── Full-screen animations for luxury gifts
│
├── Chat Panel (bottom half, expandable)
│   ├── Message List
│   │   ├── Username: Message
│   │   └── Gift notifications
│   └── Input Field + Send Button
│
└── Action Bar (bottom fixed)
    ├── Mic Toggle
    ├── Gift Button → Opens Gift Panel
    ├── Raise Hand
    ├── Share
    └── More Options (Report, Leave)
```

#### 5.2.4 1-to-1 Call Screen

```
SCREEN: 1-to-1 Call

PRE-CALL:
├── Receiver Profile
│   ├── Large Profile Photo
│   ├── Name, Age, Location
│   ├── Online Status
│   └── Rating (stars)
│
├── Call Rate Display
│   ├── "Audio: 8 coins/min"
│   ├── "Video: 12 coins/min"
│   └── Your Balance: 500 coins
│
├── Call Buttons
│   ├── Audio Call (primary)
│   └── Video Call (secondary)
│
└── Estimated Call Time
    └── "You can talk for ~62 minutes"

DURING CALL:
├── Call Display
│   ├── (Audio) Profile Photo + Name
│   ├── (Video) Video Feed
│   └── Call Duration Timer
│
├── Coin Counter
│   ├── "Coins: 492" (updating)
│   └── "Rate: 8/min"
│
├── Low Balance Warning
│   └── Shows at 2 minutes remaining
│
├── Control Bar
│   ├── Mute/Unmute
│   ├── Video On/Off (if video call)
│   ├── Speaker Toggle
│   ├── Send Gift
│   └── End Call
│
└── Gift Button → Opens Gift Panel

POST-CALL:
├── Call Summary
│   ├── Duration: 15 min 32 sec
│   ├── Coins Spent: 128
│   └── Rating Prompt (1-5 stars)
│
├── Actions
│   ├── Add to Favorites
│   ├── Block/Report
│   └── Call Again
│
└── Low Balance Prompt (if applicable)
    └── "Recharge now for 20% bonus!"
```

#### 5.2.5 Wallet Screen (User)

```
SCREEN: User Wallet
├── Balance Display
│   ├── Coin Icon + Balance (large)
│   └── "= ₹{value} value"
│
├── Quick Recharge Buttons
│   ├── ₹99 (Popular)
│   ├── ₹299
│   └── ₹499
│
├── All Packages (expandable)
│   ├── ₹49 → 50 coins
│   ├── ₹99 → 110 coins (+10%)
│   ├── ₹299 → 350 coins (+17%)
│   ├── ₹499 → 600 coins (+20%) [BEST VALUE]
│   ├── ₹999 → 1300 coins (+30%)
│   └── ₹1999 → 2800 coins (+40%)
│
├── Subscription Plans
│   ├── VIP ₹199/month
│   └── Premium ₹499/month
│
├── Transaction History
│   ├── Filter: All | Recharge | Spent
│   └── List Items:
│       ├── Icon (+ or -)
│       ├── Description
│       ├── Amount
│       └── Date/Time
│
└── Redeem Code Section
    └── Input field + Apply button
```

#### 5.2.6 Creator Dashboard

```
SCREEN: Creator Dashboard
├── Earnings Summary
│   ├── Today: ₹1,234
│   ├── This Week: ₹8,567
│   ├── This Month: ₹32,456
│   └── Total: ₹1,23,456
│
├── Balance Section
│   ├── Available: ₹5,678 [Withdraw]
│   ├── Pending: ₹2,345 (7-day hold)
│   └── Processing: ₹1,000
│
├── Quick Stats
│   ├── Total Calls: 234
│   ├── Avg Duration: 12 min
│   ├── Gifts Received: 567
│   ├── Followers: 1,234
│   └── Rating: 4.8 ⭐
│
├── Performance Graph
│   ├── Daily earnings chart (7 days)
│   └── Toggle: Earnings | Calls | Gifts
│
├── Leaderboard Rank
│   ├── Current Rank: #45
│   ├── Coins to next rank: 5,000
│   └── "Top 10 get bonus rewards!"
│
├── Withdrawal Section
│   ├── Enter Amount
│   ├── Bank Account (linked)
│   ├── TDS Notice: "10% TDS applicable"
│   └── Withdraw Button
│
└── Tips & Resources
    ├── "How to earn more"
    ├── "Best times to go live"
    └── "Creator guidelines"
```

### 5.3 Feature Priority Matrix

| Feature | Priority | MVP | Phase 2 | Phase 3 |
|---------|----------|-----|---------|---------|
| Phone OTP Auth | P0 | ✅ | | |
| Profile Setup | P0 | ✅ | | |
| Coin Wallet | P0 | ✅ | | |
| Razorpay Integration | P0 | ✅ | | |
| 1-to-1 Audio Call | P0 | ✅ | | |
| Basic Matching | P0 | ✅ | | |
| Gift System | P1 | ✅ | | |
| Creator Dashboard | P1 | ✅ | | |
| Live Voice Rooms | P1 | | ✅ | |
| Video Calls | P1 | | ✅ | |
| KYC System | P1 | | ✅ | |
| Automated Payouts | P1 | | ✅ | |
| VIP Subscription | P2 | | ✅ | |
| AI Companion | P2 | | | ✅ |
| Games Integration | P2 | | | ✅ |
| Agency Dashboard | P2 | | | ✅ |
| Multi-language | P2 | | | ✅ |

---

## 6. Technical Architecture

### 6.1 System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     SYSTEM ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────┐         ┌─────────────────────────────────┐   │
│  │   Mobile    │         │         BACKEND SERVICES         │   │
│  │    Apps     │         │                                  │   │
│  │             │  HTTPS  │  ┌─────────┐    ┌─────────┐     │   │
│  │  ┌───────┐  │◄───────►│  │ FastAPI │    │  Redis  │     │   │
│  │  │Flutter│  │         │  │   API   │◄──►│  Cache  │     │   │
│  │  │  App  │  │         │  └────┬────┘    └─────────┘     │   │
│  │  └───────┘  │         │       │                          │   │
│  │             │         │       ▼                          │   │
│  └─────────────┘         │  ┌─────────┐    ┌─────────┐     │   │
│                          │  │PostgreSQL│   │ S3/GCS  │     │   │
│  ┌─────────────┐         │  │ Database │   │ Storage │     │   │
│  │   External  │         │  └─────────┘    └─────────┘     │   │
│  │  Services   │         │                                  │   │
│  │             │         └─────────────────────────────────┘   │
│  │ ┌─────────┐ │                                               │
│  │ │ Agora   │ │◄───── Voice/Video Streaming                   │
│  │ │   SDK   │ │                                               │
│  │ └─────────┘ │                                               │
│  │             │                                               │
│  │ ┌─────────┐ │                                               │
│  │ │Razorpay │ │◄───── Payment Processing                      │
│  │ │   API   │ │                                               │
│  │ └─────────┘ │                                               │
│  │             │                                               │
│  │ ┌─────────┐ │                                               │
│  │ │Firebase │ │◄───── Auth, Push, Analytics                   │
│  │ └─────────┘ │                                               │
│  │             │                                               │
│  │ ┌─────────┐ │                                               │
│  │ │  n8n    │ │◄───── Automation Workflows                    │
│  │ └─────────┘ │                                               │
│  └─────────────┘                                               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 6.2 Technology Stack

| Layer | Technology | Justification |
|-------|------------|---------------|
| **Mobile App** | Flutter 3.x | Cross-platform, fast development |
| **Backend API** | FastAPI (Python) | High performance, async support |
| **Database** | PostgreSQL 15 | ACID compliance, JSON support |
| **Cache** | Redis 7 | Session management, real-time data |
| **Voice/Video** | Agora SDK | Reliable, low latency, India servers |
| **Payments** | Razorpay | Best India support, UPI, cards |
| **Auth** | Firebase Auth | OTP, social login, secure |
| **Push Notifications** | Firebase FCM | Reliable delivery |
| **File Storage** | AWS S3 / GCS | Scalable, CDN integration |
| **Automation** | n8n (self-hosted) | Workflow automation |
| **Monitoring** | Grafana + Prometheus | Real-time metrics |
| **CI/CD** | GitHub Actions | Automated deployments |

### 6.3 API Architecture

```
API STRUCTURE:
├── /api/v1
│   ├── /auth
│   │   ├── POST /send-otp
│   │   ├── POST /verify-otp
│   │   ├── POST /refresh-token
│   │   └── POST /logout
│   │
│   ├── /users
│   │   ├── GET /me
│   │   ├── PUT /me
│   │   ├── POST /me/photo
│   │   ├── GET /{user_id}
│   │   └── GET /search
│   │
│   ├── /wallet
│   │   ├── GET /balance
│   │   ├── POST /recharge/initiate
│   │   ├── POST /recharge/verify
│   │   └── GET /transactions
│   │
│   ├── /calls
│   │   ├── POST /start
│   │   ├── POST /end
│   │   ├── GET /history
│   │   └── GET /rates
│   │
│   ├── /gifts
│   │   ├── GET /catalog
│   │   ├── POST /send
│   │   └── GET /received
│   │
│   ├── /rooms
│   │   ├── GET /live
│   │   ├── POST /create
│   │   ├── POST /{room_id}/join
│   │   └── POST /{room_id}/leave
│   │
│   ├── /creator
│   │   ├── GET /dashboard
│   │   ├── GET /earnings
│   │   ├── POST /withdraw
│   │   └── GET /withdrawals
│   │
│   └── /admin
│       ├── GET /users
│       ├── GET /transactions
│       ├── GET /reports
│       └── POST /actions
```

---

## 7. Database Schema

### 7.1 Core Tables

```sql
-- =============================================
-- USERS & AUTHENTICATION
-- =============================================

CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    phone VARCHAR(15) UNIQUE NOT NULL,
    display_name VARCHAR(50) NOT NULL,
    gender VARCHAR(10) CHECK (gender IN ('male', 'female', 'other')),
    dob DATE NOT NULL,
    profile_photo TEXT,
    bio VARCHAR(150),
    language VARCHAR(10) DEFAULT 'en',
    location_city VARCHAR(50),
    location_state VARCHAR(50),
    
    -- User status
    is_active BOOLEAN DEFAULT TRUE,
    is_creator BOOLEAN DEFAULT FALSE,
    is_verified BOOLEAN DEFAULT FALSE,
    is_vip BOOLEAN DEFAULT FALSE,
    
    -- Timestamps
    last_online TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_users_phone ON users(phone);
CREATE INDEX idx_users_is_creator ON users(is_creator);
CREATE INDEX idx_users_location ON users(location_city, location_state);

-- User interests/tags
CREATE TABLE user_interests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    interest VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_user_interests_user ON user_interests(user_id);

-- =============================================
-- WALLET SYSTEM
-- =============================================

-- User coin wallet (for spending)
CREATE TABLE coin_wallets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    balance INTEGER DEFAULT 0 CHECK (balance >= 0),
    total_purchased INTEGER DEFAULT 0,
    total_spent INTEGER DEFAULT 0,
    total_gifted INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Creator diamond wallet (for earnings)
CREATE TABLE diamond_wallets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    available_balance DECIMAL(12,2) DEFAULT 0 CHECK (available_balance >= 0),
    pending_balance DECIMAL(12,2) DEFAULT 0 CHECK (pending_balance >= 0),
    total_earned DECIMAL(12,2) DEFAULT 0,
    total_withdrawn DECIMAL(12,2) DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Coin packages for purchase
CREATE TABLE coin_packages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(50) NOT NULL,
    price_inr INTEGER NOT NULL,
    coins INTEGER NOT NULL,
    bonus_coins INTEGER DEFAULT 0,
    bonus_percent INTEGER DEFAULT 0,
    is_popular BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW()
);

-- All transactions (unified ledger)
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    
    -- Transaction type
    type VARCHAR(20) NOT NULL CHECK (type IN (
        'recharge', 'spend_call', 'spend_gift', 'spend_room',
        'earn_call', 'earn_gift', 'earn_room', 'withdraw',
        'refund', 'bonus', 'subscription'
    )),
    
    -- Amounts
    amount_inr DECIMAL(12,2),
    coins INTEGER,
    diamonds DECIMAL(12,2),
    
    -- Reference
    reference_type VARCHAR(20),
    reference_id UUID,
    
    -- Payment info
    payment_gateway VARCHAR(20),
    payment_id VARCHAR(100),
    
    -- Status
    status VARCHAR(20) DEFAULT 'completed' CHECK (status IN (
        'pending', 'completed', 'failed', 'refunded'
    )),
    
    -- Metadata
    metadata JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_transactions_user ON transactions(user_id);
CREATE INDEX idx_transactions_type ON transactions(type);
CREATE INDEX idx_transactions_created ON transactions(created_at);

-- =============================================
-- CALLS SYSTEM
-- =============================================

CREATE TABLE calls (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    caller_id UUID REFERENCES users(id),
    receiver_id UUID REFERENCES users(id),
    
    -- Call details
    call_type VARCHAR(10) CHECK (call_type IN ('audio', 'video')),
    rate_per_minute INTEGER NOT NULL,
    
    -- Timing
    initiated_at TIMESTAMP DEFAULT NOW(),
    started_at TIMESTAMP,
    ended_at TIMESTAMP,
    duration_seconds INTEGER,
    
    -- Billing
    coins_spent INTEGER DEFAULT 0,
    diamonds_earned DECIMAL(10,2) DEFAULT 0,
    
    -- Status
    status VARCHAR(20) DEFAULT 'initiated' CHECK (status IN (
        'initiated', 'ringing', 'ongoing', 'completed', 
        'missed', 'rejected', 'failed'
    )),
    end_reason VARCHAR(20),
    
    -- Agora
    agora_channel VARCHAR(100),
    
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_calls_caller ON calls(caller_id);
CREATE INDEX idx_calls_receiver ON calls(receiver_id);
CREATE INDEX idx_calls_status ON calls(status);

-- Call rates (dynamic pricing)
CREATE TABLE call_rates (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_type VARCHAR(20) NOT NULL, -- 'regular', 'popular', 'verified', 'celebrity'
    call_type VARCHAR(10) NOT NULL, -- 'audio', 'video'
    base_rate INTEGER NOT NULL,
    peak_multiplier DECIMAL(3,2) DEFAULT 1.0,
    peak_start_hour INTEGER DEFAULT 22,
    peak_end_hour INTEGER DEFAULT 2,
    is_active BOOLEAN DEFAULT TRUE
);

-- =============================================
-- GIFTS SYSTEM
-- =============================================

CREATE TABLE gifts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(50) NOT NULL,
    name_tamil VARCHAR(50),
    icon_url TEXT NOT NULL,
    animation_url TEXT,
    
    -- Pricing
    coin_price INTEGER NOT NULL,
    diamond_value DECIMAL(10,2) NOT NULL,
    
    -- Category
    category VARCHAR(20) CHECK (category IN (
        'basic', 'premium', 'luxury', 'exclusive', 'event'
    )),
    
    -- Status
    is_active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER DEFAULT 0,
    
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE gift_transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sender_id UUID REFERENCES users(id),
    receiver_id UUID REFERENCES users(id),
    gift_id UUID REFERENCES gifts(id),
    
    quantity INTEGER DEFAULT 1,
    coins_spent INTEGER NOT NULL,
    diamonds_earned DECIMAL(10,2) NOT NULL,
    
    -- Context
    context_type VARCHAR(20) CHECK (context_type IN (
        'call', 'room', 'profile', 'chat'
    )),
    context_id UUID,
    
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_gift_tx_sender ON gift_transactions(sender_id);
CREATE INDEX idx_gift_tx_receiver ON gift_transactions(receiver_id);

-- =============================================
-- LIVE ROOMS SYSTEM
-- =============================================

CREATE TABLE rooms (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    host_id UUID REFERENCES users(id),
    
    -- Room info
    title VARCHAR(100) NOT NULL,
    description TEXT,
    tags VARCHAR(50)[],
    
    -- Settings
    max_speakers INTEGER DEFAULT 8,
    is_private BOOLEAN DEFAULT FALSE,
    password VARCHAR(20),
    
    -- Status
    status VARCHAR(20) DEFAULT 'live' CHECK (status IN (
        'live', 'ended', 'suspended'
    )),
    
    -- Stats
    current_viewers INTEGER DEFAULT 0,
    total_viewers INTEGER DEFAULT 0,
    total_gifts_value INTEGER DEFAULT 0,
    
    -- Timing
    started_at TIMESTAMP DEFAULT NOW(),
    ended_at TIMESTAMP,
    
    -- Agora
    agora_channel VARCHAR(100),
    
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE room_participants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    room_id UUID REFERENCES rooms(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id),
    
    role VARCHAR(20) DEFAULT 'audience' CHECK (role IN (
        'host', 'co_host', 'speaker', 'audience'
    )),
    
    joined_at TIMESTAMP DEFAULT NOW(),
    left_at TIMESTAMP,
    
    -- Stats during session
    gifts_sent INTEGER DEFAULT 0,
    gifts_received INTEGER DEFAULT 0
);

CREATE INDEX idx_room_participants_room ON room_participants(room_id);

-- =============================================
-- CREATOR KYC & PAYOUTS
-- =============================================

CREATE TABLE creator_kyc (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID UNIQUE REFERENCES users(id),
    
    -- PAN details
    pan_number VARCHAR(10),
    pan_name VARCHAR(100),
    pan_verified BOOLEAN DEFAULT FALSE,
    pan_verified_at TIMESTAMP,
    
    -- Bank details
    bank_account_number VARCHAR(20),
    bank_ifsc VARCHAR(11),
    bank_name VARCHAR(100),
    bank_branch VARCHAR(100),
    bank_verified BOOLEAN DEFAULT FALSE,
    
    -- UPI
    upi_id VARCHAR(50),
    
    -- Status
    kyc_status VARCHAR(20) DEFAULT 'pending' CHECK (kyc_status IN (
        'pending', 'submitted', 'verified', 'rejected'
    )),
    rejection_reason TEXT,
    
    -- Timestamps
    submitted_at TIMESTAMP,
    verified_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE withdrawals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    
    -- Amount details
    amount DECIMAL(12,2) NOT NULL,
    tds_amount DECIMAL(12,2) DEFAULT 0,
    net_amount DECIMAL(12,2) NOT NULL,
    
    -- Payout method
    payout_method VARCHAR(20) CHECK (payout_method IN (
        'bank_transfer', 'upi'
    )),
    bank_account_id UUID REFERENCES creator_kyc(id),
    
    -- Status
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN (
        'pending', 'processing', 'completed', 'failed', 'cancelled'
    )),
    failure_reason TEXT,
    
    -- Payment gateway
    payout_reference VARCHAR(100),
    payout_gateway VARCHAR(20),
    
    -- Timing
    requested_at TIMESTAMP DEFAULT NOW(),
    processed_at TIMESTAMP,
    completed_at TIMESTAMP,
    
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_withdrawals_user ON withdrawals(user_id);
CREATE INDEX idx_withdrawals_status ON withdrawals(status);

-- =============================================
-- SUBSCRIPTIONS
-- =============================================

CREATE TABLE subscription_plans (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(50) NOT NULL,
    price_inr INTEGER NOT NULL,
    duration_days INTEGER NOT NULL,
    
    -- Features (JSONB)
    features JSONB,
    
    -- Coin discount
    coin_discount_percent INTEGER DEFAULT 0,
    
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE user_subscriptions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    plan_id UUID REFERENCES subscription_plans(id),
    
    started_at TIMESTAMP DEFAULT NOW(),
    expires_at TIMESTAMP NOT NULL,
    
    -- Payment
    payment_id VARCHAR(100),
    amount_paid INTEGER,
    
    -- Status
    status VARCHAR(20) DEFAULT 'active' CHECK (status IN (
        'active', 'expired', 'cancelled'
    )),
    
    created_at TIMESTAMP DEFAULT NOW()
);

-- =============================================
-- MODERATION & REPORTS
-- =============================================

CREATE TABLE reports (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    reporter_id UUID REFERENCES users(id),
    reported_user_id UUID REFERENCES users(id),
    
    reason VARCHAR(50) NOT NULL,
    description TEXT,
    
    -- Context
    context_type VARCHAR(20),
    context_id UUID,
    
    -- Status
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN (
        'pending', 'reviewed', 'action_taken', 'dismissed'
    )),
    
    -- Resolution
    reviewed_by UUID,
    reviewed_at TIMESTAMP,
    action_taken TEXT,
    
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE user_blocks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    blocker_id UUID REFERENCES users(id),
    blocked_id UUID REFERENCES users(id),
    created_at TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(blocker_id, blocked_id)
);
```

### 7.2 Seed Data

```sql
-- Coin Packages
INSERT INTO coin_packages (name, price_inr, coins, bonus_coins, bonus_percent, is_popular, sort_order) VALUES
('Starter', 49, 45, 5, 10, FALSE, 1),
('Popular', 99, 95, 15, 16, TRUE, 2),
('Value', 299, 280, 70, 25, FALSE, 3),
('Best Seller', 499, 460, 140, 30, TRUE, 4),
('Premium', 999, 900, 400, 44, FALSE, 5),
('VIP', 1999, 1750, 1050, 60, FALSE, 6);

-- Call Rates
INSERT INTO call_rates (user_type, call_type, base_rate, peak_multiplier) VALUES
('regular', 'audio', 5, 1.5),
('regular', 'video', 8, 1.5),
('popular', 'audio', 8, 1.5),
('popular', 'video', 12, 1.5),
('verified', 'audio', 12, 1.5),
('verified', 'video', 18, 1.5),
('celebrity', 'audio', 20, 1.5),
('celebrity', 'video', 30, 1.5);

-- Gifts
INSERT INTO gifts (name, name_tamil, icon_url, coin_price, diamond_value, category, sort_order) VALUES
('Rose', 'ரோஜா', '/gifts/rose.png', 10, 4.50, 'basic', 1),
('Heart', 'இதயம்', '/gifts/heart.png', 20, 9.00, 'basic', 2),
('Coffee', 'காபி', '/gifts/coffee.png', 50, 22.50, 'basic', 3),
('Teddy Bear', 'டெடி பேர்', '/gifts/teddy.png', 100, 45.00, 'premium', 4),
('Perfume', 'வாசனை திரவியம்', '/gifts/perfume.png', 200, 90.00, 'premium', 5),
('Diamond Ring', 'வைர மோதிரம்', '/gifts/ring.png', 500, 225.00, 'luxury', 6),
('Sports Car', 'ஸ்போர்ட்ஸ் கார்', '/gifts/car.png', 1000, 450.00, 'luxury', 7),
('Private Jet', 'தனியார் விமானம்', '/gifts/jet.png', 5000, 2250.00, 'exclusive', 8),
('Castle', 'அரண்மனை', '/gifts/castle.png', 10000, 4500.00, 'exclusive', 9);

-- Subscription Plans
INSERT INTO subscription_plans (name, price_inr, duration_days, features, coin_discount_percent) VALUES
('VIP', 199, 30, '{"unlimited_calls": false, "vip_badge": true, "priority_support": true}', 20),
('Premium', 499, 30, '{"unlimited_calls": true, "vip_badge": true, "priority_support": true, "exclusive_rooms": true}', 30),
('Creator+', 999, 30, '{"analytics": true, "promotion_tools": true, "priority_matching": true}', 40);
```

---

## 8. API Specifications

### 8.1 Authentication APIs

```python
# auth_routes.py

from fastapi import APIRouter, HTTPException, Depends
from pydantic import BaseModel
import firebase_admin
from firebase_admin import auth as firebase_auth

router = APIRouter(prefix="/api/v1/auth", tags=["Authentication"])

class SendOTPRequest(BaseModel):
    phone: str  # Format: +91XXXXXXXXXX

class VerifyOTPRequest(BaseModel):
    phone: str
    otp: str
    firebase_token: str

class AuthResponse(BaseModel):
    access_token: str
    refresh_token: str
    user_id: str
    is_new_user: bool


@router.post("/send-otp")
async def send_otp(request: SendOTPRequest):
    """
    Send OTP to phone number via Firebase
    
    Flow:
    1. Validate phone format
    2. Check if user is blocked
    3. Rate limit check (max 5 OTP/hour)
    4. Trigger Firebase OTP
    """
    # Validate Indian phone number
    if not request.phone.startswith("+91") or len(request.phone) != 13:
        raise HTTPException(400, "Invalid Indian phone number")
    
    # Rate limiting (implementation in middleware)
    
    return {"message": "OTP sent successfully", "phone": request.phone}


@router.post("/verify-otp", response_model=AuthResponse)
async def verify_otp(request: VerifyOTPRequest, db: Session = Depends(get_db)):
    """
    Verify OTP and create/login user
    
    Flow:
    1. Verify Firebase token
    2. Check if user exists
    3. Create user if new
    4. Generate JWT tokens
    5. Create coin wallet if new
    """
    try:
        # Verify Firebase token
        decoded_token = firebase_auth.verify_id_token(request.firebase_token)
        phone = decoded_token.get('phone_number')
    except Exception:
        raise HTTPException(401, "Invalid authentication token")
    
    # Check if user exists
    user = db.query(User).filter(User.phone == phone).first()
    is_new_user = False
    
    if not user:
        # Create new user
        user = User(phone=phone, display_name=f"User_{phone[-4:]}")
        db.add(user)
        db.flush()
        
        # Create coin wallet
        wallet = CoinWallet(user_id=user.id)
        db.add(wallet)
        
        is_new_user = True
    
    db.commit()
    
    # Generate tokens
    access_token = create_access_token(user.id)
    refresh_token = create_refresh_token(user.id)
    
    return AuthResponse(
        access_token=access_token,
        refresh_token=refresh_token,
        user_id=str(user.id),
        is_new_user=is_new_user
    )


@router.post("/refresh-token")
async def refresh_token(refresh_token: str):
    """Refresh access token using refresh token"""
    try:
        payload = verify_refresh_token(refresh_token)
        new_access_token = create_access_token(payload['user_id'])
        return {"access_token": new_access_token}
    except Exception:
        raise HTTPException(401, "Invalid refresh token")


@router.post("/logout")
async def logout(
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """Logout user and invalidate tokens"""
    # Add token to blacklist (Redis)
    await redis.sadd(f"blacklist:{user_id}", "current_token")
    return {"message": "Logged out successfully"}
```

### 8.2 Wallet APIs

```python
# wallet_routes.py

from fastapi import APIRouter, HTTPException, Depends
from pydantic import BaseModel
from decimal import Decimal
import razorpay

router = APIRouter(prefix="/api/v1/wallet", tags=["Wallet"])

# Razorpay client
razorpay_client = razorpay.Client(auth=(RAZORPAY_KEY_ID, RAZORPAY_KEY_SECRET))


class RechargeInitiateRequest(BaseModel):
    package_id: str

class RechargeVerifyRequest(BaseModel):
    razorpay_order_id: str
    razorpay_payment_id: str
    razorpay_signature: str

class WithdrawRequest(BaseModel):
    amount: Decimal
    payout_method: str = "bank_transfer"  # or "upi"


@router.get("/balance")
async def get_balance(
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """Get user's coin and diamond balance"""
    
    coin_wallet = db.query(CoinWallet).filter(
        CoinWallet.user_id == user_id
    ).first()
    
    diamond_wallet = db.query(DiamondWallet).filter(
        DiamondWallet.user_id == user_id
    ).first()
    
    return {
        "coins": {
            "balance": coin_wallet.balance if coin_wallet else 0,
            "total_purchased": coin_wallet.total_purchased if coin_wallet else 0,
            "total_spent": coin_wallet.total_spent if coin_wallet else 0
        },
        "diamonds": {
            "available": float(diamond_wallet.available_balance) if diamond_wallet else 0,
            "pending": float(diamond_wallet.pending_balance) if diamond_wallet else 0,
            "total_earned": float(diamond_wallet.total_earned) if diamond_wallet else 0
        }
    }


@router.get("/packages")
async def get_packages(db: Session = Depends(get_db)):
    """Get all active coin packages"""
    
    packages = db.query(CoinPackage).filter(
        CoinPackage.is_active == True
    ).order_by(CoinPackage.sort_order).all()
    
    return [
        {
            "id": str(p.id),
            "name": p.name,
            "price": p.price_inr,
            "coins": p.coins,
            "bonus": p.bonus_coins,
            "total_coins": p.coins + p.bonus_coins,
            "is_popular": p.is_popular
        }
        for p in packages
    ]


@router.post("/recharge/initiate")
async def initiate_recharge(
    request: RechargeInitiateRequest,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """
    Initiate coin recharge via Razorpay
    
    Flow:
    1. Validate package
    2. Create Razorpay order
    3. Return order details for client
    """
    
    package = db.query(CoinPackage).filter(
        CoinPackage.id == request.package_id,
        CoinPackage.is_active == True
    ).first()
    
    if not package:
        raise HTTPException(404, "Package not found")
    
    # Create Razorpay order
    order_data = {
        "amount": package.price_inr * 100,  # In paise
        "currency": "INR",
        "receipt": f"rcpt_{user_id}_{int(time.time())}",
        "notes": {
            "user_id": user_id,
            "package_id": str(package.id),
            "coins": package.coins + package.bonus_coins
        }
    }
    
    razorpay_order = razorpay_client.order.create(data=order_data)
    
    return {
        "order_id": razorpay_order["id"],
        "amount": package.price_inr,
        "currency": "INR",
        "coins": package.coins + package.bonus_coins,
        "razorpay_key": RAZORPAY_KEY_ID
    }


@router.post("/recharge/verify")
async def verify_recharge(
    request: RechargeVerifyRequest,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """
    Verify Razorpay payment and credit coins
    
    Flow:
    1. Verify payment signature
    2. Fetch order details
    3. Credit coins to wallet
    4. Create transaction record
    """
    
    # Verify signature
    try:
        razorpay_client.utility.verify_payment_signature({
            'razorpay_order_id': request.razorpay_order_id,
            'razorpay_payment_id': request.razorpay_payment_id,
            'razorpay_signature': request.razorpay_signature
        })
    except razorpay.errors.SignatureVerificationError:
        raise HTTPException(400, "Invalid payment signature")
    
    # Get order details
    order = razorpay_client.order.fetch(request.razorpay_order_id)
    coins_to_add = int(order["notes"]["coins"])
    amount_inr = order["amount"] / 100
    
    # Credit coins
    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == user_id).first()
    
    if not wallet:
        wallet = CoinWallet(user_id=user_id)
        db.add(wallet)
    
    wallet.balance += coins_to_add
    wallet.total_purchased += coins_to_add
    
    # Create transaction
    transaction = Transaction(
        user_id=user_id,
        type="recharge",
        amount_inr=amount_inr,
        coins=coins_to_add,
        payment_gateway="razorpay",
        payment_id=request.razorpay_payment_id,
        status="completed"
    )
    db.add(transaction)
    db.commit()
    
    return {
        "success": True,
        "coins_added": coins_to_add,
        "new_balance": wallet.balance,
        "transaction_id": str(transaction.id)
    }


@router.get("/transactions")
async def get_transactions(
    type: str = None,
    limit: int = 20,
    offset: int = 0,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """Get user's transaction history"""
    
    query = db.query(Transaction).filter(Transaction.user_id == user_id)
    
    if type:
        query = query.filter(Transaction.type == type)
    
    transactions = query.order_by(
        Transaction.created_at.desc()
    ).offset(offset).limit(limit).all()
    
    return [
        {
            "id": str(t.id),
            "type": t.type,
            "amount_inr": float(t.amount_inr) if t.amount_inr else None,
            "coins": t.coins,
            "diamonds": float(t.diamonds) if t.diamonds else None,
            "status": t.status,
            "created_at": t.created_at.isoformat()
        }
        for t in transactions
    ]


# ============ CREATOR WITHDRAWAL ============

@router.post("/withdraw")
async def request_withdrawal(
    request: WithdrawRequest,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """
    Request withdrawal (FRND-style with TDS)
    
    Flow:
    1. Validate minimum withdrawal
    2. Check KYC status
    3. Calculate TDS
    4. Create withdrawal request
    5. Queue for processing
    """
    
    # Constants
    MIN_WITHDRAWAL = Decimal("500.00")
    TDS_THRESHOLD = Decimal("30000.00")
    TDS_RATE = Decimal("0.10")
    
    # Validate amount
    if request.amount < MIN_WITHDRAWAL:
        raise HTTPException(400, f"Minimum withdrawal is ₹{MIN_WITHDRAWAL}")
    
    # Get diamond wallet
    wallet = db.query(DiamondWallet).filter(
        DiamondWallet.user_id == user_id
    ).first()
    
    if not wallet or wallet.available_balance < request.amount:
        raise HTTPException(400, "Insufficient available balance")
    
    # Check KYC
    kyc = db.query(CreatorKYC).filter(
        CreatorKYC.user_id == user_id,
        CreatorKYC.kyc_status == "verified"
    ).first()
    
    if not kyc:
        raise HTTPException(400, "Complete KYC verification first")
    
    # Calculate TDS
    # Get financial year earnings
    fy_start = get_fy_start_date()
    yearly_earnings = db.query(func.sum(Transaction.diamonds)).filter(
        Transaction.user_id == user_id,
        Transaction.type.in_(['earn_call', 'earn_gift', 'earn_room']),
        Transaction.created_at >= fy_start
    ).scalar() or Decimal("0")
    
    if yearly_earnings > TDS_THRESHOLD:
        tds_amount = request.amount * TDS_RATE
    else:
        tds_amount = Decimal("0")
    
    net_amount = request.amount - tds_amount
    
    # Create withdrawal
    withdrawal = Withdrawal(
        user_id=user_id,
        amount=request.amount,
        tds_amount=tds_amount,
        net_amount=net_amount,
        payout_method=request.payout_method,
        bank_account_id=kyc.id,
        status="pending"
    )
    db.add(withdrawal)
    
    # Deduct from wallet
    wallet.available_balance -= request.amount
    
    db.commit()
    
    # Queue for async processing
    # process_payout.delay(str(withdrawal.id))
    
    return {
        "withdrawal_id": str(withdrawal.id),
        "amount": float(request.amount),
        "tds_deducted": float(tds_amount),
        "net_amount": float(net_amount),
        "status": "pending",
        "message": "Withdrawal will be processed within 24-48 hours"
    }
```

### 8.3 Calls APIs

```python
# calls_routes.py

from fastapi import APIRouter, HTTPException, Depends, WebSocket
from pydantic import BaseModel
from agora_token_builder import RtcTokenBuilder, Role_Publisher
import time

router = APIRouter(prefix="/api/v1/calls", tags=["Calls"])


class StartCallRequest(BaseModel):
    receiver_id: str
    call_type: str  # 'audio' or 'video'

class EndCallRequest(BaseModel):
    call_id: str
    end_reason: str = "normal"  # 'normal', 'no_answer', 'declined', 'error'


def get_call_rate(user, call_type: str) -> int:
    """Get dynamic call rate based on user type and time"""
    
    # Get base rate
    rates = {
        ("regular", "audio"): 5,
        ("regular", "video"): 8,
        ("popular", "audio"): 8,
        ("popular", "video"): 12,
        ("verified", "audio"): 12,
        ("verified", "video"): 18,
        ("celebrity", "audio"): 20,
        ("celebrity", "video"): 30,
    }
    
    user_type = "regular"
    if user.is_verified:
        user_type = "verified"
    # Add more logic for popular/celebrity
    
    base_rate = rates.get((user_type, call_type), 5)
    
    # Peak hour multiplier (10 PM - 2 AM)
    current_hour = datetime.now().hour
    if current_hour >= 22 or current_hour < 2:
        base_rate = int(base_rate * 1.5)
    
    return base_rate


def generate_agora_token(channel_name: str, uid: int) -> str:
    """Generate Agora RTC token"""
    
    expiration_time_in_seconds = 3600 * 24  # 24 hours
    current_timestamp = int(time.time())
    privilege_expired_ts = current_timestamp + expiration_time_in_seconds
    
    token = RtcTokenBuilder.buildTokenWithUid(
        AGORA_APP_ID,
        AGORA_APP_CERTIFICATE,
        channel_name,
        uid,
        Role_Publisher,
        privilege_expired_ts
    )
    
    return token


@router.get("/rates")
async def get_call_rates(db: Session = Depends(get_db)):
    """Get current call rates"""
    
    rates = db.query(CallRate).filter(CallRate.is_active == True).all()
    
    # Check if peak hour
    current_hour = datetime.now().hour
    is_peak = current_hour >= 22 or current_hour < 2
    
    return {
        "is_peak_hour": is_peak,
        "rates": [
            {
                "user_type": r.user_type,
                "call_type": r.call_type,
                "base_rate": r.base_rate,
                "current_rate": int(r.base_rate * r.peak_multiplier) if is_peak else r.base_rate
            }
            for r in rates
        ]
    }


@router.post("/start")
async def start_call(
    request: StartCallRequest,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """
    Initiate a paid call
    
    Flow:
    1. Validate receiver exists
    2. Check caller balance
    3. Get call rate
    4. Create call record
    5. Generate Agora tokens
    6. Send push notification to receiver
    """
    
    # Validate receiver
    receiver = db.query(User).filter(
        User.id == request.receiver_id,
        User.is_active == True
    ).first()
    
    if not receiver:
        raise HTTPException(404, "User not found")
    
    # Check if blocked
    is_blocked = db.query(UserBlock).filter(
        ((UserBlock.blocker_id == user_id) & (UserBlock.blocked_id == request.receiver_id)) |
        ((UserBlock.blocker_id == request.receiver_id) & (UserBlock.blocked_id == user_id))
    ).first()
    
    if is_blocked:
        raise HTTPException(400, "Cannot call this user")
    
    # Get call rate
    rate_per_minute = get_call_rate(receiver, request.call_type)
    
    # Check caller balance (minimum 3 minutes)
    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == user_id).first()
    min_required = rate_per_minute * 3
    
    if not wallet or wallet.balance < min_required:
        raise HTTPException(
            400, 
            f"Minimum {min_required} coins required. Please recharge."
        )
    
    # Create call record
    channel_name = f"call_{user_id}_{request.receiver_id}_{int(time.time())}"
    
    call = Call(
        caller_id=user_id,
        receiver_id=request.receiver_id,
        call_type=request.call_type,
        rate_per_minute=rate_per_minute,
        status="initiated",
        agora_channel=channel_name
    )
    db.add(call)
    db.commit()
    
    # Generate Agora tokens
    caller_token = generate_agora_token(channel_name, 1)  # UID 1 for caller
    receiver_token = generate_agora_token(channel_name, 2)  # UID 2 for receiver
    
    # Send push notification to receiver
    await send_push_notification(
        user_id=request.receiver_id,
        title="Incoming Call",
        body=f"{get_display_name(user_id)} is calling you",
        data={
            "type": "incoming_call",
            "call_id": str(call.id),
            "channel": channel_name,
            "token": receiver_token,
            "call_type": request.call_type,
            "caller_id": user_id
        }
    )
    
    return {
        "call_id": str(call.id),
        "channel": channel_name,
        "agora_token": caller_token,
        "agora_app_id": AGORA_APP_ID,
        "rate_per_minute": rate_per_minute,
        "your_balance": wallet.balance,
        "estimated_minutes": wallet.balance // rate_per_minute
    }


@router.post("/answer/{call_id}")
async def answer_call(
    call_id: str,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """Receiver answers the call"""
    
    call = db.query(Call).filter(
        Call.id == call_id,
        Call.receiver_id == user_id,
        Call.status == "initiated"
    ).first()
    
    if not call:
        raise HTTPException(404, "Call not found or already answered")
    
    call.status = "ongoing"
    call.started_at = datetime.now()
    db.commit()
    
    # Generate token for receiver
    receiver_token = generate_agora_token(call.agora_channel, 2)
    
    return {
        "call_id": str(call.id),
        "channel": call.agora_channel,
        "agora_token": receiver_token,
        "agora_app_id": AGORA_APP_ID
    }


@router.post("/end")
async def end_call(
    request: EndCallRequest,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """
    End call and process billing
    
    Flow:
    1. Calculate duration
    2. Calculate coins spent
    3. Deduct from caller wallet
    4. Credit diamonds to receiver
    5. Create transactions
    """
    
    call = db.query(Call).filter(
        Call.id == request.call_id,
        (Call.caller_id == user_id) | (Call.receiver_id == user_id)
    ).first()
    
    if not call:
        raise HTTPException(404, "Call not found")
    
    if call.status == "completed":
        return {"message": "Call already ended"}
    
    # Calculate duration
    call.ended_at = datetime.now()
    call.end_reason = request.end_reason
    
    if call.started_at:
        call.duration_seconds = int(
            (call.ended_at - call.started_at).total_seconds()
        )
    else:
        call.duration_seconds = 0
    
    # Calculate billing (only if call was answered)
    if call.duration_seconds > 0:
        # Round up to nearest minute
        minutes = -(-call.duration_seconds // 60)
        coins_spent = int(minutes * call.rate_per_minute)
        
        # Deduct from caller
        caller_wallet = db.query(CoinWallet).filter(
            CoinWallet.user_id == call.caller_id
        ).first()
        
        caller_wallet.balance -= coins_spent
        caller_wallet.total_spent += coins_spent
        
        # Credit to receiver (45% after platform cut)
        CREATOR_SHARE = Decimal("0.45")
        COIN_TO_INR = Decimal("1.0")  # 1 coin = ₹1
        
        diamonds_earned = Decimal(str(coins_spent)) * COIN_TO_INR * CREATOR_SHARE
        
        # Get or create receiver diamond wallet
        receiver_wallet = db.query(DiamondWallet).filter(
            DiamondWallet.user_id == call.receiver_id
        ).first()
        
        if not receiver_wallet:
            receiver_wallet = DiamondWallet(user_id=call.receiver_id)
            db.add(receiver_wallet)
        
        receiver_wallet.pending_balance += diamonds_earned
        receiver_wallet.total_earned += diamonds_earned
        
        call.coins_spent = coins_spent
        call.diamonds_earned = diamonds_earned
        
        # Create transactions
        db.add(Transaction(
            user_id=call.caller_id,
            type="spend_call",
            coins=coins_spent,
            reference_type="call",
            reference_id=call.id
        ))
        
        db.add(Transaction(
            user_id=call.receiver_id,
            type="earn_call",
            diamonds=diamonds_earned,
            reference_type="call",
            reference_id=call.id
        ))
    
    call.status = "completed"
    db.commit()
    
    return {
        "call_id": str(call.id),
        "duration_seconds": call.duration_seconds,
        "duration_display": f"{call.duration_seconds // 60}m {call.duration_seconds % 60}s",
        "coins_spent": call.coins_spent,
        "diamonds_earned": float(call.diamonds_earned) if call.diamonds_earned else 0
    }


@router.get("/history")
async def get_call_history(
    limit: int = 20,
    offset: int = 0,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """Get user's call history"""
    
    calls = db.query(Call).filter(
        (Call.caller_id == user_id) | (Call.receiver_id == user_id)
    ).order_by(Call.created_at.desc()).offset(offset).limit(limit).all()
    
    return [
        {
            "id": str(c.id),
            "type": "outgoing" if c.caller_id == user_id else "incoming",
            "call_type": c.call_type,
            "other_user": get_user_summary(
                c.receiver_id if c.caller_id == user_id else c.caller_id
            ),
            "duration_seconds": c.duration_seconds,
            "coins_spent": c.coins_spent if c.caller_id == user_id else None,
            "diamonds_earned": float(c.diamonds_earned) if c.receiver_id == user_id else None,
            "status": c.status,
            "created_at": c.created_at.isoformat()
        }
        for c in calls
    ]
```

---

## 9. Coin Economy Design

### 9.1 Coin Packages

| Package | Price (₹) | Base Coins | Bonus | Total | Per Coin | Discount |
|---------|-----------|------------|-------|-------|----------|----------|
| Starter | ₹49 | 45 | +5 | 50 | ₹0.98 | 2% |
| Popular ⭐ | ₹99 | 95 | +15 | 110 | ₹0.90 | 10% |
| Value | ₹299 | 280 | +70 | 350 | ₹0.85 | 15% |
| **Best Seller** 🔥 | ₹499 | 460 | +140 | 600 | ₹0.83 | 17% |
| Premium | ₹999 | 900 | +400 | 1300 | ₹0.77 | 23% |
| VIP | ₹1999 | 1750 | +1050 | 2800 | ₹0.71 | 29% |

### 9.2 Call Pricing Matrix

#### Base Rates (coins/minute):

| User Type | Audio | Video |
|-----------|-------|-------|
| Regular | 5 | 8 |
| Popular (100+ followers) | 8 | 12 |
| Verified (KYC done) | 12 | 18 |
| Celebrity (1000+ followers) | 20 | 30 |

#### Peak Hour Pricing (10 PM - 2 AM IST):

All rates × 1.5

| User Type | Audio | Video |
|-----------|-------|-------|
| Regular | 8 | 12 |
| Popular | 12 | 18 |
| Verified | 18 | 27 |
| Celebrity | 30 | 45 |

### 9.3 Gift Pricing

| Gift | Coins | Creator Earns (₹) | Category |
|------|-------|-------------------|----------|
| 🌹 Rose | 10 | ₹4.50 | Basic |
| ❤️ Heart | 20 | ₹9.00 | Basic |
| ☕ Coffee | 50 | ₹22.50 | Basic |
| 🧸 Teddy Bear | 100 | ₹45.00 | Premium |
| 💐 Bouquet | 150 | ₹67.50 | Premium |
| 💎 Diamond | 200 | ₹90.00 | Premium |
| 👑 Crown | 500 | ₹225.00 | Luxury |
| 🏎️ Sports Car | 1000 | ₹450.00 | Luxury |
| ✈️ Private Jet | 5000 | ₹2,250.00 | Exclusive |
| 🏰 Castle | 10000 | ₹4,500.00 | Exclusive |

### 9.4 Conversion Formula

```
User Spends X Coins
    │
    ▼
Platform Revenue = X × ₹1 × 55% = ₹0.55X
    │
    ▼
Creator Diamonds = X × ₹1 × 45% = ₹0.45X

Example: User sends 100-coin gift
- Platform keeps: ₹55
- Creator earns: ₹45 (as diamonds)
```

### 9.5 Coin Expiry Rules

1. **Purchased coins:** Expire after 365 days of account inactivity
2. **Bonus coins:** Expire after 90 days
3. **Promotional coins:** Expire after 30 days
4. **Refunds:** Only within 7 days for technical issues, unused coins only

---

## 10. Creator Payout System

### 10.1 Payout Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                      PAYOUT FLOW                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  User Spends Coins                                               │
│        │                                                         │
│        ▼                                                         │
│  ┌─────────────┐                                                │
│  │ Diamonds    │──── Credited instantly to creator wallet       │
│  │ Earned      │                                                │
│  └──────┬──────┘                                                │
│         │                                                        │
│         ▼                                                        │
│  ┌─────────────┐                                                │
│  │ Pending     │──── 7-day holding period                       │
│  │ Balance     │                                                │
│  └──────┬──────┘                                                │
│         │                                                        │
│         ▼ (After 7 days)                                        │
│  ┌─────────────┐                                                │
│  │ Available   │──── Can be withdrawn                           │
│  │ Balance     │                                                │
│  └──────┬──────┘                                                │
│         │                                                        │
│         ▼ (Withdrawal Request)                                  │
│  ┌─────────────┐     ┌─────────────┐                           │
│  │ Calculate   │────►│ Deduct TDS  │                           │
│  │ Amount      │     │ (10% if >₹30K/yr)│                      │
│  └─────────────┘     └──────┬──────┘                           │
│                             │                                    │
│                             ▼                                    │
│                      ┌─────────────┐                            │
│                      │ Process via │──── IMPS/UPI Transfer      │
│                      │ Razorpay X  │                            │
│                      └──────┬──────┘                            │
│                             │                                    │
│                             ▼                                    │
│                      ┌─────────────┐                            │
│                      │ Bank Credit │──── T+1 (Next day)         │
│                      └─────────────┘                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 10.2 TDS Calculation

```python
def calculate_tds(user_id: str, withdrawal_amount: Decimal, db: Session) -> Decimal:
    """
    Calculate TDS as per Indian Income Tax Act
    
    Section 194J: TDS @ 10% for professional services
    Threshold: ₹30,000 per financial year
    """
    
    # Get financial year start
    today = date.today()
    if today.month >= 4:
        fy_start = date(today.year, 4, 1)
    else:
        fy_start = date(today.year - 1, 4, 1)
    
    # Calculate total earnings this FY
    total_earnings = db.query(func.sum(Transaction.diamonds)).filter(
        Transaction.user_id == user_id,
        Transaction.type.in_(['earn_call', 'earn_gift', 'earn_room']),
        Transaction.created_at >= fy_start
    ).scalar() or Decimal("0")
    
    # Already withdrawn this FY
    already_withdrawn = db.query(func.sum(Withdrawal.amount)).filter(
        Withdrawal.user_id == user_id,
        Withdrawal.status == "completed",
        Withdrawal.created_at >= fy_start
    ).scalar() or Decimal("0")
    
    # Total after this withdrawal
    total_with_current = already_withdrawn + withdrawal_amount
    
    TDS_THRESHOLD = Decimal("30000")
    TDS_RATE = Decimal("0.10")
    
    if total_with_current <= TDS_THRESHOLD:
        # Below threshold, no TDS
        return Decimal("0")
    elif already_withdrawn >= TDS_THRESHOLD:
        # Already crossed threshold, full TDS
        return withdrawal_amount * TDS_RATE
    else:
        # Partially crosses threshold
        taxable_amount = total_with_current - TDS_THRESHOLD
        return taxable_amount * TDS_RATE
```

### 10.3 KYC Requirements

| Field | Required | Verification Method |
|-------|----------|---------------------|
| PAN Number | ✅ Yes | NSDL/UTI API verification |
| PAN Name | ✅ Yes | Must match bank account |
| Bank Account | ✅ Yes | Penny drop verification |
| IFSC Code | ✅ Yes | RBI IFSC API |
| UPI ID (optional) | ❌ No | Handle verification |
| Aadhaar (optional) | ❌ No | DigiLocker / UIDAI |

### 10.4 Payout Limits

| Parameter | Value |
|-----------|-------|
| Minimum withdrawal | ₹500 |
| Maximum per day | ₹50,000 |
| Maximum per month | ₹5,00,000 |
| Processing time | T+1 (within 24 hours) |
| Payout method | IMPS (instant) / NEFT (same day) |
| Holding period | 7 days (fraud prevention) |

---

## 11. N8N Automation Workflows

### 11.1 Workflow: Creator Onboarding

```json
{
  "name": "Creator Onboarding Automation",
  "nodes": [
    {
      "name": "Webhook Trigger",
      "type": "n8n-nodes-base.webhook",
      "parameters": {
        "path": "creator-application",
        "method": "POST"
      }
    },
    {
      "name": "Validate Age",
      "type": "n8n-nodes-base.if",
      "parameters": {
        "conditions": {
          "number": [
            {
              "value1": "={{ $json.age }}",
              "operation": "largerEqual",
              "value2": 18
            }
          ]
        }
      }
    },
    {
      "name": "Verify PAN via Karza",
      "type": "n8n-nodes-base.httpRequest",
      "parameters": {
        "url": "https://api.karza.in/v3/pan-verification",
        "method": "POST",
        "body": {
          "pan": "={{ $json.pan_number }}",
          "name": "={{ $json.name }}"
        }
      }
    },
    {
      "name": "PAN Valid Check",
      "type": "n8n-nodes-base.if",
      "parameters": {
        "conditions": {
          "boolean": [
            {
              "value1": "={{ $json.valid }}",
              "value2": true
            }
          ]
        }
      }
    },
    {
      "name": "Update Database - Approved",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "update",
        "table": "creator_kyc",
        "updateKey": "user_id",
        "columns": "kyc_status, pan_verified, verified_at",
        "values": "verified, true, NOW()"
      }
    },
    {
      "name": "Send Approval SMS",
      "type": "n8n-nodes-base.httpRequest",
      "parameters": {
        "url": "https://api.msg91.com/api/v5/flow/",
        "method": "POST",
        "body": {
          "template_id": "creator_approved",
          "mobile": "={{ $json.phone }}"
        }
      }
    },
    {
      "name": "Send Rejection SMS",
      "type": "n8n-nodes-base.httpRequest",
      "parameters": {
        "url": "https://api.msg91.com/api/v5/flow/",
        "method": "POST",
        "body": {
          "template_id": "creator_rejected",
          "mobile": "={{ $json.phone }}",
          "reason": "={{ $json.rejection_reason }}"
        }
      }
    }
  ]
}
```

### 11.2 Workflow: Daily Payout Processing

```json
{
  "name": "Daily Payout Processing",
  "nodes": [
    {
      "name": "Schedule Trigger",
      "type": "n8n-nodes-base.scheduleTrigger",
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "hours",
              "hoursInterval": 24
            }
          ]
        },
        "triggerAtHour": 6
      }
    },
    {
      "name": "Get Pending Withdrawals",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "executeQuery",
        "query": "SELECT w.*, k.bank_account_number, k.bank_ifsc, k.pan_number FROM withdrawals w JOIN creator_kyc k ON w.user_id = k.user_id WHERE w.status = 'pending' AND w.created_at < NOW() - INTERVAL '1 hour'"
      }
    },
    {
      "name": "Loop Each Withdrawal",
      "type": "n8n-nodes-base.splitInBatches",
      "parameters": {
        "batchSize": 1
      }
    },
    {
      "name": "Create Razorpay Payout",
      "type": "n8n-nodes-base.httpRequest",
      "parameters": {
        "url": "https://api.razorpay.com/v1/payouts",
        "method": "POST",
        "authentication": "basicAuth",
        "body": {
          "account_number": "{{ $env.RAZORPAY_ACCOUNT }}",
          "fund_account": {
            "account_type": "bank_account",
            "bank_account": {
              "name": "={{ $json.pan_name }}",
              "ifsc": "={{ $json.bank_ifsc }}",
              "account_number": "={{ $json.bank_account_number }}"
            }
          },
          "amount": "={{ Math.round($json.net_amount * 100) }}",
          "currency": "INR",
          "mode": "IMPS",
          "purpose": "payout",
          "reference_id": "={{ $json.id }}"
        }
      }
    },
    {
      "name": "Update Withdrawal Status",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "update",
        "table": "withdrawals",
        "updateKey": "id",
        "columns": "status, payout_reference, processed_at",
        "values": "'processing', '={{ $json.id }}', NOW()"
      }
    },
    {
      "name": "Send Payout SMS",
      "type": "n8n-nodes-base.httpRequest",
      "parameters": {
        "url": "https://api.msg91.com/api/v5/flow/",
        "method": "POST",
        "body": {
          "template_id": "payout_initiated",
          "mobile": "={{ $json.phone }}",
          "amount": "={{ $json.net_amount }}"
        }
      }
    }
  ]
}
```

### 11.3 Workflow: Fraud Detection

```json
{
  "name": "Real-time Fraud Detection",
  "nodes": [
    {
      "name": "Webhook - Transaction",
      "type": "n8n-nodes-base.webhook",
      "parameters": {
        "path": "fraud-check",
        "method": "POST"
      }
    },
    {
      "name": "Check Gift Loop",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "executeQuery",
        "query": "SELECT COUNT(*) as loop_count FROM gift_transactions WHERE sender_id = '{{ $json.receiver_id }}' AND receiver_id = '{{ $json.sender_id }}' AND created_at > NOW() - INTERVAL '1 hour'"
      }
    },
    {
      "name": "Loop Detected?",
      "type": "n8n-nodes-base.if",
      "parameters": {
        "conditions": {
          "number": [
            {
              "value1": "={{ $json.loop_count }}",
              "operation": "larger",
              "value2": 5
            }
          ]
        }
      }
    },
    {
      "name": "Check Unusual Spending",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "executeQuery",
        "query": "SELECT AVG(amount) as avg_spend, STDDEV(amount) as std_spend FROM transactions WHERE user_id = '{{ $json.user_id }}' AND type LIKE 'spend%' AND created_at > NOW() - INTERVAL '30 days'"
      }
    },
    {
      "name": "Spending Anomaly?",
      "type": "n8n-nodes-base.if",
      "parameters": {
        "conditions": {
          "number": [
            {
              "value1": "={{ $json.amount }}",
              "operation": "larger",
              "value2": "={{ $json.avg_spend + (3 * $json.std_spend) }}"
            }
          ]
        }
      }
    },
    {
      "name": "Flag Account",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "insert",
        "table": "fraud_flags",
        "columns": "user_id, flag_type, details, created_at",
        "values": "'{{ $json.user_id }}', 'suspicious_activity', '{{ JSON.stringify($json) }}', NOW()"
      }
    },
    {
      "name": "Hold Payouts",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "update",
        "table": "diamond_wallets",
        "updateKey": "user_id",
        "columns": "payout_hold",
        "values": "true"
      }
    },
    {
      "name": "Alert Admin",
      "type": "n8n-nodes-base.slack",
      "parameters": {
        "channel": "#fraud-alerts",
        "text": "🚨 Suspicious activity detected!\n\nUser: {{ $json.user_id }}\nType: {{ $json.flag_type }}\nDetails: {{ JSON.stringify($json.details) }}"
      }
    }
  ]
}
```

### 11.4 Workflow: Move Pending to Available Balance

```json
{
  "name": "Daily Balance Settlement",
  "nodes": [
    {
      "name": "Schedule - Midnight",
      "type": "n8n-nodes-base.scheduleTrigger",
      "parameters": {
        "rule": {
          "interval": [{"field": "hours", "hoursInterval": 24}]
        },
        "triggerAtHour": 0
      }
    },
    {
      "name": "Move Pending to Available",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "executeQuery",
        "query": "WITH settled AS ( SELECT user_id, SUM(diamonds) as amount FROM transactions WHERE type IN ('earn_call', 'earn_gift', 'earn_room') AND created_at < NOW() - INTERVAL '7 days' AND settled = false GROUP BY user_id ) UPDATE diamond_wallets dw SET available_balance = available_balance + s.amount, pending_balance = pending_balance - s.amount FROM settled s WHERE dw.user_id = s.user_id; UPDATE transactions SET settled = true WHERE type IN ('earn_call', 'earn_gift', 'earn_room') AND created_at < NOW() - INTERVAL '7 days' AND settled = false;"
      }
    },
    {
      "name": "Log Settlement",
      "type": "n8n-nodes-base.postgres",
      "parameters": {
        "operation": "insert",
        "table": "settlement_logs",
        "columns": "settled_at, records_count",
        "values": "NOW(), {{ $json.affectedRows }}"
      }
    }
  ]
}
```

---

## 12. Legal & Compliance

### 12.1 Required Registrations

| Registration | Authority | Timeline | Cost |
|--------------|-----------|----------|------|
| Company (Pvt Ltd) | MCA | 7-10 days | ₹10,000-15,000 |
| GST Registration | GST Portal | 3-5 days | ₹2,000-3,000 |
| TAN (for TDS) | NSDL | 7 days | ₹65 |
| Trademark | IP India | 6-12 months | ₹5,000-10,000 |
| Payment Aggregator | RBI (via Razorpay) | Handled by gateway | Included |

### 12.2 Legal Documents Required

#### 12.2.1 Terms of Service (Key Clauses)

```markdown
TERMS OF SERVICE - LIVVLY

1. ELIGIBILITY
- You must be at least 18 years old
- One account per person
- Valid Indian phone number required
- Accurate profile information required

2. ACCOUNT RULES
- You are responsible for account security
- Do not share login credentials
- Report unauthorized access immediately

3. VIRTUAL CURRENCY (COINS)
- Coins are purchased with real money via authorized payment methods
- Coins have no real-world value outside the platform
- Coins are non-transferable between accounts
- Coins are non-refundable except as required by law
- Unused coins expire after 365 days of account inactivity
- Bonus/promotional coins expire after 30-90 days

4. CREATOR EARNINGS
- Creators earn diamonds from user interactions
- Diamond value is determined by the platform
- Payouts require KYC verification
- TDS deducted as per Income Tax Act
- Minimum withdrawal: ₹500
- Platform commission: 55% of coin value

5. PROHIBITED CONDUCT
- Harassment, abuse, bullying
- Explicit or pornographic content
- Hate speech or discrimination
- Fraud, fake accounts, bots
- Sharing personal contact information
- Commercial solicitation
- Impersonation
- Illegal activities

6. CONTENT GUIDELINES
- You retain rights to your content
- Platform has license to display content
- No recording without consent
- Respect intellectual property

7. PRIVACY
- Data collected as per Privacy Policy
- Location used for matching
- Chat/call content not stored permanently

8. TERMINATION
- We may suspend or terminate accounts for violations
- You may delete your account anytime
- Pending earnings paid within 30 days of termination

9. LIMITATION OF LIABILITY
- Service provided "as is"
- No guarantee of matches or connections
- Not responsible for user interactions
- Maximum liability limited to fees paid

10. DISPUTE RESOLUTION
- Governed by laws of India
- Arbitration in Coimbatore, Tamil Nadu
- 30-day informal resolution period first

11. CHANGES
- We may update these terms
- Continued use means acceptance
- Material changes notified via app/email
```

#### 12.2.2 Privacy Policy (Key Sections)

```markdown
PRIVACY POLICY - LIVVLY

1. INFORMATION WE COLLECT
- Phone number (for authentication)
- Profile information (name, age, gender, photo)
- Location (city, state)
- Device information
- Usage data (calls, chats, transactions)

2. HOW WE USE INFORMATION
- Provide and improve services
- Match users based on preferences
- Process payments
- Prevent fraud
- Send notifications

3. INFORMATION SHARING
- With other users (profile only)
- Payment processors (for transactions)
- Legal requirements
- Business transfers

4. DATA RETENTION
- Active accounts: data retained
- Deleted accounts: 90-day retention for legal compliance
- Transaction records: 7 years (legal requirement)

5. YOUR RIGHTS
- Access your data
- Correct inaccurate data
- Delete your account
- Withdraw consent
- Data portability

6. SECURITY
- Encrypted transmissions (SSL/TLS)
- Secure server infrastructure
- Regular security audits
- Access controls

7. COOKIES & TRACKING
- Analytics (Firebase, Mixpanel)
- Crash reporting
- Performance monitoring

8. CHILDREN'S PRIVACY
- Service not for users under 18
- We do not knowingly collect data from minors

9. CONTACT
- Email: privacy@livvly.com
- Address: [Company Address]

10. UPDATES
- Policy may be updated
- Last updated: [Date]
```

#### 12.2.3 Creator Agreement

```markdown
CREATOR PARTNERSHIP AGREEMENT - LIVVLY

This Agreement is between:
- LIVVLY (Platform)
- Creator (You)

1. ENGAGEMENT
- You are an independent contractor
- Not an employee of LIVVLY
- No exclusive arrangement required
- You set your own schedule

2. CREATOR REQUIREMENTS
- Minimum age: 18 years
- Valid PAN card
- Active bank account
- Complete KYC verification
- Maintain 4.0+ rating

3. EARNINGS STRUCTURE
- Revenue share: 45% of coins spent on you
- Conversion: Coins → Diamonds → INR
- Rate: 1 Diamond = ₹1
- Pending period: 7 days
- Minimum withdrawal: ₹500

4. TAX OBLIGATIONS
- TDS deducted at source (10% above ₹30,000/year)
- Form 16A provided quarterly
- You are responsible for ITR filing
- GST registration if applicable

5. PAYOUT TERMS
- Payouts processed daily (T+1)
- IMPS/NEFT to verified bank account
- UPI option available
- Failed payouts retried next day

6. CONDUCT REQUIREMENTS
- No explicit/adult content
- No sharing personal contact info
- No solicitation outside platform
- Professional behavior expected
- Respect user privacy

7. CONTENT RIGHTS
- You retain ownership of your content
- Platform has license during live sessions
- No unauthorized recording
- Remove content upon termination

8. PERFORMANCE STANDARDS
- Minimum 2 hours active/week to maintain creator status
- Respond to calls/chats within reasonable time
- Maintain quality interactions

9. TERMINATION
- Either party: 7 days written notice
- Immediate termination for violations
- Pending earnings paid within 30 days
- No payout for fraudulent earnings

10. CONFIDENTIALITY
- User information is confidential
- Platform business terms confidential
- Survives termination

11. INDEMNIFICATION
- You indemnify Platform for your actions
- Platform indemnifies for platform failures

12. DISPUTE RESOLUTION
- Good faith negotiation first
- Arbitration in Coimbatore
- Indian law governs

ACCEPTED BY:
Creator Name: _______________
Creator ID: _______________
Date: _______________
```

### 12.3 TDS Compliance

```
TDS FILING CALENDAR:

Q1 (Apr-Jun): File by July 31
Q2 (Jul-Sep): File by October 31  
Q3 (Oct-Dec): File by January 31
Q4 (Jan-Mar): File by May 31

REQUIRED FORMS:
- Form 26Q: Quarterly TDS return
- Form 16A: TDS certificate to creators
- Challan 281: TDS deposit to government

TDS RATES:
- Section 194J: 10% (Professional services)
- Section 194C: 2% (Contractor payments to agencies)

THRESHOLD:
- ₹30,000 per financial year per creator
- Below threshold: No TDS required
```

---

## 13. Implementation Roadmap

### 13.1 Phase 1: MVP (Days 1-30)

#### Week 1: Foundation
| Day | Tasks | Owner |
|-----|-------|-------|
| 1-2 | Project setup (Flutter, FastAPI, PostgreSQL) | Dev |
| 3-4 | Database schema implementation | Backend |
| 5-6 | Firebase Auth integration | Full Stack |
| 7 | Basic user registration flow | Full Stack |

#### Week 2: Core Features
| Day | Tasks | Owner |
|-----|-------|-------|
| 8-9 | Profile creation & editing | Frontend |
| 10-11 | Coin wallet & Razorpay integration | Backend |
| 12-13 | Recharge flow (end-to-end) | Full Stack |
| 14 | Testing & bug fixes | QA |

#### Week 3: Communication
| Day | Tasks | Owner |
|-----|-------|-------|
| 15-16 | Agora SDK integration | Full Stack |
| 17-18 | 1-to-1 audio call flow | Full Stack |
| 19-20 | Call billing & deduction | Backend |
| 21 | Push notifications | Backend |

#### Week 4: Monetization
| Day | Tasks | Owner |
|-----|-------|-------|
| 22-23 | Gift system implementation | Full Stack |
| 24-25 | Creator dashboard (basic) | Frontend |
| 26-27 | Diamond wallet & earnings | Backend |
| 28-30 | Testing, bug fixes, polish | All |

**MVP Deliverables:**
- ✅ Android APK for internal testing
- ✅ Basic user registration & profiles
- ✅ Coin purchase & wallet
- ✅ 1-to-1 audio calls with billing
- ✅ Gift sending
- ✅ Creator earnings view

### 13.2 Phase 2: Beta Launch (Days 31-60)

#### Week 5-6: Polish & KYC
| Tasks | Owner |
|-------|-------|
| UI/UX improvements | Frontend |
| Performance optimization | Full Stack |
| KYC system (PAN verification) | Backend |
| Bank account verification | Backend |
| Withdrawal flow | Full Stack |

#### Week 7-8: Beta Testing
| Tasks | Owner |
|-------|-------|
| Soft launch (100 users) | Marketing |
| Feedback collection | Product |
| Bug fixes | Dev |
| Onboard 10 creators | Operations |
| n8n automation setup | DevOps |

**Beta Deliverables:**
- ✅ Play Store internal testing track
- ✅ KYC & payout system
- ✅ 100+ beta users
- ✅ 10+ active creators
- ✅ Automation workflows live

### 13.3 Phase 3: Public Launch (Days 61-90)

#### Week 9-10: Launch Prep
| Tasks | Owner |
|-------|-------|
| ASO optimization | Marketing |
| Play Store listing | Marketing |
| Creator recruitment campaign | Operations |
| Influencer partnerships | Marketing |
| Legal compliance check | Legal |

#### Week 11-12: Launch & Scale
| Tasks | Owner |
|-------|-------|
| Public launch on Play Store | All |
| Performance marketing (₹50K budget) | Marketing |
| Daily monitoring & optimization | All |
| Scale to 10,000 users | All |
| Add video calls | Dev |

**Launch Deliverables:**
- ✅ Public Play Store listing
- ✅ 10,000+ downloads
- ✅ 50+ active creators
- ✅ ₹5L+ monthly GMV
- ✅ Video calls live

### 13.4 Gantt Chart

```
WEEK  | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11| 12|
------+---+---+---+---+---+---+---+---+---+---+---+---+
Setup | ███                                            |
Auth  | ███                                            |
Profile|   ███                                         |
Wallet |   ███████                                     |
Calls  |       ███████                                 |
Gifts  |           ███                                 |
Creator|           ███████                             |
KYC    |               ███████                         |
Payout |                   ███████                     |
Beta   |                       ███████                 |
ASO    |                           ███████             |
Launch |                               ███████         |
Scale  |                                   ███████████ |
```

---

## 14. Cost Estimation

### 14.1 Development Costs (6 Months)

| Category | Monthly | 6 Months |
|----------|---------|----------|
| **Team** | | |
| Flutter Developer (1) | ₹60,000 | ₹3,60,000 |
| Backend Developer (1) | ₹50,000 | ₹3,00,000 |
| UI/UX Designer (part-time) | ₹20,000 | ₹1,20,000 |
| QA Tester (part-time) | ₹15,000 | ₹90,000 |
| **Subtotal Team** | ₹1,45,000 | ₹8,70,000 |

### 14.2 Infrastructure Costs

| Service | Monthly | 6 Months |
|---------|---------|----------|
| AWS/GCP (servers) | ₹15,000 | ₹90,000 |
| Agora (voice/video) | ₹20,000 | ₹1,20,000 |
| Firebase (auth, push, analytics) | ₹5,000 | ₹30,000 |
| Redis (caching) | ₹3,000 | ₹18,000 |
| CDN (media delivery) | ₹5,000 | ₹30,000 |
| **Subtotal Infra** | ₹48,000 | ₹2,88,000 |

### 14.3 Third-Party Services

| Service | Monthly | 6 Months |
|---------|---------|----------|
| SMS (OTP - MSG91) | ₹10,000 | ₹60,000 |
| Razorpay (2% of GMV) | Variable | Variable |
| Karza (KYC verification) | ₹5,000 | ₹30,000 |
| n8n (self-hosted) | ₹2,000 | ₹12,000 |
| **Subtotal Services** | ₹17,000 | ₹1,02,000 |

### 14.4 Legal & Compliance

| Item | One-time |
|------|----------|
| Company Registration (Pvt Ltd) | ₹15,000 |
| GST Registration | ₹3,000 |
| TAN Registration | ₹500 |
| Legal Documents (ToS, Privacy, Creator Agreement) | ₹25,000 |
| Trademark Application | ₹10,000 |
| **Subtotal Legal** | ₹53,500 |

### 14.5 Marketing (Month 3-6)

| Channel | Monthly | 4 Months |
|---------|---------|----------|
| Google Ads (UAC) | ₹30,000 | ₹1,20,000 |
| Facebook/Instagram Ads | ₹20,000 | ₹80,000 |
| Influencer Marketing | ₹30,000 | ₹1,20,000 |
| ASO Tools | ₹5,000 | ₹20,000 |
| **Subtotal Marketing** | ₹85,000 | ₹3,40,000 |

### 14.6 Total Budget

| Category | Amount |
|----------|--------|
| Team (6 months) | ₹8,70,000 |
| Infrastructure (6 months) | ₹2,88,000 |
| Services (6 months) | ₹1,02,000 |
| Legal (one-time) | ₹53,500 |
| Marketing (4 months) | ₹3,40,000 |
| Contingency (10%) | ₹1,55,350 |
| **TOTAL** | **₹17,08,850** |

### 14.7 Revenue Projections

| Month | Users | Paying (8%) | Avg Spend | GMV | Platform Share (38.5%) |
|-------|-------|-------------|-----------|-----|------------------------|
| 1 | 1,000 | 80 | ₹200 | ₹16,000 | ₹6,160 |
| 2 | 3,000 | 240 | ₹250 | ₹60,000 | ₹23,100 |
| 3 | 8,000 | 640 | ₹300 | ₹1,92,000 | ₹73,920 |
| 4 | 15,000 | 1,200 | ₹350 | ₹4,20,000 | ₹1,61,700 |
| 5 | 30,000 | 2,400 | ₹400 | ₹9,60,000 | ₹3,69,600 |
| 6 | 50,000 | 4,000 | ₹450 | ₹18,00,000 | ₹6,93,000 |

**Break-even:** Month 5-6

---

## 15. Success Metrics

### 15.1 Key Performance Indicators (KPIs)

#### User Metrics
| Metric | Month 1 | Month 3 | Month 6 | Month 12 |
|--------|---------|---------|---------|----------|
| Total Downloads | 2,000 | 15,000 | 75,000 | 3,00,000 |
| DAU (Daily Active Users) | 200 | 2,000 | 10,000 | 50,000 |
| MAU (Monthly Active Users) | 1,000 | 8,000 | 50,000 | 2,00,000 |
| DAU/MAU Ratio | 20% | 25% | 20% | 25% |

#### Engagement Metrics
| Metric | Target |
|--------|--------|
| Avg Session Duration | > 15 minutes |
| Sessions per User per Day | > 2 |
| Calls per Active User | > 1/day |
| Messages per Active User | > 10/day |
| Room Participation Rate | > 30% |

#### Revenue Metrics
| Metric | Month 1 | Month 3 | Month 6 | Month 12 |
|--------|---------|---------|---------|----------|
| GMV | ₹16K | ₹1.9L | ₹18L | ₹1Cr |
| Paying Users % | 5% | 8% | 8% | 10% |
| ARPU (All Users) | ₹16 | ₹24 | ₹36 | ₹50 |
| ARPPU (Paying Users) | ₹200 | ₹300 | ₹450 | ₹500 |

#### Creator Metrics
| Metric | Month 1 | Month 3 | Month 6 | Month 12 |
|--------|---------|---------|---------|----------|
| Total Creators | 20 | 100 | 500 | 2,000 |
| Active Creators (weekly) | 10 | 60 | 300 | 1,200 |
| Avg Creator Earning | ₹2K | ₹5K | ₹10K | ₹15K |
| Top Creator Earning | ₹10K | ₹30K | ₹1L | ₹3L |

### 15.2 North Star Metric

**Primary:** Monthly Active Minutes (total minutes of calls + room participation)

**Why:** Directly correlates with:
- User engagement
- Revenue (per-minute billing)
- Creator earnings
- Platform stickiness

### 15.3 Monitoring Dashboard

```
┌─────────────────────────────────────────────────────────────────┐
│                    LIVVLY DASHBOARD                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐│
│  │   DAU      │  │   GMV      │  │  Calls     │  │  Creators  ││
│  │   1,234    │  │  ₹45,678   │  │   567      │  │    89      ││
│  │   +12%     │  │   +23%     │  │   +15%     │  │   +5%      ││
│  └────────────┘  └────────────┘  └────────────┘  └────────────┘│
│                                                                  │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │                  REVENUE TREND (7 DAYS)                     ││
│  │  ₹                                                          ││
│  │  50K │                                          ████        ││
│  │  40K │                              ████████████            ││
│  │  30K │              ████████████████                        ││
│  │  20K │  ████████████                                        ││
│  │  10K │                                                      ││
│  │      └──────────────────────────────────────────────────    ││
│  │         Mon   Tue   Wed   Thu   Fri   Sat   Sun             ││
│  └─────────────────────────────────────────────────────────────┘│
│                                                                  │
│  ┌─────────────────────┐  ┌─────────────────────┐              │
│  │  TOP CREATORS       │  │  TOP SPENDERS       │              │
│  │                     │  │                     │              │
│  │  1. Creator_A ₹15K  │  │  1. User_X    ₹5K   │              │
│  │  2. Creator_B ₹12K  │  │  2. User_Y    ₹4K   │              │
│  │  3. Creator_C ₹10K  │  │  3. User_Z    ₹3K   │              │
│  └─────────────────────┘  └─────────────────────┘              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Appendix

### A. Glossary

| Term | Definition |
|------|------------|
| Coins | Virtual currency purchased by users |
| Diamonds | Virtual currency earned by creators |
| GMV | Gross Merchandise Value (total transactions) |
| ARPU | Average Revenue Per User |
| ARPPU | Average Revenue Per Paying User |
| DAU | Daily Active Users |
| MAU | Monthly Active Users |
| TDS | Tax Deducted at Source |
| KYC | Know Your Customer |
| PAN | Permanent Account Number |

### B. API Error Codes

| Code | Message | Action |
|------|---------|--------|
| 400 | Bad Request | Check input parameters |
| 401 | Unauthorized | Re-authenticate |
| 403 | Forbidden | Check permissions |
| 404 | Not Found | Resource doesn't exist |
| 429 | Too Many Requests | Rate limited, retry later |
| 500 | Internal Server Error | Contact support |

### C. Support Contacts

| Issue | Contact |
|-------|---------|
| Technical Support | tech@livvly.com |
| Creator Support | creators@livvly.com |
| Payment Issues | payments@livvly.com |
| Legal/Compliance | legal@livvly.com |
| General Inquiries | hello@livvly.com |

---

**Document Version History**

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | Jan 2026 | Initial PRD | Shadow Market |

---

*This document is confidential and intended for internal use only.*

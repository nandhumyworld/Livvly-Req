# Requirements: Feature Specifications

**Section**: 05-feature-specifications
**Source Lines**: 228-538
**Generated**: 2026-01-19
**Status**: Complete

---

## App Navigation Requirements

### REQ-FS-001: Bottom Navigation Structure
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: The app must implement a bottom navigation bar with 5 primary sections: Home, Discover, Rooms (center/prominent), Chat, and Profile.

**Rationale**: Standard mobile app pattern for voice social apps; rooms in center emphasizes core feature.

**Acceptance Criteria**:
- Bottom nav visible on all primary screens
- Active state clearly indicated
- Rooms tab is visually prominent (larger, centered)
- Tab switches are instant (< 100ms)
- Badge indicators for unread messages/notifications

**Dependencies**: None
**Related Design Decisions**: DD-FS-001

**Notes**: Consider hiding nav during calls and immersive experiences.

---

### REQ-FS-002: Secondary Navigation Access
**Priority**: P1 (High)
**Type**: Functional

**Description**: Secondary features (Calls, Wallet, Creator Dashboard) must be accessible from their parent sections without cluttering primary navigation.

**Rationale**: Balances feature discoverability with clean UI.

**Acceptance Criteria**:
- Wallet accessible from Profile and Home header
- Calls history accessible from Chat section
- Creator Dashboard accessible from Profile (for verified creators)
- Consistent back navigation patterns

**Dependencies**: REQ-FS-001
**Related Design Decisions**: DD-FS-001

**Notes**: Use progressive disclosure for creator-only features.

---

## Onboarding Requirements

### REQ-FS-003: Phone OTP Authentication
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users must authenticate using phone number with OTP verification via Firebase Auth or MSG91.

**Rationale**: Phone auth is standard in India; higher penetration than email.

**Acceptance Criteria**:
- Phone input with +91 default, 10-digit validation
- OTP sent within 30 seconds
- 6-digit OTP with auto-read from SMS
- Retry option after 60 seconds
- Maximum 3 OTP requests per phone per hour
- Session persists across app restarts

**Dependencies**: REQ-PV-011
**Related Design Decisions**: DD-FS-002

**Notes**: Consider WhatsApp OTP as fallback.

---

### REQ-FS-004: Profile Setup Flow
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: New users must complete profile setup with required fields: Display Name, Gender, DOB, Profile Photo, and optional Bio and Location.

**Rationale**: Profile completeness drives engagement and matching quality.

**Acceptance Criteria**:
- Display Name: 3-20 characters, unique check
- Gender: Male/Female/Other selection
- DOB: Date picker with 18+ validation
- Profile Photo: Required, face detection validation
- Bio: Optional, 150 character limit
- Location: Auto-detect with manual override option
- Progress indicator shows completion steps

**Dependencies**: REQ-FS-003
**Related Design Decisions**: None

**Notes**: Face detection prevents fake/inappropriate photos at signup.

---

### REQ-FS-005: Interest Selection
**Priority**: P1 (High)
**Type**: Functional

**Description**: Users must select 3-10 interests from predefined categories during onboarding.

**Rationale**: Interests enable preference-based matching (REQ-PV-012).

**Acceptance Criteria**:
- Interest categories: Music, Movies, Gaming, Sports, Travel, Food, Fashion, Tech, etc.
- Minimum 3 interests required to proceed
- Maximum 10 interests selectable
- Visual selection with checkmarks/highlights
- Categories are presented in regional language

**Dependencies**: REQ-FS-004
**Related Design Decisions**: None

**Notes**: Interest list should be data-driven for easy updates.

---

### REQ-FS-006: Language Preference Selection
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users must select primary language preference during onboarding, with optional secondary language.

**Rationale**: Regional language is core differentiator; drives content discovery.

**Acceptance Criteria**:
- Primary options: Tamil, Telugu, Kannada, Hindi, English
- One primary language required
- Secondary language optional
- Language preference affects UI locale and content recommendations
- Can be changed later in settings

**Dependencies**: REQ-ES-004, REQ-FS-004
**Related Design Decisions**: None

**Notes**: Tamil should be default selection for users detected in Tamil Nadu.

---

## Home Screen Requirements

### REQ-FS-007: Home Screen Layout
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Home screen must display header with coin balance, promotional banner carousel, live rooms section, recommended profiles grid, and quick match FAB.

**Rationale**: Home is primary engagement driver; must surface key features immediately.

**Acceptance Criteria**:
- Header shows app logo, coin balance (tappable), notification bell
- Banner carousel auto-scrolls every 5 seconds
- Live rooms section shows horizontal scroll of active rooms
- Recommended profiles in 2-column grid with infinite scroll
- Quick Match FAB visible and prominent
- Pull-to-refresh updates all sections

**Dependencies**: REQ-FS-001
**Related Design Decisions**: DD-FS-003

**Notes**: Optimize for fast initial load; lazy load images.

---

### REQ-FS-008: Live Rooms Discovery on Home
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Home screen must show "Live Now" section with active voice rooms displayed as horizontal scrolling cards.

**Rationale**: Live rooms are core engagement; immediate visibility drives participation.

**Acceptance Criteria**:
- Section title "Live Now" with live indicator
- Horizontal scroll cards showing: host photo, room name, viewer count, tags
- Maximum 2 tags per room card
- "See All" link to full rooms list
- Cards are tappable to join room
- Empty state if no rooms live

**Dependencies**: REQ-FS-007, REQ-FS-015
**Related Design Decisions**: DD-FS-003

**Notes**: Sort by viewer count or algorithm (engagement potential).

---

### REQ-FS-009: Recommended Profiles Grid
**Priority**: P1 (High)
**Type**: Functional

**Description**: Home screen must show "People You May Like" section with recommended profiles based on user preferences.

**Rationale**: Profile recommendations drive 1-to-1 call engagement.

**Acceptance Criteria**:
- 2-column grid layout
- Each card shows: profile photo, name, age, online status (green dot), quick call button
- Infinite scroll pagination
- Profiles filtered by user's language and interest preferences
- Online users prioritized in recommendations
- Pull-to-refresh gets new recommendations

**Dependencies**: REQ-PV-012 (Preference matching)
**Related Design Decisions**: None

**Notes**: Quick call button should show call rate preview.

---

### REQ-FS-010: Quick Match Feature
**Priority**: P1 (High)
**Type**: Functional

**Description**: Users must be able to initiate random matching via Floating Action Button on home screen.

**Rationale**: Random matching adds discovery element and reduces decision fatigue.

**Acceptance Criteria**:
- FAB visible on home screen
- Tap initiates matching queue
- Matching considers language preference
- Match found notification within 30 seconds or timeout message
- User can cancel matching while waiting
- Matched users can accept/decline before call starts

**Dependencies**: REQ-PV-012
**Related Design Decisions**: None

**Notes**: Consider coin cost for quick match or make free for engagement.

---

## Live Voice Room Requirements

### REQ-FS-011: Room Creation
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users (or verified creators) must be able to create and host live voice rooms.

**Rationale**: Live rooms are core engagement feature; creators need hosting capability.

**Acceptance Criteria**:
- Room creation requires: title, tags (1-3), optional cover image
- Host automatically joins as primary seat
- Room is discoverable immediately after creation
- Host can set room as public or followers-only
- Room creation may be restricted to verified creators

**Dependencies**: REQ-FS-015
**Related Design Decisions**: DD-FS-004

**Notes**: Consider requiring KYC to host rooms for safety.

---

### REQ-FS-012: Room Seat Management
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Live rooms must support host seat, up to 8 co-host seats, and unlimited audience listeners.

**Rationale**: Multi-speaker format enables engaging conversations; audience can grow without limits.

**Acceptance Criteria**:
- Host seat (1): Large, center position, cannot be removed
- Co-host seats (8): Smaller, arranged around host
- Host can invite users to co-host seats
- Host can remove co-hosts
- Audience can "raise hand" to request seat
- Speaking indicator shows who is talking
- Mute controls for each seat

**Dependencies**: REQ-FS-011
**Related Design Decisions**: DD-FS-004

**Notes**: Consider seat locking to prevent unwanted speakers.

---

### REQ-FS-013: Room Chat and Interactions
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Live rooms must have real-time chat panel, gift animations, and audience participation features.

**Rationale**: Chat and gifts drive engagement and monetization in rooms.

**Acceptance Criteria**:
- Chat panel visible during room (expandable/collapsible)
- Messages show username and text
- Gift notifications appear in chat
- Full-screen animations for luxury gifts
- Raise hand feature for audience
- Report and leave options accessible

**Dependencies**: REQ-FS-012
**Related Design Decisions**: None

**Notes**: Moderate chat for inappropriate content using automated filters.

---

### REQ-FS-014: Room Gifting
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users must be able to send gifts to hosts and co-hosts during live rooms.

**Rationale**: Gifting is primary monetization in rooms; drives creator earnings.

**Acceptance Criteria**:
- Gift button opens gift panel
- Gift panel shows available gifts with prices
- Gifts can be sent to specific seat holders or "room" (host receives)
- Gift animations play based on gift value tier
- Coin deduction and creator credit happens in real-time
- Low balance prompt when insufficient coins

**Dependencies**: REQ-ES-009, REQ-RM-002
**Related Design Decisions**: None

**Notes**: Luxury gifts (high value) should have premium animations.

---

### REQ-FS-015: Room Discovery and Join
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users must be able to discover active rooms through the Rooms tab and join as audience.

**Rationale**: Easy discovery drives room participation and engagement.

**Acceptance Criteria**:
- Rooms tab shows list/grid of active rooms
- Filter by: language, tags, viewer count
- Search by room name or host name
- Join as audience is one-tap
- Room preview shows host info and listener count
- Recently visited rooms accessible

**Dependencies**: REQ-FS-001
**Related Design Decisions**: DD-FS-004

**Notes**: Consider "For You" algorithmic room recommendations.

---

## 1-to-1 Call Requirements

### REQ-FS-016: Pre-Call Information Display
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Before initiating a call, users must see receiver profile, call rates, their balance, and estimated call time.

**Rationale**: Transparency on costs reduces abandoned calls and support issues.

**Acceptance Criteria**:
- Display: Large profile photo, name, age, location, rating
- Call rates: Audio rate, Video rate (coins per minute)
- Balance: Current coin balance
- Estimated time: "You can talk for ~XX minutes"
- Audio and Video call buttons clearly distinguished

**Dependencies**: REQ-RM-002
**Related Design Decisions**: None

**Notes**: Rates may vary by creator tier or time of day.

---

### REQ-FS-017: Audio Call Functionality
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users must be able to make 1-to-1 audio calls with real-time coin deduction and call controls.

**Rationale**: Audio calls are core engagement and monetization feature.

**Acceptance Criteria**:
- Call connects within 5 seconds on stable connection
- Display shows profile photo, name, call duration timer
- Coin counter updates in real-time (every minute deduction)
- Controls: mute/unmute, speaker toggle, gift button, end call
- Low balance warning at 2 minutes remaining
- Auto-disconnect when balance depleted
- Call quality adapts to network conditions

**Dependencies**: REQ-ES-007, REQ-FS-016
**Related Design Decisions**: DD-FS-005

**Notes**: Use WebRTC for peer-to-peer audio with TURN fallback.

---

### REQ-FS-018: Video Call Functionality
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users must be able to make 1-to-1 video calls with camera controls and higher per-minute rate.

**Rationale**: Video calls added to MVP per stakeholder decision; competitor parity.

**Acceptance Criteria**:
- Video feed displays receiver's camera
- Self-view available in corner (movable)
- Video rate higher than audio (e.g., 12 vs 8 coins/min)
- Controls: camera on/off, flip camera, mute, gift, end call
- Fallback to audio if video quality poor
- Bandwidth requirement: minimum 480p quality

**Dependencies**: REQ-FS-017
**Related Design Decisions**: DD-FS-005

**Notes**: Video calls have higher infrastructure cost; rate differential compensates.

---

### REQ-FS-019: Post-Call Summary
**Priority**: P1 (High)
**Type**: Functional

**Description**: After call ends, users must see call summary with duration, coins spent, and rating prompt.

**Rationale**: Summary provides transparency; ratings drive quality.

**Acceptance Criteria**:
- Summary shows: duration, coins spent
- Rating prompt: 1-5 stars
- Actions: Add to Favorites, Block/Report, Call Again
- Low balance prompt if applicable with recharge offer
- Summary dismissible

**Dependencies**: REQ-FS-017
**Related Design Decisions**: None

**Notes**: Consider skip option for rating to reduce friction.

---

## Wallet Requirements

### REQ-FS-020: User Wallet Display
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Users must have wallet showing coin balance, quick recharge options, all packages, and transaction history.

**Rationale**: Wallet is conversion point for monetization; must be clear and accessible.

**Acceptance Criteria**:
- Balance display with coin icon and INR equivalent
- Quick recharge buttons for popular amounts (₹99, ₹299, ₹499)
- All packages section with coin amounts and bonus percentages
- Best value badge on optimal package
- Transaction history with filters (All/Recharge/Spent)
- Redeem code section for promotions

**Dependencies**: REQ-RM-002
**Related Design Decisions**: None

**Notes**: Highlight bonus percentages to encourage larger purchases.

---

### REQ-FS-021: Coin Package Structure
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Coin packages must offer tiered pricing with bonus coins for larger purchases.

**Rationale**: Tiered bonuses incentivize larger purchases and improve LTV.

**Acceptance Criteria**:
- Minimum package: ₹49 → 50 coins
- Popular packages: ₹99 → 110 coins (+10%), ₹299 → 350 coins (+17%)
- Best value: ₹499 → 600 coins (+20%)
- Premium: ₹999 → 1300 coins (+30%), ₹1999 → 2800 coins (+40%)
- VIP subscribers get additional 20% discount on all packages
- Package prices and bonuses configurable

**Dependencies**: REQ-RM-007
**Related Design Decisions**: DD-FS-006

**Notes**: Test package pricing through A/B experiments.

---

### REQ-FS-022: Payment Integration
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Wallet must integrate Razorpay for payment processing with UPI, cards, and wallets.

**Rationale**: Razorpay is specified in PRD; covers Indian payment methods.

**Acceptance Criteria**:
- Razorpay SDK integration
- Payment methods: UPI, Debit/Credit Cards, Paytm, PhonePe, Google Pay
- Payment confirmation within 10 seconds
- Failed payment handling with retry
- Coins credited immediately on successful payment
- Payment receipts available

**Dependencies**: REQ-RM-002
**Related Design Decisions**: DD-FS-006

**Notes**: Consider Google Play Billing for in-app purchases compliance.

---

## Chat Requirements

### REQ-FS-023: 1-to-1 Direct Messaging
**Priority**: P1 (High)
**Type**: Functional

**Description**: Users must be able to send direct messages to other users they've connected with.

**Rationale**: Chat extends engagement beyond calls; builds relationships.

**Acceptance Criteria**:
- Chat accessible from profile view and chat tab
- Text messages with timestamps
- Read receipts (optional/settings)
- Message history persists
- Block and report options
- Online/offline status visible

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Consider message limits for free users to drive call conversion.

---

### REQ-FS-024: Room Chat Integration
**Priority**: P1 (High)
**Type**: Functional

**Description**: Live rooms must have integrated chat for audience participation.

**Rationale**: Room chat drives engagement and allows non-speaking participation.

**Acceptance Criteria**:
- Chat visible during room (expandable panel)
- Messages show username
- Gift notifications in chat stream
- Emoji reactions supported
- Chat rate limiting (1 message/second) to prevent spam
- Moderation: word filters, user muting

**Dependencies**: REQ-FS-013
**Related Design Decisions**: None

**Notes**: Room chat is separate from 1-to-1 DMs.

---

## Creator Dashboard Requirements

### REQ-FS-025: Creator Earnings Display
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Verified creators must have dashboard showing earnings summary: today, week, month, and total.

**Rationale**: Earnings visibility drives creator motivation and trust.

**Acceptance Criteria**:
- Today's earnings prominently displayed
- Weekly and monthly totals
- Lifetime total earnings
- Earnings breakdown by source (calls, gifts, rooms)
- Currency in INR
- Updates in near real-time (within 1 minute)

**Dependencies**: REQ-PV-008
**Related Design Decisions**: None

**Notes**: Creator dashboard only visible to verified/KYC'd creators.

---

### REQ-FS-026: Creator Balance and Withdrawal
**Priority**: P0 (Critical)
**Type**: Functional

**Description**: Creators must see available balance, pending balance, and be able to request withdrawals.

**Rationale**: Clear balance breakdown and easy withdrawal is essential for creator satisfaction.

**Acceptance Criteria**:
- Available balance: Amount ready for withdrawal
- Pending balance: Amount in 7-day hold period
- Processing: Amount being transferred
- Withdraw button with amount entry
- Bank account linking (verified)
- Minimum withdrawal amount enforced
- Withdrawal confirmation with estimated arrival

**Dependencies**: REQ-CP-xxx
**Related Design Decisions**: None

**Notes**: 7-day hold protects against chargebacks and fraud.

---

### REQ-FS-027: Creator Performance Stats
**Priority**: P1 (High)
**Type**: Functional

**Description**: Creator dashboard must show performance metrics: calls, duration, gifts, followers, rating.

**Rationale**: Performance visibility helps creators optimize and improve.

**Acceptance Criteria**:
- Total calls count
- Average call duration
- Total gifts received
- Follower count
- Average rating (1-5 stars)
- Daily earnings graph (7 days)
- Toggle between Earnings/Calls/Gifts views

**Dependencies**: REQ-FS-025
**Related Design Decisions**: None

**Notes**: Consider weekly email summaries for creators.

---

### REQ-FS-028: Creator Leaderboard
**Priority**: P2 (Medium)
**Type**: Functional

**Description**: Creator dashboard should show leaderboard position and distance to next rank.

**Rationale**: Gamification drives creator competition and engagement.

**Acceptance Criteria**:
- Current rank displayed
- Coins/earnings needed for next rank
- Top 10 bonus rewards messaging
- Weekly leaderboard reset
- Leaderboard visible to all creators

**Dependencies**: REQ-FS-025
**Related Design Decisions**: None

**Notes**: Leaderboard can be scoped by language/region.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-FS-001 | Bottom Navigation Structure | P0 | Functional |
| REQ-FS-002 | Secondary Navigation Access | P1 | Functional |
| REQ-FS-003 | Phone OTP Authentication | P0 | Functional |
| REQ-FS-004 | Profile Setup Flow | P0 | Functional |
| REQ-FS-005 | Interest Selection | P1 | Functional |
| REQ-FS-006 | Language Preference Selection | P0 | Functional |
| REQ-FS-007 | Home Screen Layout | P0 | Functional |
| REQ-FS-008 | Live Rooms Discovery on Home | P0 | Functional |
| REQ-FS-009 | Recommended Profiles Grid | P1 | Functional |
| REQ-FS-010 | Quick Match Feature | P1 | Functional |
| REQ-FS-011 | Room Creation | P0 | Functional |
| REQ-FS-012 | Room Seat Management | P0 | Functional |
| REQ-FS-013 | Room Chat and Interactions | P0 | Functional |
| REQ-FS-014 | Room Gifting | P0 | Functional |
| REQ-FS-015 | Room Discovery and Join | P0 | Functional |
| REQ-FS-016 | Pre-Call Information Display | P0 | Functional |
| REQ-FS-017 | Audio Call Functionality | P0 | Functional |
| REQ-FS-018 | Video Call Functionality | P0 | Functional |
| REQ-FS-019 | Post-Call Summary | P1 | Functional |
| REQ-FS-020 | User Wallet Display | P0 | Functional |
| REQ-FS-021 | Coin Package Structure | P0 | Functional |
| REQ-FS-022 | Payment Integration (Razorpay) | P0 | Functional |
| REQ-FS-023 | 1-to-1 Direct Messaging | P1 | Functional |
| REQ-FS-024 | Room Chat Integration | P1 | Functional |
| REQ-FS-025 | Creator Earnings Display | P0 | Functional |
| REQ-FS-026 | Creator Balance and Withdrawal | P0 | Functional |
| REQ-FS-027 | Creator Performance Stats | P1 | Functional |
| REQ-FS-028 | Creator Leaderboard | P2 | Functional |

**Total Requirements**: 28
**P0 (Critical)**: 18
**P1 (High)**: 9
**P2 (Medium)**: 1

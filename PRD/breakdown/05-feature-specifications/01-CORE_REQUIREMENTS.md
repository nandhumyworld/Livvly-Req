# Core Feature Requirements
**Section**: 05-Feature Specifications
**Last Updated**: 2026-01-20
**Status**: In Progress

## 1. User Matching & Discovery System

### 1.1 Matching Algorithm
- **Type**: Hybrid matching approach
- **Factors**: Location proximity + Interests + Voice chemistry + Creator status
- **Algorithm Logic**:
  - Primary: Assess user interests overlap (tags, preferences, hobbies)
  - Secondary: Calculate geographic distance and location preferences
  - Tertiary: Evaluate creator status alignment (creator seeking fans, fans seeking creators)
  - Quaternary: Voice/communication style compatibility signals (if available)

### 1.2 Matching Delivery
- **Model**: On-demand browsing (not proactive push)
- **User Behavior**: Users manually browse/swipe through potential matches
- **Implementation**:
  - Discovery page with swipeable cards/profiles
  - Search/filter functionality accessible from main interface
  - No automated push notifications for new matches
  - User-initiated matching flow

### 1.3 Match Management
- **Undo/Rematch**: Users can reject matches and get new recommendations
- **Match History**: Track user interactions with matches (view, like, reject, message, call)
- **Re-engagement**: Ability to revisit previously rejected profiles

## 2. Voice/Video Calling Features

### 2.1 Voice Calling (Live Feature)
- **Status**: Core feature (always available)
- **Access Level**: Available to all registered users
- **Cost Model**: Time-based limits with caller payment
  - Free calling: First 10 minutes per call (resets per call, not daily)
  - After 10 minutes: Coin deduction per minute (1 coin/minute - TBD in coin economy)
  - Payment: Caller pays for minutes after free 10-minute allowance
  - Rate: Same rate for regular users and creators

### 2.2 Video Calling
- **Status**: Premium feature
- **Subscription Tier**: Free + Premium (2-tier system)
- **Creator Privilege**: Creators can initiate video calls with any user
- **Access Requirements**:
  - Creators: Can initiate video calls to any matched user (no subscription required for initiator)
  - Regular Users: Must have Premium subscription to accept video calls
  - All video calls require a match between users
- **Cost Model**: Covered by Premium subscription or call costs (TBD with Revenue model team)

### 2.3 Call Specifications
- **Call Initiation**: Direct 1-on-1 calls between matched users only
- **Call Duration**: Tracked in minutes for coin accounting
- **Call Quality**: HD voice calling (codec: Opus or similar high-quality codec)
- **Call Recording**: NOT ALLOWED
  - Calls cannot be recorded by any party
  - Privacy-first approach
  - No storage, consent, or transcription complexity
- **Call Interruption**: Handle dropped calls, reconnection attempts
- **Call History**: Maintain call logs with duration, cost, timestamp (but no recordings)

## 3. Messaging Features

### 3.1 Real-Time Text Messaging
- **Status**: Core feature
- **Access**: Available to all matched users (regardless of subscription tier)
- **Cost Model**: Always free
- **Features**:
  - Live text chat between match pairs
  - Message history per conversation
  - Typing indicators: NOT shown (privacy-focused)
  - Read receipts: NOT shown (privacy-focused)
  - Character limits: TBD (standard mobile app limits recommended)

### 3.2 Voice Messages (Asynchronous)
- **Status**: Core feature
- **Recording**: Users can record voice messages
- **Delivery**: Send asynchronously (not live call)
- **Playback**: Recipient can play back at any time
- **Storage**: Cloud storage for voice message files
- **Cost Model**: Coin-based (determined by creator minimum settings)
  - Creators can set minimum coins per voice message
  - Different from text messages (separate minimums allowed)
  - Caller/sender pays the minimum set by recipient
- **Limitations**: Max message length TBD (suggest 120-180 seconds)

### 3.3 Video Messages
- **Status**: Core feature
- **Recording**: Users can record short video messages
- **Delivery**: Asynchronous message exchange
- **Playback**: Recipient can view anytime
- **Storage**: Cloud storage for video files
- **Cost Model**: Coin-based (determined by creator minimum settings)
  - Creators can set minimum coins per video message
  - Different from text and voice messages (separate minimums allowed)
  - Sender pays the minimum set by recipient
- **Limitations**:
  - Max video length: 30 seconds (firm limit to encourage quick content)
  - Max file size: TBD based on CDN/storage strategy
  - Storage quota per user: TBD

## 4. User Filtering & Discovery

### 4.1 Interest-Based Filtering
- **Filters**:
  - User interests/hobbies (tags or categories)
  - Communication style preferences
  - Dating intention (casual, serious, etc.)
- **Implementation**: Add/remove interest tags during profile setup
- **Matching Logic**: Show matches with overlapping interests first

### 4.2 Location-Based Filtering
- **Features**:
  - Radius-based search (e.g., within 5, 10, 25 miles)
  - City/region selection
  - Online/nearby status
- **Privacy**: TBD - Should exact location be shown or just distance/general area?

### 4.3 Additional Filtering (Optional)
- **Age Range**: Flexible min/max age filtering
  - Users can set custom minimum and maximum age preferences
  - No predefined constraints on age range selection
  - Filters applied both to user's own browsing and to who can see them
- **Creator Status**: Can filter to show/hide creators
- **Verification Status**: Show verified vs. unverified users (optional)

## 5. Safety & Control Features

### 5.1 Block/Unblock
- **Functionality**: Users can block other users
- **Effect**: Blocked users cannot contact, call, or message the blocker
- **Management**: Users can view and unblock from settings
- **Persistence**: Block status maintained across sessions

### 5.2 Report User
- **Trigger**: User can report inappropriate behavior, harassment, fake profiles
- **Information Collected**:
  - Reason for report (harassment, fake profile, inappropriate content, etc.)
  - Additional context/comments
  - Screenshots or evidence (optional)
- **Action**: Report escalated to moderation team for review

### 5.3 Call Quality Feedback
- **Timing**: Post-call feedback prompt
- **Metrics**:
  - Audio quality rating (poor, fair, good, excellent)
  - Video quality (if applicable)
  - Connection stability
  - Latency/delay issues
- **Usage**: Identify and fix technical issues, improve infrastructure

### 5.4 Profile Verification
- **Creator Verification**: Required
  - Photo verification: Required for creators
  - ID verification: Required for creators
  - Verification process: TBD (auto vs. manual review)
  - Revocation: Can be revoked for violations
  - Badge: Verified creator badge displayed on profile
- **Regular User Verification**: Optional
  - Photo verification: Optional for regular users
  - ID verification: Optional for regular users
  - Can increase trust/visibility in discovery

---

## Clarifications & Design Decisions Summary

All major feature questions clarified. See CLARIFICATION_QUESTIONS.md for full Q&A.

### Key Monetization Design
- **Voice Calls**: 10 min free → caller pays 1 coin/min after
- **Video Calls**: Premium subscription required (creator exception for initiator)
- **Text Messages**: Always free
- **Voice/Video Messages**: Coin-based per creator settings
- **Creator Commission**: 60% to creator, 40% to platform

### Creator Features
- Can initiate video calls to any matched user
- Can set different coin minimums for each message type
- On-demand payouts (minimum $50 threshold)
- Payout methods: PayPal, UPI

### User Experience
- Flexible age range filtering
- Unlimited rematch/undo capability
- Privacy-first (no typing indicators, read receipts, or call recording)
- Profile verification optional for regular users (required for creators)
- Notifications: Messages, calls, and profile visits

### Notifications
- New messages: Notify
- Incoming calls: Alert
- Profile visits: Notify
- No other push notifications

---

**Status**: All critical decisions locked
**Next Phase**: Generate design decisions document with implementation rationale
**Technical Specs**: Pending (Sections 6-8)

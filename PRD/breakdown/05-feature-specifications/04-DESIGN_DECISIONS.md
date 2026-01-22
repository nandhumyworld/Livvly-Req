# Design Decisions - Feature Specifications
**Section**: 05-Feature Specifications
**Last Updated**: 2026-01-22
**Status**: Complete

## Overview
This document details the key design decisions made for the Feature Specifications section, their rationale, and implementation implications.

---

## 1. Voice Call Monetization Strategy

### Decision: 10 Free Minutes + Caller Pays Model
**ID**: DD-FS-001
**Impact**: HIGH (Core revenue stream)

### Rationale
- **10 minutes free per call** (vs. 5, 15, or 30):
  - Balances user discovery with monetization pressure
  - Enough time to assess chemistry and connection
  - Encourages genuine matching validation
  - Not too long that it undercuts revenue opportunity

- **Caller pays** (vs. callee or split):
  - Aligns with pricing precedent (call-based services like Uber, DoorDash)
  - Clear accountability: initiator bears cost
  - Prevents gaming/abuse (caller won't spam free minutes)
  - Simpler billing model than split charges

- **1 coin/minute** (TBD in Coin Economy):
  - Consistent pricing model
  - No complexity of tiered/variable rates
  - Regular users and creators pay same rate
  - Easier to communicate to users

### Implementation Notes
- Free minutes reset per call (not daily/weekly)
- Calls longer than 10 minutes automatically trigger coin deduction
- UI should warn users at 9:30 mark (approaching paid tier)
- Grace period TBD: Force end at limit or allow overflow with deduction?

### Dependencies
- Coin Economy specification (Section 09)
- Billing engine implementation
- Call state management & tracking

---

## 2. Video Calling: Premium + Creator Exception

### Decision: Premium Tier Required, Creators Can Initiate
**ID**: DD-FS-002
**Impact**: HIGH (Monetization + Creator engagement)

### Rationale
- **Premium subscription gated** (vs. free for all or creators only):
  - Creates differentiated value for paid tier
  - Simple tier system (Free + Premium) vs. complex multi-tier
  - Easier to market: "Unlock video calling with premium"
  - Aligns with voice call monetization (free text → paid calls/video)

- **Creator exception for initiation** (vs. no exception or full exception):
  - Encourages creator partnerships with regular users
  - Creators can leverage their status for reach
  - Regular users can still accept creator calls (if they have Premium)
  - Middle ground: Creators privileged but not unlimited
  - Incentivizes creator adoption and retention

- **Subscribers can accept from anyone** (vs. creator-only acceptance):
  - Treats Premium subscribers fairly
  - Any matched Premium user can video call, not just verified creators
  - Supports organic dating use case alongside creator economy

### Implementation Notes
- Video call initiation check: If caller is creator → allow. Else → check Premium subscription
- Video call acceptance check: Recipient must have Premium subscription (regardless of caller type)
- Creator badge must be visible to indicate initiator status
- Declined video calls from creators shouldn't auto-penalize creator (fair retry logic)

### Dependencies
- Creator account type definition (Section 05, Creator Features)
- Premium subscription system
- Real-time video infrastructure
- Authentication & authorization layer

---

## 3. Messaging Cost Model: Text Free + Voice/Video Paid

### Decision: Tiered Messaging Costs
**ID**: DD-FS-003
**Impact**: MEDIUM (User experience + monetization)

### Rationale
- **Text always free** (vs. all paid or all free):
  - Lowers friction for user engagement
  - Text is abundant in dating/social apps (expected free)
  - Encourages initial message exchange (leading to calls/payments)
  - Supports connection discovery without barriers

- **Voice & video messages coin-based** (vs. free or text-only):
  - Creates distinct monetization tier for rich media
  - Video/voice messages are asynchronous (less friction than live calls)
  - Premium content warrants payment
  - Creators can monetize without forcing live calls

- **Creator minimums, not flat fees** (vs. flat rate):
  - Empowers creators with pricing control
  - Different minimums per message type (flexibility)
  - Supports variable creator tiers/pricing strategies
  - Example: Creator might want $1 video message but $0.50 voice message

### Implementation Notes
- Text messages: No coin deduction (always permitted)
- Voice messages: Sender pays creator's voice message minimum (if set)
- Video messages: Sender pays creator's video message minimum (if set)
- Creators can set minimums independently per message type
- Minimums can be changed dynamically (affects future messages only)
- Default minimums TBD (suggest: voice $0.50-$2, video $1-$5)

### User Experience
- Before sending paid message: Show "This costs N coins" prompt
- Clear consent before payment
- Decline option available
- Message not sent if user declines or has insufficient coins

### Dependencies
- Creator feature specification (minimum settings)
- Coin wallet & transaction system
- Push notification for incoming paid messages
- Analytics on paid message acceptance rates

---

## 4. Age Range Filtering: Flexible User Control

### Decision: Flexible Min/Max Range (No Constraints)
**ID**: DD-FS-004
**Impact**: LOW (UX improvement)

### Rationale
- **Flexible range** (vs. predefined brackets or constrained ranges):
  - Maximizes user agency in discovery
  - No algorithmic assumptions about acceptable age gaps
  - Supports various use cases: mentorship, May-December relationships, etc.
  - Reduces support friction (no "why can't I filter this way?" complaints)

- **No algorithmic constraints** (vs. ±5 years):
  - Dating norms vary by culture and preference
  - Legal adult users (18+) have agency
  - Reduces paternalistic gatekeeping
  - Platforms typically defer to user choice

### Implementation Notes
- Age range filters: Minimum age, Maximum age (separate fields)
- Bidirectional filtering: User sets range they're interested in, also receive visibility based on others' ranges
- Default range: Likely +/- 5 years from user age (can be changed)
- Validation: Only allow 18+ minimum (legal requirement for app)
- UI consideration: Slider vs. text inputs (slider more intuitive)

### Privacy & Safety Notes
- Age must be verified (not self-reported without validation)
- Misleading age is violation of ToS
- Age verification mechanism TBD

### Dependencies
- Age verification system
- Profile filtering & search database indexes
- UI/UX components (sliders, range inputs)

---

## 5. Call Recording: Privacy-First Approach

### Decision: No Call Recording Allowed
**ID**: DD-FS-005
**Impact**: MEDIUM (Privacy, compliance, scope)

### Rationale
- **No recording** (vs. one-party consent or mutual consent):
  - Strongest privacy protection (simplifies compliance)
  - Reduces legal liability across jurisdictions
  - Eliminates storage/retention complexity
  - Clearer value proposition: "Your calls are private"
  - Reduces platform abuse/impersonation via deepfakes
  - Focuses on real-time connection (not archival)

- **Not a core differentiator**:
  - Most dating apps don't emphasize call recording
  - Users expect privacy in voice/video calls
  - Shifts focus from content capture to relationship building
  - Reduces abuse vectors (harassment recording, non-consensual sharing)

### Implementation Notes
- No recording UI components needed
- No call recordings stored
- No transcription services
- Simpler privacy policy (no recording disclosure needed)
- Technical implementation: Disable OS recording APIs during active calls (if feasible)

### Compliance & Legal
- GDPR compliant (no biometric/voice data storage)
- CCPA friendly (no audio archives)
- Avoids one-party-consent issues
- No wiretapping concerns
- Simpler platform moderation (no audio to moderate)

### Future Consideration
- Users can screenshot/record externally (can't prevent)
- In-app recording remains blocked in MVP
- Could revisit if requested by creators (with consent/safeguards)

### Dependencies
- Call infrastructure (webRTC or similar)
- Audio codec selection (Opus recommended)
- Privacy policy & ToS

---

## 6. Video Messages: 30-Second Maximum

### Decision: Hard Limit of 30 Seconds
**ID**: DD-FS-006
**Impact**: MEDIUM (Content strategy, storage, monetization)

### Rationale
- **30 seconds** (vs. 60 seconds, 2 min, or 5+ min):
  - Encourages quick, authentic content (not polished/scripted)
  - Reduces storage burden (cost implications)
  - Fits mobile screen viewing experience
  - Aligns with social platform norms (TikTok, Reels, Shorts)
  - Easier to moderate (less content to review)
  - Monetization: Shorter content can be sent frequently (more transactions)

- **Hard limit** (vs. soft recommendation):
  - Prevents abuse (spam 10min videos)
  - Consistent experience for all users
  - Clear constraint for engineering

### Implementation Notes
- UI shows countdown timer during recording
- Record stops automatically at 30 seconds
- Cannot override or resume in same message
- Users can send multiple 30-second messages in sequence (no limit on # of messages)
- Compression/quality: Balance file size vs. playback quality
- CDN/storage: Estimate storage needs at scale (important for cost planning)

### Technical Considerations
- Video codec selection (H.264 or VP9 for compression)
- File size limits TBD (based on network assumptions)
- Thumbnail generation for quick preview
- Progressive loading (can start playback before full download)

### Future Flexibility
- Could increase to 60 seconds if user feedback warrants
- Could implement "premium video messages" with longer limits
- Could enable video message series (linked messages)

### Dependencies
- Video compression/CDN infrastructure
- Storage strategy & cost modeling
- Mobile video recording APIs
- Playback optimization

---

## 7. Creator Content Library: Private Visibility

### Decision: Visible Only to Matched/Paying Users
**ID**: DD-FS-007
**Impact**: MEDIUM (Creator monetization, discovery)

### Rationale
- **Private visibility** (vs. public or semi-private):
  - Monetizes creator content library effectively
  - Creates value for supporters (exclusive access)
  - Reduces spam/scraping risk (only matched users see)
  - Encourages direct creator-supporter relationships
  - Supports creator revenue model (pay to unlock content)

- **Not public** (vs. public library):
  - Prevents free content consumption
  - Reduces discoverability but increases monetization
  - Aligns with "creator economy" ethos (pay for quality content)

- **Not semi-private** (vs. followers only):
  - Simpler than follower system (no follow feature mentioned)
  - Clearer transactional model (pay = access)
  - Reduces tier complexity

### Implementation Notes
- Content library items accessible only from matched conversation
- Access requires active match + payment/subscription (TBD which model)
- Creators can upload unlimited content to library
- Content stored indefinitely (or until deleted)
- No preview/preview limits (can't see library until paying)
- Analytics: Track views, engagement per library item

### Creator Features
- Upload/manage library items easily
- Set pricing/minimum for library access
- Track views and engagement
- Delete items (cascade delete considerations)

### User Experience
- Icon/badge in match showing "Creator has library content"
- Access restricted message: "Subscribe or make payment to view"
- Library browse within chat (seamless)
- Streaming/download options TBD

### Dependencies
- Creator account type & permissions
- Content storage & CDN
- Payment flow integration
- Creator analytics dashboard

---

## 8. Notifications Strategy: Privacy-Focused Selective Alerts

### Decision: Three Notification Types Only
**ID**: DD-FS-008
**Impact**: LOW-MEDIUM (UX, engagement, privacy)

### Rationale
- **Notify for**: Messages, calls, profile visits (vs. all events or none):
  - Keeps users engaged with critical interactions
  - Reduces notification fatigue
  - Balances engagement with privacy
  - Includes all user-initiated activities (message, call, visit)

- **No other notifications** (vs. typing indicators, etc.):
  - Simpler notification system
  - Privacy-focused (no real-time status tracking)
  - Reduces distraction and notification overload
  - Aligns with minimalist/privacy-first design

### Implementation Notes
- Push notification types:
  1. **New message**: "John sent you a message"
  2. **Incoming call**: "Jane is calling you"
  3. **Profile visit**: "Someone visited your profile" (optional show visitor name TBD)

- Notification settings:
  - Users can mute per contact/conversation
  - Global on/off per notification type
  - Do Not Disturb mode
  - In-app notifications (always shown if app open)
  - Push notifications (configurable)

- Frequency limits:
  - Rate limit aggressive notifications (no spam)
  - Max 1 notification per minute per contact
  - Batch similar notifications (e.g., multiple messages → single notification)

### Privacy Considerations
- Don't reveal full message content in notification
- Don't reveal caller identity if contact blocked
- Don't track "read" status (complements no read receipts)

### Dependencies
- Push notification service (Firebase Cloud Messaging, APNs)
- User notification preferences DB
- Notification delivery logs & analytics

---

## 9. Undo/Rematch: Unlimited Capability

### Decision: No Limits on Rematch
**ID**: DD-FS-009
**Impact**: LOW-MEDIUM (UX, discovery experience)

### Rationale
- **Unlimited rematch** (vs. daily limits or cooldown):
  - Empowers users to change minds without friction
  - Encourages exploration (more swipes = more data for matching)
  - Reduces decision paralysis (can always undo)
  - Better user experience than time-gated features
  - Aligns with Tinder-style swiping (unlimited swipes, not limited)

- **No cooldown** (vs. 1 per hour or similar):
  - Simpler implementation (no rate limiting)
  - Natural usage patterns (users don't compulsively rematch)
  - Reduces frustration with arbitrary limits

### Implementation Notes
- Undo button: Always available after swiping
- Rematch: Get new recommendations after undo
- Match history: Track all undone matches (for analytics)
- Avoid showing same match immediately after undo (slight delay)
- Storage: Keep match history for personalization

### Abuse Prevention
- Monitor for pattern abuse (100+ rematches/hour = bot)
- Could implement rate limiting at extreme usage (tbd threshold)
- No artificial friction for normal usage patterns

### Dependencies
- Match state management
- User interaction tracking
- Algorithm to avoid repeated matches

---

## 10. Text Message Privacy: No Indicators

### Decision: Disable Typing Indicators & Read Receipts
**ID**: DD-FS-010
**Impact**: LOW (Privacy UX)

### Rationale
- **No typing indicators** (vs. show):
  - Reduces ambient awareness/surveillance feeling
  - Users can draft messages without pressure
  - Prevents "they're typing but not sending" social anxiety
  - Aligns with privacy-first messaging philosophy

- **No read receipts** (vs. show):
  - Reduces pressure to respond immediately
  - Maintains message privacy (no "seen but ignored" judgement)
  - Simplifies implementation (no read status tracking)
  - Supports async communication style

### Implementation Notes
- Compose UI: User typing, but no indicator sent to recipient
- Message UI: No "typing...", "seen", "delivered", status indicators
- Backend: Simplifies message state (sent → received, no intermediate states)
- Notifications: Users learn message was sent via notification only

### User Experience
- Messages feel more private/secure
- Less pressure to respond immediately
- More thoughtful communication (less reactive)
- Suitable for dating context (intimate, not urgent)

### Future Consideration
- Users might want read receipts for safety/reassurance (could add as toggle)
- Could implement opt-in read receipts per contact
- Current MVP prioritizes privacy over convenience

### Dependencies
- Message state machine (simplified)
- Frontend UI/UX design

---

## 11. Creator Verification: Required for Creators

### Decision: Photo + ID Verification Required
**ID**: DD-FS-011
**Impact**: HIGH (Trust, safety, monetization)

### Rationale
- **Photo verification required** (vs. optional):
  - Ensures profile pictures are genuine
  - Reduces catfishing risk
  - Builds trust for supporters/paying users
  - Aligns with creator economy norms (real identity)

- **ID verification required** (vs. optional):
  - Confirms age (legal requirement for app)
  - Confirms identity (reduces fraud)
  - Enables compliance with payment regulations
  - Supports KYC (Know Your Customer) for payouts

- **Required for creators, optional for regular users** (vs. all required or all optional):
  - Monetized creators have higher trust burden
  - Regular users: Lower friction (optional verification increases trust)
  - Two-tier approach balances safety and onboarding
  - Creators accepting payments need stronger verification

### Verification Process
- Photo verification: Compare selfie to profile photo
- ID verification: Accept government ID (passport, driver's license)
- Manual review: TBD (auto-verification vs. manual review)
- Revocation: Can be revoked for violations/policy breaches
- Re-verification: Required if ID expires or details change
- Privacy: ID data encrypted, minimal retention

### Creator Badge
- Blue checkmark or "Verified" badge on creator profiles
- Visible in discovery and conversations
- Trust signal for supporters

### Regular User Verification
- Optional photo/ID verification
- Increases profile visibility in discovery (can be incentivized)
- Shows trust score or verification badge
- May unlock features like "verified connections only" filter

### Implementation Notes
- Verification service integration (third-party provider or in-house)
- Secure document storage (PII handling)
- Fraud detection (deepfake detection for photos)
- Appeal process for rejected verifications

### Compliance & Legal
- Age verification: Legal requirement (18+)
- ID verification: Anti-fraud, AML, KYC compliance
- Data retention: Minimal, encrypted, regulated timeframe

### Dependencies
- ID verification service (vendor TBD)
- Photo upload & storage infrastructure
- Admin review dashboard
- Appeals workflow

---

## 12. Creator Commission: 60% Revenue to Creator

### Decision: 40% Platform Fee Structure
**ID**: DD-FS-012
**Impact**: HIGH (Business model, creator incentives)

### Rationale
- **40% platform fee** (vs. 20%, 30%, or 50%):
  - Balances creator incentives (60% keeps creators motivated)
  - Sustains platform operations (40% covers infrastructure, moderation, payments)
  - Competitive with other creator platforms (industry standard: 30-40%)
  - Transparent, easy to communicate ("You keep 60%")

- **60% creator revenue** (vs. 80% or 50%):
  - Fair split for creators who benefit from platform reach
  - Recognizes platform value (payment processing, discovery, safety, compliance)
  - Supports business sustainability
  - Industry precedent (YouTube: 55-45, Twitch: 50-50, OnlyFans: 80-20 varies)

### Application
- Applied to all creator earnings:
  - Voice message minimums (creator earns 60%)
  - Video message minimums (creator earns 60%)
  - Other monetization streams (TBD in Revenue Model)

- Does NOT apply to:
  - Premium subscription (separate split TBD with Revenue Model)
  - Voice call costs (split TBD with Revenue Model)
  - Video call costs (split TBD with Revenue Model)

### Implementation Notes
- Transaction calculations: Automatic fee deduction before payout
- Transparency: Creators see gross and net amounts
- Analytics: Show commission breakdown in creator dashboard
- No hidden fees (all deductions disclosed)

### Creator Messaging
- Clear documentation: "You keep 60% of earnings"
- Dashboard shows actual numbers: "$100 earned → $60 for you, $40 platform fee"
- Marketing: "Highest creator revenue share in dating apps" (if true)

### Future Flexibility
- Could offer higher split for top creators (incentive structure)
- Could offer lower split for new features/pilot programs
- Could offer tiered splits based on volume

### Dependencies
- Payment processing & payout system
- Creator analytics dashboard
- Financial reporting

---

## 13. Creator Payouts: On-Demand with $50 Threshold

### Decision: Flexible Withdrawal System
**ID**: DD-FS-013
**Impact**: MEDIUM (Creator experience, operations)

### Rationale
- **On-demand** (vs. weekly, monthly, or immediate):
  - Maximizes creator agency (withdraw when needed)
  - Reduces support friction ("When will I get paid?")
  - Builds trust (creators control their money)
  - Supports different creator needs (some need weekly, some need ad-hoc)

- **$50 minimum threshold** (vs. $10, $100, or no minimum):
  - Balances creator convenience with payment processing costs
  - $10: Too low (high transaction costs, payment processor fees)
  - $50: Sweet spot (covers payment processing, not excessive for creators)
  - $100: Too high (creates creator frustration)
  - Industry standard: $50-$100

### Implementation Notes
- Payout methods: PayPal, UPI (per user decision)
- Process: Creator initiates withdrawal via dashboard
- Verification: $50 balance required before button enabled
- Timing: Processed within 1-3 business days (pending payment processor)
- Fees: TBD (who pays payment processor fees? Creator or platform?)
- Retry: Failed payouts retry automatically (handle declined cards, etc.)

### Payment Processing
- PayPal integration: Direct payout to creator PayPal account
- UPI integration: Direct payout to creator UPI/NEFT account (India focus)
- Other methods: Stripe Connect? Wise? (TBD with payments team)
- Tax reporting: 1099/MISC or local tax documentation (TBD by region)

### Security & Fraud
- Verify payout account ownership (prevent account takeover)
- Limit withdrawal frequency (prevent abuse, fraud)
- Dispute resolution process (for fraudulent withdrawals)
- Hold periods: TBD (typically 24-48 hours for fraud detection)

### Creator Communication
- Confirmation email upon payout initiation
- Tracking number/receipt
- Estimated delivery timeline
- Support for failed payouts

### Dependencies
- Payment processor integration (PayPal, UPI, etc.)
- Creator payout dashboard
- Financial reconciliation system
- Fraud detection & dispute system

---

## 14. Flexible Message Minimums: Creator Control

### Decision: Separate Minimums per Message Type
**ID**: DD-FS-014
**Impact**: MEDIUM (Creator monetization, flexibility)

### Rationale
- **Different minimums per message type** (vs. single minimum for all):
  - Empowers creators with pricing control
  - Different value per content type (video > voice > text)
  - Creators can set competitive prices
  - Reduces creator friction (one-size-fits-all doesn't work)

- **Text excluded** (already free):
  - Messaging barrier kept low
  - Text is abundant and should enable discovery

- **Voice & video separate** (vs. same price):
  - Creators can charge differently for content formats
  - Voice message easier to produce than video
  - Video messages might warrant higher minimum
  - Example: Video $5, Voice $2 (creator choice)

### Implementation Notes
- Settings page: Three minimum inputs
  - Voice message minimum (coins)
  - Video message minimum (coins)
  - Text message: N/A (always free)

- Dynamic changes: Minimums can be changed anytime
  - New changes apply to future messages only
  - Old prices don't get retroactively updated
  - No grandfather pricing

- Messaging UX:
  - Before sending: "This will cost [X] coins"
  - User can decline (message not sent)
  - Encourages thoughtful message sending

- Default minimums: Suggested defaults TBD
  - Recommend: Voice $0.50-$2, Video $1-$5
  - Creator can set $0 (free) if desired

### Analytics & Insights
- Track acceptance rates by price point
- Show creators: "Optimal price based on similar creators"
- Experiment notifications: "Try lowering price to increase volume"

### Abuse Prevention
- Minimum price cap (max $99.99 per message, suggest)
- No free tier (prevent discoverability abuse)
- Monitor extreme pricing (fraud detection)

### Dependencies
- Creator dashboard interface
- Message pricing logic
- Transaction calculation engine
- Creator analytics dashboard

---

## Summary Matrix

| Decision | Choice | Impact | Dependencies |
|----------|--------|--------|--------------|
| Voice Call Model | 10min free, caller pays | HIGH | Coin Economy, Billing |
| Video Calling | Premium + Creator exception | HIGH | Auth, Creator account |
| Message Costs | Text free, voice/video paid | MEDIUM | Creator minimums, Payments |
| Age Filtering | Flexible min/max | LOW | DB indexing, UI |
| Call Recording | Not allowed | MEDIUM | Privacy policy, Tech stack |
| Video Messages | 30 sec max | MEDIUM | CDN, Compression |
| Content Library | Private (paying users) | MEDIUM | Payment flow, Storage |
| Notifications | Messages, calls, visits | LOW | Push service, Preferences |
| Undo/Rematch | Unlimited | LOW | Match history, UX |
| Text Indicators | None (no typing/read) | LOW | Message state machine |
| Creator Verification | Photo + ID required | HIGH | ID verification service |
| Commission | 60% to creator, 40% platform | HIGH | Finance, Analytics |
| Creator Payouts | On-demand, $50 minimum | MEDIUM | Payment processor, Dashboard |
| Message Minimums | Separate per type | MEDIUM | Creator dashboard, Pricing logic |

---

**Status**: All design decisions locked and documented
**Next Steps**: Implement technical specifications (Sections 6-8)
**Review Date**: TBD (post-implementation for learnings)

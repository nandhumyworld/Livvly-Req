# User Interaction Flows
**Section**: 05-Feature Specifications
**Last Updated**: 2026-01-20
**Status**: In Progress

## 1. User Onboarding & Profile Setup

### 1.1 Initial Signup Flow
1. User installs app and creates account (email, password, phone)
2. User chooses account type: Regular User or Creator
3. Profile Setup:
   - Profile photo (required)
   - Display name
   - Bio/description
   - Age, gender (privacy options?)
   - Location (city, willing to relocate?)
   - Interests/hobbies (select from tags or free-form)
4. Permissions:
   - Microphone access (for calls)
   - Camera access (if video enabled)
   - Location permissions (for distance filtering)
5. Verification (if required):
   - Photo verification?
   - ID verification?
   - Email/phone verification
6. Welcome tour/tutorial

### 1.2 Creator-Specific Setup
1. After profile setup, creator must:
   - Provide monetization info (payout method: bank, PayPal, etc.)
   - Set minimum coin requirements for interactions
   - Create content library items (intro video, rules, bio, etc.)
   - Customize creator dashboard view

## 2. Discovery & Matching Flow

### 2.1 Browse Matches (On-Demand)
1. User opens "Discovery" or "Browse" tab
2. System displays swipeable card interface:
   - Profile photo(s)
   - Name, age, location (distance)
   - Interests/bio excerpt
   - Verification badge (if creator)
3. User actions:
   - **Swipe Right / Like**: Signal interest
   - **Swipe Left / Pass**: Reject match
   - **Super Like**: Express strong interest (optional)
   - **View Profile**: See full profile before swiping
4. After limited swipes (optional):
   - Show premium upsell or wait time
5. Filters available:
   - Interest tags
   - Distance radius
   - Creator status (show only, hide, or mixed)

### 2.2 Search/Filter Interface
1. User can manually search by:
   - Interests (tag-based)
   - Creator name
   - Location
2. Apply filters:
   - Distance radius
   - Creator status
   - Verification status
3. Sort results by:
   - Relevance
   - Distance (nearest first)
   - Recently joined
   - Most compatible

### 2.3 Profile View
1. Full profile display:
   - All photos
   - Bio/description
   - Interests (tags)
   - Creator info (if applicable)
   - Verification status
   - Online status
2. Actions from profile:
   - Send message
   - Initiate call
   - Undo/Rematch (reject and get new match)
   - Report user
   - Block user

## 3. Messaging Flow

### 3.1 Text Message Exchange
1. User taps "Message" on matched profile
2. Conversation view opens:
   - Message history (if exists)
   - Message input field
   - Audio/video message buttons
3. User types message and sends
4. Recipient receives message with:
   - Sender name/photo
   - Message timestamp
   - Read receipt (optional)
   - Reply option
5. Real-time indicators:
   - Typing indicator when other person types
   - Delivery status (sent, delivered, read)

### 3.2 Voice Message Recording
1. User taps voice message icon
2. Recording interface:
   - Record button (tap to start/stop)
   - Timer showing recording duration
   - Cancel/Send options
   - Playback preview before sending
3. Recipient receives voice message:
   - Play button and duration
   - Can replay, download, or delete
   - Timestamp

### 3.3 Video Message Recording
1. User taps video message icon
2. Video recording interface:
   - Camera preview
   - Record/stop controls
   - Duration counter (max length TBD)
   - Cancel/review options
3. Preview playback before sending
4. Recipient receives video message:
   - Thumbnail preview
   - Play to watch
   - Timestamp

## 4. Voice Call Flow

### 4.1 Initiating a Call
1. User taps "Call" on matched profile
2. Pre-call screen:
   - Recipient's profile
   - "Start Call" button (if caller has sufficient coins/time)
   - Cancel option
   - Coin cost display (if applicable)
3. User taps "Start Call"
4. Ringing state:
   - Show "Calling [Name]..."
   - Cancel call option
   - Recipient receives incoming call notification

### 4.2 Receiving a Call
1. Incoming call notification:
   - Caller's name/photo
   - Accept / Decline buttons
   - Auto-decline after X seconds (optional)
2. Recipient taps "Accept"
3. Both users connected to call

### 4.3 Active Call Interface
1. During call:
   - Large display of other person's name/photo
   - Timer showing call duration
   - Coin deduction counter (if applicable)
   - Mute/unmute button
   - Speaker toggle
   - End call button
   - Optional: Send message while on call?
2. Audio quality indicator (optional)
3. Call controls:
   - Volume adjustment
   - Switch between speaker/headphones

### 4.4 End Call & Feedback
1. Either party taps "End Call"
2. Call ends and both disconnected
3. Post-call screen:
   - Call duration
   - Coin cost (if any)
   - Coin balance updated
4. User prompted for feedback:
   - Rate audio quality
   - Rate overall call experience
   - Report issues if any
5. Option to message or call again

## 5. Video Call Flow (Premium)

### 5.1 Initiating Video Call
1. User taps video call icon on matched profile
2. System checks:
   - User has premium subscription
   - Recipient has premium subscription (or is creator?)
   - Both users online/available
3. Pre-call screen similar to voice call
4. Ringing/connecting state

### 5.2 Active Video Call
1. Video call interface:
   - Large view of recipient's video feed
   - Small selfie view in corner
   - Call timer
   - Mute/unmute audio
   - Turn camera on/off
   - End call button
   - Optional: Switch to voice-only
2. Low-light or no-video handling (show photo instead)

## 6. Creator Dashboard Flow

### 6.1 Accessing Creator Hub
1. Creator taps "Creator Hub" or earnings icon
2. Dashboard overview:
   - Today's earnings
   - This month's earnings
   - Total earnings
   - Recent transactions
   - Quick stats (calls, supporters)

### 6.2 View Detailed Analytics
1. Creator selects "Analytics"
2. Options:
   - View call analytics
   - View supporter analytics
   - View content performance
   - Select date range
3. Detailed charts and metrics
4. Export option

### 6.3 Manage Content Library
1. Creator taps "Content Library"
2. List of uploaded content:
   - Intro video
   - Portfolio items
   - Rules/expectations
   - Other media
3. Actions:
   - Add new content
   - Edit existing content
   - Delete content
   - Change visibility (public/semi-private/private)
   - View content preview

### 6.4 Adjust Settings
1. Creator taps "Settings"
2. Options:
   - Minimum coin requirements (per action)
   - Availability status
   - Auto-reply messages
   - Preferred communication methods
   - Block/blacklist management

## 7. Safety & Reporting Flow

### 7.1 Block User Flow
1. User taps three-dot menu on profile
2. Taps "Block"
3. Confirmation dialog
4. User is blocked:
   - Blocked user cannot see profile
   - Blocked user's messages don't appear
   - Blocked user cannot call
5. User can unblock in Settings > Blocked Users

### 7.2 Report User Flow
1. User taps three-dot menu on profile
2. Taps "Report"
3. Report form:
   - Reason (harassment, fake profile, inappropriate content, etc.)
   - Additional details/comments
   - Option to attach screenshot
4. Submit report
5. Confirmation message
6. Moderation team reviews

### 7.3 Call Quality Feedback
1. After call ends, user prompted:
   - "How was the call quality?"
   - Audio quality rating (1-5 stars or Poor/Fair/Good/Excellent)
   - Any technical issues?
   - Optional: Detail description
2. Submit feedback
3. Feedback recorded for infrastructure/quality improvements

## 8. Account & Settings

### 8.1 Profile Management
1. User can edit:
   - Profile photos
   - Bio/description
   - Interests
   - Location
   - Age/gender
   - Account type (regular to creator migration?)

### 8.2 Privacy & Permissions
1. Settings for:
   - Who can message/call
   - Location visibility
   - Online status visibility
   - Profile visibility (public/private)
   - Data privacy settings

### 8.3 Account Security
1. Password change
2. Two-factor authentication (optional)
3. Login history
4. Linked accounts (social login)

---

## Open Questions for User Flows

1. **Call Initiation Restrictions**:
   - Can only matched users call each other?
   - Can users call before matching (if match exists)?

2. **Coin Deduction Timing**:
   - Charge at call start or end?
   - Handle call drops gracefully?

3. **Video Call Restrictions**:
   - Video calling for all premium users or creators only?
   - Can free users receive video calls from premium?

4. **Undo/Rematch**:
   - Can users rematch unlimited times?
   - Rematch cooldown?

5. **Offline Behavior**:
   - What happens if recipient goes offline during call initiation?
   - Can users queue calls to be made when recipient comes online?

---

*This document is part of the Feature Specifications breakdown.*

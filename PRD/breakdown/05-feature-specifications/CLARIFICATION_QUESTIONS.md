# Clarification Questions - Feature Specifications
**Section**: 05-Feature Specifications
**Status**: In Progress
**Last Updated**: 2026-01-20

## Answered Questions

### Matching & Discovery
✅ **Q1: Matching Algorithm** - Hybrid approach (location, interests, voice chemistry, creator status)
✅ **Q2: Matching Suggestions** - On-demand browsing only (users manually swipe)

### Monetization
✅ **Q3: Voice Call Costs** - Time-based limits (free first N minutes, then coins)
✅ **Q4: Video Calling** - Premium feature (requires subscription)

### Messaging
✅ **Q5: Messaging Types** - Real-time text + pre-recorded voice messages + video messages

### Filtering & Discovery
✅ **Q6: User Filtering** - Interest-based + location-based filtering

### Creator Features
✅ **Q7: Creator Tools** - Analytics dashboard, verified badge, set minimum coins, content library

### Safety
✅ **Q8: Safety Features** - Block/unblock, report user, call quality feedback, undo/rematch

---

## Answered Critical Decisions (Blocking Implementation)

1. ✅ **Voice Call Free Minutes** - 10 minutes
   - Users get 10 free minutes per voice call before coins required
   - Resets per call (not daily)

2. ✅ **Voice Call Cost Model** - Caller Pays
   - Caller pays for calls after free 10 minutes
   - Cost model: 1 coin per minute (TBD in economics section)
   - Same rates for regular users and creators

3. ✅ **Premium Subscription Details** - Free + Premium (2 tiers)
   - Tier system: Free tier + Premium tier
   - Video calling unlocked in Premium tier
   - Text messages always available in both tiers

4. ✅ **Message Costs**
   - Text messages: Always free
   - Voice messages: Coin-based (determined by creator minimum)
   - Video messages: Coin-based (determined by creator minimum)

5. ✅ **Creator Video Calling** - Creators can initiate, users need premium to accept
   - Creators can video call any user (with premium flag for accepting)
   - Users must have Premium subscription to accept video calls
   - Text and voice messaging work regardless of subscription tier

---

### Answered Important Design Questions

6. ✅ **Age Range Filtering** - Flexible min/max age range
   - Users can set custom age preferences
   - No predefined constraints on range

7. ✅ **Call Recording** - No recording allowed
   - Calls cannot be recorded
   - Privacy-first approach
   - No storage or consent complexity

8. ✅ **Video Message Constraints** - 30 seconds max
   - Max length: 30 seconds per video message
   - File size and storage quota: TBD in technical architecture

9. ✅ **Text Message Limits**
   - Character limits: TBD (standard mobile messaging ~160 chars per SMS, or longer for app)
   - Message history retention: TBD in data retention policy

10. ✅ **Verification for Regular Users** - Optional for all users
    - Photo verification: Optional for regular users
    - ID verification: Optional for regular users
    - Creators: Require photo/ID verification (TBD in creator verification process)

---

### Answered Creator-Specific Questions

11. ✅ **Creator Verification Process** - TBD
    - Auto verification criteria: TBD
    - Manual review: Likely required for trust/safety
    - Revocation: Should be possible for violations

12. ✅ **Creator Commission** - 40% platform fee (60% creator revenue)
    - Livvly takes: 40% of creator earnings
    - Creator receives: 60% of earnings
    - Supports platform sustainability while maintaining creator incentive

13. ✅ **Creator Minimum Requirements** - Flexible minimums per message type
    - Creators can set different minimums for:
      - Text messages (minimum coins required)
      - Voice messages (separate minimum)
      - Video messages (separate minimum)
    - Can change minimums dynamically

14. ✅ **Creator Withdrawal/Payouts**
    - Payout frequency: On-demand with minimum threshold
    - Minimum threshold: $50 USD equivalent
    - Payout methods: PayPal, UPI (UP)

---

### Answered Optional/Nice-to-Have Features

15. ✅ **Undo/Rematch Limits** - Unlimited rematch
    - Users can rematch unlimited times
    - No daily limits or cooldown period
    - Encourages exploration and discovery

16. ✅ **Supporter Tiers** - Not in MVP
    - Skip supporter/VIP tiers for now
    - Single subscription model (Free vs. Premium)
    - Can be added in future iterations

17. ✅ **Call Queue/Scheduling** - Real-time calls only
    - No call scheduling feature in MVP
    - No queue system for busy creators
    - Future enhancement opportunity

18. ✅ **Notifications** - Selected notifications
    - New messages: Yes (notify when message arrives)
    - Incoming calls: Yes (alert for incoming calls)
    - Profile visits: Yes (notify of profile views)
    - Other events: Minimal, privacy-focused

19. ✅ **Typing Indicators & Read Receipts** - No indicators
    - No typing indicators shown
    - No read receipts
    - Privacy-focused approach

20. ✅ **Content Library Visibility** - Private to paying users
    - Creator content library items: Private access only
    - Visible to: Matched users who have paid/support the creator
    - Not visible to general public in MVP

---

## Assumptions Made (Pending Confirmation)

- Users must match before calling/messaging each other
- Both users must have active internet/app open for real-time calls
- Voice calls use in-app technology (not PSTN)
- All calls are 1-on-1 (no group calls)
- Coins are only used for monetized features, not core app functionality
- Creators are a separate account type, not a "badge" on regular accounts

---

## Implementation Summary

All 20 clarification questions have been resolved. Key decisions locked in:

| Area | Decision |
|------|----------|
| Voice Call Model | 10 min free, caller pays, 1 coin/min |
| Subscriptions | Free + Premium (2 tiers) |
| Messages | Text free, voice/video paid |
| Creator Video | Can initiate, users need Premium to accept |
| Creator Commission | 60% revenue to creator, 40% platform |
| Payouts | On-demand, $50 minimum, PayPal/UPI |
| Recording | Not allowed |
| Age Filtering | Flexible min/max range |
| Content Library | Private (paying users only) |
| Notifications | New messages, calls, profile visits |
| No Indicators | Typing indicators & read receipts disabled |

## Next Steps

1. ✅ All clarifications complete
2. Generate updated requirements.md with specification details
3. Create design-decisions.md documenting rationale
4. Update technical architecture requirements (Section 6)
5. Update database schema (Section 7)
6. Update API specifications (Section 8)

---

*Document Status: Clarifications Complete - Ready for Specification Generation*
*Last Updated: 2026-01-22*

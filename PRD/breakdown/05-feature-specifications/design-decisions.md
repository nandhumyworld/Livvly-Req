# Design Decisions: Feature Specifications

**Section**: 05-feature-specifications
**Generated**: 2026-01-19
**Status**: Complete

---

## DD-FS-001: Navigation Architecture

**Status**: Accepted

### Context
The app requires navigation between multiple primary sections (Home, Discover, Rooms, Chat, Profile) and secondary features (Wallet, Calls, Creator Dashboard). Need to decide on navigation pattern that balances accessibility with clean UX.

### Decision
Implement **5-tab bottom navigation** with Rooms tab prominently centered, and secondary features accessible through contextual entry points (header icons, profile sections).

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **5-tab bottom nav (chosen)** | Standard pattern, rooms prominence, discoverable | Limited tabs for future features |
| 4-tab with hamburger menu | More room for secondary features | Hamburger hides important features |
| Tab + floating action button | Flexible, modern | Conflicting patterns |

### Rationale
Bottom navigation is the proven pattern for voice social apps (FRND, Tango). Centering Rooms tab emphasizes the core social feature. Secondary features (Wallet, Dashboard) don't need constant visibility and work well as contextual access.

### Consequences
- **Positive**: Clean UI, familiar pattern, rooms emphasis
- **Negative**: Adding 6th primary feature would require redesign
- **Risks**: Users might miss secondary features
- **Mitigation**: Use onboarding tips and contextual prompts

### Related Requirements
REQ-FS-001, REQ-FS-002

### Implementation Notes
- Use Material Design 3 NavigationBar component
- Rooms tab should be larger with distinct styling
- Badge support for unread counts

### References
- Material Design Navigation Guidelines
- Competitor app analysis (FRND, Tango)

---

## DD-FS-002: Authentication Provider Selection

**Status**: Accepted

### Context
Phone OTP authentication is required. Need to select between Firebase Auth, MSG91, or other providers for OTP delivery and verification.

### Decision
Use **Firebase Auth with MSG91 as SMS fallback** for OTP verification.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Firebase Auth + MSG91 (chosen)** | Reliable, auto-read, cost-effective | Two providers to manage |
| Firebase Auth only | Single provider, simpler | Higher SMS costs, reliability varies |
| MSG91 only | Indian-focused, cheaper SMS | No auto-read feature |
| Twilio | Global, reliable | Expensive for India, overkill |

### Rationale
Firebase Auth provides excellent auto-read capability on Android (reduces friction) and handles verification logic. MSG91 as fallback provides cost-effective SMS delivery for Indian numbers when Firebase quota is exceeded or delivery fails.

### Consequences
- **Positive**: Best UX with auto-read, cost optimization
- **Negative**: Two SDKs to maintain, fallback logic needed
- **Risks**: Firebase outages affect auth
- **Mitigation**: MSG91 fallback, retry logic

### Related Requirements
REQ-FS-003, REQ-PV-011

### Implementation Notes
- Firebase for primary OTP handling
- MSG91 via API for manual resend/fallback
- Rate limiting: 3 OTPs per phone per hour
- Consider WhatsApp OTP as third option

### References
- Firebase Auth documentation
- MSG91 OTP API documentation

---

## DD-FS-003: Home Screen Content Strategy

**Status**: Accepted

### Context
Home screen is the primary engagement surface. Need to decide content prioritization and layout strategy to maximize engagement and monetization.

### Decision
Prioritize **Live Rooms at top** (social proof + urgency), followed by **Recommended Profiles grid** with Quick Match FAB.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Rooms first, Profiles second (chosen)** | Live content urgency, social proof | May distract from 1-to-1 calls |
| Profiles first, Rooms section | Direct to monetization (calls) | Misses live engagement |
| Feed-based (mixed content) | Discovery variety | Harder to optimize |
| Personalized sections | Best relevance | Higher complexity |

### Rationale
Live rooms create urgency and social proof ("Join 234 others listening now"). This drives immediate engagement. Recommended profiles below cater to users seeking 1-to-1 connection. Quick Match FAB provides instant action for decisive users.

### Consequences
- **Positive**: Urgency drives action, multiple engagement paths
- **Negative**: Users might only engage with rooms
- **Risks**: Empty state if no live rooms
- **Mitigation**: Show "Start a room" prompt when no rooms live

### Related Requirements
REQ-FS-007, REQ-FS-008, REQ-FS-009

### Implementation Notes
- Lazy load images for performance
- Cache room list with 30-second refresh
- Use skeleton loaders during initial load
- Track section engagement for optimization

### References
- Competitor home screen analysis

---

## DD-FS-004: Live Room Architecture

**Status**: Accepted

### Context
Live voice rooms need to support host, co-hosts (up to 8), and unlimited audience with real-time audio, chat, and gifting. Need to decide on room topology and seat management approach.

### Decision
Implement **Stage-based room model** with fixed seat topology: 1 host seat + 8 co-host seats + unlimited audience listeners.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Fixed stage topology (chosen)** | Clear structure, predictable UX | Less flexible than free-form |
| Free-form speaking (Clubhouse style) | Flexible conversations | Chaotic with many speakers |
| Table-based (8 max participants) | Intimate conversations | Limits audience scale |
| Podcast mode (host + 1 guest) | Simple | Too limiting |

### Rationale
Fixed stage with 9 speaker positions provides structure while allowing rich conversations. Unlimited audience enables viral growth of popular rooms. This matches successful competitors (Bigo Live, Tango).

### Consequences
- **Positive**: Scalable, structured conversations, clear hierarchy
- **Negative**: 8 co-host limit may frustrate some use cases
- **Risks**: Empty seats look awkward
- **Mitigation**: Visual design that gracefully handles empty seats

### Related Requirements
REQ-FS-011, REQ-FS-012, REQ-FS-015

### Implementation Notes
- Use Agora or LiveKit for voice rooms
- Seat states: empty, occupied, muted, speaking
- Host controls: invite, remove, mute all
- Consider "DJ mode" for audio-only entertainment rooms

### References
- Agora Voice Room SDK
- LiveKit room documentation

---

## DD-FS-005: Real-Time Communication Protocol

**Status**: Accepted

### Context
1-to-1 calls and live rooms require real-time audio/video communication. Need to select protocol and infrastructure approach.

### Decision
Use **WebRTC with Agora SDK** for both 1-to-1 calls and live rooms, with TURN server fallback for NAT traversal.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Agora SDK (chosen)** | Production-ready, global infrastructure | Cost per minute, vendor lock-in |
| Pure WebRTC (self-hosted) | Full control, no per-minute cost | High ops burden, quality issues |
| LiveKit (self-hosted) | Open source, flexible | Infrastructure management |
| Twilio Video | Reliable, good docs | Expensive for India market |
| Jitsi | Free, open source | Limited scale, quality varies |

### Rationale
Agora provides production-grade real-time communication with global infrastructure, low latency, and built-in features (echo cancellation, noise suppression). Cost-per-minute is justified by reliability and reduced engineering burden. SDK supports both 1-to-1 and multi-party rooms.

### Consequences
- **Positive**: Reliable quality, fast implementation, global reach
- **Negative**: Per-minute cost impacts margins, vendor dependency
- **Risks**: Agora outages affect all calls
- **Mitigation**: Monitor quality, have LiveKit as backup plan

### Related Requirements
REQ-FS-017, REQ-FS-018, REQ-ES-007

### Implementation Notes
- Agora Voice SDK for audio calls
- Agora Video SDK for video calls (higher bandwidth mode)
- Agora RTM for signaling
- Budget: ~₹0.5-1 per call minute infrastructure cost

### References
- Agora Documentation
- WebRTC specification

---

## DD-FS-006: Payment Architecture

**Status**: Accepted

### Context
Coin purchases require payment processing. Need to decide between app store billing (Google Play) and direct payment gateway (Razorpay) or hybrid approach.

### Decision
Implement **Hybrid payment model**: Razorpay for in-app + Web purchases (primary), with Google Play Billing as compliance option.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| **Hybrid Razorpay + Play (chosen)** | Lower fees, web option, compliant | Complex implementation |
| Google Play Billing only | Simple, compliant | 30% fee reduces margins |
| Razorpay only (in-app) | Low fees (2%) | Policy violation risk |
| Web-only payments | Lowest fees | Friction, app rejection risk |

### Rationale
Hybrid approach maximizes flexibility: Razorpay for users who prefer UPI/wallets (lower fees), web portal for discount seekers (no app store fee), Google Play for users who want convenience. Must be careful about policy compliance.

### Consequences
- **Positive**: Lower average transaction fee, user choice, web discount option
- **Negative**: Complex to implement and maintain, policy compliance burden
- **Risks**: Google Play policy violation could cause app removal
- **Mitigation**: Legal review of implementation, no in-app promotion of web

### Related Requirements
REQ-FS-022, REQ-MA-003

### Implementation Notes
- Razorpay SDK for in-app purchases
- Razorpay Payment Links for web portal
- Google Play Billing Library for Play purchases
- A/B test payment method preferences
- Never explicitly promote web purchases in-app

### References
- Razorpay Integration Guide
- Google Play Billing Documentation
- Google Play Developer Program Policies

---

## Summary

| ID | Decision | Status | Impact |
|----|----------|--------|--------|
| DD-FS-001 | 5-Tab Bottom Navigation | Accepted | Navigation structure |
| DD-FS-002 | Firebase Auth + MSG91 OTP | Accepted | Authentication flow |
| DD-FS-003 | Rooms-First Home Layout | Accepted | Engagement strategy |
| DD-FS-004 | Stage-Based Room Model | Accepted | Room architecture |
| DD-FS-005 | Agora SDK for RTC | Accepted | Voice/video infrastructure |
| DD-FS-006 | Hybrid Payment Model | Accepted | Payment processing |

**Total Design Decisions**: 6
**Accepted**: 6
**Pending**: 0

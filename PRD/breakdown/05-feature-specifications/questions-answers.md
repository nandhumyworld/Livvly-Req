# Questions & Answers: Feature Specifications

**Section**: 05-feature-specifications
**Generated**: 2026-01-19

---

## Session 1: Core Questions

### Q1: Live Voice Rooms MVP Scope

**Question**: The Feature Priority Matrix shows Live Voice Rooms as Phase 2, but they're prominently featured in the home screen design. Should Live Rooms be MVP or Phase 2?

**Rationale**: Live rooms appear in home screen wireframes but are marked Phase 2 in the priority matrix. This inconsistency needs resolution as it significantly impacts MVP scope.

**Answer**: Include in MVP - Live Voice Rooms are core engagement and should be included in MVP with full functionality.

**Impact on Requirements**:
- REQ-FS-008, REQ-FS-011-015 all marked P0 (Critical)
- Live rooms infrastructure (Agora) needed from day 1
- Home screen design with live rooms section is accurate
- Significant scope increase from original PRD matrix
- DD-FS-004 created for room architecture

**Related Requirements**: REQ-FS-011, REQ-FS-012, REQ-FS-013, REQ-FS-014, REQ-FS-015

**Follow-up Actions**:
- Update Technical Architecture to include room infrastructure
- Update Cost Estimation for Agora costs
- Revise Implementation Roadmap

---

### Q2: Video Calls MVP Scope

**Question**: The PRD shows Video Calls as Phase 2. Given that competitors offer video, should video calls be in MVP?

**Rationale**: Video calls are more technically complex and costly than audio, but competitors offer them. Need to determine if MVP should include video.

**Answer**: Include in MVP - Video calls should be included alongside audio calls for competitive parity.

**Impact on Requirements**:
- REQ-FS-018 created as P0 requirement
- Higher infrastructure costs (video bandwidth)
- Agora Video SDK needed in addition to Voice SDK
- Video call rate higher than audio (12 vs 8 coins/min)
- DD-FS-005 updated for video support

**Related Requirements**: REQ-FS-018, REQ-ES-007

**Follow-up Actions**:
- Update cost estimates for video bandwidth
- Define video quality requirements (480p minimum)
- Consider optional video toggle for bandwidth savings

---

### Q3: Chat Features MVP Scope

**Question**: The chat feature shows 1-to-1 and Group chat. Which chat capabilities are needed for MVP?

**Rationale**: Chat can be a significant feature set. Need to scope appropriately for MVP.

**Answer**: 1-to-1 + Room chat - Direct messaging between users plus integrated chat within live rooms.

**Impact on Requirements**:
- REQ-FS-023 created for 1-to-1 direct messaging
- REQ-FS-024 created for room chat integration
- Group chat (separate from rooms) deferred to Phase 2
- Chat infrastructure needed (consider Firebase Realtime DB or Supabase)

**Related Requirements**: REQ-FS-023, REQ-FS-024, REQ-FS-013

**Follow-up Actions**:
- Define chat moderation requirements
- Consider message limits for free users
- Plan group chat for Phase 2

---

## Session 2: Scope Implications Summary

The stakeholder decisions significantly expand MVP scope beyond the PRD's Feature Priority Matrix:

| Feature | PRD Matrix | Stakeholder Decision | Impact |
|---------|------------|---------------------|--------|
| Live Voice Rooms | Phase 2 | MVP | +5 P0 requirements, room infrastructure |
| Video Calls | Phase 2 | MVP | +1 P0 requirement, video bandwidth costs |
| 1-to-1 Chat | Unclear | MVP | +1 P1 requirement |
| Room Chat | Phase 2 | MVP | +1 P1 requirement |

**Net Impact**: 8 additional requirements promoted to MVP, significant infrastructure additions.

---

## Summary

| Question | Answer | Impact Level |
|----------|--------|--------------|
| Live Rooms | Include in MVP | High - Major scope change |
| Video Calls | Include in MVP | High - Infrastructure change |
| Chat Features | 1-to-1 + Room chat | Medium - Additional features |

**Total Questions**: 3
**Decisions Made**: 3
**Open Items**: 0

**Context Updated**: Notes added to context.json reflecting scope changes.

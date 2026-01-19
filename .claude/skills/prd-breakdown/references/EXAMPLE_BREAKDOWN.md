# Example PRD Breakdown

This document shows what a completed PRD breakdown looks like, with sample output from each file type.

---

## Sample Directory Structure

After completing a breakdown for a "TaskFlow" project management app:

```
PRD/
├── TaskFlow_PRD.md                    (original PRD - 1,200 lines)
├── master-index.md                    (navigation guide)
└── breakdown/
    ├── .metadata.json                 (progress tracking)
    ├── context.json                   (project context)
    ├── CHANGELOG.md                   (change history)
    ├── dependency-graph.md            (dependency visualization)
    ├── exports/
    │   └── jira_import_20260118.csv
    ├── 01-executive-summary/
    │   ├── requirements.md
    │   └── questions-answers.md
    ├── 02-feature-specifications/
    │   ├── requirements.md
    │   ├── questions-answers.md
    │   ├── design-decisions.md
    │   └── screen-specs/
    │       ├── dashboard.md
    │       └── task-detail.md
    ├── 03-technical-architecture/
    │   ├── requirements.md
    │   ├── questions-answers.md
    │   ├── design-decisions.md
    │   └── research-notes.md
    └── 04-success-metrics/
        ├── requirements.md
        └── questions-answers.md
```

---

## Sample: requirements.md

```markdown
# Requirements: Executive Summary

> Extracted from: `PRD/TaskFlow_PRD.md` (Lines 1-85)
> Breakdown Date: 2026-01-18
> Last Updated: 2026-01-18

## Overview
TaskFlow is a lightweight project management application targeting small teams (5-20 people) who need simple task tracking without the complexity of enterprise tools like Jira or Monday.com.

## Functional Requirements

### REQ-ES-001: Support Small Team Collaboration
**Priority**: P0 (Critical)
**Type**: Functional
**Description**: Platform must support team-based task management for groups of 5-20 users with real-time collaboration features.
**Rationale**: Core value proposition - serving the underserved small team market
**Acceptance Criteria**:
- [ ] Teams can create shared workspaces
- [ ] Multiple users can view and edit tasks simultaneously
- [ ] Changes sync within 2 seconds across all connected clients
- [ ] Offline changes sync when connection restored

**Dependencies**: REQ-TA-003 (Real-time sync), REQ-FS-012 (Workspace management)
**Related Design Decisions**: DD-TA-001 (Real-time architecture)
**Notes**: User clarified that "small team" means 5-20 people, not including external collaborators

---

### REQ-ES-002: Freemium Business Model
**Priority**: P0 (Critical)
**Type**: Business
**Description**: Offer free tier with upgrade path to paid plans for advanced features
**Rationale**: Acquisition strategy - let teams try before buying
**Acceptance Criteria**:
- [ ] Free tier supports up to 3 users and 1 workspace
- [ ] No feature limitations on free tier except user/workspace count
- [ ] Upgrade flow is seamless (no data migration needed)
- [ ] Downgrade preserves data but restricts access

**Dependencies**: REQ-FS-045 (Billing system)
**Related Design Decisions**: None
**Notes**: Competitor analysis showed 3-user free tier is competitive

---

### REQ-ES-003: Mobile-First Design
**Priority**: P1 (High)
**Type**: Functional
**Description**: Primary interface optimized for mobile with responsive web as secondary
**Rationale**: Target users are often mobile (field workers, consultants)
**Acceptance Criteria**:
- [ ] Native iOS and Android apps available
- [ ] All core features accessible on mobile
- [ ] Touch-optimized UI (minimum 44px tap targets)
- [ ] Works on 4G networks with graceful degradation

**Dependencies**: REQ-FS-001 (Task creation), REQ-FS-005 (Notifications)
**Related Design Decisions**: DD-FS-003 (Mobile navigation pattern)
**Notes**: None

## Open Questions
- Should free tier include integrations (Slack, email)?
- What's the acceptable cold start time for mobile app?
```

---

## Sample: questions-answers.md

```markdown
# Questions & Answers: Executive Summary

> Breakdown Date: 2026-01-18
> Participants: Product Team, Claude PRD Breakdown Skill
> Scope: Clarifying core product direction and business model

## Question Session 1: Core Questions
**Asked**: 2026-01-18 10:15

### Q1: What defines a "small team" in your target market?
**Answer**: Teams of 5-20 people. These are typically startups, small agencies, or departments within larger companies who don't need enterprise features.

**Impact**: This clarifies our scaling requirements (don't need to support 100+ user teams) and pricing tiers (free tier at 3 users makes sense as entry point).

**Related Requirements**: REQ-ES-001, REQ-ES-002
**Follow-up Actions**: Define user limits for each pricing tier

---

### Q2: Why mobile-first over web-first?
**Answer**: Our target users are often not at desks - consultants, field service teams, small agency folks who work from client sites. They need to update tasks on the go.

**Impact**: This affects our architecture decisions - we need offline support and efficient data sync. Native apps become P0, not P1.

**Related Requirements**: REQ-ES-003, REQ-TA-005
**Follow-up Actions**: Research offline-first architectures

---

### Q3: What's the competitive differentiation?
**Answer**: Simplicity. Jira is too complex, Trello is too basic. We're the "just right" option with powerful features that don't require training.

**Impact**: UX design must prioritize discoverability and zero-config setup. Features should have sensible defaults.

**Related Requirements**: REQ-FS-001 through REQ-FS-010
**Follow-up Actions**: Define "zero-config" standards for each feature

## Question Session 2: Follow-up Questions
**Asked**: 2026-01-18 10:25
**Triggered By**: Answers to Session 1

### Q4: For offline support, how long might users be offline?
**Answer**: Typically short periods - subway rides, client sites with poor wifi. Maybe 30 minutes to 2 hours max. They're not working in airplane mode for days.

**Impact**: We don't need full conflict resolution for long-running offline edits. Simple "last write wins" with conflict notification is acceptable.

**Related Requirements**: REQ-TA-005
**Follow-up Actions**: Design conflict notification UX

---

### Q5: Should the free tier have any feature restrictions beyond user count?
**Answer**: No feature restrictions. We want free users to experience the full product so they understand the value before upgrading.

**Impact**: Simplifies development - no feature flags based on plan. Only user/workspace limits.

**Related Requirements**: REQ-ES-002
**Follow-up Actions**: None - simplifies our approach

## Summary
- **Total Questions Asked**: 5
- **Total Answers Received**: 5
- **Key Assumptions Clarified**: Small team = 5-20 users, Mobile-first due to user mobility, No feature gating on free tier
- **New Requirements Identified**: Offline sync capability (wasn't explicit in PRD)
- **Outstanding Issues**: Integration strategy for free tier TBD
```

---

## Sample: design-decisions.md

```markdown
# Design Decisions: Technical Architecture

> Decision Date: 2026-01-18
> Deciders: Engineering Lead, Product Team
> Review Status: Accepted

## DD-TA-001: Real-time Sync Architecture

**Status**: Accepted
**Deciders**: Engineering Lead
**Date**: 2026-01-18

### Context
The product requires real-time collaboration where multiple users can view and edit tasks simultaneously. Changes must sync within 2 seconds.

Constraints:
- Must work on mobile (battery efficiency)
- Must support offline with sync on reconnect
- Target scale: 50-100 concurrent connections per workspace

### Decision
Use WebSocket connections with Operational Transformation (OT) for conflict resolution, backed by Redis for pub/sub across server instances.

### Alternatives Considered

1. **WebSocket + Last Write Wins**
   - Pros: Simpler implementation
   - Cons: Data loss on conflicts, poor UX

2. **WebSocket + CRDT**
   - Pros: Mathematically proven conflict resolution
   - Cons: Higher complexity, larger payloads, overkill for our use case

3. **Polling**
   - Pros: Simplest, works everywhere
   - Cons: Doesn't meet 2-second sync requirement, higher server load

### Rationale
OT provides good conflict resolution without CRDT complexity. Our use case (task editing, not real-time collaborative document editing) doesn't need the guarantees CRDTs provide. WebSocket gives us the real-time capability with reasonable battery impact.

### Consequences

**Positive**:
- Real-time updates meet the 2-second requirement
- Reasonable conflict handling for concurrent edits
- Proven technology (used by Google Docs)

**Negative**:
- Added server complexity for OT transformation
- Need to handle WebSocket connection lifecycle on mobile
- Redis dependency for horizontal scaling

**Risks**:
- OT edge cases in complex edit scenarios
- Mitigation: Extensive testing, graceful fallback to "refresh to sync"

### Related Requirements
- REQ-ES-001: Real-time collaboration
- REQ-TA-003: Sync infrastructure

### Implementation Notes
- Use Socket.io for WebSocket abstraction (handles fallback)
- OT library: ot.js (well-maintained, good docs)
- Redis Pub/Sub for cross-instance communication
```

---

## Sample: research-notes.md

```markdown
# Research Notes: Technical Architecture

> Research Date: 2026-01-18
> Researcher: Claude PRD Breakdown Skill
> Methods: Web search

## External Research (Web)

### Finding 1: Competitor Architecture Analysis
**Query**: "Trello real-time sync architecture"
**Source Name**: Trello Engineering Blog
**URL**: https://tech.trello.com/sync-architecture
**Date Accessed**: 2026-01-18
**Reliability**: High (primary source)

**Finding**: Trello uses WebSockets with a custom sync protocol. They process ~500K WebSocket messages per second at peak. Key insight: they use a "sync delta" approach rather than full state transfer.

**Key Excerpt**: "Each board maintains a monotonically increasing version number. Clients request deltas from their last known version, reducing bandwidth by 90% compared to full sync."

**Relevance**: Validates WebSocket approach. Delta sync is worth implementing for bandwidth efficiency, especially for mobile users.

---

### Finding 2: OT vs CRDT Comparison
**Query**: "operational transformation vs CRDT 2026"
**Source Name**: Martin Kleppmann's Blog
**URL**: https://martin.kleppmann.com/papers/convergence.html
**Date Accessed**: 2026-01-18
**Reliability**: High (academic researcher, CRDT expert)

**Finding**: OT is sufficient for centralized systems where a server can order operations. CRDTs are better for peer-to-peer or systems requiring offline-first with eventual consistency. For client-server with short offline periods, OT is simpler.

**Key Excerpt**: "If you have a central server and users are typically online, OT gives you simpler code and smaller payloads than CRDTs."

**Relevance**: Confirms OT is appropriate choice given our "short offline periods" requirement from Q&A.

## Research Synthesis

Key insights from research:
1. WebSocket + delta sync is industry standard for real-time collaboration
2. OT is appropriate for our centralized, mostly-online use case
3. Competitor (Trello) handles similar scale successfully with this approach

**Impact on Requirements**:
- Added REQ-TA-007 for delta sync implementation
- Confirmed DD-TA-001 (OT over CRDT) decision

## Sources & Citations

### External Sources
- Trello Engineering. "Sync Architecture Deep Dive." Trello Tech Blog, 2025. Accessed 2026-01-18.
- Kleppmann, Martin. "Convergence in Collaborative Systems." Martin Kleppmann's Blog, 2024. Accessed 2026-01-18.
```

---

## Sample: context.json

```json
{
  "project_domain": "project-management",
  "project_name": "TaskFlow",
  "created_at": "2026-01-18T10:00:00Z",
  "last_updated": "2026-01-18T14:30:00Z",

  "stakeholders": [
    {
      "role": "Product",
      "responsibilities": "Feature prioritization, user research"
    },
    {
      "role": "Engineering",
      "responsibilities": "Technical architecture, implementation"
    }
  ],

  "terminology": {
    "Small Team": {
      "definition": "Teams of 5-20 people, typically startups or small agencies",
      "examples": ["Startup engineering team", "Marketing agency"],
      "related_terms": ["Workspace", "User"],
      "defined_in_section": "01-executive-summary"
    },
    "Workspace": {
      "definition": "Shared project container that team members belong to",
      "examples": ["Client project", "Internal ops"],
      "related_terms": ["Team", "Task"],
      "defined_in_section": "02-feature-specifications"
    }
  },

  "domain_context": {
    "industry": "SaaS / Productivity",
    "target_market": "Small teams in SMBs",
    "geographic_focus": ["US", "Europe"],
    "regulatory_context": ["GDPR", "SOC 2"]
  },

  "technical_context": {
    "existing_stack": ["React Native", "Node.js", "PostgreSQL"],
    "constraints": ["Mobile-first", "Offline support required"],
    "integrations": ["Slack", "Email"]
  },

  "preferences": {
    "requirement_id_prefix": "REQ",
    "design_decision_id_prefix": "DD",
    "preferred_priority_scale": "P0-P3",
    "auto_research_enabled": true
  },

  "learned_context": {
    "clarifications": [
      {
        "question": "What defines a 'small team'?",
        "answer": "5-20 people",
        "section": "01-executive-summary",
        "timestamp": "2026-01-18T10:15:00Z"
      }
    ]
  }
}
```

---

## Sample: .metadata.json (Completed)

```json
{
  "prd_source": "PRD/TaskFlow_PRD.md",
  "prd_name": "TaskFlow",
  "total_sections": 4,
  "breakdown_started": "2026-01-18T10:00:00Z",
  "last_modified": "2026-01-18T14:30:00Z",
  "sections": [
    {
      "id": "01-executive-summary",
      "title": "Executive Summary",
      "status": "completed",
      "line_start": 1,
      "line_end": 85,
      "complexity": "simple",
      "started_at": "2026-01-18T10:05:00Z",
      "completed_at": "2026-01-18T10:30:00Z",
      "files_generated": ["requirements.md", "questions-answers.md"],
      "requirement_count": 8,
      "design_decision_count": 0,
      "question_count": 5,
      "research_source_count": 0
    },
    {
      "id": "02-feature-specifications",
      "title": "Feature Specifications",
      "status": "completed",
      "line_start": 86,
      "line_end": 450,
      "complexity": "complex",
      "started_at": "2026-01-18T10:30:00Z",
      "completed_at": "2026-01-18T12:00:00Z",
      "files_generated": ["requirements.md", "questions-answers.md", "design-decisions.md"],
      "requirement_count": 32,
      "design_decision_count": 8,
      "question_count": 15,
      "research_source_count": 0
    },
    {
      "id": "03-technical-architecture",
      "title": "Technical Architecture",
      "status": "completed",
      "line_start": 451,
      "line_end": 680,
      "complexity": "complex",
      "started_at": "2026-01-18T12:00:00Z",
      "completed_at": "2026-01-18T13:45:00Z",
      "files_generated": ["requirements.md", "questions-answers.md", "design-decisions.md", "research-notes.md"],
      "requirement_count": 15,
      "design_decision_count": 6,
      "question_count": 10,
      "research_source_count": 4
    },
    {
      "id": "04-success-metrics",
      "title": "Success Metrics",
      "status": "completed",
      "line_start": 681,
      "line_end": 750,
      "complexity": "simple",
      "started_at": "2026-01-18T13:45:00Z",
      "completed_at": "2026-01-18T14:15:00Z",
      "files_generated": ["requirements.md", "questions-answers.md"],
      "requirement_count": 6,
      "design_decision_count": 0,
      "question_count": 4,
      "research_source_count": 0
    }
  ],
  "overall_progress": {
    "completed": 4,
    "in_progress": 0,
    "pending": 0,
    "percentage": 100.0
  },
  "statistics": {
    "total_requirements": 61,
    "total_design_decisions": 14,
    "total_questions_answered": 34,
    "total_research_sources": 4,
    "breakdown_duration_minutes": 255,
    "average_section_time_minutes": 63.75
  },
  "config": {
    "base_directory": "PRD",
    "git_integration": {
      "enabled": true,
      "auto_commit": true
    },
    "validation": {
      "acceptance_criteria_min": 2,
      "level": "normal"
    }
  }
}
```

---

## Sample: CHANGELOG.md

```markdown
# PRD Breakdown Changelog

> Project: TaskFlow
> Started: 2026-01-18
> Last Updated: 2026-01-18

---

## [2026-01-18]

### Initial Breakdown Complete

#### Sections Completed
1. 01-executive-summary (8 requirements)
2. 02-feature-specifications (32 requirements, 8 design decisions)
3. 03-technical-architecture (15 requirements, 6 design decisions)
4. 04-success-metrics (6 requirements)

#### Statistics
- Total Requirements: 61
- Total Design Decisions: 14
- Total Questions Answered: 34
- Total Research Sources: 4

---

## Changelog Statistics

| Metric | Count |
|--------|-------|
| Total Entries | 1 |
| Requirements Added | 61 |
| Requirements Modified | 0 |
| Requirements Deprecated | 0 |
| Design Decisions Added | 14 |
| Sections Redone | 0 |
```

---

## Summary

This example demonstrates:

1. **Complete output structure** with all file types
2. **Realistic content** showing what each file contains
3. **Proper formatting** following the templates
4. **Cross-references** between requirements and design decisions
5. **Context persistence** capturing project-specific terminology
6. **Metadata tracking** for progress and statistics

Use this as a reference when reviewing your own breakdown output.

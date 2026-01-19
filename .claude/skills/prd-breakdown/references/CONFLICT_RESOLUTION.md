# Conflict Resolution Strategy

This document describes how to detect, document, and resolve conflicts between requirements across different sections of a PRD breakdown.

---

## Overview

As PRDs are broken down into modular requirements, conflicts can arise between:
- Requirements in different sections
- Design decisions that contradict each other
- Implementation constraints that are mutually exclusive
- Stakeholder priorities that conflict

This guide provides a systematic approach to identifying and resolving these conflicts.

---

## Types of Conflicts

### 1. Direct Contradictions
Two requirements explicitly state opposite behaviors.

**Example**:
- REQ-FS-012: "Users can delete their account immediately"
- REQ-LC-005: "User data must be retained for 30 days after deletion request"

### 2. Resource Conflicts
Multiple requirements compete for the same limited resource.

**Example**:
- REQ-TA-008: "System must handle 50K concurrent users"
- REQ-COE-003: "Infrastructure cost must not exceed $5K/month"

### 3. Priority Conflicts
Different sections assign conflicting priorities to the same concern.

**Example**:
- Feature Specs: P0 - Voice quality must be crystal clear
- Cost Estimation: P1 - Use cheapest voice provider

### 4. Timeline Conflicts
Requirements have incompatible delivery schedules.

**Example**:
- REQ-IR-001: "MVP launch in 3 months"
- REQ-FS-045: "Full analytics dashboard required for launch" (6 month effort)

### 5. Stakeholder Conflicts
Different stakeholders have opposing requirements.

**Example**:
- Product: "Maximize user engagement time"
- Legal: "Implement mandatory usage breaks for minors"

---

## Conflict Detection

### Automatic Detection During Processing

```python
def detect_conflicts(all_requirements, all_design_decisions):
    """Scan for potential conflicts across all sections"""

    conflicts = []

    # Check 1: Direct keyword contradictions
    contradiction_pairs = [
        ('must', 'must not'),
        ('required', 'optional'),
        ('immediately', 'delayed'),
        ('always', 'never'),
        ('all users', 'some users'),
        ('real-time', 'batch')
    ]

    for req_a in all_requirements:
        for req_b in all_requirements:
            if req_a['id'] != req_b['id']:
                if has_contradiction(req_a, req_b, contradiction_pairs):
                    conflicts.append({
                        'type': 'direct_contradiction',
                        'requirement_a': req_a['id'],
                        'requirement_b': req_b['id'],
                        'reason': 'Contradictory keywords detected',
                        'severity': 'high'
                    })

    # Check 2: Same entity, different constraints
    entities = extract_entities(all_requirements)  # Users, Payments, Calls, etc.
    for entity in entities:
        related_reqs = filter_by_entity(all_requirements, entity)
        constraints = extract_constraints(related_reqs)
        if has_conflicting_constraints(constraints):
            conflicts.append({
                'type': 'constraint_conflict',
                'entity': entity,
                'requirements': [r['id'] for r in related_reqs],
                'reason': f'Conflicting constraints on {entity}',
                'severity': 'medium'
            })

    # Check 3: Priority misalignment
    priority_groups = group_by_feature(all_requirements)
    for feature, reqs in priority_groups.items():
        priorities = [r['priority'] for r in reqs]
        if has_priority_conflict(priorities):
            conflicts.append({
                'type': 'priority_conflict',
                'feature': feature,
                'requirements': [r['id'] for r in reqs],
                'reason': 'Same feature has conflicting priorities across sections',
                'severity': 'medium'
            })

    return conflicts
```

### Manual Conflict Flagging

During Q&A sessions, flag potential conflicts:

```python
def flag_potential_conflict(section_id, requirement_id, related_req_id, reason):
    """Manually flag a potential conflict for review"""

    conflict = {
        'flagged_by': 'user',
        'flagged_at': current_timestamp(),
        'type': 'potential_conflict',
        'requirement_a': requirement_id,
        'requirement_b': related_req_id,
        'reason': reason,
        'status': 'pending_review',
        'resolution': None
    }

    append_to_conflicts_log(conflict)
    add_to_master_index_open_questions(conflict)
```

---

## Conflict Documentation

### Conflicts Log File

Location: `PRD/breakdown/conflicts.json`

```json
{
  "detected_at": "2026-01-18T14:00:00Z",
  "last_updated": "2026-01-18T16:30:00Z",
  "conflicts": [
    {
      "id": "CONFLICT-001",
      "type": "direct_contradiction",
      "status": "resolved",
      "severity": "high",
      "requirement_a": {
        "id": "REQ-FS-012",
        "section": "05-feature-specifications",
        "text": "Users can delete their account immediately"
      },
      "requirement_b": {
        "id": "REQ-LC-005",
        "section": "12-legal-compliance",
        "text": "User data must be retained for 30 days after deletion request"
      },
      "detected_by": "automatic",
      "detected_at": "2026-01-18T14:00:00Z",
      "resolution": {
        "decision": "modify_both",
        "resolved_by": "Product + Legal",
        "resolved_at": "2026-01-18T16:30:00Z",
        "changes": [
          {
            "requirement": "REQ-FS-012",
            "old_text": "Users can delete their account immediately",
            "new_text": "Users can request account deletion, which marks the account as 'pending deletion'"
          },
          {
            "requirement": "REQ-LC-005",
            "old_text": "User data must be retained for 30 days after deletion request",
            "new_text": "User data must be retained for 30 days after deletion request, then permanently deleted"
          }
        ],
        "rationale": "Legal requires data retention period; user still gets immediate 'deleted' experience"
      }
    }
  ],
  "statistics": {
    "total_detected": 5,
    "resolved": 3,
    "pending": 2,
    "by_type": {
      "direct_contradiction": 2,
      "resource_conflict": 1,
      "priority_conflict": 1,
      "timeline_conflict": 1
    }
  }
}
```

### Conflict in Master Index

Add conflicts section to `master-index.md`:

```markdown
## Known Conflicts & Resolutions

### Resolved Conflicts

#### CONFLICT-001: Account Deletion vs Data Retention
**Status**: Resolved
**Type**: Direct Contradiction
**Severity**: High

**Conflicting Requirements**:
- REQ-FS-012 (Feature Specs): "Users can delete their account immediately"
- REQ-LC-005 (Legal Compliance): "User data must be retained for 30 days"

**Resolution**: Modified both requirements to allow immediate account marking as "deleted" while retaining data for 30 days. See updated requirements.

**Resolved By**: Product + Legal
**Date**: 2026-01-18

---

### Pending Conflicts

#### CONFLICT-002: Performance vs Cost
**Status**: Pending Review
**Type**: Resource Conflict
**Severity**: Medium

**Conflicting Requirements**:
- REQ-TA-008: "50K concurrent users support"
- REQ-COE-003: "$5K/month infrastructure budget"

**Escalation**: Requires executive decision on budget vs scale tradeoff

**Owner**: CTO
**Due**: 2026-01-25
```

---

## Resolution Process

### Resolution Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                    CONFLICT DETECTED                         │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ Step 1: CLASSIFY                                             │
│ - Determine conflict type                                    │
│ - Assess severity (high/medium/low)                          │
│ - Identify affected sections                                 │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ Step 2: PRIORITIZE                                           │
│ - Apply priority hierarchy                                   │
│ - Identify decision owner                                    │
│ - Set resolution deadline                                    │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ Step 3: RESOLVE                                              │
│ - Choose resolution strategy                                 │
│ - Document decision rationale                                │
│ - Update affected requirements                               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ Step 4: VALIDATE                                             │
│ - Verify no new conflicts created                            │
│ - Update cross-references                                    │
│ - Add to changelog                                           │
└─────────────────────────────────────────────────────────────┘
```

### Priority Hierarchy

When requirements conflict, apply this priority hierarchy:

```
P0 (Critical) > P1 (High) > P2 (Medium) > P3 (Low)

Within same priority level:
  Legal/Compliance > Security > Core Functionality > Nice-to-have

Within same category:
  Business-critical > User-facing > Internal > Future
```

### Resolution Strategies

#### Strategy 1: Modify One Requirement
**When**: One requirement is clearly more important or correct

```python
resolution = {
    'strategy': 'modify_one',
    'keep': 'REQ-LC-005',
    'modify': 'REQ-FS-012',
    'rationale': 'Legal compliance takes precedence over UX preference'
}
```

#### Strategy 2: Modify Both Requirements
**When**: Both requirements have valid concerns that need accommodation

```python
resolution = {
    'strategy': 'modify_both',
    'changes': [
        {'requirement': 'REQ-FS-012', 'change': 'Soften immediate deletion language'},
        {'requirement': 'REQ-LC-005', 'change': 'Clarify what data is retained'}
    ],
    'rationale': 'Balance UX and compliance needs'
}
```

#### Strategy 3: Create Dependency
**When**: Requirements can coexist with proper sequencing

```python
resolution = {
    'strategy': 'create_dependency',
    'primary': 'REQ-FS-012',
    'depends_on': 'REQ-LC-005',
    'note': 'Account deletion triggers retention period, then permanent deletion',
    'rationale': 'Sequential execution resolves conflict'
}
```

#### Strategy 4: Add Constraint/Condition
**When**: Requirements apply to different contexts

```python
resolution = {
    'strategy': 'add_condition',
    'requirement': 'REQ-FS-012',
    'condition': 'For users in regions without data retention laws',
    'alternative': 'REQ-LC-005 applies in regulated regions',
    'rationale': 'Different jurisdictions have different rules'
}
```

#### Strategy 5: Deprecate One Requirement
**When**: One requirement is no longer valid

```python
resolution = {
    'strategy': 'deprecate',
    'deprecated': 'REQ-FS-012',
    'reason': 'Superseded by compliance requirement',
    'replacement': 'REQ-LC-005',
    'rationale': 'Original requirement was created without legal review'
}
```

#### Strategy 6: Escalate to Stakeholder
**When**: Resolution requires business/executive decision

```python
resolution = {
    'strategy': 'escalate',
    'escalated_to': 'CTO',
    'question': 'Should we prioritize scale (50K users) or cost ($5K/month)?',
    'options': [
        'Option A: Increase budget to $10K/month for full scale',
        'Option B: Reduce target to 25K users within budget',
        'Option C: Phased approach - start small, scale with revenue'
    ],
    'deadline': '2026-01-25',
    'rationale': 'Business tradeoff requires executive decision'
}
```

---

## Stakeholder Escalation Paths

### Escalation Matrix

| Conflict Type | Primary Owner | Escalation Path |
|---------------|---------------|-----------------|
| Feature vs Legal | Legal | Legal Lead → CLO |
| Feature vs Security | Security | Security Lead → CISO |
| Cost vs Performance | Engineering | Tech Lead → CTO → CEO |
| Timeline vs Scope | Product | PM → Product Lead → CPO |
| User Experience vs Compliance | Product + Legal | Joint review → Executive |

### Escalation Template

```markdown
## Escalation Request: [CONFLICT-ID]

**Date**: [Date]
**Requester**: [Name/Role]
**Priority**: [Urgent/High/Medium]

### Conflict Summary
[Brief description of the conflicting requirements]

### Requirements Involved
- [REQ-XX-001]: [Brief text]
- [REQ-YY-002]: [Brief text]

### Options for Resolution
1. **Option A**: [Description]
   - Pros: [List]
   - Cons: [List]
   - Impact: [Scope, timeline, cost]

2. **Option B**: [Description]
   - Pros: [List]
   - Cons: [List]
   - Impact: [Scope, timeline, cost]

### Recommendation
[Your recommended approach and rationale]

### Decision Needed By
[Date]

### Decision
[To be filled by approver]

**Decision**: [A/B/Other]
**Rationale**: [Why this decision]
**Approved By**: [Name/Role]
**Date**: [Date]
```

---

## Integration with Breakdown Process

### During Section Processing

```python
def process_section_with_conflict_check(section, prd_content, metadata, existing_requirements):
    """Process section while checking for conflicts with existing requirements"""

    # ... normal section processing ...

    # After extracting requirements
    new_requirements = extract_requirements(section, prd_content, answers)

    # Check for conflicts with existing requirements
    conflicts = check_conflicts_with_existing(new_requirements, existing_requirements)

    if conflicts:
        show_conflicts_to_user(conflicts)

        for conflict in conflicts:
            resolution = ask_user_for_resolution(conflict)
            apply_resolution(conflict, resolution)
            log_conflict(conflict, resolution)

    return new_requirements, conflicts
```

### During Finalization

```python
def finalize_with_conflict_report(metadata):
    """Include conflict summary in finalization"""

    # Load all conflicts
    conflicts = load_conflicts()

    pending_conflicts = [c for c in conflicts if c['status'] == 'pending']

    if pending_conflicts:
        show_warning(f"{len(pending_conflicts)} unresolved conflicts remain")
        show_conflicts_summary(pending_conflicts)

        if not confirm("Continue with unresolved conflicts?"):
            return False

    # Add conflicts section to master index
    add_conflicts_to_master_index(conflicts)

    return True
```

---

## Validation Check

Add to `/prd-breakdown-validate`:

```python
def validate_conflicts(section_id=None):
    """Check for unresolved conflicts"""

    conflicts = load_conflicts()

    if section_id:
        # Filter to conflicts involving this section
        conflicts = [c for c in conflicts
                    if section_id in c['requirement_a']['section']
                    or section_id in c['requirement_b']['section']]

    unresolved = [c for c in conflicts if c['status'] != 'resolved']

    if unresolved:
        return {
            'valid': False,
            'violations': [
                {
                    'type': 'unresolved_conflict',
                    'conflict_id': c['id'],
                    'issue': f"Conflict between {c['requirement_a']['id']} and {c['requirement_b']['id']} not resolved"
                }
                for c in unresolved
            ]
        }

    return {'valid': True, 'violations': []}
```

---

## Best Practices

### 1. Early Detection
- Run conflict checks after each section, not just at the end
- Use terminology from context.json to detect semantic conflicts

### 2. Document Everything
- Record the rationale for every resolution
- Keep history of how conflicts were resolved for future reference

### 3. Involve Stakeholders Early
- Don't let conflicts accumulate - resolve as they're found
- Escalate promptly when needed

### 4. Prevent Future Conflicts
- After resolution, update context.json with learned constraints
- Add validation rules to prevent similar conflicts

### 5. Review Periodically
- Revisit resolved conflicts if PRD changes significantly
- Ensure resolutions are still valid after updates

# Update PRD Section

**Trigger**: `/prd-breakdown-update <section-id>`

## Purpose
Make targeted updates to an already broken-down section (add requirements, research, or design decisions).

---

## Parameters
Required: `<section-id>` (e.g., "05-feature-specifications", "02-market-analysis")

**Valid section IDs**:
```
01-executive-summary, 02-market-analysis, 03-product-vision, 04-revenue-model,
05-feature-specifications, 06-technical-architecture, 07-database-schema,
08-api-specifications, 09-coin-economy, 10-creator-payout, 11-automation-workflows,
12-legal-compliance, 13-implementation-roadmap, 14-cost-estimation,
15-success-metrics
```

---

## Workflow

### Step 1: Validate Section ID
- Check if section-id exists in metadata
- If not found: Error "Section not found. Use /prd-breakdown-status to see valid IDs"
- Load section metadata and existing files

### Step 2: Display Current Section State
```
╔═══════════════════════════════════════════════════════╗
║     UPDATE SECTION: Feature Specifications            ║
╚═══════════════════════════════════════════════════════╝

Current State:
  Status: completed
  Last Updated: 2026-01-18 10:35 AM
  Requirements: 45
  Design Decisions: 12
  Questions Asked: 28
  Research Sources: 8

Files:
  • requirements.md (45 requirements)
  • questions-answers.md (28 Q&A)
  • research-notes.md (8 sources)
  • design-decisions.md (12 decisions)
```

### Step 3: Ask What to Update
```
What would you like to do?

1. Add new requirements
2. Modify existing requirement
3. Add research findings
4. Update design decisions
5. View current content
6. Cancel

Choose option:
```

### Step 4: Execute Update (Based on Selection)

#### Option 1: Add New Requirements
```
Adding new requirements to Feature Specifications...

What requirement needs to be added?
Description: [User provides]

What priority? (P0/P1/P2/P3): [User chooses]
Type? (Functional/Non-Functional/Technical/Business): [User chooses]

Acceptance Criteria:
- [User enters criteria - can add multiple]
- ...

Dependencies? (e.g., REQ-FS-001, REQ-API-005): [User enters or "None"]

Creating new requirement: REQ-FS-046
```

#### Option 2: Modify Existing Requirement
```
Which requirement to modify? (Enter ID, e.g., REQ-FS-015):
> REQ-FS-015

Current content:
---
REQ-FS-015: [Title]
Description: [Current description]
---

What to change?
1. Description
2. Priority
3. Acceptance Criteria
4. Dependencies
5. Notes
6. Cancel

Choose:
```

#### Option 3: Add Research Findings
```
Adding research to Feature Specifications...

What research topic? [User describes]

Is this internal (codebase) or external (web) research?
1. Internal - Search codebase for existing implementations
2. External - Search web for validation/alternatives
3. Both
4. Cancel

Proceeding with external research...

[Research is conducted - see RESEARCH_APPROACH.md]

Add these findings? (yes/no):
---
[Finding details]
---
```

#### Option 4: Update Design Decisions
```
Updating design decisions for Feature Specifications...

Which design decision to update?
Available:
  1. DD-FS-001: [Title]
  2. DD-FS-002: [Title]
  ...

Or add new decision? (new)

Choose:
```

#### Option 5: View Current Content
Display selected file (requirements.md, questions-answers.md, etc.) for review.

### Step 5: Confirm Changes
```
Changes to add:
  • 1 new requirement (REQ-FS-046)
  • 3 research findings

Proceed? (yes/no):
```

### Step 6: Update Metadata
After changes are saved:
- Increment appropriate counts:
  - `requirement_count` if requirements added
  - `research_source_count` if research added
  - `design_decision_count` if decisions added
- Update `last_modified` timestamp
- Save metadata

### Step 7: Confirmation
```
✓ Updated section 05-feature-specifications

Changes:
  • Added 1 new requirement (46 total)
  • Added 3 research findings (11 total)

Last Updated: 2026-01-18 14:42 PM

View files: PRD/breakdown/05-feature-specifications/
```

---

## Examples

### Example 1: Add New Requirement
```
/prd-breakdown-update 05-feature-specifications

[follows workflow above]

New requirement added: REQ-FS-046
Description: Users can block other users
Priority: P1
Acceptance Criteria:
  - Blocked users cannot view profile
  - Blocked users cannot call or message
  - User can unblock anytime

Dependencies: REQ-FS-020 (User profiles)
```

### Example 2: Add Compliance Research
```
/prd-breakdown-update 12-legal-compliance

[follows workflow above]

Added research finding: DPDPA Requirements for India Users
Source: Government of India Ministry
URL: [official source]
Key excerpt: "[regulation text]"
Implication: Need consent management module
```

---

## References
- **WORKFLOW.md**: Section 4 (Update Command)
- **TEMPLATES.md**: File format reference
- **RESEARCH_APPROACH.md**: How to conduct research

---

## Related Commands
- `/prd-breakdown-redo` - Archive and restart section from scratch
- `/prd-breakdown-status` - View all sections for selection
- `/prd-breakdown-resume` - Continue main breakdown

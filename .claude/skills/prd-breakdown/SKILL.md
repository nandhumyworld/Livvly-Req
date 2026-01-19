---
name: prd-breakdown
description: |
  Transform monolithic Product Requirement Documents into organized, modular requirement files through systematic breakdown, interactive questioning, and comprehensive research.

  Eliminates assumptions in requirements by creating self-contained documentation that minimizes context window usage during development. Supports batch processing, resumable workflows, and flexible command-based operations.

  Trigger phrases: "break down PRD", "PRD breakdown", "organize requirements", "structure PRD", "prd-breakdown"

  Commands:
  - /prd-breakdown-start: Initialize new breakdown
  - /prd-breakdown-batch: Process multiple sections with upfront questions
  - /prd-breakdown-resume: Continue from last incomplete section
  - /prd-breakdown-update <section-id>: Targeted updates to existing section
  - /prd-breakdown-redo <section-id>: Archive and restart section breakdown
  - /prd-breakdown-status: Display progress dashboard
---

# PRD Breakdown Skill

## Overview

This skill transforms monolithic Product Requirement Documents (like a 3,000+ line PRD) into organized, detailed requirement files through:

1. **Systematic Section Breakdown**: Parse PRD structure, process section-by-section
2. **Interactive Questioning**: Progressive approach - ask core questions, follow-ups, then gap analysis
3. **Comprehensive Research**: Internal (codebase) and external (web) research to validate assumptions
4. **Modular Output**: Conditional file creation with clear organization and navigation

**Goal**: Eliminate assumptions in requirements by creating self-contained documentation that minimizes context window usage during development.

---

## Commands

### 1. `/prd-breakdown-start`
Initializes a new PRD breakdown process:
- Locates PRD file (auto-detect in PRD/ or ask for path)
- Parses structure (extracts sections from headers)
- Creates breakdown/ directory and .metadata.json
- Begins section-by-section breakdown with progressive questioning

### 2. `/prd-breakdown-batch`
Process multiple sections efficiently with upfront question collection:
- Ask which sections to process (default: all pending)
- Collect ALL questions for ALL selected sections upfront
- Display questions grouped by section
- User answers all questions in single session
- Process all sections sequentially using stored answers

### 3. `/prd-breakdown-resume`
Continue from last incomplete section:
- Loads .metadata.json
- Finds first pending or in_progress section
- Displays progress summary
- Continues breakdown from resumption point

### 4. `/prd-breakdown-update <section-id>`
Make targeted updates to already broken-down sections:
- Example: `/prd-breakdown-update 05-feature-specifications`
- Load existing section files
- Ask what to change (add requirements, research, design decisions)
- Make targeted edits and update timestamps

### 5. `/prd-breakdown-redo <section-id>`
Archive existing breakdown and restart from scratch:
- Example: `/prd-breakdown-redo 01-executive-summary`
- Archive existing files with timestamp suffix
- Reset section to pending status
- Restart breakdown

### 6. `/prd-breakdown-status`
Display progress dashboard with:
- Overall completion percentage
- Completed sections with timestamps
- In-progress section (if any)
- Pending sections with estimated time
- Statistics: total requirements, design decisions, questions, research sources
- Next action suggestion

---

## Workflow Overview

### Phase 1: Initialization
1. **Locate PRD**: Search PRD/ directory or ask user for path
2. **Parse Structure**: Extract headers and determine section boundaries with line ranges
3. **Estimate Complexity**: Classify each section (simple, moderate, complex)
4. **Create Infrastructure**:
   - Create `PRD/breakdown/` directory
   - Initialize `.metadata.json` with all sections set to "pending"
5. **Present Overview**: Show user total sections and estimated time, get confirmation

### Phase 2: Section Breakdown Loop (Core Algorithm)

For each section, follow this three-phase approach:

#### Phase 2a: Progressive Questioning
1. **Core Questions** (2-3 high-level questions):
   - Ask clarifying questions about ambiguous terms
   - Validate quantitative claims
   - Identify key constraints
   - Wait for answers before proceeding

2. **Follow-up Questions** (based on core answers):
   - Dynamically generate follow-ups based on user responses
   - Example: If user mentions "real-time updates", ask about acceptable latency
   - Don't ask predetermined lists - adapt to their answers

3. **Gap Analysis Questions** (identify missing information):
   - Review PRD text and answers for assumptions
   - Ask: "I don't see specification for [X]. Should we define that?"
   - Surface implicit requirements

#### Phase 2b: Research Phase
Determine if research is needed:
- **Internal Research**: If PRD mentions existing implementation (e.g., "similar to login flow")
- **External Research**: If PRD makes factual claims or specifies technology without rationale
- Conduct research and document findings with complete citations

#### Phase 2c: Generation Phase
1. **Extract Requirements**: Convert answers, PRD text, and research into atomic, testable requirements
   - Format: REQ-{ABBREV}-{NUM} with priority, type, acceptance criteria
2. **Create requirements.md**: Always created - contains all extracted requirements
3. **Conditional File Creation**:
   - **questions-answers.md**: Created IF questions were asked (almost always)
   - **research-notes.md**: Created IF research was conducted
   - **design-decisions.md**: Created ONLY for technical sections (05, 06, 07, 08)
4. **Update Metadata**: Mark section as completed, record counts and timestamps

### Phase 3: Finalization
1. **Master Index Generation**: Create navigation guide with:
   - Quick stats (total requirements, design decisions, questions, sources)
   - Navigation by phase (Foundation, Core Development, Scaling)
   - Task-based navigation (When implementing authentication, payment, etc.)
   - Open questions and future work

2. **Statistics Calculation**:
   - Total requirements across all sections
   - Total design decisions
   - Total questions answered
   - Total research sources cited

3. **Metadata Finalization**: Mark all sections as completed, record overall statistics

---

## Progressive Questioning Strategy

The skill uses a **three-phase progressive questioning approach** rather than asking a predetermined list:

### Phase 1: Core Questions (2-3 questions)
Ask high-level clarifying questions about the section:
- What defines ambiguous terms? ("active user", "real-time", "high performance")
- Where do quantitative claims come from? ("50-60% revenue share" - which competitors?)
- What constraints are implied? ("T+1 payout" - what time zone? cutoff time?)

**Action**: Ask these upfront, wait for answers before proceeding.

### Phase 2: Follow-up Questions (Based on Core Answers)
Analyze user responses and ask targeted follow-ups:
- If they mention "daily active users", ask about measurement method
- If they mention "offline support", ask about sync strategy
- If they mention specific technology, ask about rationale

**Action**: Generate 2-4 follow-ups based on their core answers. Don't use a predetermined list.

### Phase 3: Gap Analysis Questions (Identify Missing Info)
Review PRD text and answers for assumptions and gaps:
- Check for missing: loading states, empty states, error handling, permissions
- Ask: "I don't see specification for [X]. Should we define that?"

**Action**: Ask only questions about genuine gaps, not hypothetical "nice-to-haves".

---

## File Creation Rules

### Always Created
- **requirements.md**: One per section - contains all extracted requirements with priorities, acceptance criteria, and dependencies

### Created IF Conditions Met
- **questions-answers.md**: Created if any questions were asked (almost always yes)
  - Documents all Q&A sessions with impact analysis
  - Shows how answers informed requirements

- **research-notes.md**: Created if research was conducted
  - Internal research: codebase findings with locations and relevance
  - External research: citations with sources, URLs, dates, reliability assessments

- **design-decisions.md**: Created ONLY for technical sections (05, 06, 07, 08)
  - Feature Specifications, Technical Architecture, Database Schema, API Specifications
  - Not created for business, implementation, or operational sections

- **screen-specs/** (sub-folder): Created for Feature Specifications if multiple screens documented
  - One file per major screen/user journey
  - Example: onboarding.md, home.md, live-room.md

---

## Requirement ID Convention

### Requirement ID Format
`REQ-{SECTION_ABBREV}-{NUM}` (zero-padded to 3 digits)

**Examples**:
- REQ-FS-023 (Feature Specifications, requirement #23)
- REQ-MA-008 (Market Analysis, requirement #8)
- REQ-TA-012 (Technical Architecture, requirement #12)

**Section Abbreviations**:
- ES: Executive Summary
- MA: Market Analysis
- PV: Product Vision
- RM: Revenue Model
- FS: Feature Specifications
- TA: Technical Architecture
- DB: Database Schema
- API: API Specifications
- CE: Coin Economy
- CP: Creator Payout
- AW: Automation Workflows
- LC: Legal Compliance
- IR: Implementation Roadmap
- COE: Cost Estimation
- SM: Success Metrics

### Design Decision ID Format
`DD-{SECTION_ABBREV}-{NUM}` (zero-padded to 3 digits)

**Examples**:
- DD-TA-005 (Technical Architecture, decision #5)
- DD-FS-012 (Feature Specifications, decision #12)

---

## Output Structure

The skill creates this directory structure:

```
PRD/
├── LIVVLY_Voice_Dating_App_PRD.md    (original, preserved)
├── master-index.md                    (generated at completion)
└── breakdown/
    ├── .metadata.json                 (progress tracking)
    ├── 01-executive-summary/
    │   ├── requirements.md            (always)
    │   ├── questions-answers.md       (if questions asked)
    │   ├── research-notes.md          (if research conducted)
    │   └── design-decisions.md        (NOT created for business sections)
    ├── 05-feature-specifications/
    │   ├── requirements.md
    │   ├── questions-answers.md
    │   ├── research-notes.md
    │   ├── design-decisions.md        (YES - technical section)
    │   └── screen-specs/              (if multiple screens)
    │       ├── onboarding.md
    │       ├── home.md
    │       └── live-room.md
    ├── 06-technical-architecture/
    ├── 07-database-schema/
    ├── 08-api-specifications/
    ├── 09-coin-economy/
    ├── 10-creator-payout/
    ├── 11-automation-workflows/
    ├── 12-legal-compliance/
    ├── 13-implementation-roadmap/
    ├── 14-cost-estimation/
    ├── 02-market-analysis/
    ├── 03-product-vision/
    ├── 04-revenue-model/
    └── 15-success-metrics/
```

---

## Section Categorization

### Business Sections (Simpler, Market-Focused)
- 01-executive-summary
- 02-market-analysis
- 03-product-vision
- 04-revenue-model
- 15-success-metrics

**Processing**: Standard progressive questioning, may require market research

### Technical Sections (Complex, Requires Design Decisions)
- 05-feature-specifications
- 06-technical-architecture
- 07-database-schema
- 08-api-specifications

**Processing**: Standard + design decisions file + technical follow-ups

### Implementation Sections (Planning-Focused)
- 09-coin-economy
- 10-creator-payout
- 11-automation-workflows
- 13-implementation-roadmap
- 14-cost-estimation

**Processing**: Standard progressive questioning, timeline/resource validation

### Operational Sections (Compliance-Focused)
- 12-legal-compliance

**Processing**: Standard + compliance/regulation research

---

## Critical Guidelines

### 1. Atomic Requirements
Each requirement must be:
- **Single responsibility**: One testable behavior per requirement
- **Independent**: Minimizes dependencies
- **Testable**: Clear acceptance criteria
- **Prioritized**: P0 (Critical), P1 (High), P2 (Medium), P3 (Low)

### 2. No Assumptions
Every ambiguous term or claim in the PRD must be clarified:
- Ask: "What do you mean by [X]?"
- Validate: "Is [claim] based on [data/evidence]?"
- Document: Record answer and rationale

### 3. Complete Citations
All research findings must include:
- **Internal**: File path, line numbers, code snippet if relevant
- **External**: Source name, URL, date accessed, reliability (High/Medium/Low), direct quote

### 4. Conditional File Creation
Don't create files with empty content:
- If no questions asked for section: Don't create questions-answers.md
- If no research conducted: Don't create research-notes.md
- If non-technical section: Don't create design-decisions.md

---

## Error Handling

### Missing or Invalid PRD
- **Error**: "No PRD file found in PRD/ directory"
- **Action**: Ask user for file path, validate markdown format

### Corrupted Metadata
- **Error**: ".metadata.json exists but is invalid JSON"
- **Action**: Offer to restore from backup or reinitialize

### Interrupted Processing
- **Error**: User cancels mid-section (Ctrl+C)
- **Action**: Mark section as "in_progress", allow resume from that point
- **Recovery**: `/prd-breakdown-resume` continues exactly where left off

### Missing PRD Section
- **Error**: Section header in metadata doesn't match current PRD
- **Action**: Ask user if structure has changed, offer to re-parse

---

## References

For detailed implementation guidance, see:

- **WORKFLOW.md**: Complete workflows with pseudo-code and command handling logic
- **QUESTION_STRATEGIES.md**: Section-specific question templates and patterns
- **TEMPLATES.md**: Output file templates and formatting rules
- **METADATA_SCHEMA.md**: .metadata.json structure and validation rules
- **RESEARCH_APPROACH.md**: Internal/external research decision tree and citation guidelines

---

## Usage Example

```
User: /prd-breakdown-start

Skill: Found PRD/LIVVLY_Voice_Dating_App_PRD.md (3,041 lines)
       Detected 15 sections
       Creating PRD/breakdown/ directory
       Initializing metadata

       Starting breakdown of 15 sections (estimated 3-4 hours)

       === SECTION 1: EXECUTIVE SUMMARY ===

       Q1: What defines an "active user" in the 50K target?
       Q2: What are the key differentiators from existing competitors?
       Q3: What's the revenue goal for year 1?

User: [Answers Q1-Q3]

Skill: Following up on your answer about active users...
       Q4: How frequently would users need to engage (daily, weekly)?

User: [Answers Q4]

Skill: I notice the PRD mentions "creator payout within T+1" but doesn't specify timezone or cutoff time.
       Q5: Should we define specific payout cutoff times by timezone?

User: [Answers Q5]

Skill: Conducting research on competitor payout systems...
       [Research completes]

       Generated:
       ✓ PRD/breakdown/01-executive-summary/requirements.md (12 requirements)
       ✓ PRD/breakdown/01-executive-summary/questions-answers.md
       ✓ PRD/breakdown/01-executive-summary/research-notes.md

       Completed: 1/15 sections (6.7%)
       Next: /prd-breakdown-resume
```

---

## Next Steps

1. Run `/prd-breakdown-start` to begin breakdown
2. Use `/prd-breakdown-status` anytime to see progress
3. Use `/prd-breakdown-resume` to continue from where you left off
4. Use `/prd-breakdown-batch` for faster multi-section processing
5. Use `/prd-breakdown-update <section-id>` to modify sections
6. Use `/prd-breakdown-redo <section-id>` to restart a section

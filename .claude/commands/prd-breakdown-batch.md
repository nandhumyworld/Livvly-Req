# Batch Mode PRD Breakdown

**Trigger**: `/prd-breakdown-batch`

## Purpose
Process multiple PRD sections efficiently with all questions collected upfront in a single session.

---

## Parameters
Optional: section range (e.g., "1-5", "5-10", "all")

---

## Workflow

### Step 1: Load or Initialize Metadata
- If `.metadata.json` exists: Load existing state
- If not: Initialize new breakdown
- Identify pending/in_progress sections

### Step 2: Ask Which Sections to Process
Display:
```
Available sections:
[ ] 01-executive-summary (pending)
[ ] 02-market-analysis (pending)
...
[✓] 04-revenue-model (completed)
...

Which sections to process?
Options:
  1. All pending (recommended for first run)
  2. Specific range (e.g., "1-5", "5-10")
  3. Specific sections (e.g., "01, 05, 08")
  4. Continue from last incomplete

Enter choice:
```

### Step 3: Question Collection Phase (Upfront)
For each selected section:
- Generate core questions (2-3 per section)
- Display questions grouped by section with visual separation

Display format:
```
=== QUESTION COLLECTION PHASE ===

[== SECTION 1: Executive Summary ==]
Q1: What defines an "active user" in the 50K target?
Q2: What are the key differentiators from existing competitors?

[== SECTION 2: Market Analysis ==]
Q3: Your TAM/SAM/SOM numbers - are these from analyst reports?
Q4: You list [X] competitors. What's your unfair advantage?

[== SECTION 3: Product Vision ==]
Q5: Complete this: "We want to [enable/empower/create] [what] for [whom]?"
Q6: Walk me through a user's first experience.

Enter answers to all questions above (in any order):
A1: [user types answer]
A2: [user types answer]
...
```

### Step 4: Processing Phase (Sequential)
After all answers collected:
- Process sections one-by-one using pre-collected answers
- Generate follow-up questions based on answers
- Conduct research if needed
- Extract requirements and create files
- Update metadata

Show progress:
```
Processing sections with collected answers...

✓ Section 1/3: Executive Summary (12 requirements, 5 research sources)
✓ Section 2/3: Market Analysis (8 requirements, 12 research sources)
Processing: Section 3/3 [████████░░░░░░░░░░] 60%
```

### Step 5: Completion Summary
Display:
```
=== BATCH PROCESSING COMPLETE ===

Processed: 3 sections
Total Time: 1 hour 23 minutes

Summary:
  • Executive Summary: 12 requirements, 8 questions, 5 sources
  • Market Analysis: 8 requirements, 6 questions, 12 sources
  • Product Vision: 10 requirements, 7 questions, 3 sources

Totals: 30 requirements, 21 questions, 20 sources

Files Created:
  • PRD/breakdown/01-executive-summary/requirements.md
  • PRD/breakdown/01-executive-summary/questions-answers.md
  • PRD/breakdown/01-executive-summary/research-notes.md
  [... all created files listed ...]

Overall Progress: 3/15 sections (20%)

Next Steps:
  • /prd-breakdown-resume (continue with next sections)
  • /prd-breakdown-batch (process more sections)
  • /prd-breakdown-status (see full dashboard)
  • /prd-breakdown-update (modify a section)
```

---

## Batch Mode: How It Works & Realistic Expectations

### What IS Collected Upfront
✓ **Core Questions Only** (2-3 per section)
  - Clarifying ambiguous terms
  - Validating quantitative claims
  - Identifying constraints

### What is NOT Collected Upfront
✗ **Follow-up Questions** - Generated dynamically from core answers
✗ **Gap Analysis Questions** - Identified after PRD + core answer analysis
✗ **Research Validation** - Asked if research contradicts PRD

### Time Savings: Reality Check
- Upfront question collection: ~15% saved (fewer context switches)
- Follow-ups/gaps still sequential: reduces overall to ~20% saved

**Example**:
- Interactive (3 sections): 45 min (15 min/section with switching)
- Batch (3 sections): 36 min (12 min/section)

### When Batch Shines
✓ 5+ sections at once (compounding savings)
✓ Dedicated 1-2 hour block
✓ Want to see "big picture" before answering

### When Interactive is Better
✓ 1-2 sections (minimal switching overhead)
✓ Prefer immediate context-aware follow-ups
✓ Exploring requirements iteratively

---

## References
- **WORKFLOW.md**: Section 3 (Batch Mode Workflow)
- **QUESTION_STRATEGIES.md**: Question generation patterns
- **TEMPLATES.md**: Output file templates

---

## Related Commands
- `/prd-breakdown-resume` - Continue from next pending section
- `/prd-breakdown-status` - Show progress dashboard
- `/prd-breakdown-update` - Modify specific section

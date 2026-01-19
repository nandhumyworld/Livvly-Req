# PRD Breakdown Status

**Trigger**: `/prd-breakdown-status`

## Purpose
Display comprehensive progress dashboard showing breakdown status, statistics, and next actions.

---

## Parameters
None

---

## Workflow

### Step 1: Load Metadata
- Load `.metadata.json`
- If not found: Display "No breakdown in progress"
- Validate metadata integrity

### Step 2: Organize Sections by Status
Categorize all sections:
- Completed sections (status = "completed")
- In-progress sections (status = "in_progress")
- Pending sections (status = "pending")
- Archived sections (status = "archived")

### Step 3: Build Status Dashboard
```
╔═══════════════════════════════════════════════════════╗
║     PRD BREAKDOWN STATUS - LIVVLY Voice Dating App    ║
╚═══════════════════════════════════════════════════════╝

Overall Progress: [███████████░░░░░░░] 46.7% (7/15 complete)

┌───────────────────────────────────────────────────────┐
│ COMPLETED (7)                                          │
├────┬────────────────────┬──────────┬─────────────────┤
│ ID │ Section            │ Reqs     │ Completed       │
├────┼────────────────────┼──────────┼─────────────────┤
│ 01 │ Executive Summary  │ 12       │ 16 mins ago     │
│ 02 │ Market Analysis    │ 8        │ 12 mins ago     │
│ 03 │ Product Vision     │ 10       │ 8 mins ago      │
│ 04 │ Revenue Model      │ 12       │ 4 mins ago      │
│ 09 │ Coin Economy       │ 12       │ Yesterday 3 PM  │
│ 10 │ Creator Payout     │ 15       │ Yesterday 2 PM  │
│ 15 │ Success Metrics    │ 8        │ 2 days ago      │
└────┴────────────────────┴──────────┴─────────────────┘

┌───────────────────────────────────────────────────────┐
│ IN PROGRESS (1)                                        │
├────┼────────────────────┼──────────┼─────────────────┤
│ ID │ Section            │ Status   │ Started         │
├────┼────────────────────┼──────────┼─────────────────┤
│ 08 │ API Specifications │ Research │ 3 mins ago      │
└────┴────────────────────┴──────────┴─────────────────┘

┌───────────────────────────────────────────────────────┐
│ PENDING (7)                                            │
├────┬────────────────────┬──────────┬─────────────────┤
│ ID │ Section            │ Estimate │ Complexity      │
├────┼────────────────────┼──────────┼─────────────────┤
│ 05 │ Feature Specs      │ 28 min   │ Complex         │
│ 06 │ Tech Architecture  │ 18 min   │ Complex         │
│ 07 │ Database Schema    │ 15 min   │ Moderate        │
│ 11 │ Automation Flows   │ 10 min   │ Moderate        │
│ 12 │ Legal & Compliance │ 15 min   │ Moderate        │
│ 13 │ Roadmap            │ 12 min   │ Moderate        │
│ 14 │ Cost Estimation    │ 10 min   │ Simple          │
└────┴────────────────────┴──────────┴─────────────────┘

📊 Statistics (so far):
  • Requirements: 87
  • Design Decisions: 8
  • Questions Answered: 54
  • Research Sources: 23

⏱ Process Metrics:
  • Breakdown Started: 2026-01-18 10:00 AM
  • Elapsed Time: 2 hours 15 minutes
  • Average per Section: 19.3 minutes
  • Fastest: Success Metrics (8 min)
  • Slowest: Revenue Model (24 min)

📋 Next Actions:
  1. Resume: Continue from API Specifications (in progress)
     /prd-breakdown-resume

  2. Batch Process: Process remaining 7 sections
     /prd-breakdown-batch

  3. Skip Ahead: Jump to specific section
     /prd-breakdown-update 05-feature-specifications

Commands:
  /prd-breakdown-resume        - Continue from current section
  /prd-breakdown-batch         - Batch process multiple sections
  /prd-breakdown-update        - Modify specific section
  /prd-breakdown-redo          - Archive and restart section
```

---

## Status Display Variants

### No Breakdown in Progress
```
No breakdown in progress.

Start a new breakdown:
  /prd-breakdown-start

Or provide PRD file:
  /prd-breakdown-start [path-to-prd]
```

### All Sections Completed
```
✅ BREAKDOWN COMPLETE!

All 15 sections processed
Total Requirements: 247
Total Design Decisions: 45
Total Questions: 143
Total Research Sources: 67

Completion Time: 3 hours 42 minutes

Generated Files:
  • Master Index: PRD/breakdown/master-index.md
  • Metadata: PRD/breakdown/.metadata.json
  • 15 Section Folders with breakdown files

Next Steps:
  1. Review master-index.md for navigation
  2. Start development using relevant sections
  3. Update sections as needed: /prd-breakdown-update

📁 Location: PRD/breakdown/
```

### One Section In Progress
```
⏳ Currently Processing: 05-feature-specifications

Estimated Remaining:
  • Current section: 8 min
  • Remaining sections (7): ~2 hours 45 minutes
  • Total: ~2 hours 53 minutes

Commands:
  /prd-breakdown-resume        - Continue current session
  /prd-breakdown-status        - Refresh this dashboard
```

---

## Status Information Included

For Each Completed Section:
- Section ID and title
- Number of requirements extracted
- When it was completed (relative time)
- Time taken (if available)

For In-Progress Section:
- What phase is it in (questioning, research, generation)
- How long it's been running
- Partial stats (questions asked so far)

For Pending Sections:
- Estimated time to complete
- Complexity level (helps prioritize)
- Dependencies (if any)

Overall:
- Total sections and progress percentage
- Total statistics so far (reqs, questions, research sources)
- Process metrics (elapsed time, average per section)
- Recommendations for next action

---

## References
- **WORKFLOW.md**: Section 4 (Status Command Implementation)
- **METADATA_SCHEMA.md**: Metadata structure and statistics

---

## Related Commands
- `/prd-breakdown-resume` - Continue breakdown from current point
- `/prd-breakdown-batch` - Process multiple sections at once
- `/prd-breakdown-update` - Modify specific section
- `/prd-breakdown-redo` - Restart section from scratch

---

## Tips

**To stay on schedule**:
- Check status regularly to track pace
- Most sections take 10-20 minutes
- Technical sections (05, 06, 07, 08) take longer

**To speed up**:
- Use `/prd-breakdown-batch` to process multiple sections
- Answer questions concisely to reduce follow-ups

**To manage**:
- Can pause anytime (status preserved in metadata)
- Resume with `/prd-breakdown-resume`
- Update sections independently with `/prd-breakdown-update`

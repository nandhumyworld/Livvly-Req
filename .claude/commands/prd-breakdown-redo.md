# Redo PRD Section

**Trigger**: `/prd-breakdown-redo <section-id>`

## Purpose
Archive existing breakdown for a section and start fresh (when you want to redo a section completely).

---

## Parameters
Required: `<section-id>` (e.g., "05-feature-specifications")

---

## Workflow

### Step 1: Validate Section ID
- Check if section-id exists in metadata
- If not found: Error "Section not found. Use /prd-breakdown-status to see valid IDs"
- Load section metadata

### Step 2: Display Current State
```
╔═══════════════════════════════════════════════════════╗
║     REDO SECTION: Executive Summary                   ║
╚═══════════════════════════════════════════════════════╝

Current Content:
  Status: completed
  Created: 2026-01-18 10:05 AM
  Last Updated: 2026-01-18 14:42 PM

Files to Archive:
  • requirements.md (12 requirements)
  • questions-answers.md (8 Q&A)
  • research-notes.md (5 sources)

⚠ Warning: This will archive existing files and restart the breakdown.
Archived files can be recovered but they won't be used.

Proceed with redo? (yes/no):
```

### Step 3: Confirmation
If user confirms, proceed. If not, cancel.

### Step 4: Archive Existing Files
Rename all existing files with timestamp:
```
requirements.md
  → requirements.ARCHIVED_20260118_143000.md

questions-answers.md
  → questions-answers.ARCHIVED_20260118_143000.md

research-notes.md
  → research-notes.ARCHIVED_20260118_143000.md

design-decisions.md (if exists)
  → design-decisions.ARCHIVED_20260118_143000.md
```

Create `.metadata.archived.json` backup of current metadata.

### Step 5: Reset Metadata
For the section:
- Set status to `"pending"`
- Reset counts to 0:
  - `requirement_count = 0`
  - `design_decision_count = 0`
  - `question_count = 0`
  - `research_source_count = 0`
- Clear `started_at` timestamp
- Clear `completed_at` timestamp
- Clear `files_generated` array
- Save metadata

### Step 6: Start Fresh Breakdown
Begin section breakdown from the beginning:
- Display section overview
- Start with core questions phase
- Proceed with standard workflow

---

## Example

```
Command: /prd-breakdown-redo 01-executive-summary

Output:
╔═══════════════════════════════════════════════════════╗
║     REDO SECTION: Executive Summary                   ║
╚═══════════════════════════════════════════════════════╝

Current Content:
  Status: completed
  Created: 2026-01-18 10:05 AM
  Requirements: 12
  Questions: 8
  Research: 5 sources

Files will be archived:
  • requirements.ARCHIVED_20260118_143000.md
  • questions-answers.ARCHIVED_20260118_143000.md
  • research-notes.ARCHIVED_20260118_143000.md

Redo this section? (yes/no)
> yes

Archiving existing files...
✓ Archived 3 files with timestamp 20260118_143000

Resetting metadata...
✓ Section status: pending
✓ Counts cleared
✓ Files cleared

Starting fresh breakdown of 01-executive-summary...

=== EXECUTIVE SUMMARY (FRESH START) ===

[New breakdown begins with fresh Q&A...]
```

---

## When to Use Redo

**Use redo when**:
- You want to ask different questions and get fresh perspective
- The original breakdown missed important requirements
- The PRD was significantly updated and section needs re-evaluation
- You want to incorporate new research or insights

**Don't use redo for**:
- Small fixes or additions → Use `/prd-breakdown-update` instead
- Typo fixes → Use `/prd-breakdown-update` → View → Edit
- Adding research to existing breakdown → Use `/prd-breakdown-update`

---

## Recovery

If you archive section and later want to recover:
1. The archived files are renamed with timestamp (not deleted)
2. They're stored in the same section directory
3. You can manually restore from `.ARCHIVED_` file names
4. The `.metadata.archived.json` has the original state

**To recover**:
```
1. Rename back: requirements.ARCHIVED_20260118_143000.md → requirements.md
2. Update metadata with original counts
3. Manually restore original state
```

---

## References
- **WORKFLOW.md**: Section 4 (Redo Workflow)
- **METADATA_SCHEMA.md**: Section update operations

---

## Related Commands
- `/prd-breakdown-update` - Make targeted changes (recommended for small fixes)
- `/prd-breakdown-resume` - Continue main breakdown
- `/prd-breakdown-status` - View all sections

# Resume PRD Breakdown

**Trigger**: `/prd-breakdown-resume`

## Purpose
Continue an incomplete PRD breakdown from exactly where it left off (after interruption or pause).

---

## Parameters
None

---

## Workflow

### Step 1: Load Metadata
- Load `.metadata.json`
- If not found: Error "No breakdown in progress. Use /prd-breakdown-start"
- Validate metadata integrity

### Step 2: Find Resumption Point
Priority:
1. First section with status `"in_progress"` (was interrupted mid-section)
2. If none, first section with status `"pending"` (natural resume point)
3. If all completed: "All sections completed!"

### Step 3: Display Progress Summary
```
╔═══════════════════════════════════════════════════════╗
║              RESUME PRD BREAKDOWN                      ║
╚═══════════════════════════════════════════════════════╝

Current Progress: 3/15 sections (20%)

Completed Sections:
  ✓ 01-executive-summary (12 requirements)
  ✓ 02-market-analysis (8 requirements)
  ✓ 03-product-vision (10 requirements)

Next Section: 04-revenue-model
  • Status: pending
  • Complexity: moderate
  • Estimated Time: ~12 minutes
  • Lines in PRD: 481-650

Resume from this section? (yes/no)
```

### Step 4: Resumption Options

#### If Status = "in_progress" (Interrupted)
User can:
- **Resume**: Continue from current section (loses any partial progress in current section)
- **Redo**: Archive and restart current section from scratch
- **Cancel**: Exit

Display:
```
Current section (04-revenue-model) was interrupted mid-breakdown.
Resume will restart this section from the beginning.

Options:
  1. Resume 04-revenue-model (restart this section)
  2. Redo 04-revenue-model (archive and fully restart)
  3. Skip to next pending section (05-feature-specifications)
  4. Cancel

Choose option:
```

#### If Status = "pending" (Natural Resume)
User can:
- **Resume**: Continue with next pending section
- **Jump**: Skip to different section
- **Cancel**: Exit

### Step 5: Continue Breakdown
- Proceed with standard section breakdown process
- Use WORKFLOW.md Section 2 (Section Breakdown Loop)
- Display section overview and first questions

---

## Example

```
Command: /prd-breakdown-resume

Output:
Progress: 7/15 sections (46.7%)
Next: 08-api-specifications (was pending)

Resume from API Specifications? (yes/no)
> yes

Starting: API Specifications
Complexity: complex
Estimated time: 18 minutes

=== SECTION 8: API SPECIFICATIONS ===

Q1: How do clients authenticate? (JWT? Session? API keys?)
Q2: Do you need rate limiting? Per user? Per IP? What limits?
Q3: Will you version your API? If yes, how?

[continues with standard section breakdown...]
```

---

## Interruption Handling
If user interrupts (Ctrl+C) during resume:
- Current section marked as `"in_progress"` (not `"completed"`)
- Any partial progress within section is lost
- Metadata updated with timestamp
- Next resume will start section over (can `/prd-breakdown-redo` if preferred)

---

## References
- **WORKFLOW.md**: Section 4 (Resume Command) and Section 2 (Section Breakdown Loop)
- **METADATA_SCHEMA.md**: Status field values and update operations

---

## Related Commands
- `/prd-breakdown-status` - See full dashboard without resuming
- `/prd-breakdown-redo` - Restart a specific section
- `/prd-breakdown-batch` - Process multiple sections at once

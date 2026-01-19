# Start PRD Breakdown

**Trigger**: `/prd-breakdown-start`

## Purpose
Initialize a new PRD breakdown process from scratch.

---

## Parameters
None (will auto-detect PRD or prompt for path)

---

## Workflow

### Step 1: Locate PRD File
- Search `PRD/` directory for markdown files
- If multiple found: Ask user which to process
- If none found: Ask user for file path
- Validate file exists and is valid markdown

### Step 2: Parse PRD Structure
- Extract all top-level headers (# Title)
- Determine section boundaries with line ranges
- Estimate complexity for each section (simple/moderate/complex)
- Detect total section count

### Step 3: Create Breakdown Infrastructure
- Create `PRD/breakdown/` directory
- Create subdirectories for each section (e.g., `01-executive-summary/`)
- For complex sections (Feature Specs, API Specs): create `screen-specs/` subdirectory
- Initialize `.metadata.json` with all sections set to "pending"

### Step 4: Present Overview to User
Display:
```
Found PRD: LIVVLY_Voice_Dating_App_PRD.md (3,041 lines)
Detected: 15 sections
Total Sections:
  • Business (5): Executive Summary, Market Analysis, Product Vision, Revenue Model, Success Metrics
  • Technical (4): Feature Specs, Tech Architecture, Database Schema, API Specs
  • Implementation (5): Coin Economy, Creator Payout, Automation Workflows, Roadmap, Cost Est.
  • Operational (1): Legal & Compliance

Estimated Time: 3-4 hours for complete breakdown
Breakdown Strategy: Section-by-section with progressive questioning

Ready to start? (yes/no)
```

### Step 5: Begin First Section
- Start with section 01-executive-summary
- Proceed with standard section breakdown process (see WORKFLOW.md)
- Display section overview and first questions

---

## References
- **WORKFLOW.md**: Section 1 (Initialize Phase) and Section 2 (Section Breakdown Loop)
- **METADATA_SCHEMA.md**: Structure of .metadata.json
- **TEMPLATES.md**: Output file templates

---

## Next Commands
After starting:
- `/prd-breakdown-resume` - Continue from where you left off
- `/prd-breakdown-status` - Check progress
- `/prd-breakdown-batch` - Process multiple sections at once

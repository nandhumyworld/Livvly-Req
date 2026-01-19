# PRD Breakdown - Quick Start Guide

Get started with PRD breakdown in 5 minutes.

---

## TL;DR

```bash
# 1. Start breakdown
/prd-breakdown-start

# 2. Answer questions as they come
# 3. Check progress anytime
/prd-breakdown-status

# 4. Resume if interrupted
/prd-breakdown-resume

# 5. Export when done
/prd-breakdown-export markdown
```

---

## Step-by-Step

### Step 1: Prepare Your PRD

Place your PRD file in the `PRD/` directory:
```
your-project/
└── PRD/
    └── Your_PRD_Document.md
```

**PRD Requirements**:
- Markdown format (`.md`)
- Top-level headers (`#`) for sections
- Any length (the skill handles large PRDs)

### Step 2: Start the Breakdown

```bash
/prd-breakdown-start
```

The skill will:
1. Find your PRD file
2. Parse the section structure
3. Show you an overview
4. Start asking questions

### Step 3: Answer Questions

For each section, you'll be asked:

1. **Core Questions** (2-3): Clarify ambiguous terms and validate assumptions
2. **Follow-up Questions** (2-4): Based on your core answers
3. **Gap Analysis** (1-3): Identify missing specifications

**Tips**:
- Be specific in your answers
- Say "I don't know" if uncertain (the skill will note it as an open question)
- Reference existing documentation if available

### Step 4: Let the Skill Work

After answering, the skill will:
1. Conduct research (if needed)
2. Extract requirements
3. Generate output files
4. Move to the next section

### Step 5: Check Progress

```bash
/prd-breakdown-status
```

Shows:
- Completion percentage
- Sections completed/pending
- Statistics (requirements, questions, etc.)

### Step 6: Resume if Interrupted

```bash
/prd-breakdown-resume
```

Continues exactly where you left off.

### Step 7: Export Results

When complete:
```bash
/prd-breakdown-export markdown
```

Or for project management tools:
```bash
/prd-breakdown-export jira
/prd-breakdown-export linear
```

---

## Output Structure

After breakdown, you'll have:

```
PRD/
├── Your_PRD_Document.md          (original, preserved)
├── master-index.md               (navigation guide)
└── breakdown/
    ├── .metadata.json            (progress tracking)
    ├── context.json              (project context)
    ├── CHANGELOG.md              (change history)
    ├── 01-executive-summary/
    │   ├── requirements.md       (extracted requirements)
    │   ├── questions-answers.md  (Q&A session)
    │   └── research-notes.md     (if research conducted)
    ├── 05-feature-specifications/
    │   ├── requirements.md
    │   ├── questions-answers.md
    │   ├── design-decisions.md   (technical sections only)
    │   └── screen-specs/         (if multiple screens)
    └── [other sections...]
```

---

## Essential Commands

| Command | Purpose |
|---------|---------|
| `/prd-breakdown-start` | Begin new breakdown |
| `/prd-breakdown-resume` | Continue from where you left off |
| `/prd-breakdown-status` | Check progress |
| `/prd-breakdown-batch` | Process multiple sections at once |
| `/prd-breakdown-update <section>` | Update a completed section |
| `/prd-breakdown-validate <section>` | Check section quality |
| `/prd-breakdown-export <format>` | Export to JSON, CSV, Jira, etc. |

---

## Pro Tips

### 1. Use Batch Mode for Speed
```bash
/prd-breakdown-batch
```
Answer all questions upfront, then let the skill process everything.

### 2. Validate Before Sharing
```bash
/prd-breakdown-validate 05-feature-specifications
```
Catches missing acceptance criteria, incomplete research, etc.

### 3. Update Without Redoing
```bash
/prd-breakdown-update 05-feature-specifications
```
Add or modify requirements without restarting the section.

### 4. Check the Master Index
After completion, `PRD/breakdown/master-index.md` is your navigation guide. It shows:
- How to read the breakdown by development phase
- Which files to read for specific tasks
- Open questions and future work

---

## Common Workflows

### Workflow 1: Solo Developer

```bash
/prd-breakdown-start
# Answer questions
# ... (may take 2-4 hours for large PRD)
/prd-breakdown-export markdown
```

### Workflow 2: Team with Jira

```bash
/prd-breakdown-start
# Complete breakdown
/prd-breakdown-export jira
# Import jira_import.csv to Jira
# Create sprint from imported stories
```

### Workflow 3: Stakeholder Review

```bash
/prd-breakdown-start
# Complete 5 sections
/prd-breakdown-status --checkpoint
# Share checkpoint preview with stakeholders
# Incorporate feedback
/prd-breakdown-resume
```

---

## Getting Help

- **Detailed Workflows**: See `WORKFLOW.md`
- **Question Strategies**: See `QUESTION_STRATEGIES.md`
- **Output Templates**: See `TEMPLATES.md`
- **Metadata Schema**: See `METADATA_SCHEMA.md`

---

## Troubleshooting

### "No PRD file found"
- Ensure your PRD is in the `PRD/` directory
- File must have `.md` extension
- Check file permissions

### "Metadata corrupted"
```bash
# The skill will offer to restore from backup or reinitialize
# Choose based on how much work you want to preserve
```

### "Section already completed"
```bash
# Use update for minor changes
/prd-breakdown-update <section>

# Use redo for major changes
/prd-breakdown-redo <section>
```

### Progress not saving
- Check if `.metadata.json` is writable
- Ensure no other process is modifying the file

---

## Next Steps

1. **Read the main skill documentation** for advanced features
2. **Customize templates** if you have specific output requirements
3. **Set up git integration** for team collaboration
4. **Configure validation rules** for your quality standards

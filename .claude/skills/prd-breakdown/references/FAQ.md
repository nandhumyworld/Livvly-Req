# PRD Breakdown - FAQ & Common Patterns

Frequently asked questions and common usage patterns.

---

## General Questions

### Q: How long does a full breakdown take?

**A:** Depends on PRD size and complexity:
- Small PRD (500 lines, 5 sections): 30-60 minutes
- Medium PRD (1500 lines, 10 sections): 1.5-2.5 hours
- Large PRD (3000+ lines, 15+ sections): 3-5 hours

Use `/prd-breakdown-batch` to answer all questions upfront and reduce active time.

---

### Q: Can I pause and resume later?

**A:** Yes! The skill saves progress after each section. Use:
```bash
/prd-breakdown-resume
```
Your progress is stored in `.metadata.json` and persists across sessions.

---

### Q: What if my PRD has more or fewer than 15 sections?

**A:** The skill handles any number of sections. It parses your PRD's structure and adapts accordingly. Section count of 15 is just an example from the documentation.

---

### Q: Do I need to answer all questions?

**A:** You should answer most questions for best results, but you can:
- Say "I don't know" - recorded as an open question
- Say "Skip" - moves on without recording
- Say "Not applicable" - notes it and moves on

Unanswered questions become "Open Questions" in the master index.

---

### Q: Can multiple people work on the same breakdown?

**A:** Yes, with git integration:
1. Enable git integration in settings
2. Each person works on their branch
3. Merge changes via PR
4. Context and metadata merge automatically

See `GIT_INTEGRATION.md` for details.

---

## Section-Specific Questions

### Q: Why doesn't my business section have a design-decisions.md?

**A:** Design decisions files are only created for technical sections:
- 05-feature-specifications
- 06-technical-architecture
- 07-database-schema
- 08-api-specifications

Business sections (Executive Summary, Market Analysis, etc.) don't require technical design decisions.

---

### Q: What's the difference between Update and Redo?

**A:**
- **Update** (`/prd-breakdown-update`): Make targeted changes to existing requirements. Preserves most content, adds to changelog.
- **Redo** (`/prd-breakdown-redo`): Archives existing files and starts fresh. Use when requirements fundamentally change.

---

### Q: How do I add a completely new section?

**A:** If your PRD changes and you add a new section:
1. Edit the PRD to add the new section
2. Run `/prd-breakdown-start` - it will detect the new section
3. Or manually add to `.metadata.json` and run `/prd-breakdown-resume`

---

## Requirements Questions

### Q: How are requirement IDs generated?

**A:** Format: `REQ-{ABBREV}-{NUM}`
- `ABBREV`: 2-3 letter section abbreviation (ES, MA, FS, etc.)
- `NUM`: Sequential number, zero-padded to 3 digits (001, 002, etc.)

Example: `REQ-FS-023` = Feature Specifications, requirement #23

IDs are never reused - deprecated requirements keep their ID.

---

### Q: What if I need more than 999 requirements per section?

**A:** The skill uses alphanumeric suffixes:
- REQ-FS-999
- REQ-FS-99A
- REQ-FS-99B
- ...through 99Z, then 98A, etc.

---

### Q: How do I mark a requirement as deprecated?

**A:** Use `/prd-breakdown-update`:
```bash
/prd-breakdown-update 05-feature-specifications
# Choose "Deprecate requirement"
# Select the requirement
# Provide reason
```

The requirement gets a `~~DEPRECATED~~` marker but keeps its ID.

---

## Research Questions

### Q: When does the skill conduct research?

**A:** Research is triggered when the PRD mentions:
- Competitor data (e.g., "similar to FRND")
- Market size claims (e.g., "$X billion market")
- Existing implementations (e.g., "like our login flow")
- Technology without rationale (e.g., "use PostgreSQL")
- Compliance keywords (e.g., "GDPR", "PCI-DSS")

---

### Q: Can I disable automatic research?

**A:** Yes, in your context settings:
```json
{
  "preferences": {
    "auto_research_enabled": false
  }
}
```

Or skip research for a specific question by answering "Skip research".

---

### Q: What domains does external research use?

**A:** Web search and fetch are limited to safe domains. The skill will cite sources with:
- Source name
- URL
- Date accessed
- Reliability assessment (High/Medium/Low)

---

## Export Questions

### Q: What export formats are available?

**A:**
- `json` - Machine-readable, complete data
- `csv` - Spreadsheet import
- `jira` - Jira CSV import format
- `linear` - Linear CSV import format
- `markdown` - Human-readable summary
- `html` - Styled report for sharing

---

### Q: How do I export only certain sections?

**A:**
```bash
# Export specific sections
/prd-breakdown-export csv --sections 05,06,07,08

# Export range
/prd-breakdown-export json --sections 01-05

# Export all (default)
/prd-breakdown-export json
```

---

### Q: Can I export to Notion?

**A:** Not directly, but:
1. Export to markdown: `/prd-breakdown-export markdown`
2. Copy markdown content to Notion
3. Notion will format it automatically

Or use the JSON export and build a custom integration.

---

## Validation Questions

### Q: What does validation check?

**A:**
1. **Acceptance criteria** - Each requirement has ≥2 criteria
2. **DD rationale** - Each design decision has ≥50 char rationale
3. **Research citations** - All have source, URL, and date
4. **Dependencies** - All referenced requirements exist
5. **Cross-references** - All design decision references are valid

---

### Q: How do I customize validation thresholds?

**A:** In `.metadata.json`:
```json
{
  "validation": {
    "acceptance_criteria_min": 2,
    "dd_rationale_min_chars": 50,
    "require_research_citations": true,
    "allow_orphaned_dependencies": false
  }
}
```

---

### Q: Can validation block progress?

**A:** Depends on validation level:
- `strict`: All checks must pass
- `normal` (default): Critical violations block
- `loose`: Never blocks, only warns

---

## Troubleshooting

### Q: My breakdown seems stuck

**A:** Check:
1. Is there an "in_progress" section in metadata?
2. Run `/prd-breakdown-resume` to continue
3. If still stuck, check `.metadata.json` for errors

---

### Q: Metadata file is corrupted

**A:** The skill will offer options:
1. Restore from backup (`.metadata.BACKUP_*.json`)
2. Restore from checkpoint (`.metadata.CHECKPOINT_*.json`)
3. Reinitialize (loses progress)

---

### Q: Output files are empty

**A:** This happens when:
- No questions were asked (questions-answers.md)
- No research was conducted (research-notes.md)
- Non-technical section (design-decisions.md)

This is expected behavior - files are only created when there's content.

---

### Q: Git commits failing

**A:** Check:
1. Is the project a git repository?
2. Are there uncommitted changes blocking?
3. Do you have git configured (name/email)?

Disable git integration if not needed:
```bash
/prd-breakdown-start --no-git
```

---

## Common Patterns

### Pattern 1: Quick MVP Breakdown

Focus on essential sections only:
```bash
/prd-breakdown-batch --sections 01,05,06,08
```
Gets you: Executive Summary, Features, Architecture, API.

### Pattern 2: Technical Deep Dive

Full technical breakdown:
```bash
/prd-breakdown-start
# Complete all sections
/prd-breakdown-export json --include all
```

### Pattern 3: Stakeholder Checkpoint

Regular progress sharing:
```bash
# After every 5 sections, checkpoint is created automatically
# Share PRD/breakdown/PREVIEW_checkpoint_1.md with stakeholders
```

### Pattern 4: Team Sprint Planning

Export for sprint creation:
```bash
/prd-breakdown-export jira --sections 05
# Import to Jira
# Create sprint from P0 and P1 requirements
```

### Pattern 5: Living Documentation

Keep breakdown updated as PRD evolves:
```bash
# When PRD changes
/prd-breakdown-update <affected-section>

# Major changes
/prd-breakdown-redo <section>

# Check changelog
cat PRD/breakdown/CHANGELOG.md
```

---

## Best Practices

### 1. Answer Questions Thoughtfully
The skill generates requirements from your answers. Vague answers = vague requirements.

### 2. Review Each Section's Output
After each section, skim the generated `requirements.md` to catch errors early.

### 3. Use Batch Mode for Large PRDs
Answering all questions upfront is more efficient than context-switching.

### 4. Validate Before Exporting
```bash
/prd-breakdown-validate <section>
```
Catches issues before they reach your project management tool.

### 5. Keep Context Updated
When terminology changes, update `context.json` to maintain consistency.

### 6. Commit Regularly
Enable git integration to track changes and enable collaboration.

### 7. Export Multiple Formats
Export markdown for stakeholders, JSON for automation, Jira for development.

---

## Getting More Help

- **Main Documentation**: `skill.md`
- **Detailed Workflows**: `WORKFLOW.md`
- **Question Templates**: `QUESTION_STRATEGIES.md`
- **Output Templates**: `TEMPLATES.md`
- **Metadata Details**: `METADATA_SCHEMA.md`
- **Context Persistence**: `CONTEXT_PERSISTENCE.md`
- **Conflict Resolution**: `CONFLICT_RESOLUTION.md`
- **Git Integration**: `GIT_INTEGRATION.md`
- **Changelog**: `CHANGELOG_GENERATION.md`

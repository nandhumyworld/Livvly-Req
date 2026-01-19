# Export PRD Breakdown

**Trigger**: `/prd-breakdown-export [format] [options]`

## Purpose
Export breakdown results to various formats for integration with external tools like Jira, Linear, Notion, or stakeholder presentations.

---

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `format` | string | json | Export format: json, csv, jira, linear, markdown, html |
| `--sections` | string | all | Sections to export: "all", "01,02,03", "01-05" |
| `--include` | string | requirements | What to include: requirements, decisions, research, all |
| `--output` | string | auto | Output path (auto-generated if not specified) |
| `--flatten` | boolean | false | Flatten nested structure for CSV |

---

## Supported Formats

### 1. JSON (Machine-Readable)

```bash
/prd-breakdown-export json
```

**Output**: `PRD/breakdown/exports/breakdown_export_20260118.json`

**Structure**:
```json
{
  "export_metadata": {
    "exported_at": "2026-01-18T14:00:00Z",
    "prd_name": "LIVVLY Voice Dating App",
    "format": "json",
    "sections_included": ["all"],
    "total_requirements": 247,
    "total_design_decisions": 45
  },
  "sections": [
    {
      "id": "01-executive-summary",
      "title": "Executive Summary",
      "requirements": [
        {
          "id": "REQ-ES-001",
          "title": "Support Primary User Base - Voice-First Dating",
          "priority": "P0",
          "type": "Functional",
          "description": "Platform must enable voice-first interactions...",
          "rationale": "This is the core differentiation...",
          "acceptance_criteria": [
            "Users can initiate voice calls with matches",
            "Users can join live voice rooms",
            "Voice quality remains acceptable on 3G networks"
          ],
          "dependencies": ["REQ-FS-016", "REQ-API-031"],
          "related_design_decisions": ["DD-TA-005", "DD-FS-001"]
        }
      ],
      "design_decisions": [],
      "questions_answers": [],
      "research_notes": []
    }
  ]
}
```

### 2. CSV (Spreadsheet Import)

```bash
/prd-breakdown-export csv --flatten
```

**Output**: `PRD/breakdown/exports/requirements_20260118.csv`

**Structure**:
```csv
ID,Title,Section,Priority,Type,Description,Acceptance Criteria,Dependencies,Design Decisions
REQ-ES-001,Support Primary User Base - Voice-First Dating,01-executive-summary,P0,Functional,"Platform must enable voice-first interactions...","1. Users can initiate voice calls
2. Users can join live voice rooms
3. Voice quality acceptable on 3G",REQ-FS-016;REQ-API-031,DD-TA-005;DD-FS-001
REQ-ES-002,Target 50K Active Users in Year 1,01-executive-summary,P0,Business,"Platform must be designed to achieve 50K weekly active users...","1. Architecture supports 50K concurrent
2. All features support 50K+ scaling
3. Monitoring in place",REQ-TA-008;REQ-14-006,DD-TA-008
```

**Multiple CSV Files**:
- `requirements_20260118.csv` - All requirements
- `design_decisions_20260118.csv` - All design decisions
- `questions_answers_20260118.csv` - All Q&A

### 3. Jira Import Format

```bash
/prd-breakdown-export jira
```

**Output**: `PRD/breakdown/exports/jira_import_20260118.csv`

**Structure** (Jira CSV Import format):
```csv
Summary,Description,Issue Type,Priority,Labels,Epic Link,Acceptance Criteria
[REQ-ES-001] Support Primary User Base - Voice-First Dating,"Platform must enable voice-first interactions for dating and social connection, with text/video as secondary modalities.

Rationale: This is the core differentiation from text-first social apps.

Dependencies: REQ-FS-016, REQ-API-031",Story,Highest,prd-breakdown;executive-summary,PRD-LIVVLY,"* Users can initiate voice calls with matches
* Users can join live voice rooms
* Voice quality remains acceptable on 3G networks
* Users can fall back to text messaging if voice unavailable"
```

**Jira Field Mapping**:
| PRD Field | Jira Field |
|-----------|------------|
| Requirement ID + Title | Summary |
| Description + Rationale + Dependencies | Description |
| Always "Story" | Issue Type |
| P0→Highest, P1→High, P2→Medium, P3→Low | Priority |
| Section name | Labels |
| PRD name | Epic Link |
| Acceptance Criteria (bullet points) | Acceptance Criteria |

### 4. Linear Import Format

```bash
/prd-breakdown-export linear
```

**Output**: `PRD/breakdown/exports/linear_import_20260118.csv`

**Structure** (Linear CSV Import format):
```csv
title,description,priority,labels,estimate
[REQ-ES-001] Voice-First Dating Support,"## Description
Platform must enable voice-first interactions for dating and social connection.

## Rationale
This is the core differentiation from text-first social apps.

## Acceptance Criteria
- [ ] Users can initiate voice calls with matches
- [ ] Users can join live voice rooms
- [ ] Voice quality remains acceptable on 3G networks

## Dependencies
- REQ-FS-016
- REQ-API-031",1,prd-breakdown executive-summary,
```

**Linear Priority Mapping**:
- P0 → 1 (Urgent)
- P1 → 2 (High)
- P2 → 3 (Medium)
- P3 → 4 (Low)

### 5. Markdown Summary

```bash
/prd-breakdown-export markdown
```

**Output**: `PRD/breakdown/exports/breakdown_summary_20260118.md`

**Structure**:
```markdown
# PRD Breakdown Summary: LIVVLY Voice Dating App

> Exported: 2026-01-18
> Total Sections: 15
> Total Requirements: 247
> Total Design Decisions: 45

---

## Executive Summary

### Requirements (12)

| ID | Title | Priority | Type |
|----|-------|----------|------|
| REQ-ES-001 | Support Primary User Base - Voice-First Dating | P0 | Functional |
| REQ-ES-002 | Target 50K Active Users in Year 1 | P0 | Business |
| ... | ... | ... | ... |

### Design Decisions (0)
No design decisions for this section.

---

## Feature Specifications

### Requirements (45)

| ID | Title | Priority | Type |
|----|-------|----------|------|
| REQ-FS-001 | Onboarding - Phone Number Input | P0 | Functional |
| ... | ... | ... | ... |

### Design Decisions (12)

| ID | Title | Status |
|----|-------|--------|
| DD-FS-001 | Voice Quality Target | Accepted |
| DD-FS-002 | Real-time Update Strategy | Accepted |
| ... | ... | ... |

---

[Continues for all sections...]

---

## Statistics

- **Total Requirements**: 247
  - P0 (Critical): 45
  - P1 (High): 89
  - P2 (Medium): 78
  - P3 (Low): 35

- **Total Design Decisions**: 45
  - Accepted: 42
  - Proposed: 3
  - Deprecated: 0

- **Questions Answered**: 143

- **Research Sources**: 67
```

### 6. HTML Report

```bash
/prd-breakdown-export html
```

**Output**: `PRD/breakdown/exports/breakdown_report_20260118.html`

Generates a styled HTML report suitable for sharing with stakeholders who don't have access to the repository. Includes:
- Interactive table of contents
- Collapsible sections
- Search functionality
- Print-friendly styling
- Statistics dashboard

---

## Workflow

### Step 1: Validate Export Readiness

```python
def validate_export_readiness():
    """Check if breakdown is ready for export"""

    metadata = load_metadata()

    # Check completion status
    incomplete = [s for s in metadata['sections'] if s['status'] != 'completed']

    if incomplete:
        show_warning(f"{len(incomplete)} sections not completed")
        if not confirm("Export incomplete breakdown?"):
            return False

    # Check for unresolved conflicts
    conflicts = load_conflicts()
    unresolved = [c for c in conflicts if c['status'] != 'resolved']

    if unresolved:
        show_warning(f"{len(unresolved)} unresolved conflicts")
        if not confirm("Export with unresolved conflicts?"):
            return False

    return True
```

### Step 2: Load Data

```python
def load_export_data(sections_filter, include_filter):
    """Load data for export based on filters"""

    data = {
        'sections': [],
        'metadata': load_metadata()
    }

    sections_to_export = filter_sections(sections_filter)

    for section in sections_to_export:
        section_data = {
            'id': section['id'],
            'title': section['title'],
            'requirements': [],
            'design_decisions': [],
            'questions_answers': [],
            'research_notes': []
        }

        if 'requirements' in include_filter or 'all' in include_filter:
            section_data['requirements'] = load_requirements(section['id'])

        if 'decisions' in include_filter or 'all' in include_filter:
            section_data['design_decisions'] = load_design_decisions(section['id'])

        if 'research' in include_filter or 'all' in include_filter:
            section_data['research_notes'] = load_research_notes(section['id'])
            section_data['questions_answers'] = load_questions_answers(section['id'])

        data['sections'].append(section_data)

    return data
```

### Step 3: Transform to Format

```python
def transform_to_format(data, format):
    """Transform data to target format"""

    transformers = {
        'json': transform_to_json,
        'csv': transform_to_csv,
        'jira': transform_to_jira,
        'linear': transform_to_linear,
        'markdown': transform_to_markdown,
        'html': transform_to_html
    }

    transformer = transformers.get(format)
    if not transformer:
        error(f"Unknown format: {format}")

    return transformer(data)
```

### Step 4: Write Output

```python
def write_export(content, format, output_path):
    """Write export to file"""

    # Create exports directory if needed
    exports_dir = "PRD/breakdown/exports"
    create_directory(exports_dir)

    # Generate filename if not specified
    if not output_path:
        timestamp = format_date(current_date())
        extension = get_extension_for_format(format)
        output_path = f"{exports_dir}/breakdown_export_{timestamp}.{extension}"

    write_file(output_path, content)

    show_success(f"Exported to: {output_path}")

    return output_path
```

---

## Examples

### Export All to JSON

```bash
/prd-breakdown-export json

✓ Exported to: PRD/breakdown/exports/breakdown_export_20260118.json
  • Sections: 15
  • Requirements: 247
  • Design Decisions: 45
  • File size: 256 KB
```

### Export Specific Sections to CSV

```bash
/prd-breakdown-export csv --sections 05,06,07,08 --include requirements

✓ Exported to: PRD/breakdown/exports/requirements_technical_20260118.csv
  • Sections: 4 (technical sections only)
  • Requirements: 100
  • Format: CSV (spreadsheet-ready)
```

### Export for Jira Import

```bash
/prd-breakdown-export jira --sections all --output jira_stories.csv

✓ Exported to: PRD/breakdown/exports/jira_stories.csv
  • Requirements converted to Jira stories: 247
  • Priority mapping applied (P0→Highest, etc.)
  • Labels added from section names

Next steps:
1. Open Jira → Project Settings → External System Import
2. Select "Import issues from CSV"
3. Upload jira_stories.csv
4. Map fields and import
```

### Export Summary for Stakeholders

```bash
/prd-breakdown-export markdown --include requirements

✓ Exported to: PRD/breakdown/exports/breakdown_summary_20260118.md
  • Format: Markdown (stakeholder-friendly)
  • Includes: Requirements tables, statistics
  • Ready to share or convert to PDF
```

---

## Output Directory Structure

After multiple exports:

```
PRD/breakdown/exports/
├── breakdown_export_20260118.json       (full JSON export)
├── requirements_20260118.csv            (requirements CSV)
├── design_decisions_20260118.csv        (design decisions CSV)
├── jira_import_20260118.csv             (Jira-ready CSV)
├── linear_import_20260118.csv           (Linear-ready CSV)
├── breakdown_summary_20260118.md        (Markdown summary)
└── breakdown_report_20260118.html       (HTML report)
```

---

## Related Commands

- `/prd-breakdown-status` - Check if breakdown is complete before export
- `/prd-breakdown-validate` - Validate sections before export
- `/prd-breakdown-update` - Fix issues before export

---

## References

- **TEMPLATES.md**: Source file formats
- **METADATA_SCHEMA.md**: Metadata structure for export

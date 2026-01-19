# Metadata Schema Documentation

Complete specification for `.metadata.json` file used to track PRD breakdown progress.

---

## File Location
`PRD/breakdown/.metadata.json`

---

## Purpose
Single source of truth for breakdown progress, enabling:
- Resume from interruptions
- Update specific sections
- Display progress status
- Calculate statistics at completion
- Archive old breakdown versions

---

## Schema

### Root Level
```json
{
  "prd_source": "PRD/LIVVLY_Voice_Dating_App_PRD.md",
  "prd_name": "LIVVLY Voice Dating App",
  "total_sections": 15,
  "breakdown_started": "2026-01-18T10:00:00Z",
  "last_modified": "2026-01-18T14:30:00Z",
  "sections": [...],
  "overall_progress": {...},
  "statistics": {...}
}
```

**Fields**:
- `prd_source` (string): Relative path to original PRD file
  - Example: `"PRD/LIVVLY_Voice_Dating_App_PRD.md"`
  - Used to locate source for re-parsing

- `prd_name` (string): Human-readable project name
  - Example: `"LIVVLY Voice Dating App"`
  - Extracted from PRD filename or first header

- `total_sections` (integer): Count of sections detected
  - Example: `15`
  - Should match length of `sections` array

- `breakdown_started` (ISO 8601 timestamp): When breakdown began
  - Example: `"2026-01-18T10:00:00Z"`
  - Set once, never changed

- `last_modified` (ISO 8601 timestamp): Last update time
  - Example: `"2026-01-18T14:30:00Z"`
  - Updated on every section completion or update

- `sections` (array): Array of section objects
  - See "Section Object" schema below

- `overall_progress` (object): Aggregate progress statistics
  - See "Overall Progress Object" schema below

- `statistics` (object): Final statistics (populated on completion)
  - See "Statistics Object" schema below
  - Only present when breakdown is complete

- `checkpoints` (array): Checkpoint history (optional)
  - Array of checkpoint objects created every 5 sections
  - Each checkpoint contains:
    - `checkpoint_id` (integer): Checkpoint number (1, 2, 3...)
    - `timestamp` (ISO 8601 timestamp): When checkpoint was created
    - `sections_completed_count` (integer): Number of sections completed at checkpoint
    - `statistics_snapshot` (object): Statistics at time of checkpoint
  - Example:
    ```json
    "checkpoints": [
      {
        "checkpoint_id": 1,
        "timestamp": "2026-01-18T11:30:00Z",
        "sections_completed_count": 5,
        "statistics_snapshot": {
          "total_requirements": 58,
          "total_design_decisions": 12,
          "total_questions_answered": 34,
          "total_research_sources": 23
        }
      }
    ]
    ```

---

## Section Object Schema

```json
{
  "id": "01-executive-summary",
  "title": "Executive Summary",
  "status": "completed",
  "line_start": 1,
  "line_end": 150,
  "complexity": "moderate",
  "started_at": "2026-01-18T10:05:00Z",
  "completed_at": "2026-01-18T10:22:00Z",
  "files_generated": [
    "requirements.md",
    "questions-answers.md",
    "research-notes.md"
  ],
  "requirement_count": 12,
  "design_decision_count": 0,
  "question_count": 8,
  "research_source_count": 5
}
```

**Fields**:
- `id` (string): Section identifier
  - Format: `"{:02d}-{slug}"` (e.g., `"01-executive-summary"`)
  - Generated from section number and title
  - Used as directory name in `PRD/breakdown/`

- `title` (string): Human-readable section title
  - Example: `"Executive Summary"`
  - From PRD header

- `status` (enum): Current processing status
  - Values: `"pending"` | `"in_progress"` | `"completed"` | `"archived"`
  - Starts as `"pending"`
  - Changes to `"in_progress"` when processing begins
  - Changes to `"completed"` when processing finishes
  - Changes to `"archived"` when redo command used

- `line_start` (integer): Starting line number in PRD (0-indexed)
  - Example: `1`

- `line_end` (integer): Ending line number in PRD (0-indexed)
  - Example: `150`
  - Used to extract section text from original PRD

- `complexity` (enum): Estimated complexity level
  - Values: `"simple"` | `"moderate"` | `"complex"`
  - Determined during initialization based on:
    - Line count (simple <100, moderate 100-300, complex >300)
    - Section type (technical sections always "complex")

- `started_at` (ISO 8601 timestamp | null): When processing began
  - Set when status changes to `"in_progress"`
  - `null` if never started
  - Example: `"2026-01-18T10:05:00Z"`

- `completed_at` (ISO 8601 timestamp | null): When processing finished
  - Set when status changes to `"completed"`
  - `null` if not completed
  - Cleared when redo command used

- `files_generated` (array of strings): Files created for this section
  - Examples: `["requirements.md", "questions-answers.md", "research-notes.md"]`
  - Used to validate completeness and cleanup on redo
  - Conditional files only included if actually created:
    - `"requirements.md"`: always
    - `"questions-answers.md"`: if questions asked
    - `"research-notes.md"`: if research conducted
    - `"design-decisions.md"`: if technical section

- `requirement_count` (integer): Number of requirements extracted
  - Example: `12`
  - 0 if section not yet processed

- `design_decision_count` (integer): Number of design decisions (technical sections only)
  - Example: `4`
  - 0 for business/implementation/operational sections

- `question_count` (integer): Number of questions asked and answered
  - Example: `8`
  - Total across all three phases (core, follow-up, gap analysis)

- `research_source_count` (integer): Number of research sources cited
  - Example: `5`
  - Includes internal (codebase) and external (web) sources

---

## Overall Progress Object Schema

```json
{
  "completed": 3,
  "in_progress": 1,
  "pending": 11,
  "percentage": 20.0
}
```

**Fields**:
- `completed` (integer): Count of sections with status "completed"
- `in_progress` (integer): Count of sections with status "in_progress" (should be 0 or 1)
- `pending` (integer): Count of sections with status "pending" or "archived"
- `percentage` (float): Completion percentage, 1 decimal place
  - Formula: `(completed / total_sections) * 100`
  - Example: `20.0` for 3 complete out of 15 total
  - Used for progress display

---

## Statistics Object Schema

Present only when breakdown is complete.

```json
{
  "total_requirements": 247,
  "total_design_decisions": 45,
  "total_questions_answered": 143,
  "total_research_sources": 67,
  "breakdown_duration_minutes": 223,
  "average_section_time_minutes": 14.9,
  "longest_section": {
    "id": "05-feature-specifications",
    "title": "Feature Specifications",
    "duration_minutes": 28
  },
  "shortest_section": {
    "id": "15-success-metrics",
    "title": "Success Metrics",
    "duration_minutes": 9
  }
}
```

**Fields**:
- `total_requirements` (integer): Sum of all requirements across all sections
- `total_design_decisions` (integer): Sum of all design decisions
- `total_questions_answered` (integer): Sum of all questions asked
- `total_research_sources` (integer): Sum of all research sources
- `breakdown_duration_minutes` (integer): Total time from start to completion
- `average_section_time_minutes` (float): Average processing time per section
- `longest_section` (object): Section that took the most time
  - `id`, `title`, `duration_minutes`
- `shortest_section` (object): Section that took the least time
  - `id`, `title`, `duration_minutes`

---

## Validation Rules

1. **Section Uniqueness**: All `id` values must be unique
2. **Status Constraint**: Exactly one section can be `"in_progress"` at a time (or zero)
3. **Timestamp Consistency**:
   - `started_at` must be set when status = `"in_progress"`
   - `completed_at` must be set when status = `"completed"`
   - `completed_at` > `started_at` (if both present)
   - Both timestamps ≥ `breakdown_started`
4. **Progress Calculation**:
   - `overall_progress.percentage` = `(completed / total_sections) * 100`
   - `completed + in_progress + pending` = `total_sections`
5. **Statistics Validation** (when present):
   - `total_requirements` = sum of all `sections[*].requirement_count`
   - `total_design_decisions` = sum of all `sections[*].design_decision_count`
   - `total_questions_answered` = sum of all `sections[*].question_count`
   - `total_research_sources` = sum of all `sections[*].research_source_count`

---

## Update Operations

### On Skill Start
```
if .metadata.json exists:
    load metadata
else:
    create new metadata with all sections "pending"
```

### On Section Start
```
section.status = "in_progress"
section.started_at = current_timestamp()
metadata.last_modified = current_timestamp()
```

### On Section Complete
```
section.status = "completed"
section.completed_at = current_timestamp()
section.requirement_count = extracted_requirement_count
section.design_decision_count = extracted_dd_count
section.question_count = asked_question_count
section.research_source_count = cited_source_count
section.files_generated = [list of created files]
metadata.overall_progress.completed += 1
metadata.overall_progress.in_progress -= 1
metadata.overall_progress.percentage = (completed / total) * 100
metadata.last_modified = current_timestamp()
```

### On Resume
```
load metadata
find first section where status = "in_progress"
  or if none, find first section where status = "pending"
display progress and continue from that section
```

### On Update Section
```
section.last_modified = current_timestamp()
increment appropriate counts (requirement_count, etc.)
metadata.last_modified = current_timestamp()
```

### On Redo Section
```
archive existing files (rename with timestamp)
section.status = "pending"
section.requirement_count = 0
section.design_decision_count = 0
section.question_count = 0
section.research_source_count = 0
section.files_generated = []
section.completed_at = null
metadata.last_modified = current_timestamp()
restart breakdown for that section
```

### On Completion
```
verify all sections.status = "completed"
calculate statistics object
metadata.statistics = {...}
metadata.overall_progress.percentage = 100.0
metadata.last_modified = current_timestamp()
```

---

## Example Instance

```json
{
  "prd_source": "PRD/LIVVLY_Voice_Dating_App_PRD.md",
  "prd_name": "LIVVLY Voice Dating App",
  "total_sections": 15,
  "breakdown_started": "2026-01-18T10:00:00Z",
  "last_modified": "2026-01-18T10:25:00Z",
  "sections": [
    {
      "id": "01-executive-summary",
      "title": "Executive Summary",
      "status": "completed",
      "line_start": 1,
      "line_end": 150,
      "complexity": "moderate",
      "started_at": "2026-01-18T10:05:00Z",
      "completed_at": "2026-01-18T10:22:00Z",
      "files_generated": ["requirements.md", "questions-answers.md", "research-notes.md"],
      "requirement_count": 12,
      "design_decision_count": 0,
      "question_count": 8,
      "research_source_count": 5
    },
    {
      "id": "02-market-analysis",
      "title": "Market Analysis",
      "status": "completed",
      "line_start": 151,
      "line_end": 320,
      "complexity": "moderate",
      "started_at": "2026-01-18T10:22:30Z",
      "completed_at": "2026-01-18T10:35:00Z",
      "files_generated": ["requirements.md", "questions-answers.md", "research-notes.md"],
      "requirement_count": 8,
      "design_decision_count": 0,
      "question_count": 6,
      "research_source_count": 12
    },
    {
      "id": "03-product-vision",
      "title": "Product Vision",
      "status": "in_progress",
      "line_start": 321,
      "line_end": 480,
      "complexity": "simple",
      "started_at": "2026-01-18T10:35:00Z",
      "completed_at": null,
      "files_generated": [],
      "requirement_count": 0,
      "design_decision_count": 0,
      "question_count": 0,
      "research_source_count": 0
    },
    {
      "id": "04-revenue-model",
      "title": "Revenue Model",
      "status": "pending",
      "line_start": 481,
      "line_end": 650,
      "complexity": "moderate",
      "started_at": null,
      "completed_at": null,
      "files_generated": [],
      "requirement_count": 0,
      "design_decision_count": 0,
      "question_count": 0,
      "research_source_count": 0
    }
  ],
  "overall_progress": {
    "completed": 2,
    "in_progress": 1,
    "pending": 12,
    "percentage": 13.3
  }
}
```

This example shows:
- 2 sections completed (01, 02)
- 1 section in progress (03)
- 12 sections pending (04-15)
- Overall progress: 13.3% (2/15)

---

## Section 8: Validation Functions

### validate_metadata_integrity(metadata)
**Run**: After every section update, before saving

```python
def validate_metadata_integrity(metadata):
    errors = []

    # Check 1: Section ID uniqueness
    section_ids = [s['id'] for s in metadata['sections']]
    if len(section_ids) != len(set(section_ids)):
        errors.append("ERROR: Duplicate section IDs")

    # Check 2: File existence matches files_generated
    for section in metadata['sections']:
        if section['status'] == 'completed':
            for file in section['files_generated']:
                path = f"PRD/breakdown/{section['id']}/{file}"
                if not file_exists(path):
                    errors.append(f"ERROR: {path} missing")

    # Check 3: Section count consistency
    if len(metadata['sections']) != metadata['total_sections']:
        errors.append("ERROR: Section count mismatch")

    return errors or None
```

**Recovery**: If errors → prompt (1) Auto-fix, (2) Manual review, (3) Reinitialize

### validate_status_constraints(metadata)
**Checks**:
- Max one section with status="in_progress"
- Completed sections have completion timestamp
- Completed sections have requirement_count > 0 (warn if 0)

### validate_timestamps(metadata)
**Checks**:
- Valid ISO 8601 format
- completed_at >= started_at
- last_modified >= breakdown_started
- No future timestamps

### validate_statistics(metadata)
**Run**: During finalization, before master-index generation
**Checks**: Sum of section counts matches stored statistics

---

## File Management

### Backup Creation
Before processing a section, create timestamped backup:
```
.metadata.json.BACKUP_20260118_100500.json
```

### Archive on Redo
When section is redone, existing files archived:
```
requirements.md → requirements.ARCHIVED_20260118_103000.md
questions-answers.md → questions-answers.ARCHIVED_20260118_103000.md
research-notes.md → research-notes.ARCHIVED_20260118_103000.md
design-decisions.md → design-decisions.ARCHIVED_20260118_103000.md
```

### Cleanup
After successful completion, archived files can be optionally deleted.

---

## Section 9: Configuration Schema

The metadata file can include a `config` object for customizing skill behavior.

### Configuration Object Schema

```json
{
  "config": {
    "base_directory": "PRD",
    "git_integration": {
      "enabled": true,
      "auto_commit": true,
      "commit_on": ["section_complete", "update", "finalize"],
      "branch_strategy": "single",
      "commit_message_template": "prd-breakdown: {action} - {section}"
    },
    "validation": {
      "acceptance_criteria_min": 2,
      "dd_rationale_min_chars": 50,
      "require_research_citations": true,
      "allow_orphaned_dependencies": false,
      "level": "normal"
    },
    "changelog": {
      "enabled": true,
      "auto_update": true,
      "include_statistics": true,
      "include_diff_details": true,
      "max_entries": 100
    },
    "context": {
      "auto_learn_terminology": true,
      "persist_clarifications": true,
      "share_across_sessions": true
    },
    "abbreviation_overrides": {
      "User Analytics": "UA",
      "Security Audit": "SA"
    }
  }
}
```

### Configuration Fields

#### base_directory
- **Type**: string
- **Default**: "PRD"
- **Description**: Base directory for PRD files and breakdown output

#### git_integration
| Field | Type | Default | Description |
|-------|------|---------|-------------|
| enabled | boolean | true | Enable/disable git integration |
| auto_commit | boolean | true | Automatically commit after triggers |
| commit_on | array | ["section_complete"] | When to create commits |
| branch_strategy | string | "single" | "single", "per-section", or "feature" |
| commit_message_template | string | see above | Template for commit messages |

#### validation
| Field | Type | Default | Description |
|-------|------|---------|-------------|
| acceptance_criteria_min | integer | 2 | Minimum acceptance criteria per requirement |
| dd_rationale_min_chars | integer | 50 | Minimum characters for DD rationale |
| require_research_citations | boolean | true | Require complete citations |
| allow_orphaned_dependencies | boolean | false | Allow missing dependency targets |
| level | string | "normal" | "strict", "normal", or "loose" |

#### changelog
| Field | Type | Default | Description |
|-------|------|---------|-------------|
| enabled | boolean | true | Enable/disable changelog |
| auto_update | boolean | true | Auto-update on changes |
| include_statistics | boolean | true | Include running statistics |
| include_diff_details | boolean | true | Include detailed diffs |
| max_entries | integer | 100 | Max entries before archiving |

#### context
| Field | Type | Default | Description |
|-------|------|---------|-------------|
| auto_learn_terminology | boolean | true | Automatically add terms to context |
| persist_clarifications | boolean | true | Save Q&A clarifications |
| share_across_sessions | boolean | true | Reuse context in future sessions |

#### abbreviation_overrides
- **Type**: object (string → string)
- **Default**: {}
- **Description**: Custom abbreviations for section names not in predefined list

---

## Section 10: Dynamic Section Handling

The skill supports PRDs with any number of sections, not just the documented 15.

### Section Detection Algorithm

```python
def detect_sections(prd_content):
    """Detect sections from PRD headers"""

    sections = []
    lines = prd_content.split('\n')

    for i, line in enumerate(lines):
        # Match top-level headers: # Title
        if line.startswith('# ') and not line.startswith('## '):
            title = line[2:].strip()
            sections.append({
                'title': title,
                'line_start': i,
                'id': generate_section_id(title, len(sections) + 1),
                'abbreviation': generate_abbreviation(title)
            })

    # Calculate line_end for each section
    for i, section in enumerate(sections):
        if i + 1 < len(sections):
            section['line_end'] = sections[i + 1]['line_start'] - 1
        else:
            section['line_end'] = len(lines) - 1

    return sections
```

### Abbreviation Generation

```python
def generate_abbreviation(title):
    """Generate section abbreviation"""

    # Check predefined abbreviations
    predefined = {
        'Executive Summary': 'ES',
        'Market Analysis': 'MA',
        'Product Vision': 'PV',
        # ... etc
    }

    if title in predefined:
        return predefined[title]

    # Check config overrides
    if title in config.get('abbreviation_overrides', {}):
        return config['abbreviation_overrides'][title]

    # Auto-generate from title
    words = [w for w in title.split() if len(w) > 2]

    if len(words) == 1:
        return words[0][:3].upper()
    elif len(words) == 2:
        return (words[0][0] + words[1][0]).upper()
    else:
        return ''.join(w[0] for w in words[:3]).upper()
```

### Section Type Detection

```python
def detect_section_type(title, content):
    """Detect if section is business, technical, implementation, or operational"""

    technical_keywords = ['architecture', 'database', 'api', 'schema', 'technical', 'specification']
    business_keywords = ['executive', 'market', 'vision', 'revenue', 'metrics', 'analysis']
    implementation_keywords = ['roadmap', 'timeline', 'cost', 'workflow', 'automation', 'payout']
    operational_keywords = ['legal', 'compliance', 'security', 'audit']

    title_lower = title.lower()

    if any(kw in title_lower for kw in technical_keywords):
        return 'technical'
    elif any(kw in title_lower for kw in business_keywords):
        return 'business'
    elif any(kw in title_lower for kw in implementation_keywords):
        return 'implementation'
    elif any(kw in title_lower for kw in operational_keywords):
        return 'operational'
    else:
        # Default based on content analysis
        return analyze_content_for_type(content)
```

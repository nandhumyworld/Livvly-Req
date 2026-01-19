# Changelog Generation

This document describes the automatic changelog generation feature for tracking changes to PRD breakdown artifacts.

---

## Overview

The changelog provides a human-readable history of all changes made to the PRD breakdown, including:
- New requirements added
- Existing requirements modified
- Requirements deprecated or removed
- Design decisions added or changed
- Section updates and redos

---

## File Location

`PRD/breakdown/CHANGELOG.md`

---

## Changelog Format

```markdown
# PRD Breakdown Changelog

> Project: LIVVLY Voice Dating App
> Started: 2026-01-18
> Last Updated: 2026-01-20

---

## [2026-01-20]

### Section: 05-feature-specifications (Update)
**Updated by**: `/prd-breakdown-update 05-feature-specifications`

#### Added
- **REQ-FS-046**: Voice filter feature - Allow users to apply voice effects during calls
  - Priority: P2
  - Type: Functional

#### Modified
- **REQ-FS-012**: Real-time voice call initiation
  - Changed: Added acceptance criterion for call quality monitoring
  - Old: 3 acceptance criteria
  - New: 4 acceptance criteria

#### Deprecated
- None

---

## [2026-01-19]

### Section: 10-creator-payout (Redo)
**Updated by**: `/prd-breakdown-redo 10-creator-payout`

#### Notes
- Archived previous version: `requirements.ARCHIVED_20260119_103000.md`
- Reason: Major business model change (T+1 to T+0 payout)

#### Added (Fresh Breakdown)
- **REQ-CP-001** through **REQ-CP-018**: 18 new requirements
- **DD-CP-001** through **DD-CP-004**: 4 design decisions

---

## [2026-01-18]

### Initial Breakdown Complete

#### Sections Completed
1. 01-executive-summary (12 requirements)
2. 02-market-analysis (8 requirements)
3. 03-product-vision (10 requirements)
4. 04-revenue-model (12 requirements)
5. 05-feature-specifications (45 requirements, 12 design decisions)
6. 06-technical-architecture (15 requirements, 15 design decisions)
7. 07-database-schema (18 requirements, 8 design decisions)
8. 08-api-specifications (22 requirements, 6 design decisions)
9. 09-coin-economy (12 requirements)
10. 10-creator-payout (15 requirements)
11. 11-automation-workflows (8 requirements)
12. 12-legal-compliance (9 requirements)
13. 13-implementation-roadmap (8 requirements)
14. 14-cost-estimation (6 requirements)
15. 15-success-metrics (8 requirements)

#### Statistics
- Total Requirements: 247
- Total Design Decisions: 45
- Total Questions Answered: 143
- Total Research Sources: 67

---

## Changelog Statistics

| Metric | Count |
|--------|-------|
| Total Entries | 3 |
| Requirements Added | 265 |
| Requirements Modified | 1 |
| Requirements Deprecated | 0 |
| Design Decisions Added | 49 |
| Sections Redone | 1 |
```

---

## Automatic Generation

### When Changelog is Updated

1. **On Section Completion** (initial breakdown)
   - Add entry for new section with requirement count

2. **On `/prd-breakdown-update`**
   - Add entry with Added/Modified/Deprecated sections
   - Track specific changes to requirements

3. **On `/prd-breakdown-redo`**
   - Add entry noting archive of previous version
   - Record reason for redo
   - List fresh requirements added

4. **On Conflict Resolution**
   - Add entry noting resolved conflicts
   - Link to conflict ID

### Generation Logic

```python
def update_changelog(action, section, changes=None):
    """Update CHANGELOG.md with new entry"""

    changelog_path = "PRD/breakdown/CHANGELOG.md"

    # Load existing changelog or create new
    if file_exists(changelog_path):
        content = read_file(changelog_path)
        entries = parse_changelog(content)
    else:
        entries = []
        content = generate_changelog_header()

    # Create new entry
    today = format_date(current_date())
    entry = {
        'date': today,
        'section': section['id'],
        'action': action,
        'changes': changes or {}
    }

    # Format entry
    entry_text = format_changelog_entry(entry)

    # Insert after header (most recent first)
    new_content = insert_entry_after_header(content, entry_text)

    # Update statistics
    new_content = update_changelog_statistics(new_content, entry)

    # Write back
    write_file(changelog_path, new_content)
```

### Change Detection

```python
def detect_changes(old_requirements, new_requirements):
    """Detect what changed between old and new requirements"""

    changes = {
        'added': [],
        'modified': [],
        'deprecated': []
    }

    old_ids = {r['id']: r for r in old_requirements}
    new_ids = {r['id']: r for r in new_requirements}

    # Find added
    for req_id, req in new_ids.items():
        if req_id not in old_ids:
            changes['added'].append({
                'id': req_id,
                'title': req['title'],
                'priority': req['priority'],
                'type': req['type']
            })

    # Find modified
    for req_id, req in new_ids.items():
        if req_id in old_ids:
            old_req = old_ids[req_id]
            diffs = compare_requirements(old_req, req)
            if diffs:
                changes['modified'].append({
                    'id': req_id,
                    'title': req['title'],
                    'diffs': diffs
                })

    # Find deprecated
    for req_id, req in old_ids.items():
        if req_id not in new_ids:
            changes['deprecated'].append({
                'id': req_id,
                'title': req['title']
            })

    return changes


def compare_requirements(old_req, new_req):
    """Compare two requirements and return differences"""

    diffs = []

    # Compare fields
    fields_to_compare = ['title', 'priority', 'type', 'description', 'rationale']

    for field in fields_to_compare:
        if old_req.get(field) != new_req.get(field):
            diffs.append({
                'field': field,
                'old': old_req.get(field),
                'new': new_req.get(field)
            })

    # Compare acceptance criteria count
    old_ac = len(old_req.get('acceptance_criteria', []))
    new_ac = len(new_req.get('acceptance_criteria', []))
    if old_ac != new_ac:
        diffs.append({
            'field': 'acceptance_criteria',
            'old': f"{old_ac} criteria",
            'new': f"{new_ac} criteria"
        })

    # Compare dependencies
    old_deps = set(old_req.get('dependencies', []))
    new_deps = set(new_req.get('dependencies', []))
    if old_deps != new_deps:
        diffs.append({
            'field': 'dependencies',
            'old': list(old_deps),
            'new': list(new_deps)
        })

    return diffs
```

---

## Entry Templates

### Initial Breakdown Entry

```markdown
## [YYYY-MM-DD]

### Initial Breakdown Complete

#### Sections Completed
1. {section_id} ({req_count} requirements)
2. ...

#### Statistics
- Total Requirements: {total_reqs}
- Total Design Decisions: {total_dds}
- Total Questions Answered: {total_qs}
- Total Research Sources: {total_sources}
```

### Update Entry

```markdown
## [YYYY-MM-DD]

### Section: {section_id} (Update)
**Updated by**: `/prd-breakdown-update {section_id}`

#### Added
- **{REQ-ID}**: {Title}
  - Priority: {Priority}
  - Type: {Type}

#### Modified
- **{REQ-ID}**: {Title}
  - Changed: {description of change}
  - Old: {old value}
  - New: {new value}

#### Deprecated
- **{REQ-ID}**: {Title}
  - Reason: {reason}
```

### Redo Entry

```markdown
## [YYYY-MM-DD]

### Section: {section_id} (Redo)
**Updated by**: `/prd-breakdown-redo {section_id}`

#### Notes
- Archived previous version: `{archive_filename}`
- Reason: {reason for redo}

#### Added (Fresh Breakdown)
- **{REQ-ID}** through **{REQ-ID}**: {count} new requirements
- **{DD-ID}** through **{DD-ID}**: {count} design decisions
```

### Conflict Resolution Entry

```markdown
## [YYYY-MM-DD]

### Conflict Resolution: {CONFLICT-ID}
**Resolved by**: {resolver name/role}

#### Conflict
- **{REQ-A}**: {brief description}
- **{REQ-B}**: {brief description}

#### Resolution
- Strategy: {resolution strategy}
- Changes made:
  - {REQ-A}: {change description}
  - {REQ-B}: {change description}

#### Rationale
{Brief explanation of why this resolution was chosen}
```

---

## Configuration

### Enable/Disable Changelog

In `.metadata.json` or project settings:

```json
{
  "changelog": {
    "enabled": true,
    "auto_update": true,
    "include_statistics": true,
    "include_diff_details": true,
    "max_entries": 100
  }
}
```

### Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `enabled` | boolean | true | Enable/disable changelog |
| `auto_update` | boolean | true | Auto-update on changes |
| `include_statistics` | boolean | true | Include running statistics |
| `include_diff_details` | boolean | true | Include detailed diffs |
| `max_entries` | integer | 100 | Max entries before archiving |

---

## Viewing Changelog

### View Recent Changes

```bash
/prd-breakdown-status --changelog

╔═══════════════════════════════════════════════════════════╗
║              RECENT CHANGELOG ENTRIES                      ║
╚═══════════════════════════════════════════════════════════╝

[2026-01-20] Section 05 Updated
  • Added: REQ-FS-046 (Voice filter feature)
  • Modified: REQ-FS-012 (Added acceptance criterion)

[2026-01-19] Section 10 Redone
  • Archived: requirements.ARCHIVED_20260119_103000.md
  • Reason: Business model change

[2026-01-18] Initial Breakdown Complete
  • 15 sections, 247 requirements

Full changelog: PRD/breakdown/CHANGELOG.md
```

### View Changes for Specific Section

```bash
/prd-breakdown-status --changelog --section 05-feature-specifications

Changes to 05-feature-specifications:
• [2026-01-20] Update: +1 requirement, 1 modified
• [2026-01-18] Initial: 45 requirements, 12 design decisions
```

---

## Integration with Git

When git integration is enabled, changelog updates are included in commits:

```
prd-breakdown: Update section - 05-feature-specifications

Updated Feature Specifications section:
- Added: REQ-FS-046 (Voice filter feature)
- Modified: REQ-FS-012 (Updated acceptance criteria)

Files changed:
- PRD/breakdown/05-feature-specifications/requirements.md
- PRD/breakdown/.metadata.json
- PRD/breakdown/CHANGELOG.md  <-- Changelog updated
```

---

## Archiving Old Entries

When changelog exceeds `max_entries`:

```python
def archive_old_entries(changelog_path, max_entries):
    """Archive old changelog entries"""

    entries = parse_changelog_entries(changelog_path)

    if len(entries) <= max_entries:
        return

    # Split entries
    recent_entries = entries[:max_entries]
    archived_entries = entries[max_entries:]

    # Create archive file
    archive_path = f"PRD/breakdown/CHANGELOG_ARCHIVE_{format_date(current_date())}.md"
    write_changelog_archive(archive_path, archived_entries)

    # Update main changelog
    update_changelog_with_entries(changelog_path, recent_entries)

    # Add archive note
    add_archive_note(changelog_path, archive_path, len(archived_entries))
```

Archive note in main changelog:

```markdown
---

> **Note**: Older entries archived to `CHANGELOG_ARCHIVE_20260601.md` (150 entries)
```

---

## Best Practices

### 1. Keep Changelog Updated
- Enable `auto_update: true` to ensure all changes are logged
- Review changelog periodically for accuracy

### 2. Meaningful Reasons
- When redoing sections, always provide a reason
- Helps future readers understand why changes were made

### 3. Link to Conflicts
- When resolving conflicts, reference the conflict ID
- Provides traceability for audit purposes

### 4. Version with Git
- Commit changelog with related changes
- Provides additional context in git history

### 5. Review Before Export
- Check changelog before exporting to ensure completeness
- Include changelog in stakeholder communications

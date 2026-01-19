# Git Integration

This document describes how the PRD breakdown skill integrates with Git for version control of breakdown artifacts.

---

## Overview

Git integration provides:
- **Automatic commits** after section completion
- **Change tracking** for requirement updates
- **Collaboration support** through branches and PRs
- **History** for auditing requirement changes

---

## Configuration

### Enable/Disable Git Integration

In `.metadata.json` or project settings:

```json
{
  "git_integration": {
    "enabled": true,
    "auto_commit": true,
    "commit_on": ["section_complete", "update", "finalize"],
    "branch_strategy": "single",
    "commit_message_template": "prd-breakdown: {action} - {section}",
    "include_stats": true
  }
}
```

### Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `enabled` | boolean | true | Enable/disable git integration |
| `auto_commit` | boolean | true | Automatically commit after triggers |
| `commit_on` | array | ["section_complete"] | When to create commits |
| `branch_strategy` | string | "single" | "single" or "per-section" |
| `commit_message_template` | string | see below | Template for commit messages |
| `include_stats` | boolean | true | Include stats in commit message |

### Commit Triggers

- `section_complete`: Commit when a section breakdown finishes
- `update`: Commit when `/prd-breakdown-update` completes
- `redo`: Commit when `/prd-breakdown-redo` completes
- `finalize`: Commit when all sections complete
- `checkpoint`: Commit at every checkpoint (every 5 sections)

---

## Commit Message Format

### Default Template

```
prd-breakdown: {action} - {section}

{description}

Stats:
- Requirements: {req_count}
- Design Decisions: {dd_count}
- Questions Answered: {qa_count}

Files changed:
{file_list}
```

### Example Commit Messages

#### Section Complete
```
prd-breakdown: Complete section - 05-feature-specifications

Completed breakdown of Feature Specifications section with 45 requirements,
12 design decisions, and 28 questions answered.

Stats:
- Requirements: 45
- Design Decisions: 12
- Questions Answered: 28

Files changed:
- PRD/breakdown/05-feature-specifications/requirements.md
- PRD/breakdown/05-feature-specifications/questions-answers.md
- PRD/breakdown/05-feature-specifications/design-decisions.md
- PRD/breakdown/05-feature-specifications/research-notes.md
- PRD/breakdown/.metadata.json
```

#### Update Section
```
prd-breakdown: Update section - 05-feature-specifications

Updated Feature Specifications section:
- Added: REQ-FS-046 (Voice filter feature)
- Modified: REQ-FS-012 (Updated acceptance criteria)

Files changed:
- PRD/breakdown/05-feature-specifications/requirements.md
- PRD/breakdown/.metadata.json
- PRD/breakdown/CHANGELOG.md
```

#### Finalization
```
prd-breakdown: Complete breakdown - LIVVLY Voice Dating App

Completed full PRD breakdown:
- Total sections: 15
- Total requirements: 247
- Total design decisions: 45
- Total questions answered: 143

Final artifacts:
- PRD/breakdown/master-index.md
- PRD/breakdown/dependency-graph.md
- PRD/breakdown/.metadata.json (finalized)
```

---

## Workflow Integration

### On Section Complete

```python
def commit_section_complete(section, metadata):
    """Create git commit after section completion"""

    if not git_integration_enabled():
        return

    # Stage files
    files_to_stage = [
        f"PRD/breakdown/{section['id']}/requirements.md",
        "PRD/breakdown/.metadata.json"
    ]

    # Add optional files if they exist
    for optional_file in ['questions-answers.md', 'research-notes.md', 'design-decisions.md']:
        path = f"PRD/breakdown/{section['id']}/{optional_file}"
        if file_exists(path):
            files_to_stage.append(path)

    # Stage files
    git_add(files_to_stage)

    # Create commit message
    message = format_commit_message(
        action='Complete section',
        section=section['title'],
        description=f"Completed breakdown of {section['title']} section",
        stats={
            'req_count': section['requirement_count'],
            'dd_count': section['design_decision_count'],
            'qa_count': section['question_count']
        },
        files=files_to_stage
    )

    # Commit
    git_commit(message)
```

### On Update

```python
def commit_section_update(section, changes, metadata):
    """Create git commit after section update"""

    if not git_integration_enabled() or 'update' not in config['commit_on']:
        return

    # Stage modified files
    files_to_stage = identify_changed_files(section, changes)
    files_to_stage.append("PRD/breakdown/.metadata.json")

    if changelog_exists():
        files_to_stage.append("PRD/breakdown/CHANGELOG.md")

    git_add(files_to_stage)

    # Create commit message with change details
    change_description = format_changes(changes)

    message = format_commit_message(
        action='Update section',
        section=section['title'],
        description=change_description,
        files=files_to_stage
    )

    git_commit(message)
```

### On Finalization

```python
def commit_finalization(metadata):
    """Create git commit for complete breakdown"""

    if not git_integration_enabled():
        return

    # Stage all breakdown files
    files_to_stage = [
        "PRD/breakdown/master-index.md",
        "PRD/breakdown/dependency-graph.md",
        "PRD/breakdown/.metadata.json"
    ]

    # Add all section directories
    for section in metadata['sections']:
        section_files = list_files(f"PRD/breakdown/{section['id']}")
        files_to_stage.extend(section_files)

    git_add(files_to_stage)

    # Create comprehensive commit message
    message = format_commit_message(
        action='Complete breakdown',
        section=metadata['prd_name'],
        description="Completed full PRD breakdown",
        stats=metadata['statistics'],
        files=['PRD/breakdown/*']
    )

    git_commit(message)
```

---

## Branch Strategies

### Single Branch (Default)

All commits go to current branch.

```
main
  │
  ├── commit: prd-breakdown: Complete section - 01-executive-summary
  ├── commit: prd-breakdown: Complete section - 02-market-analysis
  ├── commit: prd-breakdown: Complete section - 03-product-vision
  │   ...
  └── commit: prd-breakdown: Complete breakdown - LIVVLY
```

### Per-Section Branches

Each section gets its own branch, merged on completion.

```json
{
  "git_integration": {
    "branch_strategy": "per-section",
    "branch_prefix": "prd-breakdown/",
    "auto_merge": true,
    "delete_after_merge": true
  }
}
```

```
main
  │
  ├─── prd-breakdown/01-executive-summary ──┐
  │    └── commit: Complete breakdown       │
  ├────────────────────────────────────────┘ (merge)
  │
  ├─── prd-breakdown/02-market-analysis ───┐
  │    └── commit: Complete breakdown      │
  ├───────────────────────────────────────┘ (merge)
  │
  └─── (continues for all sections)
```

### Feature Branch

All breakdown work on a dedicated branch.

```json
{
  "git_integration": {
    "branch_strategy": "feature",
    "feature_branch": "feature/prd-breakdown",
    "create_pr": true
  }
}
```

```
main
  │
  └─── feature/prd-breakdown
       ├── commit: Initialize breakdown
       ├── commit: Complete 01-executive-summary
       ├── commit: Complete 02-market-analysis
       │   ...
       ├── commit: Finalize breakdown
       │
       └── PR: PRD Breakdown Complete → main
```

---

## Commands

### Check Git Status

```
/prd-breakdown-status --git
```

Output:
```
╔═══════════════════════════════════════════════════════════╗
║           GIT INTEGRATION STATUS                           ║
╚═══════════════════════════════════════════════════════════╝

Repository: Livvly-Req
Branch: main
Git Integration: Enabled

Commits Created: 8
  • 5 section completions
  • 2 updates
  • 1 checkpoint

Uncommitted Changes:
  ✗ PRD/breakdown/06-technical-architecture/requirements.md (modified)
  ✗ PRD/breakdown/.metadata.json (modified)

Last Commit: 2 hours ago
  "prd-breakdown: Complete section - 05-feature-specifications"
```

### Force Commit

Manually trigger a commit:

```
/prd-breakdown-commit [message]
```

### Disable Git (per-session)

```
/prd-breakdown-start --no-git
```

Or:

```
/prd-breakdown-resume --no-git
```

---

## .gitignore Recommendations

Add to project `.gitignore` if needed:

```gitignore
# PRD Breakdown - Optional excludes

# Exclude backup files
PRD/breakdown/**/*.BACKUP_*
PRD/breakdown/**/*.ARCHIVED_*

# Exclude checkpoint previews (optional)
PRD/breakdown/PREVIEW_*.md

# Exclude sensitive context (if contains confidential info)
# PRD/breakdown/context.json
```

---

## Error Handling

### Git Not Available

```python
def check_git_available():
    """Check if git is available and repo is initialized"""

    try:
        result = run_command('git status')
        return True
    except CommandNotFound:
        log_warning("Git not found - disabling git integration")
        return False
    except NotAGitRepo:
        log_warning("Not a git repository - disabling git integration")
        return False
```

### Commit Failures

```python
def handle_commit_failure(error):
    """Handle git commit failures gracefully"""

    if "nothing to commit" in str(error):
        # No changes - this is fine
        log_info("No changes to commit")
        return

    if "uncommitted changes" in str(error):
        # Stash changes and retry
        log_warning("Uncommitted changes detected, stashing...")
        git_stash()
        git_commit(message)
        git_stash_pop()
        return

    # Other errors - log and continue without blocking
    log_error(f"Git commit failed: {error}")
    log_info("Continuing without git commit - changes are saved locally")
```

### Merge Conflicts

```python
def handle_merge_conflict(branch, target):
    """Handle merge conflicts in per-section branch strategy"""

    log_warning(f"Merge conflict detected merging {branch} into {target}")

    # Show conflict details
    conflicts = get_conflict_files()
    show_to_user(f"""
Merge conflict detected!

Conflicting files:
{format_file_list(conflicts)}

Options:
1. Resolve manually and run: /prd-breakdown-commit --continue
2. Abort merge and keep separate branches
3. Force overwrite (loses changes in {target})
    """)

    choice = ask_user_choice(['resolve', 'abort', 'force'])

    if choice == 'abort':
        git_merge_abort()
    elif choice == 'force':
        git_checkout_theirs(conflicts)
        git_add(conflicts)
        git_commit("Resolve conflicts: keep breakdown changes")
```

---

## Collaboration Workflows

### Team Workflow: Pull Request Review

1. **Developer A** starts breakdown on feature branch
2. Creates PR when section batch complete
3. **Reviewer** reviews requirements in PR
4. Comments/suggestions addressed
5. Merge to main when approved

### Team Workflow: Shared Breakdown

1. **Developer A** starts breakdown, completes sections 1-5
2. Commits and pushes
3. **Developer B** pulls, runs `/prd-breakdown-resume`
4. Completes sections 6-10
5. Both developers' work tracked in git history

### Handling Simultaneous Edits

If two people edit the same section:

```python
def handle_concurrent_edit(section_id):
    """Handle concurrent edits to same section"""

    # Fetch latest
    git_fetch()

    # Check if remote has changes
    if remote_has_changes(f"PRD/breakdown/{section_id}"):
        show_warning(f"""
Remote changes detected for {section_id}!

Someone else has updated this section since you started.

Options:
1. Pull and merge their changes first
2. Continue and resolve conflicts later
3. Discard your changes and use remote version
        """)

        choice = ask_user_choice(['pull', 'continue', 'discard'])

        if choice == 'pull':
            git_pull()
            # May need to re-run validation
```

---

## Best Practices

### 1. Commit Frequently
- Use `auto_commit: true` for automatic commits
- Don't let multiple sections accumulate uncommitted

### 2. Meaningful Messages
- Customize commit message template for your team
- Include stats for easier review

### 3. Branch Strategy Selection
- **Single branch**: Simple projects, solo work
- **Feature branch**: Team collaboration, PR review
- **Per-section**: Large PRDs, parallel work

### 4. Review Before Finalize
- Use PR workflow for final review
- Ensure all conflicts resolved

### 5. Backup Strategy
- Git provides history, but also keep checkpoint files
- Consider remote backup (push regularly)

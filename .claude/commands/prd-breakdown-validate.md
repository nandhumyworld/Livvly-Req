# Validate PRD Section

**Trigger**: `/prd-breakdown-validate <section-id>`

**Purpose**: Run quality checks on completed section

---

## Parameters
- `<section-id>`: Required. Section to validate (e.g., "05-feature-specifications")

---

## Validation Checks

### 1. Requirements Have Acceptance Criteria
**Rule**: Every requirement needs ≥2 acceptance criteria

**Check**:
```python
for requirement in requirements:
    if len(requirement['acceptance_criteria']) < 2:
        violations.append({
            'req_id': requirement['id'],
            'issue': f"Only {len(requirement['acceptance_criteria'])} criterion (min: 2)"
        })
```

### 2. Design Decisions Have Rationale
**Rule**: Every DD needs non-empty "Rationale" section (≥50 chars)

**Check**:
```python
for dd in design_decisions:
    if len(dd['rationale']) < 50:
        violations.append({
            'dd_id': dd['id'],
            'issue': f"Rationale too short ({len(dd['rationale'])} chars, min: 50)"
        })
```

### 3. Research Citations Complete
**Rule**: Every external finding needs source + URL + date

**Check**:
```python
for finding in external_research:
    if not finding.get('url'):
        violations.append({
            'finding': finding['query'],
            'issue': "Missing URL"
        })
    if not finding.get('date_accessed'):
        violations.append({
            'finding': finding['query'],
            'issue': "Missing date"
        })
```

### 4. No Orphaned Requirements
**Rule**: Dependencies must reference existing requirements

**Check**:
```python
all_req_ids = get_all_requirement_ids()

for requirement in requirements:
    for dep in requirement['dependencies']:
        if dep not in all_req_ids and dep != "None":
            violations.append({
                'req_id': requirement['id'],
                'issue': f"Dependency '{dep}' does not exist"
            })
```

### 5. Cross-Reference Integrity
**Rule**: Design decision references must be valid

**Check**:
```python
all_dd_ids = get_all_design_decision_ids()

for requirement in requirements:
    for dd_ref in requirement['related_design_decisions']:
        if dd_ref not in all_dd_ids and dd_ref != "None":
            violations.append({
                'req_id': requirement['id'],
                'issue': f"Design decision '{dd_ref}' does not exist"
            })
```

---

## Output

### Success
```
╔═══════════════════════════════════════════════════════╗
║     VALIDATION: 05-feature-specifications              ║
╚═══════════════════════════════════════════════════════╝

✓ Acceptance Criteria: All have ≥2
✓ DD Rationale: All documented
✓ Research Citations: All complete
✓ Dependencies: No orphans
✓ Cross-References: All valid

Section Status: VALID ✓
```

### Failure
```
╔═══════════════════════════════════════════════════════╗
║     VALIDATION: 05-feature-specifications              ║
╚═══════════════════════════════════════════════════════╝

✗ Acceptance Criteria (3 violations):
  • REQ-FS-012: Only 1 criterion (min: 2)
  • REQ-FS-023: Only 0 criteria
  • REQ-FS-034: Only 1 criterion

✗ Research Citations (2 violations):
  • Finding 'FRND share': Missing date
  • Finding 'Market size': Missing reliability

Section Status: INVALID ✗
Found 5 violations.

Fix: /prd-breakdown-update 05-feature-specifications
```

---

## Workflow

### Step 1: Load Section
```python
def validate_section(section_id):
    # Load section metadata
    metadata = load_metadata()
    section = find_section_by_id(metadata, section_id)

    if not section:
        error(f"Section {section_id} not found")

    if section['status'] != 'completed':
        warning(f"Section {section_id} not completed, validation may be incomplete")

    # Load section files
    requirements = load_requirements(section_id)
    design_decisions = load_design_decisions(section_id) if exists else []
    research = load_research_notes(section_id) if exists else []

    return {
        'requirements': requirements,
        'design_decisions': design_decisions,
        'research': research
    }
```

### Step 2: Run Validation Checks
```python
def run_validation_checks(section_data):
    violations = []

    # Check 1: Acceptance Criteria
    violations.extend(validate_acceptance_criteria(section_data['requirements']))

    # Check 2: DD Rationale
    violations.extend(validate_dd_rationale(section_data['design_decisions']))

    # Check 3: Research Citations
    violations.extend(validate_research_citations(section_data['research']))

    # Check 4: Dependencies
    violations.extend(validate_dependencies(section_data['requirements']))

    # Check 5: Cross-References
    violations.extend(validate_cross_references(section_data['requirements']))

    return violations
```

### Step 3: Display Results
```python
def display_validation_results(section_id, violations):
    if not violations:
        show_success(section_id)
    else:
        show_failures(section_id, violations)

        # Group violations by type
        grouped = group_violations_by_type(violations)

        # Display each violation type
        for vtype, v_list in grouped.items():
            print(f"\n✗ {vtype} ({len(v_list)} violations):")
            for v in v_list:
                print(f"  • {v['issue']}")

        # Suggest fix
        print(f"\nFix: /prd-breakdown-update {section_id}")
```

---

## Auto-Validate Option

**Configuration**: Enable in user settings

```json
{
  "prd_breakdown": {
    "auto_validate_on_completion": true,
    "validation_level": "strict"  // "strict" | "normal" | "loose"
  }
}
```

**Behavior**:
- When section completes, automatically run validation
- If validation fails, prompt user to fix before continuing
- If validation passes, continue to next section

**Validation Levels**:

**Strict**:
- All 5 checks must pass
- No warnings allowed
- Blocks progression on any violation

**Normal** (default):
- All 5 checks run
- Warnings allowed (but displayed)
- Blocks progression only on critical violations

**Loose**:
- Only checks 1, 4, 5 (critical checks)
- Warnings allowed
- Never blocks progression

---

## Examples

### Example 1: Valid Section
```bash
/prd-breakdown-validate 01-executive-summary

╔═══════════════════════════════════════════════════════╗
║     VALIDATION: 01-executive-summary                   ║
╚═══════════════════════════════════════════════════════╝

✓ Acceptance Criteria: All 12 requirements have ≥2
✓ DD Rationale: N/A (no design decisions)
✓ Research Citations: All 5 sources complete
✓ Dependencies: No orphans
✓ Cross-References: All valid

Section Status: VALID ✓
```

### Example 2: Invalid Section
```bash
/prd-breakdown-validate 05-feature-specifications

╔═══════════════════════════════════════════════════════╗
║     VALIDATION: 05-feature-specifications              ║
╚═══════════════════════════════════════════════════════╝

✗ Acceptance Criteria (3 violations):
  • REQ-FS-012: Only 1 criterion (min: 2)
    Current: "User can initiate voice call"
    Add: At least one more testable criterion

  • REQ-FS-023: Only 0 criteria
    Missing: Acceptance criteria section entirely

  • REQ-FS-034: Only 1 criterion
    Current: "Dashboard loads within 2s"
    Add: At least one more testable criterion

✗ Design Decisions (1 violation):
  • DD-FS-003: Rationale too short (28 chars, min: 50)
    Current: "WebSocket for real-time"
    Expand: Explain why WebSocket over alternatives

Section Status: INVALID ✗
Found 4 violations.

Recommended Actions:
1. Add missing acceptance criteria to REQ-FS-012, REQ-FS-034
2. Add acceptance criteria section to REQ-FS-023
3. Expand DD-FS-003 rationale with comparison to alternatives

Fix: /prd-breakdown-update 05-feature-specifications
```

---

## Related Commands
- `/prd-breakdown-update <section-id>` - Fix validation issues
- `/prd-breakdown-status` - Show progress dashboard
- `/prd-breakdown-redo <section-id>` - Restart section if major issues

---

## References
- **METADATA_SCHEMA.md**: Section 8 (Validation Functions)
- **TEMPLATES.md**: Expected structure for requirements and design decisions
- **WORKFLOW.md**: Section 7 (Error Handling)

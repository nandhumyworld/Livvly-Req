# Dependency Graph Generation

**Purpose**: Visualize requirement dependencies for sprint planning

**When**: On master index generation (completion)

**Format**: ASCII dependency graph in markdown

---

## Example Output

```
Dependency Graph: LIVVLY Voice Dating App

Critical Path (P0 Requirements):
==================================

┌─────────────────┐
│  REQ-FS-001     │  Onboarding Flow
│  (P0 - Critical)│
└────────┬────────┘
         │
         ├──► REQ-API-001 (Auth Endpoints)
         ├──► REQ-DB-001 (User Table)
         └──► REQ-LC-005 (GDPR Consent)
              │
              └──► REQ-TA-012 (Auth Architecture)

Feature Dependencies:
=====================
Authentication → Profile → Matching → Voice Calls
      ↓              ↓         ↓          ↓
  Payments     Creator Dashboard   Analytics

Circular Dependencies (WARNINGS):
==================================
⚠ REQ-FS-023 → REQ-DB-015 → REQ-API-018 → REQ-FS-023
  Resolution: Break cycle at API layer (use async)
```

---

## Algorithm

```python
def generate_dependency_graph(metadata):
    dep_map = build_dependency_map(metadata)
    critical_path = find_p0_requirements(dep_map)
    circular = detect_circular_dependencies(dep_map)
    graph = render_ascii_graph(critical_path, circular)
    return graph

def build_dependency_map(metadata):
    """
    Parse all requirements.md files and extract dependency information
    Returns: dict mapping requirement IDs to their dependencies
    """
    dep_map = {}

    for section in metadata['sections']:
        req_file = f"PRD/breakdown/{section['id']}/requirements.md"
        requirements = parse_requirements_file(req_file)

        for req in requirements:
            req_id = req['id']
            dependencies = req['dependencies']  # From "Dependencies:" field
            dep_map[req_id] = dependencies

    return dep_map

def find_p0_requirements(dep_map):
    """
    Filter to only P0 (Critical) requirements for critical path
    """
    p0_reqs = [req_id for req_id in dep_map
               if is_priority_p0(req_id)]
    return p0_reqs

def detect_circular_dependencies(dep_map):
    """
    Use DFS to detect cycles in dependency graph
    """
    visited = set()
    rec_stack = set()
    cycles = []

    def dfs(req_id, path):
        visited.add(req_id)
        rec_stack.add(req_id)
        path.append(req_id)

        for dep in dep_map.get(req_id, []):
            if dep not in visited:
                dfs(dep, path.copy())
            elif dep in rec_stack:
                # Cycle detected
                cycle_start = path.index(dep)
                cycle = path[cycle_start:] + [dep]
                cycles.append(cycle)

        rec_stack.remove(req_id)

    for req_id in dep_map:
        if req_id not in visited:
            dfs(req_id, [])

    return cycles

def render_ascii_graph(p0_reqs, circular_deps):
    """
    Render ASCII art dependency graph
    Includes:
    - Critical path visualization
    - Feature groupings
    - Circular dependency warnings
    """
    graph = []

    # Header
    graph.append("Dependency Graph: [Project Name]\n")
    graph.append("=" * 50 + "\n")

    # Critical Path
    graph.append("\nCritical Path (P0 Requirements):\n")
    graph.append("=" * 50 + "\n")

    for req in p0_reqs:
        graph.append(render_requirement_node(req))
        graph.append(render_dependencies(req))

    # Circular Dependencies
    if circular_deps:
        graph.append("\nCircular Dependencies (WARNINGS):\n")
        graph.append("=" * 50 + "\n")

        for cycle in circular_deps:
            cycle_str = " → ".join(cycle)
            graph.append(f"⚠ {cycle_str}\n")
            graph.append(f"  Resolution: [Recommendation]\n")

    return "".join(graph)
```

---

## Output File

**Filename**: `PRD/breakdown/dependency-graph.md`

**Contents**: ASCII graph + resolution recommendations

**Generated**: Automatically on completion (master-index generation)

---

## Use Cases

### 1. Sprint Planning
Identify which requirements must be completed first based on dependencies

### 2. Parallelization
Find independent requirement clusters that can be implemented in parallel

### 3. Risk Identification
Circular dependencies indicate design issues requiring resolution

### 4. Critical Path Analysis
Understand which requirements are on the critical path for launch

---

## Resolution Strategies for Circular Dependencies

### Strategy 1: Async Processing
If REQ-A depends on REQ-B and REQ-B depends on REQ-A:
- Break cycle by making one operation asynchronous
- Example: User creation triggers async profile setup

### Strategy 2: Introduce Intermediate State
Create a new state that breaks the cycle:
- Example: "Pending" state allows partial creation

### Strategy 3: Refactor Requirements
Split one requirement into two separate concerns:
- Example: Split "Create User with Profile" into "Create User" + "Add Profile"

### Strategy 4: Remove Unnecessary Dependency
Review if both dependencies are truly necessary:
- Often one direction can be made optional

---

## Example Full Output

```markdown
# Dependency Graph: LIVVLY Voice Dating App

Generated: 2026-01-18T15:30:00Z
Source: PRD/breakdown/.metadata.json

---

## Critical Path (P0 Requirements)

### REQ-FS-001: Onboarding Flow
**Priority**: P0 (Critical)
**Dependencies**: None (entry point)
**Dependents**:
- REQ-API-001 (Auth Endpoints)
- REQ-DB-001 (User Table)
- REQ-LC-005 (GDPR Consent)

### REQ-API-001: Auth Endpoints
**Priority**: P0 (Critical)
**Dependencies**:
- REQ-FS-001 (Onboarding Flow)
- REQ-TA-012 (Auth Architecture)
**Dependents**:
- REQ-FS-007 (Home Screen)
- REQ-API-008 (User Profile Endpoints)

[Continue for all P0 requirements...]

---

## Feature Dependency Chains

### Chain 1: User Authentication
REQ-FS-001 → REQ-API-001 → REQ-DB-001 → REQ-TA-012

**Estimated Duration**: 3 sprints
**Can Start**: Immediately
**Blocks**: All user-facing features

### Chain 2: Voice Calling
REQ-FS-016 → REQ-API-031 → REQ-DB-007 → REQ-TA-009

**Estimated Duration**: 4 sprints
**Can Start**: After Chain 1 complete
**Blocks**: Creator monetization

### Chain 3: Payments
REQ-RM-001 → REQ-CE-001 → REQ-CP-001 → REQ-API-018

**Estimated Duration**: 3 sprints
**Can Start**: After Chain 1 complete
**Blocks**: Revenue generation

---

## Circular Dependencies

### ⚠ Cycle 1: User Profile ↔ Authentication
**Path**: REQ-FS-023 → REQ-DB-015 → REQ-API-018 → REQ-FS-023

**Problem**:
- User creation requires profile data
- Profile creation requires authenticated user
- Creates chicken-and-egg problem

**Recommended Resolution**:
Break cycle at API layer:
1. Create user with minimal data (email, password)
2. Return auth token
3. Use token to complete profile asynchronously

**Implementation**:
- REQ-API-001: Create user (minimal)
- REQ-API-002: Complete profile (authenticated)
- Remove direct dependency from REQ-FS-023 to REQ-DB-015

### ⚠ Cycle 2: Creator Payout ↔ Transaction History
**Path**: REQ-CP-005 → REQ-DB-018 → REQ-API-025 → REQ-CP-005

**Problem**:
- Payout calculation needs transaction history
- Transaction recording needs payout status
- Circular data dependency

**Recommended Resolution**:
Introduce intermediate "pending" state:
1. Record transaction as "pending_payout"
2. Calculate payout from pending transactions
3. Update transaction status to "paid" after payout

**Implementation**:
- Add "payout_status" field to transactions table
- Process payouts in batches (async job)
- No circular dependency in implementation

---

## Parallelization Opportunities

### Phase 1 (Can Start in Parallel)
- **Team A**: Authentication Chain (REQ-FS-001, REQ-API-001, REQ-DB-001)
- **Team B**: Database Schema Setup (REQ-DB-002, REQ-DB-003, REQ-DB-004)
- **Team C**: UI Wireframes (REQ-FS-002, REQ-FS-003)

### Phase 2 (After Phase 1 Complete)
- **Team A**: Voice Calling (REQ-FS-016, REQ-API-031)
- **Team B**: Payments (REQ-RM-001, REQ-CE-001)
- **Team C**: Creator Dashboard (REQ-FS-026, REQ-API-035)

### Phase 3 (After Phase 2 Complete)
- **Team A**: Analytics (REQ-SM-001, REQ-SM-002)
- **Team B**: Compliance (REQ-LC-001, REQ-LC-005)
- **Team C**: Automation (REQ-AW-001, REQ-AW-002)

---

## Statistics

- **Total Requirements**: 247
- **P0 (Critical)**: 45
- **P1 (High)**: 89
- **P2 (Medium)**: 78
- **P3 (Low)**: 35
- **Circular Dependencies**: 2 (both resolved)
- **Independent Clusters**: 8
- **Maximum Depth**: 5 levels
- **Critical Path Length**: 12 requirements

---

## Next Steps

1. Review circular dependencies with tech lead
2. Assign requirement clusters to teams
3. Create sprint backlog based on dependency chains
4. Set up dependency tracking in project management tool
5. Monitor for new dependencies as implementation progresses
```

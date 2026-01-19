# Context Persistence

This document describes the context persistence mechanism for maintaining project-specific information across sessions.

---

## Overview

Context persistence allows the PRD breakdown skill to remember project-specific information across sessions, eliminating repetitive questions and maintaining consistency throughout the breakdown process.

---

## File Location

`PRD/breakdown/context.json`

---

## Purpose

- **Eliminate redundant questions**: Don't ask about terminology already defined
- **Maintain consistency**: Use same definitions throughout breakdown
- **Speed up processing**: Skip clarification for known domain concepts
- **Support team collaboration**: Share context across team members

---

## Schema

```json
{
  "project_domain": "string",
  "project_name": "string",
  "created_at": "ISO 8601 timestamp",
  "last_updated": "ISO 8601 timestamp",

  "stakeholders": [
    {
      "role": "string",
      "name": "string (optional)",
      "responsibilities": "string"
    }
  ],

  "terminology": {
    "term": {
      "definition": "string",
      "examples": ["string"],
      "related_terms": ["string"],
      "defined_in_section": "string (optional)"
    }
  },

  "domain_context": {
    "industry": "string",
    "target_market": "string",
    "geographic_focus": ["string"],
    "regulatory_context": ["string"]
  },

  "technical_context": {
    "existing_stack": ["string"],
    "constraints": ["string"],
    "integrations": ["string"]
  },

  "preferences": {
    "requirement_id_prefix": "string (default: REQ)",
    "design_decision_id_prefix": "string (default: DD)",
    "preferred_priority_scale": "P0-P3 | MoSCoW | High-Medium-Low",
    "include_time_estimates": "boolean",
    "auto_research_enabled": "boolean"
  },

  "learned_context": {
    "clarifications": [
      {
        "question": "string",
        "answer": "string",
        "section": "string",
        "timestamp": "ISO 8601 timestamp"
      }
    ]
  }
}
```

---

## Field Descriptions

### Core Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `project_domain` | string | Yes | High-level domain (e.g., "dating-app", "e-commerce", "fintech") |
| `project_name` | string | Yes | Project name extracted from PRD |
| `created_at` | timestamp | Yes | When context was first created |
| `last_updated` | timestamp | Yes | Last modification timestamp |

### Stakeholders

Define key stakeholders involved in the project:

```json
"stakeholders": [
  {
    "role": "Product",
    "name": "John Doe",
    "responsibilities": "Feature prioritization, user stories"
  },
  {
    "role": "Engineering",
    "responsibilities": "Technical feasibility, architecture decisions"
  },
  {
    "role": "Legal",
    "responsibilities": "Compliance review, terms of service"
  }
]
```

### Terminology

Project-specific term definitions to ensure consistency:

```json
"terminology": {
  "Creator": {
    "definition": "Content creator who hosts live rooms and receives payments",
    "examples": ["Room host", "Streamer", "Influencer"],
    "related_terms": ["Viewer", "Subscriber"],
    "defined_in_section": "01-executive-summary"
  },
  "Viewer": {
    "definition": "User who watches streams and sends gifts/coins",
    "examples": ["Audience member", "Subscriber"],
    "related_terms": ["Creator", "Gift"],
    "defined_in_section": "01-executive-summary"
  },
  "Active User": {
    "definition": "User who engages at least once per week (view profiles, make calls, or join rooms)",
    "examples": ["Weekly active user"],
    "related_terms": ["DAU", "MAU"],
    "defined_in_section": "15-success-metrics"
  }
}
```

### Domain Context

Industry and market-specific information:

```json
"domain_context": {
  "industry": "Social/Dating",
  "target_market": "India Tier-1 and Tier-2 cities",
  "geographic_focus": ["India", "South Asia"],
  "regulatory_context": ["DPDPA (India)", "IT Act 2000", "RBI Payment Guidelines"]
}
```

### Technical Context

Existing technical landscape:

```json
"technical_context": {
  "existing_stack": ["React Native", "Node.js", "PostgreSQL", "Redis"],
  "constraints": ["Must support 3G networks", "Battery efficiency critical"],
  "integrations": ["Razorpay", "Agora.io", "Firebase"]
}
```

### Preferences

User preferences for breakdown output:

```json
"preferences": {
  "requirement_id_prefix": "REQ",
  "design_decision_id_prefix": "DD",
  "preferred_priority_scale": "P0-P3",
  "include_time_estimates": false,
  "auto_research_enabled": true
}
```

### Learned Context

Clarifications captured during Q&A sessions:

```json
"learned_context": {
  "clarifications": [
    {
      "question": "What defines an 'active user'?",
      "answer": "Users who engage at least once per week",
      "section": "01-executive-summary",
      "timestamp": "2026-01-18T10:15:00Z"
    }
  ]
}
```

---

## Usage Workflow

### Initialization

```python
def initialize_context(prd_path, prd_content):
    """Create initial context from PRD analysis"""

    context = {
        'project_domain': detect_domain(prd_content),
        'project_name': extract_project_name(prd_path),
        'created_at': current_timestamp(),
        'last_updated': current_timestamp(),
        'stakeholders': [],
        'terminology': {},
        'domain_context': {},
        'technical_context': {},
        'preferences': get_default_preferences(),
        'learned_context': {
            'clarifications': []
        }
    }

    # Auto-detect domain context
    context['domain_context'] = analyze_prd_for_domain(prd_content)

    # Auto-detect technical context
    context['technical_context'] = analyze_prd_for_tech(prd_content)

    return context
```

### Loading Context

```python
def load_context(breakdown_dir):
    """Load existing context or return None"""

    context_path = join(breakdown_dir, 'context.json')

    if file_exists(context_path):
        return load_json(context_path)

    return None
```

### Updating Context from Q&A

```python
def update_context_from_qa(context, section_id, question, answer):
    """Add clarification to learned context"""

    # Check if this defines a term
    if is_terminology_definition(question, answer):
        term = extract_term(question)
        context['terminology'][term] = {
            'definition': answer,
            'examples': [],
            'related_terms': [],
            'defined_in_section': section_id
        }

    # Always add to clarifications
    context['learned_context']['clarifications'].append({
        'question': question,
        'answer': answer,
        'section': section_id,
        'timestamp': current_timestamp()
    })

    context['last_updated'] = current_timestamp()

    return context
```

### Using Context in Question Generation

```python
def generate_questions_with_context(section, prd_content, context):
    """Generate questions, skipping those already answered in context"""

    questions = generate_core_questions(section, prd_content)
    filtered_questions = []

    for question in questions:
        # Check if question is already answered in terminology
        if is_terminology_question(question):
            term = extract_term_from_question(question)
            if term in context['terminology']:
                # Skip - already defined
                log(f"Skipping question about '{term}' - already defined in context")
                continue

        # Check if question is similar to previous clarification
        if is_similar_to_clarification(question, context['learned_context']['clarifications']):
            similar = find_similar_clarification(question, context)
            log(f"Using existing answer from context: {similar['answer']}")
            # Optionally confirm with user: "Last time you said X. Is that still correct?"
            continue

        filtered_questions.append(question)

    return filtered_questions
```

---

## Context Merging (Team Collaboration)

When multiple team members work on the same PRD:

```python
def merge_contexts(context_a, context_b):
    """Merge two context files, preserving all unique information"""

    merged = deepcopy(context_a)

    # Merge terminology (prefer most recent definition)
    for term, definition in context_b['terminology'].items():
        if term not in merged['terminology']:
            merged['terminology'][term] = definition
        else:
            # Keep the more recent one
            if definition['defined_in_section'] > merged['terminology'][term]['defined_in_section']:
                merged['terminology'][term] = definition

    # Merge clarifications (deduplicate by question)
    existing_questions = {c['question'] for c in merged['learned_context']['clarifications']}
    for clarification in context_b['learned_context']['clarifications']:
        if clarification['question'] not in existing_questions:
            merged['learned_context']['clarifications'].append(clarification)

    # Merge stakeholders (deduplicate by role)
    existing_roles = {s['role'] for s in merged['stakeholders']}
    for stakeholder in context_b['stakeholders']:
        if stakeholder['role'] not in existing_roles:
            merged['stakeholders'].append(stakeholder)

    merged['last_updated'] = current_timestamp()

    return merged
```

---

## Example Instance

```json
{
  "project_domain": "dating-app",
  "project_name": "LIVVLY Voice Dating App",
  "created_at": "2026-01-18T10:00:00Z",
  "last_updated": "2026-01-18T14:30:00Z",

  "stakeholders": [
    {
      "role": "Product",
      "name": "Product Team",
      "responsibilities": "Feature prioritization, user research, roadmap"
    },
    {
      "role": "Engineering",
      "responsibilities": "Technical architecture, implementation, scalability"
    },
    {
      "role": "Legal",
      "responsibilities": "Compliance, terms of service, data privacy"
    },
    {
      "role": "Finance",
      "responsibilities": "Payment processing, creator payouts, taxation"
    }
  ],

  "terminology": {
    "Creator": {
      "definition": "Content creator who hosts live voice rooms and can earn money through gifts",
      "examples": ["Room host", "Streamer", "Voice talent"],
      "related_terms": ["Viewer", "Gift", "Coin"],
      "defined_in_section": "01-executive-summary"
    },
    "Viewer": {
      "definition": "User who joins rooms, watches content, and can send gifts to creators",
      "examples": ["Audience member", "Participant"],
      "related_terms": ["Creator", "Gift"],
      "defined_in_section": "01-executive-summary"
    },
    "Coin": {
      "definition": "In-app virtual currency purchased with real money, used to send gifts",
      "examples": ["Virtual currency", "Credits"],
      "related_terms": ["Gift", "Recharge", "Payout"],
      "defined_in_section": "09-coin-economy"
    },
    "Active User": {
      "definition": "User who opens the app and engages (view profiles, make calls, or join rooms) at least once per week",
      "examples": ["WAU - Weekly Active User"],
      "related_terms": ["DAU", "MAU", "Engagement"],
      "defined_in_section": "15-success-metrics"
    },
    "T+1 Payout": {
      "definition": "Creator earnings paid out the day after they are earned (next business day)",
      "examples": ["Next-day settlement"],
      "related_terms": ["Payout", "Settlement"],
      "defined_in_section": "10-creator-payout"
    }
  },

  "domain_context": {
    "industry": "Social/Dating",
    "target_market": "India - Tier 1 and Tier 2 cities, ages 18-35",
    "geographic_focus": ["India", "South Asia expansion planned"],
    "regulatory_context": [
      "DPDPA (Digital Personal Data Protection Act) - India",
      "IT Act 2000",
      "RBI Payment Aggregator Guidelines",
      "App Store Guidelines (Apple/Google)"
    ]
  },

  "technical_context": {
    "existing_stack": [
      "React Native (Mobile)",
      "Node.js (Backend)",
      "PostgreSQL (Primary DB)",
      "Redis (Caching, Pub/Sub)",
      "AWS (Infrastructure)"
    ],
    "constraints": [
      "Must work on 3G networks (latency tolerance)",
      "Battery efficiency critical for mobile",
      "Support Android 8+ and iOS 13+",
      "Must scale to 50K concurrent users"
    ],
    "integrations": [
      "Razorpay (Payments)",
      "Agora.io (Voice/Video)",
      "Firebase (Push notifications, analytics)",
      "AWS S3 (Media storage)"
    ]
  },

  "preferences": {
    "requirement_id_prefix": "REQ",
    "design_decision_id_prefix": "DD",
    "preferred_priority_scale": "P0-P3",
    "include_time_estimates": false,
    "auto_research_enabled": true
  },

  "learned_context": {
    "clarifications": [
      {
        "question": "What defines an 'active user' in the 50K target?",
        "answer": "Users who open the app and engage (view profiles, make calls, or join rooms) at least once per week.",
        "section": "01-executive-summary",
        "timestamp": "2026-01-18T10:15:00Z"
      },
      {
        "question": "What's the acceptable latency for real-time voice?",
        "answer": "Sub-200ms for voice calls, under 500ms for room broadcasts",
        "section": "06-technical-architecture",
        "timestamp": "2026-01-18T11:30:00Z"
      },
      {
        "question": "What timezone applies to T+1 payout cutoff?",
        "answer": "IST (Indian Standard Time), cutoff at 11:59 PM",
        "section": "10-creator-payout",
        "timestamp": "2026-01-18T13:00:00Z"
      }
    ]
  }
}
```

---

## Integration Points

### 1. `/prd-breakdown-start`
- Create initial `context.json` during initialization
- Auto-populate domain and technical context from PRD analysis
- Ask user to confirm/edit detected context

### 2. Section Breakdown Loop
- Load context before generating questions
- Filter questions based on existing terminology
- Update context after each Q&A session
- Save context after each section completion

### 3. `/prd-breakdown-resume`
- Load existing context
- Display relevant context for current section
- Continue building on existing terminology

### 4. `/prd-breakdown-update`
- Check if update affects terminology definitions
- Update context if definitions change
- Log changes in learned_context

### 5. Master Index Generation
- Include terminology glossary from context
- Reference stakeholders in documentation

---

## Commands

### View Context
Display current context:
```
/prd-breakdown-status --context
```

### Edit Context
Manually edit context:
```
/prd-breakdown-context edit
```

Opens context.json for manual editing or provides interactive prompts.

### Export Context
Export context for sharing:
```
/prd-breakdown-context export --format json|yaml
```

### Import Context
Import context from another project or team member:
```
/prd-breakdown-context import <file-path>
```

---

## Migration

For existing breakdowns without context.json:

```python
def migrate_to_context(breakdown_dir):
    """Generate context from existing Q&A files"""

    context = create_empty_context()

    # Scan all questions-answers.md files
    for section_dir in list_section_dirs(breakdown_dir):
        qa_file = join(section_dir, 'questions-answers.md')
        if file_exists(qa_file):
            qa_content = parse_qa_file(qa_file)
            for qa in qa_content:
                update_context_from_qa(context, section_dir, qa['question'], qa['answer'])

    # Save generated context
    write_json(join(breakdown_dir, 'context.json'), context)

    return context
```

---

## Privacy Considerations

- Context may contain sensitive project information
- Add to `.gitignore` if project details are confidential
- Option to exclude context from version control:
  ```json
  "preferences": {
    "exclude_from_git": true
  }
  ```

# Complete PRD Breakdown Workflows

This document details the complete workflows for all PRD breakdown operations with pseudo-code and decision trees.

---

## Section 1: Initialize Phase

### PRD Location and Parsing Logic

#### Step 1: Locate PRD File

```
if user_provided_path:
    prd_path = user_provided_path
else:
    search_prd_directory()
    if multiple_files_found:
        ask_user_which_to_process()
    elif single_file_found:
        prd_path = found_file
    else:
        ask_user_for_path()

validate_prd_file(prd_path):
    if not file_exists(prd_path):
        error("File not found")
    if not is_markdown(prd_path):
        error("Not a markdown file")
    return prd_path
```

#### Step 2: Parse PRD Structure

```
def parse_prd_structure(prd_path):
    lines = read_file_lines(prd_path)
    sections = []
    current_section = None

    for line_num, line in enumerate(lines):
        # Match header patterns: # Section or ## Subsection
        if line.startswith("# ") and not line.startswith("## "):
            # Top-level section
            if current_section:
                current_section['line_end'] = line_num - 1
                sections.append(current_section)

            section_title = line[2:].strip()
            current_section = {
                'title': section_title,
                'id': generate_section_id(section_title),
                'line_start': line_num,
                'line_end': None,
                'subsections': []
            }

        elif line.startswith("## ") and current_section:
            # Subsection (for complex sections like Feature Specs)
            subsection_title = line[3:].strip()
            subsection = {
                'title': subsection_title,
                'line_start': line_num
            }
            current_section['subsections'].append(subsection)

    # Close last section
    if current_section:
        current_section['line_end'] = len(lines) - 1
        sections.append(current_section)

    return sections

def generate_section_id(title):
    # Convert "Executive Summary" → "01-executive-summary"
    # Keep sequential numbering
    number = "{:02d}".format(section_index)
    slug = title.lower().replace(" ", "-").replace("&", "and")
    return f"{number}-{slug}"

def estimate_complexity(section):
    """
    Enhanced complexity estimation with multiple factors.
    Returns: {"level": str, "time": int (minutes)}
    """
    line_count = section['line_end'] - section['line_start']
    section_text = extract_section_text(prd_content, section)

    # Base from line count
    if line_count < 100:
        base = 8
    elif line_count < 300:
        base = 12
    else:
        base = 18

    score = base

    # Modifiers
    if section['id'] in ['05','06','07','08']:  # Technical sections
        score += 5
    if count_tables(section_text) > 3:
        score += 3  # Tables require careful parsing
    if count_code_blocks(section_text) > 2:
        score += 4  # Code blocks need analysis
    if has_legal_terms(section_text):
        score += 3  # Legal/compliance needs research
    if count_quantitative_claims(section_text) > 5:
        score += 2  # Data claims need validation
    if count_competitor_mentions(section_text) > 3:
        score += 2  # Competitor research needed

    # Classify
    if score < 10:
        level = "simple"
    elif score < 18:
        level = "moderate"
    else:
        level = "complex"

    return {"level": level, "time": score}

def display_complexity_breakdown(section, complexity):
    """
    Show user why section has this complexity estimate
    """
    print(f"""
Section: {section['title']}
Complexity: {complexity['level'].title()} ({complexity['time']} min estimated)

Factors:
  • Line count: {section['line_end'] - section['line_start']} (+{base_score})
  • Technical section: {is_technical} (+{5 if is_technical else 0})
  • Tables: {table_count} (+{table_score})
  • Code blocks: {code_count} (+{code_score})
  • Legal terms: {legal_count} (+{legal_score})
Total: {complexity['time']} minutes
    """)
```

#### Step 3: Create Directory Structure

```
def create_breakdown_structure(prd_path):
    prd_dir = parent_directory(prd_path)
    breakdown_dir = join(prd_dir, "breakdown")

    create_directory(breakdown_dir)

    # Create subdirectories for each section
    for section in sections:
        section_dir = join(breakdown_dir, section['id'])
        create_directory(section_dir)

        # For complex sections with multiple screens, create sub-folder
        if should_create_screen_specs(section):
            screen_specs_dir = join(section_dir, "screen-specs")
            create_directory(screen_specs_dir)
```

#### Step 4: Initialize Metadata

```
def initialize_metadata(prd_path, sections):
    metadata = {
        'prd_source': relative_path(prd_path),
        'prd_name': extract_project_name(prd_path),  # "LIVVLY Voice Dating App"
        'total_sections': len(sections),
        'breakdown_started': current_timestamp(),
        'last_modified': current_timestamp(),
        'sections': [],
        'overall_progress': {
            'completed': 0,
            'in_progress': 0,
            'pending': len(sections),
            'percentage': 0.0
        }
    }

    for idx, section in enumerate(sections):
        section_obj = {
            'id': section['id'],
            'title': section['title'],
            'status': 'pending',  # pending | in_progress | completed | archived
            'line_start': section['line_start'],
            'line_end': section['line_end'],
            'complexity': estimate_complexity(section),
            'started_at': None,
            'completed_at': None,
            'files_generated': [],
            'requirement_count': 0,
            'design_decision_count': 0,
            'question_count': 0,
            'research_source_count': 0
        }
        metadata['sections'].append(section_obj)

    write_json(join(breakdown_dir, ".metadata.json"), metadata)
    return metadata
```

---

## Section 2: Section Breakdown Loop (Core Algorithm)

### Progressive Questioning & Answering

```
def breakdown_section(section, prd_content, metadata):
    # Update metadata: section in_progress
    update_metadata_status(metadata, section['id'], 'in_progress')
    show_progress(f"Processing: {section['title']}")

    # ===== PHASE 0: PRE-FLIGHT PREVIEW (OPTIONAL) =====
    if user_config['enable_question_preview']:
        core_questions = generate_core_questions(section, prd_content)
        estimated_followups = estimate_followup_count(section)
        estimated_gaps = estimate_gap_count(section)
        total_estimated = len(core_questions) + estimated_followups + estimated_gaps
        estimated_time = total_estimated * 1.5  # ~1.5 min per question

        show_preflight_preview(section, core_questions, estimated_followups,
                               estimated_gaps, estimated_time)

        user_choice = ask_user("Options: (1) See all questions first, (2) Answer interactively, (3) Skip questions")

        if user_choice == "1":
            # Display all questions, collect all answers at once
            all_questions = collect_all_questions_upfront(section, prd_content, core_questions)
            all_answers = wait_for_all_answers(all_questions)
            core_answers = all_answers['core']
            followup_answers = all_answers['followups']
            gap_answers = all_answers['gaps']
            skip_to_research = True
        elif user_choice == "3":
            # Skip all questions, proceed with PRD only
            core_answers = {}
            followup_answers = {}
            gap_answers = {}
            skip_to_research = True
        else:
            # Proceed with standard interactive questioning
            skip_to_research = False

    # ===== PHASE 1: CORE QUESTIONS =====
    if not skip_to_research:
        core_questions = generate_core_questions(section, prd_content)
        # 2-3 questions, displayed all at once
        show_to_user(core_questions)
        core_answers = wait_for_answers(core_questions)
    record_answers(metadata, section['id'], 'phase_1_core', core_answers)

    # ===== PHASE 2: FOLLOW-UP QUESTIONS =====
    followup_questions = analyze_and_generate_followups(
        section,
        prd_content,
        core_answers
    )
    # Only ask if there are meaningful follow-ups (not empty list)
    if followup_questions:
        show_to_user(followup_questions)
        followup_answers = wait_for_answers(followup_questions)
        record_answers(metadata, section['id'], 'phase_2_followup', followup_answers)
    else:
        followup_answers = {}

    # ===== PHASE 3: GAP ANALYSIS QUESTIONS =====
    gap_questions = identify_gaps(
        section,
        prd_content,
        core_answers,
        followup_answers
    )
    # Only ask if gaps exist
    if gap_questions:
        show_to_user(gap_questions)
        gap_answers = wait_for_answers(gap_questions)
        record_answers(metadata, section['id'], 'phase_3_gaps', gap_answers)
    else:
        gap_answers = {}

    # Combine all answers
    all_answers = combine(core_answers, followup_answers, gap_answers)

    # ===== RESEARCH PHASE =====
    research_needed = determine_research_needed(section, all_answers, prd_content)
    research_notes = {}

    if research_needed:
        # Internal research first (codebase)
        internal_findings = conduct_internal_research(section, all_answers)
        research_notes['internal'] = internal_findings

        # Then external research if needed
        if needs_external_research(section, all_answers):
            external_findings = conduct_external_research(section, all_answers)
            research_notes['external'] = external_findings

        record_research(metadata, section['id'], research_notes)

    # ===== REQUIREMENTS EXTRACTION =====
    requirements = extract_requirements(
        section,
        prd_content,
        all_answers,
        research_notes
    )

    # ===== FILE GENERATION =====
    # Always create requirements.md
    write_requirements_file(
        join(breakdown_dir, section['id'], "requirements.md"),
        section,
        requirements
    )

    # Conditionally create questions-answers.md
    if all_answers:
        write_qa_file(
            join(breakdown_dir, section['id'], "questions-answers.md"),
            section,
            all_answers
        )

    # Conditionally create research-notes.md
    if research_notes:
        write_research_file(
            join(breakdown_dir, section['id'], "research-notes.md"),
            section,
            research_notes
        )

    # Conditionally create design-decisions.md (technical sections only)
    if is_technical_section(section):
        design_decisions = extract_design_decisions(section, all_answers, research_notes)
        if design_decisions:
            write_design_decisions_file(
                join(breakdown_dir, section['id'], "design-decisions.md"),
                section,
                design_decisions
            )

    # ===== UPDATE METADATA =====
    update_metadata(metadata, section['id'], {
        'status': 'completed',
        'completed_at': current_timestamp(),
        'files_generated': [
            'requirements.md',
            'questions-answers.md' if all_answers else None,
            'research-notes.md' if research_notes else None,
            'design-decisions.md' if is_technical_section(section) else None
        ].filter(not_none),
        'requirement_count': len(requirements),
        'design_decision_count': len(design_decisions) if is_technical_section(section) else 0,
        'question_count': len(all_answers),
        'research_source_count': count_sources(research_notes)
    })

    # Show completion
    show_progress(f"✓ Completed: {section['title']} ({len(requirements)} requirements)")

    return metadata
```

### Question Generation Logic

```
def generate_core_questions(section, prd_content):
    """Generate 2-3 core clarifying questions"""

    questions = []
    section_text = extract_section_text(prd_content, section)

    # Strategy 1: Identify ambiguous terms
    ambiguous_terms = find_ambiguous_terms(section_text)
    for term in ambiguous_terms[:1]:  # Pick one
        questions.append({
            'type': 'clarification',
            'text': f"What do you mean by '{term}' in this section?",
            'rationale': f"The term '{term}' appears in the PRD but isn't explicitly defined"
        })

    # Strategy 2: Validate quantitative claims
    quantitative_claims = find_quantitative_claims(section_text)
    for claim in quantitative_claims[:1]:  # Pick one
        questions.append({
            'type': 'validation',
            'text': f"You mention '{claim}'. What's the basis for this number?",
            'rationale': f"Need to validate the assumption behind {claim}"
        })

    # Strategy 3: Identify constraints
    implied_constraints = find_implied_constraints(section_text)
    for constraint in implied_constraints[:1]:  # Pick one
        questions.append({
            'type': 'constraint',
            'text': f"I see you mention '{constraint}'. What specific limitations does this impose?",
            'rationale': f"Need to understand the implementation implications"
        })

    return questions[:3]  # Return max 3 core questions

def analyze_and_generate_followups(section, prd_content, core_answers):
    """Dynamically generate follow-up questions based on core answers"""

    followup_questions = []

    for question, answer in core_answers.items():
        # Analyze the answer to determine follow-ups

        if "real-time" in answer.lower():
            followup_questions.append({
                'parent_question': question,
                'type': 'follow-up',
                'text': "What's the acceptable latency for real-time updates? (sub-second, <1s, <5s?)",
                'rationale': "Different latency targets have different architectural implications"
            })

        if "offline" in answer.lower():
            followup_questions.append({
                'parent_question': question,
                'type': 'follow-up',
                'text': "How should data be synced when users come back online?",
                'rationale': "Need to understand conflict resolution strategy"
            })

        if "manual" in answer.lower():
            followup_questions.append({
                'parent_question': question,
                'type': 'follow-up',
                'text': "What happens if manual entry takes longer than expected?",
                'rationale': "Need to understand timeout/error handling"
            })

        # Add more conditional follow-ups based on domain knowledge
        # Technology-specific follow-ups
        if "microservices" in answer.lower():
            followup_questions.append({
                'type': 'follow-up',
                'text': "How will you handle service-to-service communication reliability?",
                'rationale': "Critical for distributed systems"
            })

    return followup_questions[:4]  # Return max 4 follow-ups

def identify_gaps(section, prd_content, core_answers, followup_answers):
    """Identify missing information and ask gap analysis questions"""

    gap_questions = []
    all_answers = combine(core_answers, followup_answers)
    section_text = extract_section_text(prd_content, section)

    # Common missing elements by section type
    if section['id'].startswith('05'):  # Feature Specifications
        if "loading state" not in section_text.lower():
            gap_questions.append({
                'type': 'gap',
                'text': "I don't see specification for loading states while [action] is in progress. Should we define that?",
                'rationale': "UX completeness - loading states affect user experience"
            })

        if "empty state" not in section_text.lower():
            gap_questions.append({
                'type': 'gap',
                'text': "What should users see if there's no data to display?",
                'rationale': "Empty state specification is often overlooked"
            })

    if section['id'].startswith('06'):  # Technical Architecture
        if "scalability" not in all_answers_combined.lower():
            gap_questions.append({
                'type': 'gap',
                'text': "What's the expected scale? (users, requests per second, data volume?)",
                'rationale': "Scale drives architectural decisions"
            })

    if section['id'].startswith('12'):  # Legal Compliance
        if "gdpr" not in section_text.lower() and "dpdpa" not in section_text.lower():
            gap_questions.append({
                'type': 'gap',
                'text': "Which data protection regulations apply to your users?",
                'rationale': "Compliance requirements vary by jurisdiction"
            })

    return gap_questions[:3]  # Return max 3 gap questions
```

### Research Decision Tree

```
def determine_research_needed(section, all_answers, prd_content):
    """Decision tree: should we research this section?"""

    section_text = extract_section_text(prd_content, section)

    # Rule 1: Does PRD mention competitor data?
    if has_competitor_references(section_text):
        return True

    # Rule 2: Does PRD make market size claims?
    if has_market_size_claims(section_text):
        return True

    # Rule 3: Does PRD mention existing implementation (Razorpay, etc.)?
    if has_existing_tech_references(section_text):
        return True  # Conduct internal research

    # Rule 4: Are there technology choices without rationale?
    if has_technology_choices(section_text) and not has_rationale(section_text):
        return True

    # Rule 5: Are there compliance/legal implications?
    if has_legal_terms(section_text) or has_compliance_keywords(section_text):
        return True

    return False

def conduct_internal_research(section, all_answers):
    """Search codebase for existing implementations"""

    findings = []

    # Identify search terms from section and answers
    search_terms = extract_search_terms(section, all_answers)
    # Examples: "Razorpay", "login flow", "payment service"

    for term in search_terms:
        # Search codebase
        results = search_codebase(term)

        for result in results:
            finding = {
                'query': term,
                'location': result['file'],
                'line_range': result['lines'],
                'code_snippet': result['snippet'],
                'relevance': analyze_relevance(result, section),
                'how_it_relates': describe_how_relates_to_requirement(result, section)
            }
            findings.append(finding)

    return findings

def conduct_external_research(section, all_answers):
    """Research web for competitor data, tech best practices, compliance"""

    findings = []

    # Identify research queries from section content
    research_queries = extract_research_queries(section, all_answers)
    # Examples: "competitor revenue share", "GDPR requirements India"

    for query in research_queries:
        # Perform web search
        results = web_search(query)

        for result in results[:3]:  # Take top 3 results per query
            # Fetch and analyze result
            content = fetch_url(result['url'])

            finding = {
                'query': query,
                'source_name': result['source'],
                'url': result['url'],
                'date_accessed': current_date(),
                'reliability': assess_reliability(result['source']),
                'key_excerpt': extract_key_excerpt(content),
                'relevance_to_requirement': describe_relevance(content, section)
            }
            findings.append(finding)

    return findings
```

---

## Section 3: Batch Mode Workflow

```
def batch_mode(prd_path, section_range=None):
    """Process multiple sections with upfront question collection"""

    # Load or initialize metadata
    if metadata_exists(prd_path):
        metadata = load_metadata(prd_path)
        pending_sections = [s for s in metadata['sections']
                           if s['status'] in ['pending', 'in_progress']]
    else:
        initialize_prd(prd_path)
        pending_sections = metadata['sections']

    # Ask which sections to process
    if section_range:
        selected_sections = parse_range(section_range, pending_sections)
    else:
        ask_user("Which sections to process? (1-5, 10-15, all)")
        selected_sections = get_user_selection()

    show_to_user(f"Selected {len(selected_sections)} sections for batch processing")

    # ===== PHASE 1: QUESTION COLLECTION =====
    all_questions_by_section = {}

    for section in selected_sections:
        show_to_user(f"\n=== {section['title']} ===")

        # Generate core questions for each section
        core_qs = generate_core_questions(section, prd_content)
        all_questions_by_section[section['id']] = {
            'phase_1': core_qs,
            'phase_2': [],  # Will be generated in processing phase
            'phase_3': []
        }

        display_formatted_questions(section['id'], core_qs)

    # Display all questions at once, ask user to answer
    show_to_user("\n=== ALL QUESTIONS FOR BATCH PROCESSING ===")
    show_formatted_qa_form(all_questions_by_section)

    all_answers = wait_for_all_answers()

    # ===== PHASE 2: SEQUENTIAL PROCESSING =====
    prd_content = read_prd(prd_path)

    for idx, section in enumerate(selected_sections):
        section_answers = all_answers[section['id']]

        # Continue with standard section breakdown
        # (using answers already collected in Phase 1)
        breakdown_section_with_preloaded_answers(
            section,
            prd_content,
            metadata,
            section_answers
        )

        # Show progress
        show_progress(f"Completed: {idx + 1}/{len(selected_sections)}")

    # Summary
    show_summary(selected_sections, all_answers, metadata)
```

---

## Section 4: Command Handling

### Resume Command

```
def resume_breakdown(prd_path):
    """Continue from last incomplete section"""

    # Load metadata
    metadata = load_metadata(prd_path)

    # Find resumption point
    in_progress = [s for s in metadata['sections'] if s['status'] == 'in_progress']
    if in_progress:
        resume_section = in_progress[0]
        reason = "was in progress"
    else:
        pending = [s for s in metadata['sections'] if s['status'] == 'pending']
        if pending:
            resume_section = pending[0]
            reason = "is next pending"
        else:
            show_to_user("✓ All sections completed!")
            return

    # Show progress summary
    completed_count = len([s for s in metadata['sections'] if s['status'] == 'completed'])
    total_count = metadata['total_sections']
    percentage = (completed_count / total_count) * 100

    show_to_user(f"""
    ╔═══════════════════════════════════════════════════════╗
    ║              RESUME PRD BREAKDOWN                      ║
    ╚═══════════════════════════════════════════════════════╝

    Progress: {completed_count}/{total_count} sections ({percentage:.1f}%)

    Next Section: {resume_section['title']} ({reason})
    Complexity: {resume_section['complexity']}
    Estimated Time: ~{estimate_time(resume_section)} minutes
    """)

    # Ask confirmation
    if confirm("Resume from this section?"):
        prd_content = read_prd(prd_path)
        breakdown_section(resume_section, prd_content, metadata)
```

### Update Command

```
def update_section(section_id):
    """Make targeted updates to an existing section"""

    section = load_section_metadata(section_id)
    if not section:
        error(f"Section {section_id} not found")

    # Load existing files
    requirements_file = load_file(f"breakdown/{section_id}/requirements.md")
    qa_file = load_file(f"breakdown/{section_id}/questions-answers.md") if exists else None
    research_file = load_file(f"breakdown/{section_id}/research-notes.md") if exists else None

    # Ask what to update
    show_to_user("What would you like to update?")
    update_type = ask_user_choice([
        "Add new requirements",
        "Modify existing requirements",
        "Add research findings",
        "Update design decisions",
        "Other"
    ])

    if update_type == "Add new requirements":
        new_reqs = ask_for_new_requirements()
        append_to_file(requirements_file, new_reqs)

    elif update_type == "Modify existing requirements":
        show_requirements(requirements_file)
        req_to_modify = ask_user("Which requirement to modify? (ID: REQ-XX-001)")
        modifications = ask_user("What changes?")
        update_requirement(requirements_file, req_to_modify, modifications)

    # Update metadata
    update_metadata(section_id, {
        'last_modified': current_timestamp()
    })

    show_to_user(f"✓ Updated section {section_id}")
```

### Redo Command

```
def redo_section(section_id):
    """Archive existing and restart section"""

    section = load_section_metadata(section_id)
    if not section:
        error(f"Section {section_id} not found")

    # Confirm with user
    if not confirm(f"Archive existing files for {section['title']} and restart? This cannot be undone."):
        return

    # Archive existing files
    section_dir = f"breakdown/{section_id}"
    for file in list_files(section_dir):
        timestamp = format_timestamp(current_timestamp())
        new_name = f"{file}.ARCHIVED_{timestamp}"
        rename_file(join(section_dir, file), join(section_dir, new_name))

    # Reset metadata
    update_metadata(section_id, {
        'status': 'pending',
        'started_at': None,
        'completed_at': None,
        'files_generated': [],
        'requirement_count': 0,
        'design_decision_count': 0,
        'question_count': 0,
        'research_source_count': 0
    })

    # Start fresh breakdown
    prd_content = read_prd(prd_path)
    breakdown_section(section, prd_content, metadata)
```

### Status Command

```
def display_status():
    """Show comprehensive progress dashboard"""

    metadata = load_metadata()

    completed = [s for s in metadata['sections'] if s['status'] == 'completed']
    in_progress = [s for s in metadata['sections'] if s['status'] == 'in_progress']
    pending = [s for s in metadata['sections'] if s['status'] == 'pending']

    percentage = metadata['overall_progress']['percentage']

    # Build status display
    status_display = f"""
    ╔═══════════════════════════════════════════════════════╗
    ║     PRD BREAKDOWN STATUS - {metadata['prd_name']}
    ╚═══════════════════════════════════════════════════════╝

    Overall Progress: [{'█' * int(percentage/5)}{'░' * (20 - int(percentage/5))}] {percentage:.1f}% ({len(completed)}/{metadata['total_sections']} complete)

    ┌───────────────────────────────────────────────────────┐
    │ COMPLETED ({len(completed)})
    ├────┬────────────────────┬──────────┬─────────────────┤
    │ ID │ Section            │ Reqs     │ Completed       │
    """

    for section in completed:
        ago = format_time_ago(section['completed_at'])
        status_display += f"\n│ {section['id'][:2]} │ {section['title']:<18} │ {section['requirement_count']:<8} │ {ago:<15} │"

    if in_progress:
        status_display += f"""

    ┌───────────────────────────────────────────────────────┐
    │ IN PROGRESS ({len(in_progress)})
    ├────┼────────────────────┼──────────┼─────────────────┤
    """
        for section in in_progress:
            ago = format_time_ago(section['started_at'])
            status_display += f"\n│ {section['id'][:2]} │ {section['title']:<18} │ {section['complexity']:<8} │ {ago:<15} │"

    status_display += f"""

    ┌───────────────────────────────────────────────────────┐
    │ PENDING ({len(pending)})
    ├────┬────────────────────┬──────────┬─────────────────┤
    │ ID │ Section            │ Time Est │ Complexity      │
    """

    for section in pending[:5]:  # Show first 5 pending
        time_est = estimate_time(section)
        status_display += f"\n│ {section['id'][:2]} │ {section['title']:<18} │ {time_est}min   │ {section['complexity']:<15} │"

    if len(pending) > 5:
        status_display += f"\n│ ... │ {len(pending)-5} more sections"

    # Statistics so far
    total_reqs = sum(s['requirement_count'] for s in completed)
    total_dds = sum(s['design_decision_count'] for s in completed)
    total_qs = sum(s['question_count'] for s in completed)
    total_sources = sum(s['research_source_count'] for s in completed)

    status_display += f"""

    Statistics (so far):
    • Requirements: {total_reqs}
    • Design Decisions: {total_dds}
    • Questions Answered: {total_qs}
    • Research Sources: {total_sources}

    Next Action: /prd-breakdown-resume
    Commands: /prd-breakdown-resume | /prd-breakdown-update <section> | /prd-breakdown-status
    """

    show_to_user(status_display)
```

---

## Section 4.5: Checkpoint Feature (Long Breakdowns)

**Trigger**: Every 5 completed sections (automatic)

**Actions**:
```python
def create_checkpoint(metadata, n):
    # Save checkpoint metadata
    write_json(f"PRD/breakdown/.metadata.CHECKPOINT_{n}.json", metadata)

    # Generate mini master-index preview
    preview = generate_master_index_preview(metadata)
    write_file(f"PRD/breakdown/PREVIEW_checkpoint_{n}.md", preview)

    # Display dashboard
    show_checkpoint_dashboard(n, metadata)
```

**Dashboard**:
```
╔═══════════════════════════════════════════════════════╗
║       CHECKPOINT #2 REACHED (10/15 sections)           ║
╚═══════════════════════════════════════════════════════╝

Progress: 66.7% (10/15)
Time Elapsed: 2h 15m | Remaining: ~1h 8m

Statistics: 127 reqs, 34 DDs, 89 questions, 45 sources

✓ Checkpoint: .metadata.CHECKPOINT_2.json
✓ Preview: PREVIEW_checkpoint_2.md

Next: /prd-breakdown-resume or take a break
```

---

## Section 5: Master Index Generation Rules

### When to Generate

**1. On Completion** (always)
- Trigger: All sections completed
- File: `PRD/breakdown/master-index.md`

**2. On Status Check** (optional)
- Trigger: `/prd-breakdown-status --with-preview`
- File: `PRD/breakdown/PREVIEW_master-index.md`
- Contains: Completed sections only

**3. On Section Update** (conditional)
- Trigger: `/prd-breakdown-update <section-id>` completes
- Action: Prompt "Regenerate master-index? (y/n)"
- Only if master-index.md already exists

**4. On Checkpoint** (always)
- Trigger: Every 5 sections
- File: `PRD/breakdown/PREVIEW_checkpoint_{N}.md`

### Master vs Preview

| Feature | Master (Final) | Preview (Interim) |
|---------|----------------|-------------------|
| Filename | master-index.md | PREVIEW_*.md |
| Completeness | All sections | Completed only |
| Statistics | Final totals | Running totals |
| Generated | On completion | On demand/checkpoints |

### Finalization Phase

```
def finalize_breakdown(metadata):
    """Generate master index and finalize metadata"""

    # Verify all sections completed
    incomplete = [s for s in metadata['sections'] if s['status'] != 'completed']
    if incomplete:
        ask_user(f"Warning: {len(incomplete)} sections incomplete. Continue anyway? (yes/no)")

    # ===== GENERATE MASTER INDEX =====
    master_index = generate_master_index(metadata)
    write_file("PRD/breakdown/master-index.md", master_index)

    # ===== CALCULATE STATISTICS =====
    statistics = {
        'total_requirements': sum(s['requirement_count'] for s in metadata['sections']),
        'total_design_decisions': sum(s['design_decision_count'] for s in metadata['sections']),
        'total_questions_answered': sum(s['question_count'] for s in metadata['sections']),
        'total_research_sources': sum(s['research_source_count'] for s in metadata['sections']),
        'breakdown_duration': calculate_duration(
            metadata['breakdown_started'],
            current_timestamp()
        ),
        'average_section_time': calculate_average_time(metadata),
        'longest_section': find_longest_section(metadata),
        'shortest_section': find_shortest_section(metadata)
    }

    # ===== UPDATE METADATA =====
    metadata['statistics'] = statistics
    metadata['last_modified'] = current_timestamp()
    write_json("PRD/breakdown/.metadata.json", metadata)

    # ===== DISPLAY COMPLETION SUMMARY =====
    show_to_user(f"""
    ╔═══════════════════════════════════════════════════════╗
    ║              BREAKDOWN COMPLETE! ✓                     ║
    ╚═══════════════════════════════════════════════════════╝

    Processed: {metadata['total_sections']} sections
    Time Taken: {statistics['breakdown_duration']}
    Average per Section: {statistics['average_section_time']:.1f} minutes

    Output Statistics:
    • Requirements: {statistics['total_requirements']}
    • Design Decisions: {statistics['total_design_decisions']}
    • Questions Answered: {statistics['total_questions_answered']}
    • Research Sources: {statistics['total_research_sources']}

    Files Created:
    • Master Index: PRD/breakdown/master-index.md
    • Metadata: PRD/breakdown/.metadata.json
    • Section Folders: 15 organized directories

    Next Steps:
    1. Review PRD/breakdown/master-index.md
    2. Start development using relevant sections
    3. Use /prd-breakdown-update <section> to make changes
    4. Use /prd-breakdown-status to track progress

    Location: PRD/breakdown/
    """)
```

---

## Section 6: Edge Cases & Error Handling

### Corrupted Metadata Recovery

```
def handle_corrupted_metadata(prd_path):
    """Recover from corrupted .metadata.json"""

    try:
        metadata = load_json("PRD/breakdown/.metadata.json")
    except JSONDecodeError:
        show_to_user("⚠ Metadata file is corrupted")

        if confirm("Restore from backup? (if available)"):
            try:
                backup = find_most_recent_backup()
                restore_file(backup, "PRD/breakdown/.metadata.json")
                metadata = load_json("PRD/breakdown/.metadata.json")
            except:
                show_to_user("No backup found")
                if confirm("Reinitialize metadata (this may lose progress)?"):
                    # Re-parse PRD and initialize fresh
                    sections = parse_prd_structure(prd_path)
                    metadata = initialize_metadata(prd_path, sections)
                else:
                    abort("Cannot proceed without valid metadata")

    return metadata
```

### Interrupted Processing

```
def handle_interrupted_processing():
    """Allow resumption after interruption (Ctrl+C)"""

    # When user interrupts mid-section:
    # 1. Current section remains "in_progress" (not "completed")
    # 2. Incomplete files are NOT written
    # 3. User can run /prd-breakdown-resume to continue

    # Example: User is on section 5, halfway through Q&A
    # - Question 1: Answered ✓
    # - Question 2: Interrupted ✗

    # When resumed:
    # - Section 5 is still "in_progress"
    # - Previous answers to Q1 are lost (restart questioning)
    # - User can choose: continue or redo the section
```

### Missing Sections in PRD

```
def handle_missing_sections():
    """If PRD structure changes"""

    # Scenario: User updates PRD after breakdown started
    # Original: 15 sections
    # New: 16 sections (one added)

    old_sections = load_from_metadata()
    new_sections = parse_prd_structure(updated_prd)

    if len(new_sections) != len(old_sections):
        added = find_new_sections(old_sections, new_sections)
        removed = find_removed_sections(old_sections, new_sections)

        if added:
            show_to_user(f"Found {len(added)} new sections in PRD")
            if confirm("Add to breakdown?"):
                for section in added:
                    add_section_to_metadata(section)

        if removed:
            show_to_user(f"Removed {len(removed)} sections from PRD")
            if confirm("Archive these from breakdown?"):
                for section in removed:
                    update_status(section, "archived")
```

---

## Decision Trees

### When to Create Design Decisions File

```
if section_id in ['05-feature-specifications',
                  '06-technical-architecture',
                  '07-database-schema',
                  '08-api-specifications']:
    # These are technical sections - create DD file
    if has_meaningful_design_decisions(section):
        create_design_decisions_file()
else:
    # Business, implementation, operational sections - skip DD file
    skip_design_decisions_file()
```

### When to Conduct Research

```
if has_competitor_references(section_text) \
   or has_market_size_claims(section_text) \
   or has_existing_tech_references(section_text) \
   or has_technology_choices_without_rationale(section_text) \
   or has_compliance_keywords(section_text):
    conduct_research()
else:
    skip_research()
```

### Question Complexity Estimation

```
simple_section = {
    'line_count': <100,
    'complexity': 'simple',
    'estimated_time': '8 minutes',
    'question_count': 2,
    'follow_ups': 1
}

moderate_section = {
    'line_count': 100-300,
    'complexity': 'moderate',
    'estimated_time': '12 minutes',
    'question_count': 3,
    'follow_ups': 2
}

complex_section = {
    'line_count': >300,
    'complexity': 'complex',
    'estimated_time': '18 minutes',
    'question_count': 3,
    'follow_ups': 4,
    'research_likely': True
}
```

---

## Section 7: Comprehensive Error Handling

### Network Failures During Research
**Scenarios**: WebSearch timeout, WebFetch connection refused, rate limiting

**Decision Tree**:
```
if web_search_fails:
    if attempt_count < 3:
        retry_with_exponential_backoff()
    else:
        log_failure(query, error_type)
        ask_user("Research failed for [query]. Should I: (1) Skip, (2) Retry, (3) Ask you?")

if skip: mark_section_research_incomplete() + add_to_open_questions()
if retry: wait_for_user_signal() + retry_search()
if ask: convert_research_to_question()
```

**Recovery**:
- Log failed queries to metadata under `failed_research_queries`
- Continue section without research
- Mark requirements with "Research Incomplete" flag
- Generate summary: "3 research queries failed, see open questions"

### File System Errors
**Scenarios**: Disk full, permission denied, file locked

**Recovery**: Create .tmp files first, verify write, then rename

### PRD Parse Errors
**Scenarios**: Malformed markdown, no headers found, invalid line ranges

**Recovery**: Offer manual section definition, validate structure

### User Timeout/Abandonment
**Scenarios**: No response for 10+ minutes during Q&A

**Recovery**: Save partial progress to metadata, allow resume

### Validation Failures
**Scenarios**: Metadata integrity check fails

**Recovery**: Auto-fix common issues, offer manual review, reinitialize option

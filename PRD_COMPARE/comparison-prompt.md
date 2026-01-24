# App Development Requirements Comparison Prompt

You are a requirements analyst. Your task is to comprehensively compare the app development requirements from two different stakeholders: **Kapil** and **M. Kumaran**.

The requirements are organized in 15 sections, each containing:
- A `requirements.md` file (main requirements documentation)
- A `questions-answers.md` file (supporting Q&A and clarifications)

## Sections to Compare:
1. 01-executive-summary
2. 02-market-analysis
3. 03-product-vision
4. 04-revenue-model
5. 05-feature-specifications
6. 06-technical-architecture
7. 07-database-schema
8. 08-api-specifications
9. 09-coin-economy-design
10. 10-creator-payout-system
11. 11-n8n-automation-workflows
12. 12-legal-and-compliance
13. 13-implementation-roadmap
14. 14-cost-estimation
15. 15-success-metrics

---

## REPORT 1: REQUIREMENTS.MD COMPARISON

For each section, analyze the `requirements.md` files from both stakeholders and provide:

### [Section Name]

**Kapil's Requirements:**
- Summarize the main requirements from Kapil's requirements.md

**M. Kumaran's Requirements:**
- Summarize the main requirements from M. Kumaran's requirements.md

**Key Differences:**
- List specific differences in approach, scope, or technical decisions
- Include priority differences (if evident)
- Note any conflicting requirements

**Similarities:**
- List what both agree on
- Highlight aligned goals and objectives

**Gaps & Conflicts:**
- Identify contradictions between requirements
- Point out missing elements in one vs. the other
- Flag potential implementation conflicts

**Unique to Kapil:**
- Requirements/features only Kapil mentions

**Unique to M. Kumaran:**
- Requirements/features only M. Kumaran mentions

**Risk Assessment:**
- Identify requirements that may be risky if not aligned
- Flag scope creep potential
- Note integration challenges

**Recommendations for Merging:**
- How to reconcile the requirements
- Suggested prioritization
- Implementation approach that satisfies both

---

## REPORT 2: QUESTIONS-ANSWERS.MD COMPARISON

For each section, analyze the `questions-answers.md` files from both stakeholders and provide:

### [Section Name]

**Kapil's Key Questions & Answers:**
- List main Q&A points from Kapil's questions-answers.md

**M. Kumaran's Key Questions & Answers:**
- List main Q&A points from M. Kumaran's questions-answers.md

**Alignment in Understanding:**
- Where do their answers to similar questions align?
- What shared concerns do they express?

**Divergent Interpretations:**
- Where do their Q&A show different interpretations?
- Which questions were handled differently?

**Clarifications Needed:**
- Questions answered by one but not the other
- Ambiguities that need resolution
- Missing clarifications

**Unique Concerns:**
- Unique risks/concerns raised by Kapil
- Unique risks/concerns raised by M. Kumaran

**Technical Insights from Q&A:**
- Technical decisions revealed in Q&A
- Implementation preferences shown
- Dependencies or constraints mentioned

**Recommendation Based on Q&A:**
- Which Q&A answers should guide final decisions?
- Missing Q&A that should be addressed?
- Common ground from discussion?

---

## EXECUTIVE SUMMARY

After completing both reports (Requirements.md and Questions-Answers.md), provide a comprehensive executive summary with:

### OVERALL DIFFERENCES SUMMARY
- Core philosophical/approach differences between Kapil and M. Kumaran
- Scope differences (what each person wants to include/exclude)
- Technical architecture differences
- Timeline and cost estimate differences
- Priority differences (if evident)
- Risk tolerance differences

### OVERALL SIMILARITIES SUMMARY
- Shared vision for the app
- Common goals and objectives
- Aligned technical expectations
- Shared concerns and priorities
- Agreed-upon scope areas
- Common success metrics

### CRITICAL CONFLICTS SUMMARY
- **High Priority Conflicts:** Must be resolved before proceeding
  - List each conflict
  - Impact if unresolved
  - Suggested resolution

- **Medium Priority Conflicts:** Should be resolved early
  - List each conflict
  - Impact if unresolved
  - Suggested resolution

- **Low Priority Conflicts:** Can be addressed during implementation
  - List each conflict
  - Impact if unresolved
  - Suggested resolution

### MERGED REQUIREMENTS ROADMAP
- Top 5 agreed-upon priorities
- Top 3 areas needing stakeholder alignment
- Recommended approach combining best of both perspectives
- Suggested timeline/phasing that addresses both visions
- Resource requirements from both perspectives

### STAKEHOLDER ALIGNMENT SCORE
For each major area (0-100%):
- Executive Summary alignment: X%
- Market Analysis alignment: X%
- Product Vision alignment: X%
- Revenue Model alignment: X%
- Feature Specifications alignment: X%
- Technical Architecture alignment: X%
- Database Schema alignment: X%
- API Specifications alignment: X%
- Coin Economy Design alignment: X%
- Creator Payout System alignment: X%
- N8N Workflows alignment: X%
- Legal & Compliance alignment: X%
- Implementation Roadmap alignment: X%
- Cost Estimation alignment: X%
- Success Metrics alignment: X%

**Overall Alignment: X%**

### ACTION ITEMS
- List specific discussions/decisions needed between Kapil and M. Kumaran
- Priority order for alignment meetings
- Key decision-makers needed for each conflict area

---

## ADDITIONAL ANALYSIS

### Technology Stack Differences
- Frontend preferences
- Backend preferences
- Database choices
- Third-party integrations
- DevOps and deployment approaches

### Feature Priority Differences
- Must-have features per stakeholder
- Nice-to-have features per stakeholder
- Out-of-scope items per stakeholder

### Timeline & Cost Differences
- Estimated timelines
- Budget assumptions
- Resource allocation
- Phase prioritization

### Success Metrics Alignment
- KPIs each stakeholder cares about
- Measurement approaches
- Success definitions

---

## OUTPUT FORMAT

Please structure the final output as:

**REPORT 1: REQUIREMENTS.MD DETAILED COMPARISON**
[Detailed comparison of all 15 sections]

**REPORT 2: QUESTIONS-ANSWERS.MD DETAILED COMPARISON**
[Detailed comparison of all 15 sections]

**EXECUTIVE SUMMARY**
[Consolidated summary with all analyses above]

---

## INSTRUCTIONS

1. Read all files from both `kapil/breakdown/` and `mkumaran/breakdown/` directories
2. For each section, analyze both requirements.md AND questions-answers.md
3. Provide honest assessment of alignment and conflicts
4. Be specific with examples and quotes when possible
5. Highlight areas where one stakeholder has more detailed planning
6. Note where assumptions differ
7. Identify technical debt or implementation risks from each perspective
8. Flag any requirements that might be unrealistic or need revisiting

Begin the analysis now.

# Research Approach Guidelines

Framework for conducting internal (codebase) and external (web) research during PRD breakdown.

---

## When to Research: Decision Tree

### Rule 1: Does PRD mention competitor data?
**Examples**:
- "Competitors offer 50-60% creator revenue share"
- "Market leaders Hike and Instagram have [feature]"
- "Existing platforms struggle with [problem]"

**Action**: Conduct external research to validate claims and gather current data

---

### Rule 2: Does PRD make market size claims?
**Examples**:
- "TAM of $2B in India voice apps"
- "50K active users target"
- "5% market penetration in 18 months"

**Action**: Conduct external research to validate with analyst reports, market sizing methodologies

---

### Rule 3: Does PRD mention existing implementation?
**Examples**:
- "Similar to our existing login flow"
- "Use Razorpay integration"
- "Based on creator dashboard pattern"

**Action**: Conduct internal research to find and analyze existing code

---

### Rule 4: Are there technology choices without rationale?
**Examples**:
- "Use WebSocket for real-time updates"
- "PostgreSQL for primary datastore"
- "Redis for caching"

**Action**: Conduct external research on alternatives and best practices

---

### Rule 5: Are there compliance/legal implications?
**Examples**:
- "DPDPA compliance required"
- "TDS auto-deduction for creator payouts"
- "COPPA compliance for <13 users"

**Action**: Conduct external research on current regulatory requirements

---

### Rule 6: Are there implicit assumptions?
**Examples**:
- "Tamil-first experience" (implies language, cultural nuances)
- "Creator empowerment" (implies features, transparency)
- "Secure payment processing" (implies encryption, PCI compliance)

**Action**: May require research to validate feasibility or common approaches

---

## Internal Research (Codebase)

### Tools Available
- `find_symbol(name_path_pattern)`: Search for classes, functions, methods
- `search_for_pattern(regex, file_type)`: Search for code patterns
- `find_referencing_symbols(name_path, file)`: Find usages
- `read_file(path)`: Read complete file content
- `get_symbols_overview(file)`: Get high-level file structure

### Process

#### Step 1: Identify Search Terms
From PRD section and Q&A answers, extract terms to search:

Example:
```
PRD mentions: "similar to login flow"
Search terms: ["login", "authentication", "auth_service", "LoginFlow"]
```

#### Step 2: Search Codebase
```
for search_term in search_terms:
    results = find_symbol(search_term) OR search_for_pattern(search_term)
    if results:
        read relevant files
        document findings
```

#### Step 3: Analyze Findings
For each finding:
- **What**: What is the component/pattern?
- **How**: How is it implemented?
- **Where**: File path and line range
- **Why**: Why was this design chosen (if evident)?

#### Step 4: Document with Attribution
```
Finding: Existing Razorpay Integration

Location: src/services/payment_service.py:45-120
Method: find_symbol("RazorpayClient")

Component Details:
- Class: RazorpayClient
- Methods: create_checkout(), verify_payment(), create_refund()
- Integration: Stripe-compatible webhook handling
- Rate Limit: 100 requests/minute

Code Snippet:
[Key code excerpt]

Relevance: Can reuse for coin recharge (REQ-RM-012) and creator payouts (REQ-CP-005)
Reusability: 70% reusable - existing checkout can be extended for coin bundles
Gaps: Doesn't handle recurring payments or subscriptions

Next Steps:
1. Extend RazorpayClient to support subscriptions
2. Add tax deduction middleware
3. Implement dispute handling
```

### Example Research: Payment Service

**Query**: "How does current payment integration work?"

**Search Terms**: ["razorpay", "payment", "checkout", "payment_service"]

**Findings**:
1. **Location**: `src/payment/razorpay_client.py`
   - RazorpayClient class with checkout integration
   - Webhook verification implemented
   - No subscription support (found in code review)

2. **Location**: `src/models/transaction.py`
   - Transaction model tracks order details
   - Missing field: creator_id (need to add)
   - Has payment_status but missing payout_status

3. **Location**: `src/api/payment_routes.py`
   - POST /checkout endpoint returns payment link
   - No POST /payout endpoint (need to create)

**Impact on Requirements**:
- REQ-RM-012 (Coin purchase): Can extend existing checkout
- REQ-CP-001 (Creator payout): Need to create new payout endpoints
- REQ-DB-015 (Schema): Need to add creator_id and payout_status to transactions

---

## External Research (Web)

### Tools Available
- `WebSearch(query)`: Search the web
- `WebFetch(url, prompt)`: Fetch and analyze URL content

### Research Categories

#### Category 1: Market Data
**When**: PRD makes market sizing or competitor claims

**Examples**:
- "FRND app creator revenue share"
- "India voice chat app market size 2026"
- "Competitor comparison: Instagram vs Hike vs custom platforms"

**Process**:
1. Search for reputable sources (TechCrunch, Gartner, Statista)
2. Fetch articles and extract key data
3. Cross-reference multiple sources
4. Note any contradictions

**Documentation**:
```
Finding: FRND Creator Revenue Share

Source: TechCrunch
URL: https://techcrunch.com/2025/12/15/indian-social-apps-creators
Date Accessed: 2026-01-18
Reliability: High (reputable tech journalism)

Key Excerpt:
"FRND offers 50-55% creator share after platform fees (30% app store cut)"

Validation: PRD claims competitors offer "50-60% revenue share"
Result: ✓ Validated. FRND is 50-55%, we propose 60%, which is slightly above market.

Citation: TechCrunch. (2025). "Indian Social Apps Race for Creator Revenue."
```

#### Category 2: Technology Options
**When**: Technology choice needs validation or alternatives analysis

**Examples**:
- "WebSocket vs SSE for real-time updates"
- "PostgreSQL vs MongoDB for user data"
- "Agora vs WebRTC for voice calling"

**Process**:
1. Search for comparison articles
2. Find pros/cons analysis
3. Look for performance benchmarks
4. Check latest documentation

**Documentation**:
```
Finding: WebSocket vs Long-Polling Comparison

Sources:
1. Socket.io Official Docs (2026)
2. "Real-time Web Communication" article on Medium
3. Performance benchmarks from serverless guide

Key Data:
- WebSocket latency: <100ms
- Long-polling latency: 5-30s (depends on interval)
- Memory per connection: WebSocket 10KB, polling negligible
- Scalability: WebSocket to 100K concurrent, polling to 10K

Decision Impact: Chose WebSocket with fallback to long-polling for reliability
Trade-off: Slightly higher memory cost for much better latency

Citations:
- Socket.io. (2026). "Transport Detection." https://socket.io/docs/
- [Author]. (2025). "Real-time Communication Patterns." Medium.
```

#### Category 3: Legal & Compliance
**When**: PRD mentions jurisdictions, regulations, or compliance requirements

**Examples**:
- "DPDPA requirements for India users"
- "TDS rules for contractor payments"
- "COPPA compliance for apps targeting children"

**Process**:
1. Search official government/regulatory sources
2. Look for recent updates (regulatory landscape changes)
3. Find practical compliance guides
4. Verify with legal databases

**Documentation**:
```
Finding: DPDPA Requirements for India

Sources:
1. Official DPDPA text from Ministry of Electronics & IT
2. NASSCOM compliance guide (2026)
3. Practical implementation guide from legal firm

Key Requirements:
- Consent: Explicit consent required for data collection
- RTBI: Right to be informed - privacy policy must be clear
- Data Minimization: Collect only necessary data
- Retention: Delete data when no longer needed

Impact on System:
- Need consent management module
- Privacy policy must be reviewed by legal
- Data retention policies required
- User data export feature (RTBI requirement)

Related Requirements:
REQ-LC-005 (Data privacy), REQ-LC-008 (User rights)

Latest Update: DPDPA rules updated January 2026 - need to review for latest changes
```

#### Category 4: Best Practices & Standards
**When**: Implementing common patterns or industry standards

**Examples**:
- "REST API design best practices"
- "Mobile app performance optimization"
- "Authentication flow security patterns"

**Process**:
1. Search for authoritative sources (RFC, official docs)
2. Find implementation guides
3. Check for security recommendations
4. Validate against recent industry trends

**Documentation**:
```
Finding: REST API Rate Limiting Best Practices

Sources:
1. RFC 6585 (HTTP 429 Too Many Requests)
2. AWS API Gateway documentation
3. "API Design Best Practices" from Google Cloud

Key Recommendations:
- Use HTTP 429 status code
- Include rate-limit headers in response
- Implement exponential backoff on client
- Document rate limits clearly

Implementation:
- Rate limit: 100 requests/minute per user
- Burst allowance: 20 requests/second
- Headers: X-RateLimit-Limit, X-RateLimit-Remaining
- Response body: { "error": "Rate limit exceeded", "retry_after": 60 }

Related Requirements:
REQ-API-015 (Rate limiting)
```

---

## Research Documentation Format

### Internal Finding Template
```
**Location**: `src/path/to/file.py:X-Y`
**Component**: [Class/Function name]
**Method**: [How it was found - find_symbol, search_for_pattern, etc.]
**Finding**: [What was discovered - 2-3 sentences]
**Code Snippet**: [Key code - keep to <10 lines]
**Relevance**: [How this relates to current requirement]
**Gaps**: [What's missing or needs extension]
**Reusability**: [% estimate - 0-100%]
```

### External Finding Template
```
**Source**: [Publication/Website name]
**URL**: [Full URL]
**Date Accessed**: [YYYY-MM-DD]
**Reliability**: [High | Medium | Low]
**Key Data**: [Main finding - 1-2 sentences]
**Excerpt**: "[Direct quote from source]"
**Context**: [What question this answers]
**Validation**: [Does it validate PRD claim? Contradicts? Neutral?]
**Citation**: [Full academic-style citation]
```

---

## Research Synthesis

After conducting research, synthesize findings into impact:

### Validation Summary
- **Validated**: Which PRD claims were confirmed by research?
- **Contradicted**: Which claims need revision?
- **Uncertain**: Which claims couldn't be verified (and why)?

### Gap Identification
- What important information is missing from PRD?
- What assumptions should be validated with user?

### Requirement Impact
- Do any research findings suggest new requirements?
- Do any findings suggest modifying existing requirements?
- Are there implementation implications?

### Example Synthesis
```
Research Synthesis: Revenue Model Validation

Validated:
✓ Competitor revenue share (50-60%) - confirmed via TechCrunch
✓ India market size assumptions - supported by Gartner analyst report
✓ Creator monetization demand - supported by 5 startup case studies

Contradicted:
✗ TDS auto-deduction prevalence - only 30% of platforms implement
  (More research needed on actual best practice)

Uncertain:
? Willingness to pay - PRD assumes 10% of free users will convert to paid
  (No direct market data found - recommendation: validate with user surveys)

Gaps Identified:
- Payment provider selection (Razorpay? Stripe? Both?)
- Tax handling per state in India (SGST/CGST implications)
- Dispute resolution process for payment failures

Requirement Impacts:
NEW REQ: Multi-payment-provider support (Razorpay primary, Stripe fallback)
MODIFY REQ-RM-008: Add tax handling details for state-level GST
NEW REQ: Payment dispute escalation workflow (currently not in PRD)

Implementation Notes:
1. Evaluate both Razorpay and Stripe for India payment support
2. Consult tax advisor on GST implications by state
3. Design dispute resolution based on competitor best practices
```

---

## Research Summary Cards (Post-Research Display)

**When**: Immediately after research phase completes

**Format**:
```
╔═══════════════════════════════════════════════════════╗
║      RESEARCH SUMMARY: Market Analysis                 ║
╚═══════════════════════════════════════════════════════╝

Duration: 23 min | Sources: 12 (5 internal, 7 external)

┌───────────────────────────────────────────────────────┐
│ VALIDATION RESULTS                                     │
├────────────────────────────────────────────────────────┤
│ ✓ Validated (5):                                      │
│   • Competitor revenue 50-60% ✓                       │
│   • Market size $2B India ✓                           │
│   • DPDPA compliance reqs ✓                           │
│                                                        │
│ ✗ Contradicted (1):                                   │
│   • "T+1 payout standard" → Actually 60% use T+3      │
│     RECOMMEND: Revise to T+3 or charge premium        │
│                                                        │
│ ? Uncertain (2):                                      │
│   • "10% conversion" → No data found                  │
│     RECOMMEND: User survey needed                     │
└────────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────┐
│ IMPACT ON REQUIREMENTS                                 │
├────────────────────────────────────────────────────────┤
│ Modified: 3 | Added: 2                                │
│   • REQ-MA-008: Added competitor data                 │
│   • REQ-MA-023: Multi-payment provider (NEW)          │
└────────────────────────────────────────────────────────┘

Press Enter to continue...
```

---

## Citation Requirements

### For Internal Research
Always include:
- File path and line range
- Component name (class/function)
- Brief description of what's there
- How it relates to current requirement

**Example**:
```
Location: src/services/payment_service.py:45-120
Component: RazorpayClient class
Description: Existing Razorpay checkout integration with webhook verification
Relation: Can extend for coin recharge (REQ-RM-012)
```

### For External Research
Always include:
- Source name (publication, website, organization)
- URL (full, not shortened)
- Date accessed
- Reliability assessment (High/Medium/Low)
- Direct quote if key data
- How it validates/informs requirement

**Example**:
```
Source: TechCrunch
URL: https://techcrunch.com/2025/12/15/indian-social-apps-creators
Date Accessed: 2026-01-18
Reliability: High

Finding: "FRND offers 50-55% creator share after platform fees"
Validates: REQ-RM-008 (50-60% revenue share claim)
Citation: TechCrunch. (2025). "Indian Social Apps Race for Creator Revenue."
```

---

## When Research Conflicts with PRD

If research contradicts PRD claims:

1. **Document the Contradiction**: Note exactly what conflicts
2. **Assess Reliability**: Which source is more authoritative?
3. **Flag for User Review**: Add to Q&A or gap analysis questions
4. **Provide Recommendation**: Suggest modification if evidence is strong

**Example**:
```
Contradiction Found:

PRD Claim: "T+1 payout standard in industry"
Research Finding: 60% of competitors offer T+3, 30% T+7, only 10% T+1
Reliability: High (5 competitor websites verified, all current)

Impact: PRD claim appears optimistic
Recommendation: Either:
1. Offer T+1 as premium feature (higher fees), OR
2. Revise to T+3 as standard with T+1 optional

User Question: Should we offer T+1 payout, and at what cost premium?
```

---

## Research Effort Guidelines

### Minimal Research
For straightforward claims with clear sources:
- 1-2 web searches
- 5-10 minutes total

### Moderate Research
For complex topics with multiple angles:
- 3-5 web searches
- Internal codebase review
- 20-30 minutes total

### Extensive Research
For critical decisions or uncertain areas:
- 5-10 web searches across multiple sources
- Deep codebase analysis
- Cross-reference multiple findings
- 45-60 minutes total

### Research Limits
- Don't spend more than 1 hour on single research topic
- If still uncertain after research, flag for user clarification
- Use time efficiently - broad strokes first, deep dive only if needed

---

## Research Quality Gates: "Good Enough" Criteria

### Quality Gate 1: Minimum Source Threshold

| Claim Type | Min Sources | Quality Required |
|------------|-------------|------------------|
| Market sizing | 2 | High (analyst reports, govt data) |
| Competitor data | 3 | Medium+ (tech journalism, official sites) |
| Technology choice | 2 | High (official docs, RFCs) |
| Best practice | 1 | High (industry standard) |
| Legal/compliance | 1 | High (official regulation) |

**Example**:
Query: "FRND creator revenue"
Found: 2 high-quality sources → STOP (threshold met)

### Quality Gate 2: Consistency Check
**Rule**: If first 3 sources agree → stop. If contradict → get 2 more (max 5 total).

```
if first_3_sources_agree:
    stop → status="validated"
elif sources_contradict and attempt_count < 5:
    get_2_more()
    if majority_agrees: stop → status="validated with contradictions"
else:
    stop → status="uncertain - conflicting data, escalate to user"
```

### Quality Gate 3: Time Limits
**Hard Stops**:
- Single query: 15 min max
- Section research: 45 min max
- If exceeded → stop, mark "research incomplete", add to open questions

**Soft Stops**:
- Found 1 high-quality source: 5 min
- Found 2 medium sources: 10 min
- Found contradictions: 15 min

### Quality Gate 4: Diminishing Returns
**Rule**: Last 3 searches returned same info → stop (sufficient coverage)

### Escalation: When to Ask User

Escalate (instead of continuing research) if:
1. **No credible sources** after 3 search variations
2. **Strong contradiction** between sources and PRD
3. **Domain expertise needed** (company-specific process)
4. **Time limit exceeded** without resolution

### Research Quality Score
```
Score = (Sources × Reliability × Consistency) / Time

High (≥8): Multiple high-reliability, consistent, quick
Medium (4-7): Some medium-reliability sources, more time
Low (<4): Few low-reliability or inconsistent, lots of time

If Low: Escalate to user question
```

---

## Tools & Resources

### Web Search Strategy
- Search specific: "FRND app creator revenue share 2026" (not "creator revenue")
- Include year for current data: "DPDPA requirements 2026"
- Cross-reference: Search competitor names individually
- Look for primary sources: Official docs > reputable journalism > blogs

### Documentation to Prioritize
1. Official docs (Socket.io, PostgreSQL, AWS)
2. Technical RFCs (REST, HTTP, security)
3. Reputable publications (TechCrunch, Gartner, Statista)
4. Case studies from similar companies
5. Blog posts and Medium articles (lower priority)

### Reliability Assessment
**High**: Official documentation, government sites, major analyst firms
**Medium**: Established tech publications, case studies from known companies
**Low**: Personal blogs, Reddit discussions, outdated articles

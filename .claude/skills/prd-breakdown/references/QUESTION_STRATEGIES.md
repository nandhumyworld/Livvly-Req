# Question Generation Strategies

This document provides section-specific question templates and generation principles for progressive questioning.

---

## Question Generation Principles

### 1. Ask About Ambiguous Terms
Look for undefined or vague terminology and ask for clarification:

**Examples**:
- "What do you mean by 'active user' in the context of 50K target?"
- "Define 'real-time' - sub-second? <5 seconds? <1 minute?"
- "What constitutes 'high performance' in this context? Latency? Throughput?"

**Pattern**: `"What do you mean by [TERM]? How is it measured in practice?"`

### 2. Validate Quantitative Claims
Challenge numbers and percentages with sources:

**Examples**:
- "You mention '50-60% revenue share' to creators. Is this based on competitor research? Which competitors?"
- "You target '50K active users by year end'. What's the basis? Market research? Growth projections?"
- "What's the basis for the claim that users will engage with [feature] 3x/week?"

**Pattern**: `"Where does the [NUMBER] come from? What assumptions underlie this claim?"`

### 3. Identify Missing Constraints
Surface implications of stated requirements:

**Examples**:
- "You mention 'T+1 payout'. Which timezone? What time of day is the cutoff?"
- "You require 'KYC verification'. How long is the verification process? What's the failure rate?"
- "You specify 'DPDPA compliance'. Does this apply to all users or just India-based?"

**Pattern**: `"You mention [REQUIREMENT]. What's the practical implication? What's the scope?"`

### 4. Surface Implicit Assumptions
Identify unstated requirements:

**Examples**:
- "You target 'Tamil-first' experience. What % of UI needs translation? All screens or key flows only?"
- "You mention 'voice-first platform'. Can users interact via text if they prefer?"
- "You emphasize 'creator empowerment'. What level of analytics/control do creators need?"

**Pattern**: `"You emphasize [VALUE]. How does this manifest? What do users actually see/do?"`

---

## Business Section Questions

### 01. Executive Summary

#### Phase 1: Core Questions (2-3)
1. **Target Audience Validation**: "In your target of '50K active users by year end', who are these users? (Age range, geography, language, socioeconomic profile?)"
   - *Why*: Impacts product design, compliance, and market research validation

2. **Business Goals**: "What's your primary business goal? (Revenue? Market share? User growth? Engagement?)"
   - *Why*: Changes prioritization and success metrics

3. **Competitive Differentiation**: "You claim to be 'Tamil-first' and 'creator-empowered'. Which existing apps lack this? What makes yours better?"
   - *Why*: Validates market need and positioning

#### Phase 2: Follow-up Questions (Based on Answers)
- If they mention "India expansion": "Beyond India, what's your long-term geography strategy?"
- If they mention "creator monetization": "How does creator income compare to day job for average creator?"
- If they mention "voice calling": "What's your unique advantage over Agora/Twilio?"

#### Phase 3: Gap Analysis
- Check for: Business model clarity, revenue assumptions, unit economics
- Questions: "How do you make money? Is it subscription, premium features, commission, or ads?"

---

### 02. Market Analysis

#### Phase 1: Core Questions (2-3)
1. **Market Sizing Basis**: "Your TAM/SAM/SOM numbers - are these from analyst reports, bottom-up calculation, or industry benchmarks?"
   - *Why*: Affects credibility and implementation priorities

2. **Competitor Differentiation**: "You list [X] competitors. What's your unfair advantage? Why would users switch from [main competitor]?"
   - *Why*: Informs feature prioritization and marketing

3. **Market Timing**: "Why now? What's changed in the market that makes this the right time to launch?"
   - *Why*: Affects go-to-market strategy

#### Phase 2: Follow-up Questions
- If they mention "60% female users": "Where does this demographic projection come from? Surveys? Historical app data?"
- If they mention "emerging market opportunity": "Which country specifically? What's the addressable market there?"

#### Phase 3: Gap Analysis
- Check for: Missing competitor substitutes, SWOT analysis depth, market trend validation

---

### 03. Product Vision

#### Phase 1: Core Questions (2-3)
1. **Core Mission Clarity**: "Complete this: 'We want to [enable/empower/create] [what] for [whom]?'"
   - *Why*: Ensures alignment on product purpose

2. **User Journey**: "Walk me through a user's first experience. What's the first thing they see? Do?"
   - *Why*: Validates usability assumptions

3. **Values Measurement**: "You mention 'creator empowerment' and 'community-first'. How do you measure success here?"
   - *Why*: Enables metric definition

#### Phase 2: Follow-up Questions
- If they mention "authentic connections": "What does 'authentic' mean? How do you prevent fake profiles/bots?"
- If they mention "cultural celebration": "Which culture? How is it reflected in product design?"

---

### 04. Revenue Model

#### Phase 1: Core Questions (2-3)
1. **Revenue Split Rationale**: "Why 50-60% to creators vs 40-50% to platform? How did you arrive at this?"
   - *Why*: May affect creator satisfaction, compliance

2. **Monetization Mechanism**: "How do users spend money? Coins? Subscriptions? Gifts? Each with different mechanics?"
   - *Why*: Different implementations

3. **Payment Providers**: "Will you use Razorpay? Which payment methods? Does this support creator payouts in [country]?"
   - *Why*: Technical and compliance implications

#### Phase 2: Follow-up Questions
- If they mention "freemium model": "What features are free vs paid? Does this affect core functionality?"
- If they mention "premium subscription": "What's included? Creator features? Ad-free? Priority support?"

#### Phase 3: Gap Analysis
- Check for: Tax implications, payment provider limitations, chargeback handling

---

### 15. Success Metrics

#### Phase 1: Core Questions (2-3)
1. **KPI Definition**: "What are your top 3 metrics for success? How do you measure each? (What's the data source?)"
   - *Why*: Informs analytics implementation

2. **Dashboard Requirements**: "Who needs to see metrics? (Executives, product team, creators?) What do they need to see?"
   - *Why*: Different views for different stakeholders

3. **Alert Thresholds**: "When do you want to be alerted? (e.g., if DAU drops 10% in a day?)"
   - *Why*: Enables operational dashboards

---

## Technical Section Questions

### 05. Feature Specifications

#### Phase 1: Core Questions (2-3)
1. **Screen Interaction**: "Walk me through the [main screen]. What happens when users [primary action]?"
   - *Why*: Clarifies UX model

2. **Error Handling**: "What happens when [action] fails? (Network error, permission denied, timeout?)"
   - *Why*: Often overlooked, critical for UX

3. **State Dependencies**: "Must users complete [X] before accessing [Y]? Or can they skip?"
   - *Why*: Affects user flow and data model

#### Phase 2: Follow-up Questions
- If they mention "real-time updates": "Acceptable latency? <1s? <5s? <30s?"
- If they mention "offline support": "Which features work offline? How is data synced on reconnect?"
- If they mention "notifications": "Push? In-app? Email? Opt-in or default?"

#### Phase 3: Gap Analysis
- Check for: Loading states, empty states, permission flows, error messages, accessibility
- Questions: "I don't see specification for [X]. Should we define that now?"

---

### 06. Technical Architecture

#### Phase 1: Core Questions (2-3)
1. **Scale Requirements**: "What's your expected scale? (DAU, concurrent users, requests/second, data volume?)"
   - *Why*: Drives architecture decisions

2. **Technology Rationale**: "Why [tech stack]? What alternatives did you consider?"
   - *Why*: Validates choices aren't arbitrary

3. **Integration Needs**: "What external services must you integrate? (Payment, analytics, etc.)"
   - *Why*: Affects architecture complexity

#### Phase 2: Follow-up Questions
- If they mention "microservices": "How will services communicate? REST? gRPC? Message queue?"
- If they mention "WebRTC for voice": "What's your fallback if WebRTC fails? Agora?"

#### Phase 3: Gap Analysis
- Check for: Scalability targets, disaster recovery, monitoring, security

---

### 07. Database Schema

#### Phase 1: Core Questions (2-3)
1. **Data Relationships**: "How are [core entities] related? (User:Profile = 1:1? User:Calls = 1:Many?)"
   - *Why*: Determines schema design

2. **Query Patterns**: "What are the common queries? (Find user by ID? List calls by creator? etc.)"
   - *Why*: Determines indexing strategy

3. **Data Retention**: "How long do you keep [sensitive data]? (Call logs, user messages, payment history?)"
   - *Why*: GDPR/compliance implication

#### Phase 2: Follow-up Questions
- If they mention "hot data": "How much hot data? Affects caching strategy"
- If they mention "historical data": "Do you need audit trail for all changes?"

---

### 08. API Specifications

#### Phase 1: Core Questions (2-3)
1. **Authentication**: "How do clients authenticate? (JWT? Session? API keys?)"
   - *Why*: Affects security and implementation

2. **Rate Limiting**: "Do you need rate limiting? Per user? Per IP? What are the limits?"
   - *Why*: Affects API design

3. **Versioning**: "Will you version your API? If yes, how? (URL path? Header?)"
   - *Why*: Affects backwards compatibility strategy

#### Phase 2: Follow-up Questions
- If they mention "real-time data": "WebSocket? Polling? Server-Sent Events?"
- If they mention "file uploads": "Size limits? Types? Where stored? (S3, local?)"

#### Phase 3: Gap Analysis
- Check for: Error response formats, pagination, filtering

---

## Implementation Section Questions

### 09. Coin Economy

#### Phase 1: Core Questions (2-3)
1. **Coin Pricing Psychology**: "How many coins for [action]? (1 coin = $0.01? $0.10?) Is this competitive?"
   - *Why*: Affects conversion and creator satisfaction

2. **Coin Bundles**: "What coin packages do you offer? (100 coins for $1? $5? $10?)"
   - *Why*: Drives revenue and psychology

3. **Coin Expiry**: "Do coins expire? If yes, how long? This affects user behavior significantly"
   - *Why*: Legal and user trust implications

#### Phase 2: Follow-up Questions
- If they mention "gift coins": "Can users gift coins to each other? Or only to creators?"
- If they mention "bonus coins": "When do users get bonus coins? Onboarding? Referral?"

---

### 10. Creator Payout

#### Phase 1: Core Questions (2-3)
1. **Tax Compliance**: "How do you handle TDS/taxes? Auto-deduct? Inform creator? (Critical in India)"
   - *Why*: Legal requirement, affects creator net income

2. **Payout Frequency**: "T+1, T+7, or monthly? Why this frequency?"
   - *Why*: Affects cash flow and creator satisfaction

3. **Minimum Payout**: "Is there a minimum balance before payout? (e.g., $5 minimum?)"
   - *Why*: Affects operational overhead

#### Phase 2: Follow-up Questions
- If they mention "bank transfer": "Which banks? Does Razorpay support all banks in [country]?"
- If they mention "international payouts": "How do you handle cross-border payments? Taxes?"

---

### 11. Automation Workflows

#### Phase 1: Core Questions (2-3)
1. **Trigger Conditions**: "When should workflows trigger? On event? On schedule? On threshold?"
   - *Why*: Determines implementation complexity

2. **Error Handling**: "If a workflow fails, what happens? Retry? Alert? Manual intervention?"
   - *Why*: Affects reliability

3. **Monitoring**: "How do you monitor workflow health? Alert when things go wrong?"
   - *Why*: Operational requirement

---

### 13. Implementation Roadmap

#### Phase 1: Core Questions (2-3)
1. **Dependency Validation**: "Which features depend on others? (Can you release [X] without [Y]?)"
   - *Why*: Affects sprint planning

2. **Resource Allocation**: "How many engineers per feature? Full-time? Part-time?"
   - *Why*: Affects timeline

3. **Risk Mitigation**: "What are your biggest risks? How will you mitigate?"
   - *Why*: Informs contingency planning

---

### 14. Cost Estimation

#### Phase 1: Core Questions (2-3)
1. **Assumption Validation**: "How did you estimate these costs? (Quotes from vendors? Industry benchmarks?)"
   - *Why*: Determines accuracy

2. **Vendor Pricing**: "Which payment provider? Hosting? Analytics? Are these prices locked in?"
   - *Why*: Affects budget

3. **Contingency**: "Do you have a contingency budget? If yes, what % above estimates?"
   - *Why*: Project planning

---

## Operational Section Questions

### 12. Legal & Compliance

#### Phase 1: Core Questions (2-3)
1. **Jurisdiction Requirements**: "Which jurisdictions do you operate in? (India only? Or international?)"
   - *Why*: Determines applicable laws

2. **Data Protection**: "Does GDPR apply? DPDPA? What about other regulations?"
   - *Why*: Affects data handling requirements

3. **Content Moderation**: "How do you handle inappropriate content? Manual review? AI? Both?"
   - *Why*: Legal liability and operational cost

#### Phase 2: Follow-up Questions
- If they mention "DPDPA compliance": "What specific requirements? Data consent? RTBI?"
- If they mention "COPPA": "Do you accept users <13? If yes, what's your compliance strategy?"

#### Phase 3: Gap Analysis
- Check for: Terms of Service coverage, Privacy Policy completeness, Audit trail requirements

---

## Question Formatting Best Practices

### For User Display
```
Q1. [Core Question]
    Why: [Rationale - why this matters]

[Wait for answer]

Q2. [Follow-up question based on answer]
    Why: [Context from their answer]
```

### For Documentation
```
### Section: [Name]
- **Question Type**: [Clarification | Validation | Constraint | Gap Analysis]
- **Core Question**: [Question text]
- **Follow-up Triggers**:
  - If answer mentions "X": Ask Y
  - If answer mentions "Z": Ask W
- **Context**: [Why this matters for requirements]
```

---

## Tips for Progressive Questioning

1. **Listen Before Planning**: Read their answer fully before asking follow-ups
2. **Connect to Context**: Reference their specific choices ("You mentioned [feature]. How does [constraint] affect this?")
3. **Avoid Lists**: Don't ask predetermined sequences - adapt based on their responses
4. **Prioritize Ambiguity**: Focus on unclear terms, not comprehensive coverage
5. **Be Domain-Aware**: Technical questions for technical sections, business questions for business sections
6. **Validate Assumptions**: Challenge claims but respectfully ("I want to validate this number...")
7. **Scope Questions**: Ask about scope of features, users, timeframes, not hypotheticals

# Legal & Compliance - Requirements

**Section**: 12. Legal & Compliance
**Lines**: 2445-2705 (261 lines)
**Complexity**: Moderate
**Last Updated**: 2026-01-23

---

## Functional Requirements

### REQ-LC-001: Company Registrations
**Priority**: P0 (Critical - Blocking Launch)
**Type**: Legal/Operational
**User Story**: As a platform operator, I must complete all required legal registrations before launch to operate legally.

**Description**:
Complete all mandatory registrations with government authorities before launching the platform.

**Required Registrations** (from PRD lines 2447-2455):

| Registration | Authority | Timeline | Cost | Purpose |
|--------------|-----------|----------|------|---------|
| Private Limited Company | MCA (Ministry of Corporate Affairs) | 7-10 days | ₹10,000-15,000 | Legal entity formation |
| GST Registration | GST Portal | 3-5 days | ₹2,000-3,000 | Tax collection & remittance |
| TAN (Tax Deduction Account Number) | NSDL | 7 days | ₹65 | TDS deduction & deposit |
| Trademark "LIVVLY" | IP India | 6-12 months | ₹5,000-10,000 | Brand protection |
| Payment Aggregator License | RBI (via Razorpay) | Handled by gateway | Included | Payment processing |

**Acceptance Criteria**:
- Private Limited Company incorporated with MCA (Certificate of Incorporation obtained)
- GST Registration Number (GSTIN) obtained
- TAN issued by NSDL for TDS filing
- Trademark application filed with IP India (registration can complete post-launch)
- Razorpay payment gateway agreement signed (handles PA license)
- All registration documents stored in secure company vault

**Dependencies**: None (must complete before any other operations)

**Timeline**: ~3-4 weeks for critical registrations (Company, GST, TAN)

**Technical Notes**:
- Use CA/CS services for company incorporation and GST registration
- Trademark application: File in Class 42 (Technology Services) and Class 45 (Social Networking)
- Payment aggregator: Razorpay holds RBI license, platform operates as merchant

---

### REQ-LC-002: Terms of Service (Updated)
**Priority**: P0 (Critical)
**Type**: Legal
**User Story**: As a user, I need clear terms of service that accurately reflect platform policies and protect both parties.

**Description**:
Draft and publish comprehensive Terms of Service (ToS) with ALL critical clauses updated to match technical implementation.

**Critical Updates from PRD** (lines 2459-2532):

**CORRECTED CLAUSES**:
1. **Minimum Withdrawal** (line 2488):
   - ❌ PRD states: "Minimum withdrawal: ₹500"
   - ✅ **CORRECTED**: "Minimum withdrawal: ₹100"
   - Rationale: Aligns with REQ-CP-001 and Section 10 Q1 decision

2. **Platform Commission** (line 2489):
   - ❌ PRD states: "Platform commission: 55% of coin value" (= 45% creator share)
   - ✅ **CORRECTED**: "Creator earnings: Tiered revenue share model
     - Tier 1: 75% of coin value (new creators)
     - Tier 2: 80% of coin value (₹50K+ monthly earnings)
     - Tier 3: 85% of coin value (₹2L+ monthly earnings)"
   - Rationale: Aligns with REQ-RM-003 and Sections 4, 8 decisions

**Key Clauses to Include**:
- Eligibility: 18+ age requirement (line 2464)
- Virtual Currency: Non-refundable, 365-day expiry for unused coins (lines 2476-2481)
- Prohibited Conduct: 8 categories (harassment, explicit content, fraud, etc.) (lines 2491-2498)
- Content Guidelines: User retains rights, no recording without consent (lines 2501-2505)
- Privacy: Data collection as per Privacy Policy (lines 2507-2510)
- Termination: Pending earnings paid within 30 days (line 2515)
- Dispute Resolution: Arbitration in Coimbatore, 30-day informal resolution first (lines 2523-2526)

**Acceptance Criteria**:
- ToS drafted by legal counsel, reviewed by founders
- All clauses match technical implementation (minimum withdrawal, revenue share, etc.)
- Displayed during signup with "I Accept" checkbox (mandatory)
- Accessible via footer link: "/terms-of-service"
- Versioned (v1.0 with effective date)
- Changes to ToS require 30-day notice to users

**Dependencies**: REQ-CP-001, REQ-RM-003, REQ-LC-003 (Privacy Policy)

**Technical Implementation**:
- ToS content stored in database (supports versioning)
- User acceptance tracked: users.tos_accepted_at, users.tos_version
- API endpoint: GET /api/v1/legal/terms (returns current ToS)

---

### REQ-LC-003: Privacy Policy (DPDP Act 2023 Compliant)
**Priority**: P0 (Critical)
**Type**: Legal
**User Story**: As a user, I need transparency about how my data is collected, used, and protected.

**Description**:
Draft and publish comprehensive Privacy Policy compliant with Digital Personal Data Protection Act 2023 (India's GDPR equivalent).

**Key Sections** (from PRD lines 2534-2593):

1. **Information Collection** (lines 2539-2544):
   - Phone number (authentication)
   - Profile info (name, age, gender, photo)
   - Location (city, state - NOT precise GPS)
   - Device info
   - Usage data (calls, chats, transactions)

2. **Information Usage** (lines 2546-2551):
   - Service provision & improvement
   - User matching based on preferences
   - Payment processing
   - Fraud prevention
   - Notifications

3. **Data Sharing** (lines 2553-2557):
   - With other users (profile only, not contact info)
   - Payment processors (Razorpay for transactions)
   - Legal requirements (court orders, law enforcement)
   - Business transfers (M&A, asset sale)

4. **Data Retention** (lines 2559-2562):
   - Active accounts: data retained indefinitely
   - Deleted accounts: 90-day retention for legal compliance, then purged
   - Transaction records: 7 years (Income Tax Act requirement)

5. **User Rights** (DPDP Act 2023 compliant) (lines 2564-2569):
   - Right to access data
   - Right to correct inaccurate data
   - Right to delete account
   - Right to withdraw consent
   - Right to data portability (export profile data as JSON)

6. **Security Measures** (lines 2571-2575):
   - SSL/TLS encryption for all data transmission
   - AES-256 encryption for PII at rest
   - Regular security audits (quarterly penetration testing)
   - Access controls (role-based permissions)

7. **Data Localization** (NEW - user decision Q4):
   - **ALL data stored within India (AWS ap-south-1 Mumbai)**
   - No cross-border data transfers
   - Compliant with anticipated DPDP Act regulations

**Acceptance Criteria**:
- Privacy Policy drafted by legal counsel specializing in data protection
- DPDP Act 2023 compliance verified by external audit
- Accessible via footer link: "/privacy-policy"
- Displayed during signup with summary ("We collect phone, profile, location data. Read full policy")
- Contact email: privacy@livvly.com (dedicated inbox)
- Data Subject Access Request (DSAR) process documented

**Dependencies**: REQ-TA-001 (AWS Mumbai infrastructure), REQ-LC-007 (data residency)

**Technical Implementation**:
- Privacy Policy stored in database (versioned)
- User consent tracked: users.privacy_accepted_at, users.privacy_version
- DSAR workflow: POST /api/v1/legal/data-request → Generates user data export (JSON + CSV)
- Account deletion: POST /api/v1/users/delete → Soft delete, 90-day grace period

---

### REQ-LC-004: Creator Partnership Agreement (Updated)
**Priority**: P0 (Critical)
**Type**: Legal
**User Story**: As a creator, I need a clear agreement that defines my rights, earnings, and obligations.

**Description**:
Draft and publish Creator Partnership Agreement with ALL terms updated to match technical implementation and tax compliance.

**Critical Updates from PRD** (lines 2595-2678):

**CORRECTED CLAUSES**:
1. **Earnings Structure** (line 2618):
   - ❌ PRD states: "Revenue share: 45% of coins spent on you"
   - ✅ **CORRECTED**: "Revenue share: Tiered model based on trailing 30-day earnings
     - Tier 1 (75%): <₹50,000 monthly earnings
     - Tier 2 (80%): ₹50,000-₹2,00,000 monthly earnings
     - Tier 3 (85%): >₹2,00,000 monthly earnings
     - Tier recalculated daily, applies to future earnings only"
   - Rationale: Aligns with REQ-RM-003

2. **Pending Period** (line 2621):
   - ❌ PRD states: "Pending period: 7 days"
   - ✅ **CORRECTED**: "Earnings available for withdrawal immediately. Bank credit within T+1 (next calendar day) of withdrawal request."
   - Rationale: Aligns with REQ-CP-002 and Section 10 Q3 decision

3. **Minimum Withdrawal** (line 2622):
   - ❌ PRD states: "Minimum withdrawal: ₹500"
   - ✅ **CORRECTED**: "Minimum withdrawal: ₹100"
   - Rationale: Aligns with REQ-CP-001

**Key Clauses to Include**:
- Engagement: Independent contractor, not employee (line 2604-2608)
- Creator Requirements: 18+, PAN, bank account, KYC, 4.0+ rating (lines 2610-2615)
- Tax Obligations: TDS @ 10% above ₹30K/year, Form 16A quarterly, ITR filing (lines 2624-2628)
- Payout Terms: Daily T+1, IMPS/NEFT, failed payout retries (lines 2630-2634)
- Conduct Requirements: No explicit content, no personal info sharing (lines 2636-2641)
- Performance Standards: Minimum 2 hours active/week (lines 2649-2652)
- Termination: 7-day notice, pending earnings paid within 30 days (lines 2654-2658)
- Dispute Resolution: Arbitration in Coimbatore (lines 2669-2672)

**Acceptance Criteria**:
- Creator Agreement drafted by employment lawyer (ensures IC classification holds)
- All clauses match technical implementation (tiered revenue, T+1, ₹100 minimum)
- Signed electronically via in-app signature (DocuSign/HelloSign integration)
- Creator cannot go live until agreement signed
- Agreement acceptance tracked: creator_kyc.agreement_signed_at, agreement_version
- Copy emailed to creator and stored in admin dashboard

**Dependencies**: REQ-CP-001, REQ-CP-002, REQ-CP-003, REQ-RM-003

**Technical Implementation**:
- E-signature workflow: Creator reviews agreement → Signs via DocuSign → Backend receives webhook → Updates creator_kyc.agreement_signed = true
- PDF copy generated and stored in S3 (versioned, immutable)
- Unsigned creators blocked from accepting paid calls

---

### REQ-LC-005: TDS Compliance Automation
**Priority**: P0 (Critical - Tax Law Compliance)
**Type**: Compliance
**User Story**: As a platform, I must deduct and file TDS correctly to comply with Income Tax Act Section 194J.

**Description**:
Implement automated TDS deduction, quarterly filing (Form 26Q), and TDS certificate generation (Form 16A) for creator earnings.

**TDS Rules** (from PRD lines 2680-2702):

**Quarterly Filing Calendar**:
- Q1 (Apr-Jun): File by July 31
- Q2 (Jul-Sep): File by October 31
- Q3 (Oct-Dec): File by January 31
- Q4 (Jan-Mar): File by May 31

**Required Forms**:
1. **Form 26Q**: Quarterly TDS return filed with Income Tax Department
2. **Form 16A**: TDS certificate issued to each creator quarterly
3. **Challan 281**: TDS deposit to government (within 7 days of month-end)

**TDS Calculation** (aligns with REQ-CP-003):
- **Section 194J**: 10% on creator earnings >₹30,000 per financial year
- **Threshold**: No TDS if annual earnings ≤₹30,000
- **Deduction**: Applied at withdrawal time, not at earning time

**Acceptance Criteria**:
- TDS deducted automatically during withdrawal processing (REQ-CP-003 logic)
- TDS amount stored in withdrawals.tds_amount column
- Monthly TDS summary generated (total deducted per month)
- Challan 281 filed within 7 days of month-end (via ClearTax/TaxBuddy API)
- Form 26Q generated and filed quarterly (automated)
- Form 16A auto-generated for each creator quarterly (PDF emailed + downloadable in app)
- TDS mismatch alerts: Flag creators with ₹30K+ earnings but $0 TDS (audit trail)

**Dependencies**: REQ-CP-003 (TDS withholding), REQ-LC-006 (automated filing integration)

**Penalties for Non-Compliance**:
- Late filing: ₹200/day (max ₹20,000 per return)
- Non-filing: 100% of TDS amount due
- Late deposit: 1% interest per month
- **Risk Level**: HIGH - Platform liability, not creator liability

**Technical Implementation**:
```python
# TDS Filing Workflow (n8n cron job)
1. Schedule: 20th of month following quarter end (e.g., July 20 for Q1)
2. Generate 26Q:
   - Aggregate all withdrawals for quarter
   - Group by creator (deductor-deductee relationship)
   - Format as per 26Q file format (pipe-delimited text)
3. Upload to ClearTax API:
   - POST /api/v1/tds/file-26q
   - Attach 26Q file, get acknowledgment number
4. Generate 16A certificates:
   - For each creator with TDS deducted
   - PDF with: PAN, amount, TDS deducted, quarter, acknowledgment number
5. Email 16A to creators + store in app (dashboard → Tax Documents)
```

---

### REQ-LC-006: Automated TDS Filing Integration
**Priority**: P1 (High)
**Type**: Integration
**User Story**: As a finance admin, I want automated TDS filing to reduce manual errors and save time.

**Description**:
Integrate with ClearTax or TaxBuddy API for automated Form 26Q generation, filing, and Form 16A certificate issuance.

**Integration Options**:
1. **ClearTax** (recommended):
   - API endpoint: `/api/v1/tds/file-26q`
   - Features: 26Q generation, e-filing, 16A generation, challan tracking
   - Pricing: ~₹5,000/year + ₹10 per certificate

2. **TaxBuddy**:
   - Similar features, slightly lower cost
   - Less mature API documentation

**Acceptance Criteria**:
- ClearTax account created and API keys obtained
- API integration tested in staging environment
- Quarterly filing workflow automated (n8n cron job)
- Form 16A auto-generated and emailed to creators
- Challan 281 payment tracking (manual verification initially, auto-deposit future enhancement)
- Error handling: Alert finance admin if API fails (Slack notification)

**Dependencies**: REQ-LC-005 (TDS compliance), REQ-AW-009 (error workflow)

**User Decision**: Q9 answer - Automated TDS filing integration (vs manual)

**Cost Estimate**: ₹10,000-15,000 per year (ClearTax subscription + certificates for 100-500 creators)

---

### REQ-LC-007: Data Residency (India-Only)
**Priority**: P0 (Critical)
**Type**: Compliance
**User Story**: As a platform, I must store all user data within India to comply with data protection regulations.

**Description**:
Ensure 100% of user data, transactions, and media are stored in AWS ap-south-1 (Mumbai) region with NO cross-border data transfers.

**Data Categories**:
1. **Personal Identifiable Information (PII)**:
   - Phone numbers, names, photos, location (city/state)
   - Stored in: PostgreSQL database (AWS RDS, Mumbai)

2. **Financial Data**:
   - Transactions, coin purchases, payouts, bank details (encrypted)
   - Stored in: PostgreSQL database (AWS RDS, Mumbai)

3. **Content Data**:
   - Profile photos, voice recordings (if any), chat messages
   - Stored in: AWS S3 (ap-south-1 Mumbai bucket)

4. **Analytics & Logs**:
   - User activity logs, API logs, error logs
   - Stored in: CloudWatch Logs (ap-south-1), PostgreSQL analytics tables

**Acceptance Criteria**:
- ALL AWS resources provisioned in ap-south-1 (Mumbai) region only
- S3 bucket: Block public access, restrict to Mumbai region only (bucket policy)
- RDS database: Mumbai region, no read replicas in other regions
- CloudFront CDN: Mumbai edge locations prioritized (geo-restriction to India)
- No third-party services storing data outside India (verify Razorpay, MSG91, Karza data residency)
- Privacy Policy explicitly states: "All data stored within India"
- Annual audit: Verify no data leakage to non-Indian servers

**Dependencies**: REQ-TA-001 (AWS Mumbai infrastructure), REQ-LC-003 (Privacy Policy)

**User Decision**: Q4 answer - All data in India (vs hybrid model)

**Technical Notes**:
- Use `aws s3 mb s3://livvly-media --region ap-south-1 --restrict-bucket-policy`
- RDS: `--availability-zone ap-south-1a --backup-retention-period 30`
- Third-party data residency verification:
  - Razorpay: Data stored in India (confirmed)
  - MSG91: SMS logs stored in India (confirmed)
  - Karza: KYC data processed in India (confirmed)

---

### REQ-LC-008: Content Moderation Compliance (IT Rules 2021)
**Priority**: P1 (High)
**Type**: Compliance
**User Story**: As a platform, I must comply with IT Rules 2021 for content moderation and grievance redressal.

**Description**:
Implement content moderation framework compliant with Information Technology (Intermediary Guidelines and Digital Media Ethics Code) Rules, 2021.

**IT Rules 2021 Requirements**:
1. **Grievance Officer** (required for platforms >5M users):
   - Appoint dedicated Grievance Officer
   - Contact details published on website
   - Respond to complaints within 72 hours (business days)

2. **User Complaint Mechanism**:
   - In-app "Report" button on all content (profiles, chats, live rooms)
   - Complaint categories: Harassment, Explicit Content, Fraud, Hate Speech, Copyright
   - Ticket system with 72-hour SLA for acknowledgment

3. **Content Takedown**:
   - Remove explicitly illegal content within 24 hours (court orders, law enforcement requests)
   - Review flagged content within 72 hours (AI + human moderation)
   - Notify reporter and content owner of decision

4. **Monthly Transparency Report**:
   - Published on website by 10th of next month
   - Includes: Complaints received, action taken, content removed, accounts suspended

**Acceptance Criteria**:
- Grievance Officer appointed (employee or external counsel)
- Contact page: "/grievance-officer" with name, email, phone
- In-app report flow: Report button → Select category → Submit → Auto-acknowledge
- Complaint tracking system: Admin dashboard with ticket status, 72h SLA alerts
- AI moderation (REQ-FS-022) + human review queue (10% sample, 100% flagged content)
- Transparency report template created, published monthly
- Appeals process: User can appeal content removal decision (7-day response)

**Dependencies**: REQ-FS-022 (AI moderation), admin dashboard

**User Decision**: Q7 answer - Grievance Officer + 72h response (vs basic moderation only)

**Penalties for Non-Compliance**:
- Loss of safe harbor protection (platform liable for user content)
- Government blocking orders
- Fines up to ₹25 Lakh
- Criminal liability for non-compliance with takedown orders

**Technical Implementation**:
- Report flow: POST /api/v1/reports → Create ticket → Assign to human moderator queue
- Grievance officer dashboard: View tickets, respond, escalate
- Transparency report: Auto-generate from reports table (monthly cron job)

---

### REQ-LC-009: Age Verification (AI-Enhanced)
**Priority**: P1 (High)
**Type**: Compliance
**User Story**: As a platform, I must prevent minors (<18) from accessing the platform to comply with regulations and ensure safety.

**Description**:
Implement two-layer age verification: Self-declaration + AI face age estimation to reduce risk of minors on platform.

**Verification Layers**:
1. **Primary: Self-Declaration**
   - Signup form: "Confirm you are 18 years or older" checkbox (mandatory)
   - Date of birth input (validates age ≥18)
   - Stored in: users.date_of_birth

2. **Secondary: AI Face Age Estimation**
   - After profile photo upload, run AI age estimation (via AWS Rekognition or Azure Face API)
   - If estimated age <18: Flag account for manual review
   - Manual reviewer checks profile, contacts user for ID verification if suspicious
   - Account suspended if confirmed minor

**AI Age Estimation Workflow**:
```
1. User uploads profile photo
2. Backend calls AWS Rekognition DetectFaces API
   - Returns: AgeRange { Low: 16, High: 20 }
3. If AgeRange.High < 18:
   - Flag account: user_flags.flag_type = 'age_verification_required'
   - Send to manual review queue
4. Manual review (within 24h):
   - Reviewer checks profile (DOB, photo, behavior)
   - If suspicious: Request govt-issued ID (Aadhaar, PAN) for age proof
   - If confirmed minor: Ban account, issue refund if coins purchased
```

**Acceptance Criteria**:
- Self-declaration checkbox on signup (blocks signup if unchecked)
- DOB field validates age ≥18 (rejects if <18)
- AI age estimation runs on all profile photo uploads
- Accounts flagged if estimated age <18 (auto-review queue)
- Manual review process documented (SOP for reviewers)
- 99%+ accuracy: False positives <1% (AI estimates 17-year-old as <18 acceptable, estimates 25-year-old as <18 unacceptable)

**Dependencies**: AWS Rekognition API subscription, manual review team

**User Decision**: Q8 answer - Self-declaration with AI face age estimation (vs self-declaration only)

**Technical Notes**:
- AWS Rekognition pricing: ~$1 per 1,000 images
- Face API call: POST /api/detect-faces → Returns age range
- Ethical consideration: AI age estimation has racial bias (test across demographics)

---

### REQ-LC-010: GST Alert for High-Earning Creators
**Priority**: P2 (Medium)
**Type**: Compliance Support
**User Story**: As a high-earning creator, I want to be alerted when I need GST registration so I stay tax-compliant.

**Description**:
Monitor creator annual earnings and send in-app alert when they cross ₹20 Lakh threshold (GST registration requirement).

**GST Rules (India)**:
- Threshold: ₹20 Lakh annual turnover (service providers)
- Registration: Mandatory within 30 days of crossing threshold
- Penalty: ₹10,000 or 10% of tax due (whichever is higher) for late registration

**Alert Workflow**:
```
1. Daily cron job: Calculate trailing 365-day earnings for all creators
2. If earnings cross ₹20,00,000:
   - Send in-app notification: "You may need GST registration. Consult a tax advisor."
   - Email with GST registration guide (PDF attachment)
   - Dashboard banner: "GST Registration Recommended"
3. Provide resources:
   - Link to GST portal: https://www.gst.gov.in
   - Link to CA directory (for GST filing support)
   - FAQ: "Do I need GST registration as a creator?"
```

**Acceptance Criteria**:
- Daily cron job monitors creator earnings (trailing 365 days)
- Alert triggered at ₹19,50,000 (buffer before ₹20L threshold)
- In-app notification + email sent once (not repeated daily)
- GST resources page: "/legal/gst-guide" with FAQs and registration steps
- Platform does NOT register creators for GST (they're independent contractors)
- Platform does NOT deduct GST from creator earnings (creators handle their own GST)

**Dependencies**: REQ-RM-003 (creator earnings tracking), email service

**User Decision**: Q5 answer - Alert creators at ₹20L threshold (vs no GST facilitation)

**Technical Implementation**:
```sql
-- Daily cron job query
SELECT user_id, SUM(diamonds * tier_percent * COIN_TO_INR) as annual_earnings
FROM transactions
WHERE type IN ('earn_call', 'earn_gift', 'earn_room')
  AND created_at > NOW() - INTERVAL '365 days'
GROUP BY user_id
HAVING annual_earnings >= 1950000
  AND user_id NOT IN (SELECT user_id FROM gst_alerts_sent)
```

---

### REQ-LC-011: Mandatory Arbitration Clause
**Priority**: P1 (High)
**Type**: Legal
**User Story**: As a platform, I want disputes resolved through arbitration to reduce litigation costs and time.

**Description**:
Enforce mandatory arbitration for all disputes (except IP, fraud, injunction matters) with arbitration seat in Coimbatore, Tamil Nadu.

**Arbitration Clause** (ToS Section 10, lines 2523-2526):
```
DISPUTE RESOLUTION:
1. Informal Resolution (30 days):
   - Contact support@livvly.com with dispute details
   - Platform responds within 7 days
   - Good faith negotiation for 30 days

2. Arbitration (if informal fails):
   - Governed by Arbitration & Conciliation Act, 1996
   - Single arbitrator mutually appointed
   - Seat: Coimbatore, Tamil Nadu
   - Language: English
   - Costs: Losing party bears arbitration costs
   - Exceptions: IP disputes, fraud, injunctions can go to court

3. No Class Actions:
   - All disputes resolved individually
   - No class action or representative action allowed
```

**Acceptance Criteria**:
- Arbitration clause in ToS and Creator Agreement
- No opt-out mechanism (mandatory for all users/creators)
- Exceptions clearly stated (IP, fraud, injunctions)
- Arbitrator panel identified (law firms in Coimbatore with arbitration practice)
- Arbitration process documented (SOP for when dispute arises)

**Dependencies**: REQ-LC-002 (ToS), REQ-LC-004 (Creator Agreement)

**User Decision**: Q6 answer - Mandatory arbitration (vs optional with opt-out)

**Benefits**:
- Faster resolution (3-6 months vs 2-5 years for litigation)
- Lower cost (₹50K-2L arbitration vs ₹5L-20L litigation)
- Confidential (arbitration awards not public)
- Enforceable (Arbitration Act makes awards binding)

**Risks**:
- User perception: "Platform hiding behind arbitration"
- Some courts may strike down arbitration clauses in consumer contracts (case law evolving)

---

## Non-Functional Requirements

### REQ-LC-012: Legal Document Versioning
**Priority**: P2 (Medium)
**Type**: System

**Description**:
- All legal documents (ToS, Privacy Policy, Creator Agreement) stored in database with version control
- Users/creators accept specific version (tracked in users.tos_version, creator_kyc.agreement_version)
- Material changes require re-acceptance (blocking modal on next login)
- Version history accessible: "/legal/terms/history" shows all versions with diffs

---

### REQ-LC-013: Compliance Audit Trail
**Priority**: P1 (High)
**Type**: Compliance

**Description**:
- All compliance actions logged: TDS deductions, content removals, account suspensions, DSAR requests
- Audit trail retained for 7 years (Income Tax Act, IT Rules 2021)
- Quarterly compliance reports generated (TDS, content moderation, data requests)
- External audit readiness: Export compliance data for auditor review

---

## Summary

**Total Requirements**: 13 (11 functional, 2 non-functional)
**Priority Breakdown**:
- P0 (Critical): 7 requirements
- P1 (High): 5 requirements
- P2 (Medium): 2 requirements

**Critical Legal Document Updates**:
1. ✅ Terms of Service: Update ₹500 → ₹100 minimum, 45% → 75-85% tiered revenue
2. ✅ Creator Agreement: Update ₹500 → ₹100 minimum, 45% → 75-85% tiered, remove 7-day hold
3. ✅ Privacy Policy: Add data localization (India-only) clause, DPDP Act 2023 compliance

**Key Compliance Decisions**:
- ✅ All data stored in India (AWS ap-south-1 Mumbai)
- ✅ Grievance Officer + 72h response for content moderation
- ✅ AI-enhanced age verification (self-declaration + face estimation)
- ✅ Automated TDS filing via ClearTax API
- ✅ GST alert for creators at ₹20L threshold
- ✅ Mandatory arbitration (no opt-out)

**Pre-Launch Checklist**:
1. Company incorporation + GST + TAN (3-4 weeks)
2. ToS, Privacy Policy, Creator Agreement drafted and reviewed by legal counsel
3. Grievance Officer appointed and contact page published
4. TDS compliance automation tested (ClearTax integration)
5. Data residency audit (verify all resources in ap-south-1)
6. Trademark application filed (can complete post-launch)

**Ongoing Compliance**:
- Quarterly TDS filing (26Q, 16A)
- Monthly transparency reports (content moderation)
- Annual data protection audit
- Trademark monitoring (cease & desist for infringers)

**Legal Counsel Required**:
- Tech/startup lawyer: ToS, Privacy Policy, Creator Agreement
- Tax lawyer/CA: TDS compliance, GST advisory
- IP lawyer: Trademark filing and enforcement
- **Estimated Cost**: ₹2-3 Lakh for initial legal setup

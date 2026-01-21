# Requirements: Legal & Compliance

**Section**: 12-legal-and-compliance
**Source Lines**: 2445-2705
**Generated**: 2026-01-20
**Status**: Complete

---

## Business Registration Requirements

### REQ-LC-001: Company Registration
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Register Private Limited Company with Ministry of Corporate Affairs (MCA) before launch.

**Rationale**: Legal entity required for business operations, contracts, and payment processing.

**Acceptance Criteria**:
- Register with MCA as Private Limited Company
- Obtain Certificate of Incorporation
- Timeline: 7-10 days
- Estimated cost: ₹10,000-15,000
- Required before payment gateway activation

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Prerequisite for all other registrations.

---

### REQ-LC-002: GST Registration
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Register for Goods and Services Tax (GST) as digital service provider.

**Rationale**: GST registration mandatory for digital services; required for invoicing.

**Acceptance Criteria**:
- Apply via GST Portal
- Obtain GSTIN
- Timeline: 3-5 days
- Estimated cost: ₹2,000-3,000
- GST invoices for coin purchases
- GST returns filed monthly/quarterly

**Dependencies**: REQ-LC-001
**Related Design Decisions**: None

**Notes**: 18% GST on digital services; consider input tax credit.

---

### REQ-LC-003: TAN Registration
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Obtain Tax Deduction Account Number (TAN) for TDS deduction on creator payouts.

**Rationale**: TAN required by law for deducting and depositing TDS.

**Acceptance Criteria**:
- Apply via NSDL
- Obtain TAN number
- Timeline: 7 days
- Cost: ₹65
- Required before first creator payout

**Dependencies**: REQ-LC-001
**Related Design Decisions**: None

**Notes**: Prerequisite for REQ-CP-005 (TDS Calculation).

---

### REQ-LC-004: Trademark Registration
**Priority**: P1 (High)
**Type**: Compliance

**Description**: Register "LIVVLY" trademark for brand protection.

**Rationale**: Protect brand identity and prevent infringement.

**Acceptance Criteria**:
- Apply via IP India portal
- Class 9 (mobile apps) and Class 42 (software services)
- Timeline: 6-12 months
- Cost: ₹5,000-10,000
- TM status (application) can be used immediately

**Dependencies**: REQ-LC-001
**Related Design Decisions**: None

**Notes**: Long timeline; initiate early but not blocking launch.

---

## Legal Documentation Requirements

### REQ-LC-005: Terms of Service Document
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Create comprehensive Terms of Service covering all platform rules and user obligations.

**Rationale**: Legal protection and clear user expectations.

**Acceptance Criteria**:
- Cover all 11 sections per PRD:
  1. Eligibility (18+, one account, valid phone)
  2. Account Rules
  3. Virtual Currency (Coins)
  4. Creator Earnings
  5. Prohibited Conduct
  6. Content Guidelines
  7. Privacy reference
  8. Termination
  9. Limitation of Liability
  10. Dispute Resolution (Coimbatore arbitration)
  11. Changes notification
- Legal review completed
- Available in English and Tamil
- User must accept before using app
- Version history maintained

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Update on any feature or policy changes.

---

### REQ-LC-006: Privacy Policy Document
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Create Privacy Policy compliant with Indian data protection regulations.

**Rationale**: Legal requirement and user trust.

**Acceptance Criteria**:
- Cover all 10 sections per PRD:
  1. Information Collected (phone, profile, location, device, usage)
  2. How Information Used
  3. Information Sharing
  4. Data Retention (90 days deleted, 7 years transactions)
  5. User Rights (access, correct, delete, portability)
  6. Security measures
  7. Cookies & Tracking
  8. Children's Privacy (no under 18)
  9. Contact information
  10. Updates process
- Legal review completed
- English and Tamil versions
- Link in app settings
- Accessible before signup

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Must comply with upcoming DPDP Act.

---

### REQ-LC-007: Creator Partnership Agreement
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Create legally binding agreement for creators covering earnings, obligations, and termination.

**Rationale**: Clear contractual relationship with creators for payment legitimacy.

**Acceptance Criteria**:
- Cover all 12 sections per PRD:
  1. Engagement (independent contractor)
  2. Creator Requirements (18+, PAN, bank, KYC, 4.0+ rating)
  3. Earnings Structure (45% share, 7-day pending)
  4. Tax Obligations (TDS, Form 16A, ITR)
  5. Payout Terms (T+1, IMPS/NEFT/UPI)
  6. Conduct Requirements
  7. Content Rights
  8. Performance Standards (2 hrs/week minimum)
  9. Termination (7 days notice, 30 days payout)
  10. Confidentiality
  11. Indemnification
  12. Dispute Resolution
- Digital signature support
- Acceptance required for creator activation
- Stored in creator_kyc records

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Legal review essential; covers payment legitimacy.

---

### REQ-LC-008: Community Guidelines Document
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Create clear community guidelines for content and behavior standards.

**Rationale**: Sets expectations and provides basis for enforcement actions.

**Acceptance Criteria**:
- Cover prohibited conduct categories:
  - Harassment, abuse, bullying
  - Explicit/pornographic content
  - Hate speech, discrimination
  - Fraud, fake accounts, bots
  - Personal contact sharing
  - Commercial solicitation
  - Impersonation
  - Illegal activities
- Clear examples for each category
- Consequences explained (warning → ban)
- Reporting mechanism documented
- Available in-app and during onboarding

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Referenced in Terms of Service.

---

## TDS Compliance Requirements

### REQ-LC-009: TDS Filing System
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Implement quarterly TDS filing process with all required forms.

**Rationale**: Legal requirement for tax compliance.

**Acceptance Criteria**:
- Quarterly filing calendar:
  - Q1 (Apr-Jun): File by July 31
  - Q2 (Jul-Sep): File by October 31
  - Q3 (Oct-Dec): File by January 31
  - Q4 (Jan-Mar): File by May 31
- Form 26Q: Quarterly TDS return
- Challan 281: TDS deposit to government
- Track all creator PANs for filing
- Aggregate by PAN for quarterly totals

**Dependencies**: REQ-LC-003, REQ-CP-005
**Related Design Decisions**: None

**Notes**: May outsource to CA firm initially.

---

### REQ-LC-010: Form 16A Generation
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Generate and provide Form 16A (TDS certificate) to creators quarterly.

**Rationale**: Creators need Form 16A for their income tax filing.

**Acceptance Criteria**:
- Generate Form 16A for each creator with TDS deducted
- Include: TAN, PAN, deduction period, amount
- Provide via app download (PDF)
- Email notification when available
- Issue within 15 days of quarterly filing
- Historical access to all certificates

**Dependencies**: REQ-LC-009, REQ-CP-013
**Related Design Decisions**: None

**Notes**: Template per Income Tax Act format.

---

### REQ-LC-011: TDS Rate Application
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Apply correct TDS rates based on payment type and threshold.

**Rationale**: Different TDS sections apply to different payment types.

**Acceptance Criteria**:
- Section 194J: 10% for professional services (creators)
- Section 194C: 2% for contractor payments (agencies)
- Threshold: ₹30,000 per FY per creator
- Track cumulative earnings per FY per PAN
- Correctly handle threshold crossing mid-payment
- Higher rate if PAN not provided (20%)

**Dependencies**: REQ-CP-005
**Related Design Decisions**: None

**Notes**: Agency payments (Section 194C) may apply in Phase 2.

---

## Data Protection Requirements

### REQ-LC-012: Data Retention Policy
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Implement data retention periods as per legal requirements.

**Rationale**: Legal compliance and user privacy.

**Acceptance Criteria**:
- Active accounts: Retain data while active
- Deleted accounts: 90-day retention for legal compliance
- Transaction records: 7-year retention (tax/legal)
- Chat messages: Not stored permanently (session only)
- Call recordings: Not stored (live only)
- Automated purge for expired data
- Audit log of deletions

**Dependencies**: REQ-DB-021
**Related Design Decisions**: DD-DB-001

**Notes**: Must comply with upcoming DPDP Act.

---

### REQ-LC-013: User Data Rights Implementation
**Priority**: P1 (High)
**Type**: Compliance

**Description**: Implement user rights for data access, correction, and deletion.

**Rationale**: Privacy compliance and user trust.

**Acceptance Criteria**:
- Data Access: Export user data in machine-readable format
- Data Correction: Allow profile edits via app
- Account Deletion: Full deletion with confirmation
- Consent Withdrawal: Option to opt out of non-essential data use
- Request response: Within 30 days
- In-app request flow
- Admin dashboard for handling requests

**Dependencies**: REQ-LC-006
**Related Design Decisions**: None

**Notes**: Data portability format: JSON export.

---

## App Store Compliance Requirements

### REQ-LC-014: App Store Guidelines Compliance
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Ensure app complies with Google Play Store guidelines for dating/social apps.

**Rationale**: Required for app distribution.

**Acceptance Criteria**:
- Age rating: 18+ (Mature)
- Content rating questionnaire completed
- Dating app category guidelines followed
- In-app purchase rules followed
- Privacy policy link in store listing
- Ad policy compliance (if applicable)
- User safety features documented

**Dependencies**: None
**Related Design Decisions**: None

**Notes**: Review guidelines before each major update.

---

### REQ-LC-015: Payment Compliance
**Priority**: P0 (Critical)
**Type**: Compliance

**Description**: Ensure payment flows comply with RBI and Play Store guidelines.

**Rationale**: Required for payment processing legitimacy.

**Acceptance Criteria**:
- Razorpay handles PA/PG compliance
- Play Billing for in-app purchases
- Clear pricing displayed before purchase
- Refund policy documented
- No misleading payment UI
- Transaction receipts provided
- Currency displayed as INR

**Dependencies**: REQ-TA-007
**Related Design Decisions**: DD-FS-006

**Notes**: Hybrid model (Razorpay + Play Billing) per DD-FS-006.

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-LC-001 | Company Registration | P0 | Compliance |
| REQ-LC-002 | GST Registration | P0 | Compliance |
| REQ-LC-003 | TAN Registration | P0 | Compliance |
| REQ-LC-004 | Trademark Registration | P1 | Compliance |
| REQ-LC-005 | Terms of Service Document | P0 | Compliance |
| REQ-LC-006 | Privacy Policy Document | P0 | Compliance |
| REQ-LC-007 | Creator Partnership Agreement | P0 | Compliance |
| REQ-LC-008 | Community Guidelines Document | P0 | Compliance |
| REQ-LC-009 | TDS Filing System | P0 | Compliance |
| REQ-LC-010 | Form 16A Generation | P0 | Compliance |
| REQ-LC-011 | TDS Rate Application | P0 | Compliance |
| REQ-LC-012 | Data Retention Policy | P0 | Compliance |
| REQ-LC-013 | User Data Rights Implementation | P1 | Compliance |
| REQ-LC-014 | App Store Guidelines Compliance | P0 | Compliance |
| REQ-LC-015 | Payment Compliance | P0 | Compliance |

**Total Requirements**: 15
**P0 (Critical)**: 13
**P1 (High)**: 2

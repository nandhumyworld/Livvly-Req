# Legal & Compliance - Questions & Answers

**Section**: 12. Legal & Compliance
**Date**: 2026-01-23
**Answered By**: User
**Total Questions**: 9

---

## Core Questions (Q1-Q3)

### Q1: The Terms of Service (line 2488) and Creator Agreement (line 2622) show ₹500 minimum withdrawal, but Section 10 approved ₹100. What should the legal documents state?

**Answer**: Update to ₹100 (Recommended)

**Rationale**:
- **Consistency**: Legal documents must match technical implementation
- **User trust**: If ToS says ₹500 but platform allows ₹100, creates confusion and potential breach of contract claims
- **REQ-CP-001 alignment**: Section 10 approved ₹100 minimum for creator-friendly access to earnings

**Document Updates Required**:
1. **Terms of Service** (Section 4, line 2488):
   - OLD: "Minimum withdrawal: ₹500"
   - NEW: "Minimum withdrawal: ₹100"

2. **Creator Agreement** (Section 3, line 2622):
   - OLD: "Minimum withdrawal: ₹500"
   - NEW: "Minimum withdrawal: ₹100"

**Impact on Requirements**:
- ✅ REQ-LC-002: Terms of Service updated to reflect ₹100 minimum
- ✅ REQ-LC-004: Creator Agreement updated to reflect ₹100 minimum

**Legal Risk Mitigation**:
- Without update: Creators could argue platform breached agreement by allowing ₹100 withdrawals when contract states ₹500
- With update: Alignment between legal docs and technical implementation = no ambiguity

---

### Q2: The Creator Agreement (line 2618) states 45% creator revenue share, but Sections 4 & 8 approved 75-85% tiered model. What should the legal documents state?

**Answer**: Update to 75-85% tiered (Recommended)

**Rationale**:
- **CRITICAL MISMATCH**: Platform pays 75-85% but agreement says 45% → Platform could be accused of underpaying creators
- **Revenue model alignment**: REQ-RM-003 defines tiered model (Tier 1: 75%, Tier 2: 80%, Tier 3: 85%)
- **Legal liability**: If agreement says 45% and platform pays 75%, creators have no grounds to complain. But if agreement says 45% and platform accidentally pays 45% in one transaction, creator can sue for balance.

**Document Updates Required**:
1. **Terms of Service** (Section 4, line 2489):
   - OLD: "Platform commission: 55% of coin value" (= 45% creator)
   - NEW: "Creator earnings: Tiered revenue share model
     - Tier 1: 75% of coin value (monthly earnings <₹50,000)
     - Tier 2: 80% of coin value (monthly earnings ₹50,000-₹2,00,000)
     - Tier 3: 85% of coin value (monthly earnings >₹2,00,000)
     - Tier recalculated daily based on trailing 30-day earnings
     - Tier changes apply to future earnings only (no retroactive adjustment)"

2. **Creator Agreement** (Section 3, line 2618):
   - Same update as above

**Impact on Requirements**:
- ✅ REQ-LC-002: ToS updated with tiered revenue model
- ✅ REQ-LC-004: Creator Agreement updated with tiered revenue model
- ✅ Aligns with REQ-RM-003, REQ-CP-010

**Example Creator Agreement Clause**:
```
3. EARNINGS STRUCTURE
You will receive diamonds based on coins spent on your services according to the following tiered revenue share model:

Tier 1 (New Creators): 75% of coin value
  - Applies when your trailing 30-day earnings are less than ₹50,000

Tier 2 (Growing Creators): 80% of coin value
  - Applies when your trailing 30-day earnings are ₹50,000 to ₹2,00,000

Tier 3 (Top Creators): 85% of coin value
  - Applies when your trailing 30-day earnings exceed ₹2,00,000

Tier Calculation:
- Your tier is recalculated daily at 1:00 AM IST based on your earnings from the previous 30 days
- Tier upgrades/downgrades apply to earnings after the tier change date only
- Past earnings are not recalculated when your tier changes

Conversion Rate:
- 1 Coin = ₹0.90 (platform value)
- 1 Diamond = ₹1.00 (creator payout value)
- Example: User spends 100 coins (₹90 value), Tier 1 creator earns 68 diamonds (₹68) = 75% of ₹90
```

**Legal Risk Mitigation**:
- Without update: Massive underpayment risk (45% vs 75-85% = 30-40% discrepancy)
- Platform could face class action lawsuit from creators for unpaid earnings
- With update: Agreement matches implementation, no dispute risk

---

### Q3: The Creator Agreement (line 2621) states 7-day pending period, but Section 10 approved T+1 with no holding period. What should the legal documents state?

**Answer**: Update to T+1 only (Recommended)

**Rationale**:
- **Section 10 Q3 decision**: No holding period, T+1 from withdrawal request only
- **Current implementation**: Earnings instantly available for withdrawal (no "pending balance" state)
- **User expectation**: If agreement says 7-day hold but system allows instant withdrawal, creators get better deal (no legal issue). But if agreement says 7 days and system later enforces it, creators feel deceived.

**Document Updates Required**:
1. **Creator Agreement** (Section 3, line 2621):
   - OLD: "Pending period: 7 days"
   - NEW: "Earnings Availability: Your diamonds are available for withdrawal immediately after earning. Bank credit within T+1 (next calendar day) of withdrawal request."

2. **Terms of Service** (if mentioned):
   - Add clarity: "Creator earnings available for withdrawal immediately, subject to minimum ₹100 threshold and daily/monthly limits."

**Impact on Requirements**:
- ✅ REQ-LC-004: Creator Agreement updated to remove 7-day hold, clarify T+1 processing
- ✅ Aligns with REQ-CP-002 (T+1 payout without holding period)

**Example Creator Agreement Clause**:
```
5. PAYOUT TERMS

Earnings Availability:
- Your diamonds are credited to your available balance immediately upon earning
- No holding period or pending state
- You may request withdrawal anytime your available balance is ≥₹100

Withdrawal Processing:
- Withdrawal requests processed within T+1 (next calendar day)
- T+1 means: Request on Monday 11:00 PM → Bank credit by Tuesday 11:59 PM
- Cutoff time: 11:59 PM IST daily (requests after rollover to next T+1 cycle)

Payout Method:
- IMPS (instant transfer): For amounts up to ₹2,00,000
- NEFT (same/next banking day): For amounts above ₹2,00,000
- Method automatically selected by platform for optimal speed and cost

Withdrawal Limits:
- Minimum per withdrawal: ₹100
- Maximum per day: ₹50,000
- Maximum per month: ₹5,00,000
- Higher limits available upon request for top creators
```

**Legal Risk Mitigation**:
- Without update: Agreement says 7 days but system gives instant access → Creates expectation mismatch if policy later changes
- With update: Agreement matches implementation, no ambiguity

---

## Follow-up Questions (Q4-Q6)

### Q4: The PRD doesn't specify data localization requirements. India's data protection laws require certain data to be stored within India. What's the data residency strategy?

**Answer**: All data in India (Recommended)

**Rationale**:
- **Digital Personal Data Protection (DPDP) Act 2023**: Anticipated regulations require all personal data of Indian citizens to be stored within India
- **Simplest compliance**: Store ALL data in India = no ambiguity about which data must stay vs can leave
- **Technical alignment**: AWS ap-south-1 (Mumbai) already approved in Section 6 for all infrastructure
- **User trust**: "Your data never leaves India" is strong privacy message

**Data Categories Covered**:
1. **Personal Identifiable Information (PII)**:
   - Phone numbers, names, photos, location (city/state)
   - Date of birth, gender
   - MUST stay in India per DPDP Act

2. **Financial Data**:
   - Transactions, coin purchases, payouts
   - Bank account numbers, PAN, TAN (encrypted)
   - MUST stay in India per RBI guidelines

3. **Content Data**:
   - Profile photos, voice recordings (if any), chat messages
   - No legal requirement but keep in India for consistency

4. **Analytics & Logs**:
   - User activity logs, API logs, error logs
   - No legal requirement but keep in India for simplicity

**Implementation**:
- ALL AWS resources: ap-south-1 (Mumbai) region only
- S3 buckets: Mumbai region, no cross-region replication
- RDS database: Mumbai primary, Mumbai read replicas only (no other regions)
- CloudFront CDN: Mumbai edge locations, geo-restriction to India
- Third-party verification:
  - Razorpay: Stores data in India ✅
  - MSG91: SMS logs in India ✅
  - Karza: KYC processing in India ✅

**Impact on Requirements**:
- ✅ REQ-LC-007: Data Residency (India-Only) - All data in Mumbai
- ✅ REQ-LC-003: Privacy Policy explicitly states "All data stored within India"
- ✅ REQ-TA-001: AWS Mumbai infrastructure (already approved)

**Alternative Considered**: "Hybrid: Critical data in India"
- Store PII + financial in India, analytics/logs in cheaper US regions
- More complex: Need to classify each data type, verify compliance
- Risk: Accidental data leakage to non-Indian servers (audit complexity)
- Decision: Not worth the cost savings (~₹10K-20K/month) vs compliance risk

---

### Q5: For creator tax compliance, should the platform facilitate GST registration for high-earning creators?

**Answer**: Alert creators at ₹20L threshold (Recommended)

**Rationale**:
- **GST threshold**: ₹20 Lakh annual turnover requires GST registration (service providers)
- **Creator responsibility**: Creators are independent contractors, responsible for their own tax compliance
- **Platform role**: Alert and educate, but don't register or file on their behalf
- **Liability**: If platform registers creators for GST, platform becomes responsible for their GST compliance (opens liability)

**Alert Workflow**:
1. **Monitor earnings**: Daily cron job calculates trailing 365-day earnings
2. **Trigger alert**: When earnings cross ₹19,50,000 (buffer before ₹20L)
3. **In-app notification**: "You may need GST registration. Consult a tax advisor."
4. **Email with resources**:
   - Link to GST portal: https://www.gst.gov.in
   - PDF guide: "GST Registration for Creators"
   - CA directory: List of CAs for GST filing support
5. **Dashboard banner**: "GST Registration Recommended" (persistent until creator dismisses)

**What Platform Does NOT Do**:
- ❌ Register creators for GST (they handle themselves)
- ❌ Deduct GST from creator earnings (creators invoice platform)
- ❌ File GST returns on behalf of creators (they file independently)

**Impact on Requirements**:
- ✅ REQ-LC-010: GST Alert for High-Earning Creators
- ✅ Compliance support without assuming creator tax liability

**Alternative Considered**: "No GST facilitation"
- Simpler for platform (no monitoring, no alerts)
- Risk: Creators unknowingly violate GST law, face penalties
- Reputation damage: "Platform doesn't care about creator compliance"
- Decision: ₹20L alert is minimal effort, high creator value

**Technical Implementation**:
```sql
-- Daily cron job
INSERT INTO notifications (user_id, type, title, message)
SELECT user_id, 'gst_registration_alert',
  'GST Registration Recommended',
  'Your annual earnings have crossed ₹19.5 Lakh. You may need to register for GST within 30 days. Consult a tax advisor for guidance.'
FROM (
  SELECT user_id, SUM(diamonds * tier_percent * COIN_TO_INR) as annual_earnings
  FROM transactions
  WHERE type IN ('earn_call', 'earn_gift', 'earn_room')
    AND created_at > NOW() - INTERVAL '365 days'
  GROUP BY user_id
) earnings
WHERE annual_earnings >= 1950000
  AND user_id NOT IN (SELECT user_id FROM gst_alerts_sent);

INSERT INTO gst_alerts_sent (user_id, alerted_at) VALUES (...);
```

---

### Q6: The PRD shows arbitration in Coimbatore. Should users/creators be allowed to opt-out of arbitration and pursue litigation?

**Answer**: Mandatory arbitration (Recommended)

**Rationale**:
- **Cost efficiency**: Arbitration (₹50K-2L per case) is 10x cheaper than litigation (₹5L-20L)
- **Speed**: Arbitration (3-6 months) is 5x faster than litigation (2-5 years)
- **Confidentiality**: Arbitration awards are not public (protects platform reputation)
- **Enforceability**: Arbitration & Conciliation Act 1996 makes awards binding and enforceable
- **Industry standard**: Most consumer platforms (Uber, Zomato, Swiggy) use mandatory arbitration

**Arbitration Clause Structure**:
```
10. DISPUTE RESOLUTION

1. Informal Resolution (30 days):
   - Contact support@livvly.com with dispute description
   - Platform responds within 7 business days
   - Good faith negotiation for 30 days

2. Binding Arbitration (if informal fails):
   - Governed by Arbitration & Conciliation Act, 1996
   - Single arbitrator mutually appointed (or appointed by court if no agreement)
   - Seat of arbitration: Coimbatore, Tamil Nadu
   - Language: English
   - Arbitration costs: Losing party bears costs
   - Arbitrator's decision is final and binding

3. Exceptions (Can Go to Court):
   - Intellectual property disputes (trademark infringement, copyright)
   - Fraud or criminal matters
   - Requests for injunctive relief (immediate court orders)

4. No Class Actions:
   - All disputes resolved individually
   - No class action, consolidated action, or representative action allowed
   - Each user/creator must bring disputes in their individual capacity

5. No Opt-Out:
   - Arbitration clause is mandatory
   - Continued use of platform constitutes acceptance
   - Disputes arising from this agreement must follow this process
```

**Impact on Requirements**:
- ✅ REQ-LC-011: Mandatory Arbitration Clause (ToS and Creator Agreement)
- ✅ REQ-LC-002: ToS Section 10 includes arbitration clause
- ✅ REQ-LC-004: Creator Agreement Section 12 includes arbitration clause

**Alternative Considered**: "Optional arbitration with opt-out"
- User can opt-out within 30 days of signup and pursue litigation
- More "user-friendly" on paper
- Risk: Every dispute becomes litigation (expensive, time-consuming)
- US platforms offer opt-out due to state laws, but India has no such requirement
- Decision: Mandatory arbitration standard in India

**Legal Validity in India**:
- Supreme Court: Arbitration clauses in consumer contracts are VALID (Vidya Drolia vs Durga Trading, 2021)
- Exception: Clause cannot be "one-sided" or "unconscionable"
- Our clause: Both parties bound by same process (not one-sided) ✅

**Risk**: Some courts may strike down if deemed "unfair to consumers"
- Mitigation: Arbitration clause includes exceptions (IP, fraud can go to court)
- Mitigation: Informal resolution period (30 days) shows good faith

---

## Gap Analysis Questions (Q7-Q9)

### Q7: How should the platform handle Content Moderation compliance under IT Rules 2021?

**Answer**: Grievance Officer + 72h response (Recommended)

**Rationale**:
- **IT Rules 2021 requirement**: Platforms with >5M users MUST appoint Grievance Officer
- **72-hour SLA**: Acknowledge complaints within 72 hours (business days)
- **Transparency**: Monthly transparency reports required (complaints received, action taken)
- **Penalties**: Loss of safe harbor protection (platform liable for user content) if non-compliant

**Grievance Officer Responsibilities**:
1. **Receive complaints**: Email grievance@livvly.com, in-app report button
2. **Acknowledge within 72h**: Auto-acknowledge receipt, assign to review queue
3. **Investigate**: AI moderation + human review (10% sample, 100% flagged)
4. **Take action**: Remove content, suspend accounts, issue warnings
5. **Respond to complainant**: Notify outcome within 15 days
6. **Monthly transparency report**: Publish on website by 10th of next month

**Transparency Report Template**:
```
LIVVLY - Transparency Report for [Month Year]

1. User Complaints Received: 45
   - Harassment: 20
   - Explicit Content: 15
   - Fraud: 8
   - Other: 2

2. Actions Taken:
   - Content Removed: 30 items
   - Accounts Suspended: 12 accounts
   - Warnings Issued: 25 warnings
   - No Action: 3 complaints (unfounded)

3. Law Enforcement Requests:
   - Requests Received: 2
   - Data Disclosed: 1 (court order)
   - Requests Rejected: 1 (incomplete paperwork)

4. Proactive Removal:
   - AI Moderation Flags: 150
   - Human Review: 150 reviewed, 80 content removed

Grievance Officer: [Name]
Email: grievance@livvly.com
Phone: [Number]
```

**Impact on Requirements**:
- ✅ REQ-LC-008: Content Moderation Compliance (IT Rules 2021)
- ✅ Appoint Grievance Officer (employee or external counsel)
- ✅ Publish contact page: "/grievance-officer"
- ✅ Implement 72h SLA tracking in admin dashboard
- ✅ Monthly transparency report workflow (n8n cron job)

**Alternative Considered**: "Basic moderation only"
- AI moderation (REQ-FS-022) with no Grievance Officer
- Non-compliant for platforms with >5M users
- Risk: Government blocking orders, fines, criminal liability for non-compliance
- Decision: Grievance Officer mandatory for legal compliance

**Cost**: ₹3-5 Lakh per year (part-time employee or external counsel)

---

### Q8: Should the platform have age verification beyond self-declaration (18+ requirement)?

**Answer**: Self-declaration with AI face age estimation (Recommended)

**Rationale**:
- **Legal requirement**: Platform must prevent minors (<18) from accessing dating services
- **Self-declaration alone**: Easy to bypass (minor lies about age)
- **AI face estimation**: Secondary check reduces minor access risk by ~70-80%
- **Balance**: AI verification doesn't add friction (runs in background)

**Two-Layer Age Verification**:

**Layer 1: Self-Declaration** (Primary)
- Signup form: "Confirm you are 18 years or older" checkbox (mandatory)
- Date of birth input: Validates age ≥18, rejects if <18
- Stored in: users.date_of_birth

**Layer 2: AI Face Age Estimation** (Secondary)
- After profile photo upload, call AWS Rekognition DetectFaces API
- API returns: AgeRange { Low: 16, High: 20 }
- Logic: If AgeRange.High < 18 → Flag account for manual review

**Flagged Account Workflow**:
```
1. AI estimates age <18 from profile photo
2. Account flagged: user_flags.flag_type = 'age_verification_required'
3. Send to manual review queue (reviewed within 24 hours)
4. Manual reviewer checks:
   - Profile photo: Does person appear <18?
   - Date of birth: Plausible? (e.g., DOB shows 18 but photo shows 14)
   - Behavior: Recent signup? No activity yet?
5. If suspicious: Request govt-issued ID (Aadhaar, PAN, driving license)
   - Send email: "Please upload age proof document for verification"
   - Account restricted until verified (can't make calls, can't spend coins)
6. If confirmed minor:
   - Ban account immediately
   - Issue full refund if coins purchased
   - Report to law enforcement if required
7. If false positive (actually 18+):
   - Approve account, remove flag
   - Apologize for inconvenience
```

**Acceptance Criteria**:
- Self-declaration checkbox on signup (blocks signup if unchecked)
- DOB validation rejects age <18
- AI age estimation runs on all profile photo uploads (background process)
- Flagged accounts sent to manual review queue within 1 hour
- Manual review SLA: 24 hours
- False positive rate: <1% (AI occasionally flags 18-20 year-olds, acceptable)

**Impact on Requirements**:
- ✅ REQ-LC-009: Age Verification (AI-Enhanced)
- ✅ AWS Rekognition API integration
- ✅ Manual review queue in admin dashboard
- ✅ User flag system: user_flags table

**Alternative Considered**: "Self-declaration only"
- User declares 18+, no secondary verification
- Simplest UX, no AI cost
- Risk: High minor access rate (10-20% of users may be minors lying about age)
- Reputation risk: "Platform allows minors" = PR disaster + regulatory action
- Decision: AI verification worth the cost (₹1 per 1,000 photos = ₹5K-10K/month)

**Technical Implementation**:
```python
# Backend: After profile photo upload
import boto3

rekognition = boto3.client('rekognition', region_name='ap-south-1')

def verify_age_from_photo(photo_s3_key):
    response = rekognition.detect_faces(
        Image={'S3Object': {'Bucket': 'livvly-media', 'Name': photo_s3_key}},
        Attributes=['AGE_RANGE']
    )

    if not response['FaceDetails']:
        return {'flag': False, 'reason': 'No face detected'}

    age_range = response['FaceDetails'][0]['AgeRange']

    if age_range['High'] < 18:
        # Flag for manual review
        return {'flag': True, 'reason': f"AI estimated age: {age_range['Low']}-{age_range['High']}"}

    return {'flag': False}
```

**Ethical Consideration**:
- AI age estimation has racial bias (accuracy varies by ethnicity)
- Mitigation: Test across Indian demographics (North, South, East, West)
- Mitigation: False positives reviewed by human (no auto-ban based on AI alone)

---

### Q9: For quarterly TDS filing (Form 26Q), should this be automated or manual process?

**Answer**: Automated TDS filing integration (Recommended)

**Rationale**:
- **Complexity**: Manual 26Q filing is error-prone (wrong PAN, calculation errors, missed deadlines)
- **Scale**: 100+ creators = 100+ Form 16A certificates per quarter (time-consuming)
- **Penalties**: Late filing (₹200/day), wrong filing (100% of TDS) are expensive
- **ROI**: ClearTax API costs ₹10-15K/year, saves ~₹50K/year in accountant time

**Automated TDS Workflow**:
1. **Data Collection** (Continuous):
   - TDS deducted at each withdrawal stored in: withdrawals.tds_amount
   - Creator PAN stored in: creator_kyc.pan_number
   - Creator earnings tracked in: transactions table

2. **Quarterly 26Q Generation** (20th of month following quarter end):
   - N8n cron job triggers: July 20 (Q1), October 20 (Q2), January 20 (Q3), May 20 (Q4)
   - Backend aggregates data:
     ```sql
     SELECT creator_kyc.pan_number, users.name,
            SUM(withdrawals.amount) as total_paid,
            SUM(withdrawals.tds_amount) as total_tds
     FROM withdrawals
     JOIN creator_kyc ON withdrawals.user_id = creator_kyc.user_id
     WHERE withdrawals.created_at BETWEEN 'Q_START' AND 'Q_END'
       AND withdrawals.tds_amount > 0
     GROUP BY creator_kyc.pan_number, users.name
     ```
   - Generate 26Q file (pipe-delimited text format per govt spec)

3. **ClearTax API Filing**:
   - POST /api/v1/tds/file-26q with 26Q file
   - ClearTax validates file, files with Income Tax portal
   - Returns acknowledgment number (confirmation of filing)

4. **Form 16A Generation** (1 per creator):
   - For each creator with TDS deducted in quarter
   - PDF certificate includes:
     - Creator PAN, name
     - Total income paid (withdrawals amount)
     - TDS deducted
     - Quarter and financial year
     - Acknowledgment number from 26Q filing
   - Auto-email to creator: "Your TDS certificate (Form 16A) for Q1 FY 2026-27"
   - Store in app: Dashboard → Tax Documents → Download 16A

5. **Challan 281 Payment Tracking**:
   - Monthly TDS deducted must be deposited by 7th of next month
   - ClearTax tracks: "₹45,000 TDS deducted in Jan 2026, due by Feb 7"
   - Alert finance admin: "TDS payment due in 3 days"
   - Future enhancement: Auto-debit from company account (requires RBI approval)

**Acceptance Criteria**:
- ClearTax account created, API keys configured in backend
- Quarterly 26Q filing automated (n8n cron job)
- Form 16A auto-generated and emailed to creators
- Challan 281 payment reminders sent to finance admin
- Audit trail: All filings logged with acknowledgment numbers
- Error handling: Alert finance admin if API fails (Slack notification)

**Impact on Requirements**:
- ✅ REQ-LC-005: TDS Compliance Automation (quarterly filing, certificates)
- ✅ REQ-LC-006: Automated TDS Filing Integration (ClearTax API)
- ✅ REQ-AW-005: Daily tier recalculation (provides earnings data for TDS)

**Alternative Considered**: "Manual TDS filing by accountant"
- Accountant downloads transaction data from admin dashboard
- Manually prepares 26Q file (Excel → govt format converter)
- Manually files via govt TDS portal
- Manually generates 16A certificates (PDF template)
- Cheaper initially (no API cost)
- Risk: Human errors (wrong PAN, calculation mistakes), missed deadlines
- Time-consuming: 2-3 days per quarter for 100+ creators
- Decision: Automation worth the cost (₹10-15K/year vs ₹50K/year accountant time)

**ClearTax Pricing** (2026 estimates):
- Annual subscription: ₹5,000
- Form 16A certificates: ₹10 per certificate
- Example cost: 100 creators × 4 quarters × ₹10 = ₹4,000/year certificates
- **Total**: ₹9,000-10,000/year (vs ₹50K accountant time)

**ROI Calculation**:
- Manual filing: ₹50,000/year (accountant time @ ₹12,500 per quarter)
- Automated filing: ₹10,000/year (ClearTax subscription + certificates)
- **Savings**: ₹40,000/year
- **Break-even**: Immediate (API costs less than 1 quarter of manual work)

---

## Summary

**Total Questions Answered**: 9
**Core Questions**: 3
**Follow-up Questions**: 3
**Gap Analysis Questions**: 3

**Critical Decisions Made**:
1. ✅ Update ToS and Creator Agreement: ₹500 → ₹100 minimum withdrawal
2. ✅ Update ToS and Creator Agreement: 45% → 75-85% tiered revenue share
3. ✅ Update Creator Agreement: Remove 7-day hold → T+1 only
4. ✅ Data residency: All data in India (AWS ap-south-1 Mumbai)
5. ✅ GST support: Alert creators at ₹20L threshold
6. ✅ Dispute resolution: Mandatory arbitration (no opt-out)
7. ✅ Content moderation: Grievance Officer + 72h response (IT Rules 2021)
8. ✅ Age verification: Self-declaration + AI face age estimation
9. ✅ TDS automation: ClearTax API integration for 26Q/16A

**Requirements Impacted**: 13 total requirements (REQ-LC-001 through REQ-LC-013)

**PRD Conflicts Identified & Resolved**:
1. ⚠️ ToS minimum withdrawal: ₹500 → **CORRECTED to ₹100**
2. ⚠️ Creator Agreement minimum withdrawal: ₹500 → **CORRECTED to ₹100**
3. ⚠️ Creator revenue share: 45% → **CORRECTED to 75-85% tiered**
4. ⚠️ Pending period: 7 days → **REMOVED (T+1 only)**

**Pre-Launch Legal Checklist**:
1. ✅ Company incorporation + GST + TAN (3-4 weeks)
2. ✅ Trademark application filed (6-12 months to complete)
3. ✅ ToS, Privacy Policy, Creator Agreement drafted by legal counsel
4. ✅ Grievance Officer appointed and contact page published
5. ✅ TDS automation tested (ClearTax integration)
6. ✅ Data residency audit (all resources in ap-south-1)

**Ongoing Compliance**:
- Quarterly: TDS filing (26Q, 16A certificates) - Automated
- Monthly: Transparency reports (content moderation) - Automated
- Annually: Data protection audit - External auditor
- Ongoing: Trademark monitoring - Legal counsel

**Legal Counsel Budget**:
- Initial setup (ToS, Privacy, Creator Agreement): ₹1.5-2 Lakh
- TDS compliance (CA/tax lawyer advisory): ₹50,000
- IP lawyer (trademark filing): ₹10,000-15,000
- **Total Initial**: ₹2-3 Lakh

**Ongoing Compliance Budget**:
- ClearTax TDS automation: ₹10,000/year
- Grievance Officer (part-time): ₹3-5 Lakh/year
- Annual data protection audit: ₹50,000/year
- Legal counsel retainer: ₹1 Lakh/year
- **Total Ongoing**: ₹4.5-6.5 Lakh/year

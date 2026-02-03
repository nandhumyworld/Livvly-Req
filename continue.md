# How to Continue: LIVVLY Architecture Workflow

**Last Updated:** 2026-02-03
**Current Status:** Step 5 of Architecture Workflow (Implementation Patterns) - In Progress
**Project:** LIVVLY Voice Dating App

---

## Current Progress Summary

### Completed Steps

| Step | Name                         | Status         | Date       |
| ---- | ---------------------------- | -------------- | ---------- |
| 1    | Document Initialization      | ✅ Complete    | 2026-02-02 |
| 2    | Project Context Analysis     | ✅ Complete    | 2026-02-03 |
| 3    | Starter Template Evaluation  | ✅ Complete    | 2026-02-03 |
| 4    | Core Architectural Decisions | ✅ Complete    | 2026-02-03 |
| 5    | Implementation Patterns      | 🔄 In Progress | 2026-02-03 |
| 6    | Project Structure            | 📋 Pending     | -          |
| 7    | Validation                   | 📋 Pending     | -          |
| 8    | Complete                     | 📋 Pending     | -          |

### Architecture Document Location

- **Path:** `_bmad-output/planning-artifacts/architecture.md`
- **Frontmatter:** `stepsCompleted: [1, 2, 3, 4]`

---

## Key Decisions Made (Steps 1-4)

### Step 3: Starter Templates Selected

| Platform            | Starter                               | Repository                                                     |
| ------------------- | ------------------------------------- | -------------------------------------------------------------- |
| **Backend**   | fastapi_best_architecture             | https://github.com/fastapi-practices/fastapi_best_architecture |
| **Mobile**    | Flutter Official + Clean Architecture | `flutter create --org com.livvly`                            |
| **Structure** | Custom Monorepo                       | `livvly/apps/{mobile,api}`                                   |

### Step 4: Core Decisions Confirmed

| #  | Category          | Decision        | Choice                                               |
| -- | ----------------- | --------------- | ---------------------------------------------------- |
| 1  | Data Modeling     | Access Pattern  | Repository Pattern                                   |
| 2  | Data Architecture | Caching TTLs    | 1h profiles, 30s creator status, 24h gifts           |
| 3  | Authentication    | JWT Config      | RS256, 1h access / 30d refresh                       |
| 4  | Authorization     | RBAC Structure  | user / creator / admin roles                         |
| 5  | API Design        | Response Format | Standardized JSON (`success`, `data`, `error`) |
| 6  | Real-time         | Communication   | Agora + FCM + API polling                            |
| 7  | Mobile State      | Management      | Bloc/Cubit                                           |
| 8  | Mobile Navigation | Router          | GoRouter                                             |
| 9  | DevOps            | Environments    | Local / Dev / Staging / Prod                         |
| 10 | Security          | Secrets         | AWS Secrets Manager                                  |

---

## Step 5: Implementation Patterns (In Progress)

### Patterns Proposed (Pending Confirmation)

The following patterns were drafted and need confirmation when resuming:

#### Naming Conventions

| Element       | Backend (Python) | Mobile (Dart) | Database           |
| ------------- | ---------------- | ------------- | ------------------ |
| Files         | snake_case       | snake_case    | -                  |
| Classes       | PascalCase       | PascalCase    | -                  |
| Functions     | snake_case       | camelCase     | -                  |
| Variables     | snake_case       | camelCase     | -                  |
| Tables        | -                | -             | snake_case, plural |
| Columns       | -                | -             | snake_case         |
| API Endpoints | snake_case       | -             | -                  |
| JSON Fields   | snake_case       | -             | -                  |

#### Bloc Event/State Naming

```dart
// Events: {Domain}{Action}
UserProfileRequested
CallInitiated

// States: {Domain}{Status}
UserProfileLoading
UserProfileLoaded
UserProfileError
```

#### Error Code Pattern

```
{CATEGORY}_{NUMBER}
AUTH_001, VALIDATION_001, BUSINESS_001, SYSTEM_001
```

#### Date/Time Format

- API: ISO 8601 UTC (`"2026-02-03T10:30:00Z"`)
- Database: TIMESTAMP WITH TIME ZONE (UTC)
- Display: User's locale (formatted on client)

---

## How to Resume

### Quick Resume (Same Session)

```
Continue the architecture workflow from Step 5 (Implementation Patterns).
The patterns have been proposed - confirm them and save to architecture.md,
then proceed to Step 6 (Project Structure).
```

### Full Resume (New Session)

```
I need to continue the LIVVLY architecture workflow. Context:

1. Architecture document: _bmad-output/planning-artifacts/architecture.md
2. Steps completed: [1, 2, 3, 4] - currently on Step 5
3. Step 5 (Implementation Patterns) was in progress with patterns proposed

Please read:
- continue.md (this file) for current status
- _bmad-output/planning-artifacts/architecture.md for decisions made

Next actions:
1. Confirm the proposed implementation patterns (naming, events, errors, dates)
2. Append Step 5 content to architecture.md
3. Proceed to Step 6 (Project Structure)
```

---

## Files Reference

### Architecture Output

| File                                                | Purpose                    | Status             |
| --------------------------------------------------- | -------------------------- | ------------------ |
| `_bmad-output/planning-artifacts/architecture.md` | Main architecture document | Steps 1-4 complete |
| `continue.md`                                     | Session continuation guide | This file          |

### PRD Input (Read-Only)

| File                                                             | Content                   |
| ---------------------------------------------------------------- | ------------------------- |
| `PRD/master-index.md`                                          | 203 requirements overview |
| `PRD/breakdown/06-technical-architecture/requirements.md`      | 18 tech requirements      |
| `PRD/breakdown/06-technical-architecture/questions-answers.md` | Scalability review        |
| `PRD/breakdown/07-database-schema/requirements.md`             | 22 tables, indexes        |
| `PRD/breakdown/08-api-specifications/requirements.md`          | 40+ endpoints             |

### BMAD Workflow Files

| Step   | File Location                                                                         |
| ------ | ------------------------------------------------------------------------------------- |
| Step 5 | `_bmad/bmm/workflows/3-solutioning/create-architecture/steps/step-05-patterns.md`   |
| Step 6 | `_bmad/bmm/workflows/3-solutioning/create-architecture/steps/step-06-structure.md`  |
| Step 7 | `_bmad/bmm/workflows/3-solutioning/create-architecture/steps/step-07-validation.md` |
| Step 8 | `_bmad/bmm/workflows/3-solutioning/create-architecture/steps/step-08-complete.md`   |

---

## Technology Stack Summary

### Confirmed Stack

| Layer              | Technology     | Version        |
| ------------------ | -------------- | -------------- |
| Mobile             | Flutter        | 3.x            |
| Backend            | FastAPI        | Latest         |
| Language (Backend) | Python         | 3.11+          |
| Database           | PostgreSQL     | 15.x (AWS RDS) |
| Cache              | Redis          | 7.x (Cluster)  |
| ORM                | SQLAlchemy     | 2.0 (async)    |
| State Management   | Bloc/Cubit     | ^8.x           |
| Navigation         | GoRouter       | ^13.x          |
| Real-time          | Agora SDK      | ^6.x           |
| Payments           | Razorpay       | Latest         |
| Auth               | Firebase Auth  | Latest         |
| Push               | Firebase FCM   | Latest         |
| Cloud              | AWS            | Mumbai region  |
| IaC                | Terraform      | Latest         |
| CI/CD              | GitHub Actions | -              |

### External Services

| Service      | Purpose            | Criticality |
| ------------ | ------------------ | ----------- |
| Agora        | Voice/video calls  | CRITICAL    |
| Razorpay     | Payments & payouts | CRITICAL    |
| Firebase     | Auth + Push        | CRITICAL    |
| Google/Azure | AI moderation      | HIGH        |
| Karza        | KYC verification   | HIGH        |

---

## Remaining Workflow Steps

### Step 5: Implementation Patterns (Current)

- Confirm naming conventions
- Confirm event/state patterns
- Confirm error handling patterns
- Append to architecture.md

### Step 6: Project Structure

- Define complete folder structure
- File organization patterns
- Module boundaries

### Step 7: Validation

- Review architecture completeness
- Cross-reference with PRD requirements
- Identify gaps

### Step 8: Complete

- Finalize document
- Generate summary
- Archive workflow state

---

## Quick Reference: Confirmed Decisions

### Caching TTLs

- User Profiles: 1 hour
- Creator Status: 30 seconds
- Gift Catalog: 24 hours
- Rate Limiting: 1 minute
- JWT Refresh: 30 days

### JWT Config

- Algorithm: RS256
- Access: 1 hour
- Refresh: 30 days
- Storage: Flutter Secure Storage / Redis

### RBAC Roles

- `user` - Default consumer
- `creator` - Extends user with earnings/payouts
- `admin` - Full system access

### API Response Format

```json
{"success": true, "data": {}, "meta": {}}
{"success": false, "error": {"code": "", "message": "", "details": {}}}
```

### Real-time Stack

- Calls: Agora RTC
- Signaling: Agora RTM + FCM
- Status: Redis + API polling (30s)

---

## Session Notes

- **Date:** 2026-02-03
- **Session Type:** Architecture workflow continuation
- **User:** Nandhu
- **Paused At:** Step 5 - Implementation Patterns proposed, awaiting confirmation
- **Next Action:** Confirm patterns → Save to doc → Proceed to Step 6

---

**Ready to continue? Use the resume commands above!**

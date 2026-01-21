# Requirements: API Specifications

**Section**: 08-api-specifications
**Source Lines**: 1140-1927
**Generated**: 2026-01-20
**Status**: Complete

**Note**: APIs implemented in Node.js Edge Functions on Supabase, not FastAPI as shown in PRD code samples.

---

## Authentication API Requirements

### REQ-API-001: API Base Structure
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: All APIs must follow RESTful conventions with /api/v1 prefix, proper HTTP methods, and consistent JSON response format.

**Rationale**: Consistent API structure enables easier development, testing, and documentation.

**Acceptance Criteria**:
- All endpoints under /api/v1/* prefix
- Use proper HTTP methods (GET, POST, PUT, DELETE)
- JSON request/response format
- Consistent error response structure: `{error: string, code: string, details?: object}`
- HTTP status codes used correctly (200, 201, 400, 401, 403, 404, 500)
- CORS configured for mobile app origins

**Dependencies**: REQ-TA-010
**Related Design Decisions**: DD-API-001, DD-TA-003

**Notes**: Supabase PostgREST auto-generates standard REST endpoints.

---

### REQ-API-002: Send OTP Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/auth/send-otp must validate phone format, apply rate limiting, and trigger Firebase OTP.

**Rationale**: OTP is the primary authentication method; must be secure and rate-limited.

**Acceptance Criteria**:
- Accepts phone in +91XXXXXXXXXX format
- Validates Indian phone number (10 digits after +91)
- Rate limit: max 5 OTP requests per phone per hour
- Returns success message with phone masked
- Integrates with Firebase Auth phone provider
- Logs OTP request for debugging (not OTP value)

**Dependencies**: REQ-TA-008
**Related Design Decisions**: DD-FS-002

**Notes**: Firebase handles actual OTP delivery; endpoint is a proxy.

---

### REQ-API-003: Verify OTP Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/auth/verify-otp must verify Firebase token, create/update user, and return Supabase JWT tokens.

**Rationale**: This endpoint bridges Firebase OTP auth with Supabase session management.

**Acceptance Criteria**:
- Accepts phone, OTP, and Firebase ID token
- Verifies Firebase token authenticity
- Creates new user if not exists (with coin wallet)
- Returns access_token, refresh_token, user_id, is_new_user flag
- Access token valid for 1 hour
- Refresh token valid for 7 days
- Supabase session created for RLS access

**Dependencies**: REQ-API-002, REQ-DB-001
**Related Design Decisions**: DD-API-003

**Notes**: New users need to complete profile setup after first login.

---

### REQ-API-004: Refresh Token Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/auth/refresh-token must accept valid refresh token and return new access token.

**Rationale**: Enables seamless session continuation without re-authentication.

**Acceptance Criteria**:
- Accepts refresh_token in request body
- Validates refresh token (not expired, not revoked)
- Returns new access_token
- Implements refresh token rotation (issue new refresh token)
- Revoked tokens return 401 error

**Dependencies**: REQ-API-003
**Related Design Decisions**: DD-API-003

**Notes**: Use Supabase built-in refresh mechanism.

---

### REQ-API-005: Logout Endpoint
**Priority**: P1 (High)
**Type**: Technical

**Description**: POST /api/v1/auth/logout must invalidate current session and tokens.

**Rationale**: Users must be able to securely sign out of the application.

**Acceptance Criteria**:
- Requires valid access token
- Invalidates current session
- Adds tokens to blacklist (Redis)
- Returns success confirmation
- Clears any cached user data

**Dependencies**: REQ-API-003, REQ-TA-005
**Related Design Decisions**: None

**Notes**: Consider invalidating all sessions for security-sensitive logout.

---

## Wallet API Requirements

### REQ-API-006: Get Balance Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: GET /api/v1/wallet/balance must return user's coin and diamond balances.

**Rationale**: Users and creators need to see their current balances.

**Acceptance Criteria**:
- Requires authentication
- Returns coin wallet: balance, total_purchased, total_spent
- Returns diamond wallet: available, pending, total_earned
- Returns 0 values if wallet doesn't exist
- Fast response (<200ms)

**Dependencies**: REQ-DB-004, REQ-DB-005
**Related Design Decisions**: DD-DB-002

**Notes**: Use Supabase RLS to ensure users only see own balance.

---

### REQ-API-007: Initiate Recharge Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/wallet/recharge/initiate must create Razorpay order for coin purchase.

**Rationale**: Users need to purchase coins to use paid features.

**Acceptance Criteria**:
- Accepts package_id
- Validates package exists and is active
- Creates Razorpay order with amount in paise
- Includes user_id, package_id, coins in order notes
- Returns order_id, amount, currency, coins, razorpay_key
- Order valid for 15 minutes

**Dependencies**: REQ-DB-018, REQ-TA-007
**Related Design Decisions**: DD-FS-006

**Notes**: Implement as Edge Function for secure Razorpay key handling.

---

### REQ-API-008: Verify Recharge Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/wallet/recharge/verify must verify Razorpay payment and credit coins.

**Rationale**: Ensures coins are only credited for successful payments.

**Acceptance Criteria**:
- Accepts razorpay_order_id, payment_id, signature
- Verifies Razorpay signature
- Prevents double-crediting (idempotent)
- Credits coins + bonus to wallet
- Creates 'recharge' transaction record
- Returns success, coins_added, new_balance

**Dependencies**: REQ-API-007, REQ-DB-004
**Related Design Decisions**: DD-API-004

**Notes**: Also implement as webhook handler for reliability.

---

### REQ-API-009: Transaction History Endpoint
**Priority**: P1 (High)
**Type**: Technical

**Description**: GET /api/v1/wallet/transactions must return paginated transaction history.

**Rationale**: Users need to view their spending and earning history.

**Acceptance Criteria**:
- Requires authentication
- Accepts optional type filter (recharge, spend_call, earn_gift, etc.)
- Accepts limit (default 20, max 100) and offset pagination
- Returns transactions sorted by created_at desc
- Each transaction includes: id, type, amount_inr, coins, diamonds, status, created_at
- RLS ensures users only see own transactions

**Dependencies**: REQ-DB-006
**Related Design Decisions**: None

**Notes**: Consider cursor-based pagination for large histories.

---

## Call API Requirements

### REQ-API-010: Start Call Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/calls/start must validate, create call record, generate Agora tokens, and notify receiver.

**Rationale**: Core functionality for initiating paid voice/video calls.

**Acceptance Criteria**:
- Accepts receiver_id, call_type (audio/video)
- Validates receiver exists and is active
- Checks not blocked by either party
- Gets dynamic call rate based on receiver type and time
- Checks caller balance >= rate * 3 (minimum 3 minutes)
- Creates call record with status 'initiated'
- Generates Agora tokens for caller and receiver
- Sends push notification to receiver with call data
- Returns call_id, channel, agora_token, rate_per_minute, balance, estimated_minutes

**Dependencies**: REQ-DB-007, REQ-DB-019, REQ-TA-006
**Related Design Decisions**: DD-API-002, DD-API-005

**Notes**: Receiver has 30 seconds to answer before call times out.

---

### REQ-API-011: Answer Call Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/calls/answer/{call_id} must update call status and return Agora credentials.

**Rationale**: Receivers need to accept incoming calls.

**Acceptance Criteria**:
- Accepts call_id in path
- Validates call exists and belongs to current user (as receiver)
- Validates call status is 'initiated' or 'ringing'
- Updates status to 'ongoing', sets started_at timestamp
- Generates/returns Agora token for receiver
- Returns call_id, channel, agora_token, agora_app_id

**Dependencies**: REQ-API-010
**Related Design Decisions**: DD-API-005

**Notes**: Call duration starts from this moment.

---

### REQ-API-012: End Call Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/calls/end must calculate billing, process payments, and close call.

**Rationale**: Calls must be properly billed and recorded.

**Acceptance Criteria**:
- Accepts call_id, end_reason (normal, no_answer, declined, error)
- Validates call exists and user is participant
- If already completed, returns existing data
- Calculates duration (ended_at - started_at)
- If duration > 0: calculates coins (round up to minute), deducts from caller, credits diamonds to receiver
- Creates spend_call and earn_call transaction records
- Updates call status to 'completed'
- Returns call_id, duration_seconds, duration_display, coins_spent, diamonds_earned

**Dependencies**: REQ-API-010, REQ-DB-004, REQ-DB-005
**Related Design Decisions**: DD-API-002

**Notes**: Handle concurrent end requests gracefully (idempotent).

---

### REQ-API-013: Call Rates Endpoint
**Priority**: P1 (High)
**Type**: Technical

**Description**: GET /api/v1/calls/rates must return current call rates with peak hour status.

**Rationale**: Users need to see call costs before initiating.

**Acceptance Criteria**:
- Public endpoint (no auth required)
- Returns is_peak_hour boolean
- Returns rates array with user_type, call_type, base_rate, current_rate
- Current rate applies peak multiplier if peak hours (22:00-02:00 IST)
- Cacheable response (5 minute TTL)

**Dependencies**: REQ-DB-019
**Related Design Decisions**: None

**Notes**: Peak hours defined in database, configurable.

---

### REQ-API-014: Call History Endpoint
**Priority**: P1 (High)
**Type**: Technical

**Description**: GET /api/v1/calls/history must return user's call history with pagination.

**Rationale**: Users need to review past calls and spending.

**Acceptance Criteria**:
- Requires authentication
- Returns calls where user is caller OR receiver
- Accepts limit (default 20) and offset
- Each call includes: id, type (incoming/outgoing), call_type, other_user summary, duration, coins_spent/diamonds_earned, status, created_at
- Sorted by created_at desc

**Dependencies**: REQ-DB-007
**Related Design Decisions**: None

**Notes**: Use RLS to filter to user's calls only.

---

## Creator/Withdrawal API Requirements

### REQ-API-015: Request Withdrawal Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/wallet/withdraw must validate, calculate TDS, and create withdrawal request.

**Rationale**: Creators need to withdraw their earnings.

**Acceptance Criteria**:
- Requires authentication as creator
- Accepts amount, payout_method (bank_transfer, upi)
- Validates minimum withdrawal (500)
- Validates KYC status is 'verified'
- Validates available_balance >= amount
- Calculates TDS (10% if yearly earnings > 30000)
- Creates withdrawal record with pending status
- Deducts from available_balance
- Returns withdrawal_id, amount, tds_deducted, net_amount, status

**Dependencies**: REQ-DB-005, REQ-DB-011, REQ-DB-013
**Related Design Decisions**: None

**Notes**: Actual payout processed by background job.

---

### REQ-API-016: Get Packages Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: GET /api/v1/wallet/packages must return active coin packages.

**Rationale**: Users need to see available coin purchase options.

**Acceptance Criteria**:
- Public endpoint (no auth required)
- Returns active packages sorted by sort_order
- Each package: id, name, price, coins, bonus, total_coins, is_popular
- Cacheable response (1 hour TTL)

**Dependencies**: REQ-DB-018
**Related Design Decisions**: None

**Notes**: Supabase PostgREST can handle this with proper RLS.

---

## Gift API Requirements

### REQ-API-017: Get Gifts Catalog Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: GET /api/v1/gifts must return active virtual gifts catalog.

**Rationale**: Users need to browse available gifts to send.

**Acceptance Criteria**:
- Public endpoint (no auth required)
- Returns active gifts sorted by category, sort_order
- Each gift: id, name, name_tamil, icon_url, animation_url, coin_price, category
- Cacheable response (1 hour TTL)

**Dependencies**: REQ-DB-009
**Related Design Decisions**: None

**Notes**: Supabase PostgREST handles this automatically.

---

### REQ-API-018: Send Gift Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/gifts/send must process gift transaction between users.

**Rationale**: Gift sending is core monetization feature.

**Acceptance Criteria**:
- Requires authentication
- Accepts receiver_id, gift_id, quantity, context_type, context_id
- Validates gift exists and is active
- Validates sender has sufficient coin balance
- Calculates total cost (coin_price * quantity)
- Deducts coins from sender
- Credits diamonds to receiver
- Creates gift_transaction record
- Creates spend_gift and earn_gift transaction records
- Returns success, gift_name, coins_spent, receiver notification sent

**Dependencies**: REQ-DB-009, REQ-DB-010, REQ-DB-004, REQ-DB-005
**Related Design Decisions**: None

**Notes**: Trigger real-time notification/animation to receiver.

---

## User API Requirements

### REQ-API-019: Get User Profile Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: GET /api/v1/users/{id} must return public user profile information.

**Rationale**: Users need to view profiles before calling or following.

**Acceptance Criteria**:
- Accepts user_id in path
- Returns: display_name, profile_photo, bio, is_creator, is_verified, is_vip
- Includes interests/tags
- Includes online status (if recently online)
- Does NOT include: phone, location details, wallet info
- 404 if user not found or inactive

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: Use Supabase PostgREST with column filtering.

---

### REQ-API-020: Update My Profile Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: PUT /api/v1/users/me must update authenticated user's profile.

**Rationale**: Users need to customize their profiles.

**Acceptance Criteria**:
- Requires authentication
- Accepts: display_name, bio, profile_photo, language, interests
- Validates display_name length (3-50 chars)
- Validates bio length (max 150 chars)
- Updates updated_at timestamp
- Returns updated profile data
- Cannot update: phone, is_creator, is_verified, is_vip (admin only)

**Dependencies**: REQ-DB-001
**Related Design Decisions**: None

**Notes**: Profile photo should be a Supabase Storage URL.

---

## Room API Requirements

### REQ-API-021: Create Room Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/rooms must create a new live room.

**Rationale**: Creators need to host live rooms for audience engagement.

**Acceptance Criteria**:
- Requires authentication as creator
- Accepts: title, description, tags, is_private, password (if private)
- Validates title length (5-100 chars)
- Validates max 5 tags
- Creates room record with status 'live'
- Generates Agora channel for room
- Creates room_participant record for host
- Returns room_id, channel, agora_token

**Dependencies**: REQ-DB-008, REQ-TA-006
**Related Design Decisions**: DD-FS-004, DD-DB-005

**Notes**: Host automatically joins as 'host' role.

---

### REQ-API-022: Join Room Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: POST /api/v1/rooms/{id}/join must add user as participant and return Agora credentials.

**Rationale**: Users need to join live rooms to watch and interact.

**Acceptance Criteria**:
- Accepts room_id in path
- Accepts optional password for private rooms
- Validates room exists and is live
- Validates password if room is private
- Creates room_participant record with 'audience' role
- Increments current_viewers count
- Generates Agora token with subscriber privilege
- Returns room details, channel, agora_token

**Dependencies**: REQ-API-021, REQ-DB-008
**Related Design Decisions**: DD-FS-004

**Notes**: Token privilege determines if user can speak.

---

### REQ-API-023: Leave Room Endpoint
**Priority**: P1 (High)
**Type**: Technical

**Description**: POST /api/v1/rooms/{id}/leave must remove user from room participants.

**Rationale**: Proper cleanup when users leave rooms.

**Acceptance Criteria**:
- Accepts room_id in path
- Updates room_participant left_at timestamp
- Decrements current_viewers count
- If host leaves, room status changes to 'ended'
- Returns success confirmation

**Dependencies**: REQ-API-022
**Related Design Decisions**: None

**Notes**: Handle abrupt disconnects via Agora callbacks.

---

### REQ-API-024: Get Live Rooms Endpoint
**Priority**: P0 (Critical)
**Type**: Technical

**Description**: GET /api/v1/rooms must return currently live rooms.

**Rationale**: Users need to discover active rooms to join.

**Acceptance Criteria**:
- Public endpoint (can filter by auth status)
- Returns rooms with status 'live'
- Sorted by current_viewers desc (popular first)
- Each room: id, title, tags, host summary, current_viewers, is_private
- Excludes private rooms for non-VIP users
- Pagination with limit/offset

**Dependencies**: REQ-DB-008
**Related Design Decisions**: None

**Notes**: Cache briefly (30 seconds) for performance.

---

## Moderation API Requirements

### REQ-API-025: Report User Endpoint
**Priority**: P1 (High)
**Type**: Technical

**Description**: POST /api/v1/reports must create user report for moderation review.

**Rationale**: User safety requires ability to report violations.

**Acceptance Criteria**:
- Requires authentication
- Accepts: reported_user_id, reason, description, context_type, context_id
- Validates reported user exists
- Cannot report self
- Creates report record with 'pending' status
- Returns report_id, confirmation message
- Rate limit: max 10 reports per user per day

**Dependencies**: REQ-DB-016
**Related Design Decisions**: None

**Notes**: Reports queued for moderation dashboard review.

---

### REQ-API-026: Block User Endpoint
**Priority**: P1 (High)
**Type**: Technical

**Description**: POST /api/v1/users/{id}/block must block a user from contacting current user.

**Rationale**: Users need control over who can contact them.

**Acceptance Criteria**:
- Requires authentication
- Accepts user_id in path
- Cannot block self
- Creates user_blocks record
- Bidirectional: blocked user also cannot contact blocker
- Returns success confirmation
- Duplicate block requests are idempotent (no error)

**Dependencies**: REQ-DB-017
**Related Design Decisions**: None

**Notes**: Block affects calls, gifts, room invites.

---

### REQ-API-027: Unblock User Endpoint
**Priority**: P1 (High)
**Type**: Technical

**Description**: DELETE /api/v1/users/{id}/block must remove block on a user.

**Rationale**: Users may want to reverse blocks.

**Acceptance Criteria**:
- Requires authentication
- Accepts user_id in path
- Deletes user_blocks record if exists
- Returns success confirmation
- No error if not blocked

**Dependencies**: REQ-API-026
**Related Design Decisions**: None

**Notes**: Unblocking is unilateral (other user may still have block).

---

## Summary

| ID | Requirement | Priority | Type |
|----|-------------|----------|------|
| REQ-API-001 | API Base Structure | P0 | Technical |
| REQ-API-002 | Send OTP Endpoint | P0 | Technical |
| REQ-API-003 | Verify OTP Endpoint | P0 | Technical |
| REQ-API-004 | Refresh Token Endpoint | P0 | Technical |
| REQ-API-005 | Logout Endpoint | P1 | Technical |
| REQ-API-006 | Get Balance Endpoint | P0 | Technical |
| REQ-API-007 | Initiate Recharge Endpoint | P0 | Technical |
| REQ-API-008 | Verify Recharge Endpoint | P0 | Technical |
| REQ-API-009 | Transaction History Endpoint | P1 | Technical |
| REQ-API-010 | Start Call Endpoint | P0 | Technical |
| REQ-API-011 | Answer Call Endpoint | P0 | Technical |
| REQ-API-012 | End Call Endpoint | P0 | Technical |
| REQ-API-013 | Call Rates Endpoint | P1 | Technical |
| REQ-API-014 | Call History Endpoint | P1 | Technical |
| REQ-API-015 | Request Withdrawal Endpoint | P0 | Technical |
| REQ-API-016 | Get Packages Endpoint | P0 | Technical |
| REQ-API-017 | Get Gifts Catalog Endpoint | P0 | Technical |
| REQ-API-018 | Send Gift Endpoint | P0 | Technical |
| REQ-API-019 | Get User Profile Endpoint | P0 | Technical |
| REQ-API-020 | Update My Profile Endpoint | P0 | Technical |
| REQ-API-021 | Create Room Endpoint | P0 | Technical |
| REQ-API-022 | Join Room Endpoint | P0 | Technical |
| REQ-API-023 | Leave Room Endpoint | P1 | Technical |
| REQ-API-024 | Get Live Rooms Endpoint | P0 | Technical |
| REQ-API-025 | Report User Endpoint | P1 | Technical |
| REQ-API-026 | Block User Endpoint | P1 | Technical |
| REQ-API-027 | Unblock User Endpoint | P1 | Technical |

**Total Requirements**: 27
**P0 (Critical)**: 19
**P1 (High)**: 8

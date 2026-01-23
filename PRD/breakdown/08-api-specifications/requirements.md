# Section 8: API Specifications - Requirements

**Section:** API Specifications
**Total Requirements:** 20
**Priority Breakdown:** P0: 12 | P1: 6 | P2: 2
**Last Updated:** 2026-01-20

**CRITICAL FIX:** Revenue share calculation corrected from 45% (PRD error) to 75-85% tiered model (REQ-RM-001)

---

## REQ-API-001: Comprehensive API Endpoint Coverage (30-40 Endpoints)

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement comprehensive RESTful API coverage for all 22 database tables across 12 endpoint categories, totaling 30-40 endpoints vs. PRD's 12 endpoints.

**Specifications:**

**Endpoint Categories (12 total):**

**1. Authentication APIs** (4 endpoints) - Existing in PRD
- POST /api/v1/auth/send-otp
- POST /api/v1/auth/verify-otp
- POST /api/v1/auth/refresh-token
- POST /api/v1/auth/logout

**2. Wallet APIs** (5 endpoints) - Existing in PRD
- GET /api/v1/wallet/balance
- GET /api/v1/wallet/packages
- POST /api/v1/wallet/recharge/initiate
- POST /api/v1/wallet/recharge/verify
- GET /api/v1/wallet/transactions

**3. Calls APIs** (5 endpoints) - Existing in PRD
- GET /api/v1/calls/rates
- POST /api/v1/calls/start
- POST /api/v1/calls/answer/{call_id}
- POST /api/v1/calls/end
- GET /api/v1/calls/history

**4. Users/Profiles APIs** (6 endpoints) - NEW
- GET /api/v1/users/me (get own profile)
- PUT /api/v1/users/me (update profile: display_name, bio, language)
- POST /api/v1/users/me/photo (upload profile photo to S3)
- GET /api/v1/users/{user_id} (get other user profile, public fields only)
- GET /api/v1/users/search (search by name, interests, location)
- POST /api/v1/users/interests (add/update user interests)

**5. Gifts APIs** (4 endpoints) - NEW
- GET /api/v1/gifts (catalog with i18n names, filter by category)
- POST /api/v1/gifts/send (send gift to user)
- GET /api/v1/gifts/received (gifts received by user)
- GET /api/v1/gifts/sent (gifts sent by user)

**6. Live Rooms APIs** (6 endpoints) - NEW
- GET /api/v1/rooms/live (discover live rooms, filter by tags)
- POST /api/v1/rooms (create new room)
- GET /api/v1/rooms/{room_id} (get room details)
- POST /api/v1/rooms/{room_id}/join (join room as audience/speaker)
- POST /api/v1/rooms/{room_id}/leave (leave room)
- POST /api/v1/rooms/{room_id}/gifts (send gift in room)

**7. Creator Dashboard APIs** (5 endpoints) - NEW
- GET /api/v1/creator/dashboard (earnings overview, stats)
- GET /api/v1/creator/earnings (detailed earnings breakdown by source)
- GET /api/v1/creator/rates (get custom call rates from creator_call_rates table)
- PUT /api/v1/creator/rates (set custom audio/video rates ₹8-25/min)
- POST /api/v1/creator/withdraw (request withdrawal with TDS calculation)

**8. Matching APIs** (2 endpoints) - NEW (Section 7)
- GET /api/v1/matching/preferences (get matching preferences)
- PUT /api/v1/matching/preferences (update age range, location, interests)

**9. Referrals APIs** (3 endpoints) - NEW (Section 7)
- GET /api/v1/referrals (referral history, rewards earned)
- POST /api/v1/referrals/code (generate referral code)
- POST /api/v1/referrals/apply (apply referral code, credit rewards)

**10. Verification APIs** (3 endpoints) - NEW (Section 7)
- GET /api/v1/verification/status (current verification tier, status)
- POST /api/v1/verification/upload (upload selfie/ID for verification)
- GET /api/v1/verification/tiers (get verification tier benefits)

**11. Notifications APIs** (2 endpoints) - NEW (Section 7)
- GET /api/v1/notifications (notification feed with pagination)
- PUT /api/v1/notifications/{notification_id}/mark-read (mark as read)

**12. Admin APIs** (4 endpoints) - NEW
- GET /api/v1/admin/users (user management, search, filter)
- GET /api/v1/admin/moderation (ai_moderation_transcripts, flagged content)
- POST /api/v1/admin/users/{user_id}/suspend (suspend user account)
- GET /api/v1/admin/reports (user reports, pending review)

**Total: 40+ endpoints**

**API Standards:**
- RESTful conventions (GET, POST, PUT, DELETE)
- JSON request/response format
- HTTPS only (TLS 1.3)
- /api/v1 URL prefix (versioned)
- Standardized error responses (REQ-API-006)
- Pagination on all list endpoints (REQ-API-010)

**Rationale:**
Comprehensive API coverage enables full-featured mobile app development without backend gaps. All 22 tables from Section 7 accessible via REST APIs.

**Acceptance Criteria:**
- [ ] All 40+ endpoints implemented in FastAPI
- [ ] Request/response schemas defined with Pydantic models
- [ ] All endpoints tested with unit and integration tests
- [ ] OpenAPI schema generated and validated
- [ ] Postman collection created for all endpoints
- [ ] Frontend team can develop mobile app with complete API coverage

**Dependencies:**
- REQ-DB-002 (Core Table Structures from Section 7)
- REQ-TA-006 (API Architecture from Section 6)
- REQ-API-002 (JWT Authentication)
- REQ-API-006 (Error Handling)

**Design Decisions:**
- **DD-API-001**: 40+ comprehensive endpoints chosen over minimal 15 for complete feature coverage
- **DD-API-002**: RESTful conventions chosen for industry-standard API design

**Notes:**
- PRD only shows 12 endpoints (Auth, Wallet, Calls)
- This requirement adds 28+ endpoints for complete coverage
- Timeline: +2-3 weeks for comprehensive API development

**Testing Requirements:**
- Unit tests for all endpoints (>90% coverage)
- Integration tests for API flows (signup → recharge → call → withdraw)
- Load testing for high-traffic endpoints (calls/start, gifts/send)
- API contract testing (request/response schema validation)

---

## REQ-API-002: Dynamic Tiered Revenue Calculation (CRITICAL FIX)

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
**CRITICAL FIX:** Correct PRD's 45% creator share error to implement approved 75-85% tiered revenue model based on creator_performance table.

**PRD ERROR (Line 1846):**
```python
CREATOR_SHARE = Decimal("0.45")  # 45% - INCORRECT
diamonds_earned = Decimal(str(coins_spent)) * COIN_TO_INR * CREATOR_SHARE
```

**Approved Model (REQ-RM-001, REQ-ES-004):**
- **Tier 1**: 75% revenue share (default, <₹50K monthly earnings)
- **Tier 2**: 80% revenue share (₹50K-200K monthly, top 20%)
- **Tier 3**: 85% revenue share (₹200K+ monthly, top 5%)

**Specifications:**

**Implementation:**
```python
def calculate_creator_earnings(
    creator_id: UUID,
    coins_spent: Decimal,
    db: Session
) -> Tuple[Decimal, Decimal, Decimal]:
    """
    Calculate creator earnings using dynamic tiered revenue share

    Args:
        creator_id: Creator's user_id
        coins_spent: Coins spent by caller/sender
        db: Database session

    Returns:
        Tuple of (creator_earnings, platform_earnings, tier_percent)
    """
    # Constants
    COIN_TO_INR = Decimal("1.0")  # 1 coin = ₹1

    # Query creator performance from database
    performance = db.query(CreatorPerformance).filter(
        CreatorPerformance.user_id == creator_id
    ).first()

    # Determine tier percentage based on trailing 30-day earnings
    if not performance:
        # New creator, no performance data yet
        tier_percent = Decimal("0.75")  # Default: Tier 1
        tier_name = "tier_1_75"
    elif performance.total_earnings_30d >= Decimal("200000.00"):
        # Top 5% performers (₹2L+ per month)
        tier_percent = Decimal("0.85")
        tier_name = "tier_3_85"
    elif performance.total_earnings_30d >= Decimal("50000.00"):
        # Top 20% performers (₹50K-200K per month)
        tier_percent = Decimal("0.80")
        tier_name = "tier_2_80"
    else:
        # All others (<₹50K per month)
        tier_percent = Decimal("0.75")
        tier_name = "tier_1_75"

    # Calculate earnings
    gross_amount = coins_spent * COIN_TO_INR
    creator_earnings = gross_amount * tier_percent
    platform_earnings = gross_amount - creator_earnings

    # Log tier used (for analytics)
    logger.info(
        f"Revenue calculation: creator={creator_id}, "
        f"coins={coins_spent}, tier={tier_name}, "
        f"creator_earnings={creator_earnings}, "
        f"platform_earnings={platform_earnings}"
    )

    return creator_earnings, platform_earnings, tier_percent
```

**Usage in Call Endpoint (REQ-API-001):**
```python
@router.post("/api/v1/calls/end")
async def end_call(request: EndCallRequest, user_id: str, db: Session):
    call = db.query(Call).filter(Call.id == request.call_id).first()

    # Calculate billing
    if call.duration_seconds > 0:
        minutes = -(-call.duration_seconds // 60)  # Round up
        coins_spent = Decimal(str(minutes * call.rate_per_minute))

        # Use dynamic tiered revenue calculation (FIXED)
        creator_earnings, platform_earnings, tier_percent = \
            calculate_creator_earnings(call.receiver_id, coins_spent, db)

        # Deduct from caller
        caller_wallet = db.query(CoinWallet).filter(
            CoinWallet.user_id == call.caller_id
        ).first()
        caller_wallet.balance -= int(coins_spent)
        caller_wallet.total_spent += int(coins_spent)

        # Credit to receiver
        receiver_wallet = db.query(DiamondWallet).filter(
            DiamondWallet.user_id == call.receiver_id
        ).first()
        if not receiver_wallet:
            receiver_wallet = DiamondWallet(user_id=call.receiver_id)
            db.add(receiver_wallet)

        receiver_wallet.pending_balance += creator_earnings
        receiver_wallet.total_earned += creator_earnings

        call.coins_spent = int(coins_spent)
        call.diamonds_earned = creator_earnings

        # ... create transactions ...
```

**Affected Endpoints:**
- POST /api/v1/calls/end (call billing)
- POST /api/v1/gifts/send (gift revenue)
- POST /api/v1/rooms/{room_id}/gifts (room gift revenue)

**Performance Consideration:**
- Database lookup per transaction (creator_performance table)
- Query optimized with REQ-DB-003 indexes
- Acceptable latency: <5ms per lookup
- Alternative: Redis cache with daily N8N update (deferred to optimization phase)

**Rationale:**
**CRITICAL FIX** for financial accuracy. PRD's 45% would cause massive revenue loss for creators (should be 75-85%). This is the most critical bug fix in the entire API specification.

**Acceptance Criteria:**
- [ ] calculate_creator_earnings() function implemented
- [ ] All revenue calculation endpoints use dynamic tiers
- [ ] creator_performance table queried correctly
- [ ] Tier thresholds validated (75% default, 80% at ₹50K, 85% at ₹200K)
- [ ] Unit tests verify correct tier assignment
- [ ] Integration tests validate end-to-end revenue flow
- [ ] Revenue calculations match REQ-RM-001 exactly

**Dependencies:**
- REQ-RM-001 (Revenue Model - 75-85% tiered share)
- REQ-ES-004 (Creator Revenue Tiers)
- REQ-DB-004 (creator_performance table)

**Design Decisions:**
- **DD-API-003**: Database lookup chosen over Redis cache for MVP accuracy
- **DD-API-004**: Tier thresholds set at ₹50K and ₹200K based on market analysis

**Notes:**
- This fixes the single most critical error in the PRD
- Creators would lose 30-40% revenue with 45% share vs. approved 75-85%
- Must be implemented correctly from MVP launch

**Testing Requirements:**
- Unit test: New creator (no performance) → 75% tier
- Unit test: ₹60K monthly creator → 80% tier
- Unit test: ₹250K monthly creator → 85% tier
- Integration test: Full call flow with tier-based revenue split
- Edge case: Creator crosses tier threshold during month

---

## REQ-API-003: Accept-Language Header i18n Support

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement Accept-Language header parsing for multi-language API responses across 5 languages (Tamil, Telugu, Kannada, Hindi, English) using JSONB content from Section 7.

**Specifications:**

**Language Parsing Middleware:**
```python
from fastapi import Request

async def parse_language(request: Request) -> str:
    """
    Parse Accept-Language header, return supported language or default to English

    Supported: ta, te, kn, hi, en

    Examples:
    - "ta-IN" → "ta"
    - "en-US,ta;q=0.9" → "en" (primary language)
    - "fr-FR" → "en" (unsupported, fallback)
    - None → "en" (default)
    """
    accept_lang = request.headers.get("Accept-Language", "en")

    # Extract primary language code
    lang = accept_lang.split(",")[0].split("-")[0][:2].lower()

    # Validate against supported languages
    SUPPORTED_LANGUAGES = ["ta", "te", "kn", "hi", "en"]
    if lang not in SUPPORTED_LANGUAGES:
        return "en"

    return lang
```

**i18n Response Implementation:**
```python
@router.get("/api/v1/gifts")
async def get_gifts(
    request: Request,
    category: str = None,
    db: Session = Depends(get_db)
):
    """
    Get gift catalog with localized names and descriptions

    Headers:
        Accept-Language: ta (Tamil), te (Telugu), kn (Kannada), hi (Hindi), en (English)

    Returns:
        Gift catalog with names/descriptions in requested language
    """
    # Parse language from header
    lang = await parse_language(request)

    # Query gifts
    query = db.query(Gift).filter(Gift.is_active == True)
    if category:
        query = query.filter(Gift.category == category)

    gifts = query.order_by(Gift.sort_order).all()

    # Return with localized content (JSONB lookup)
    return {
        "data": [
            {
                "id": str(g.id),
                "name": g.names.get(lang, g.names.get("en")),  # Fallback to English
                "description": g.descriptions.get(lang, g.descriptions.get("en")),
                "icon_url": g.icon_url,
                "coin_price": float(g.coin_price),
                "diamond_value": float(g.diamond_value),
                "category": g.category
            }
            for g in gifts
        ],
        "language": lang  # Confirm language used
    }
```

**Endpoints Requiring i18n:**
1. GET /api/v1/gifts (gift names, descriptions)
2. GET /api/v1/wallet/packages (coin package names, descriptions)
3. GET /api/v1/subscriptions/plans (plan names, feature descriptions)
4. All error responses (via system_messages table - REQ-API-006)

**Request Example:**
```http
GET /api/v1/gifts HTTP/1.1
Host: api.livvly.com
Accept-Language: ta-IN
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

**Response Example (Tamil):**
```json
{
  "data": [
    {
      "id": "123e4567-e89b-12d3-a456-426614174000",
      "name": "ரோஜா",  // Tamil name from JSONB
      "description": "அன்பின் அடையாளம்",  // Tamil description
      "icon_url": "/gifts/rose.png",
      "coin_price": 10.00,
      "diamond_value": 7.50,
      "category": "basic"
    }
  ],
  "language": "ta"
}
```

**Fallback Behavior:**
- If requested language not in JSONB → fallback to English
- If JSONB field null → return empty string or skip field
- If Accept-Language header missing → default to English

**Rationale:**
Accept-Language header is standard HTTP practice for content negotiation. Enables mobile app to request content in user's preferred language without changing endpoints.

**Acceptance Criteria:**
- [ ] parse_language() middleware implemented
- [ ] All i18n endpoints return localized content based on header
- [ ] JSONB lookups with English fallback working
- [ ] Unsupported languages default to English
- [ ] Missing header defaults to English
- [ ] Response includes "language" field confirming used language
- [ ] Mobile app documentation shows Accept-Language usage

**Dependencies:**
- REQ-DB-005 (JSONB i18n Columns from Section 7)
- REQ-FS-001 (5 Languages at MVP)

**Design Decisions:**
- **DD-API-005**: Accept-Language header chosen over query parameter for REST standards
- **DD-API-006**: English fallback ensures graceful degradation

**Notes:**
- Standard HTTP content negotiation
- Client controls language (mobile app can switch languages)
- Server-side JSONB lookup (~1ms overhead, acceptable)

**Testing Requirements:**
- Test each language (ta, te, kn, hi, en)
- Test unsupported language fallback (fr → en)
- Test missing header (→ en)
- Test JSONB field missing (fallback to en)
- Test malformed header (ignore, default to en)

---

## REQ-API-004: Enhanced OpenAPI Documentation with Examples

**Priority:** P0
**Type:** Documentation Requirement
**Status:** Approved

**Description:**
Generate comprehensive OpenAPI (Swagger) documentation for all 40+ endpoints with detailed examples, error responses, and authentication requirements.

**Specifications:**

**OpenAPI Configuration:**
```python
from fastapi import FastAPI
from fastapi.openapi.utils import get_openapi

app = FastAPI(
    title="LIVVLY Voice Dating API",
    description="RESTful API for LIVVLY voice dating platform with 5-language support",
    version="1.0.0",
    terms_of_service="https://livvly.com/terms",
    contact={
        "name": "LIVVLY API Support",
        "email": "api@livvly.com"
    },
    license_info={
        "name": "Proprietary"
    }
)

def custom_openapi():
    if app.openapi_schema:
        return app.openapi_schema

    openapi_schema = get_openapi(
        title="LIVVLY Voice Dating API",
        version="1.0.0",
        description="Comprehensive API for voice dating platform",
        routes=app.routes,
    )

    # Add security schemes
    openapi_schema["components"]["securitySchemes"] = {
        "BearerAuth": {
            "type": "http",
            "scheme": "bearer",
            "bearerFormat": "JWT"
        }
    }

    app.openapi_schema = openapi_schema
    return app.openapi_schema

app.openapi = custom_openapi
```

**Example Endpoint Documentation:**
```python
@router.post(
    "/api/v1/gifts/send",
    summary="Send gift to user",
    description="""
    Send a virtual gift to another user during a call, in a live room, or from their profile.

    **Flow:**
    1. Validate gift exists and is active
    2. Check sender has sufficient coins
    3. Check receiver exists and is not blocked
    4. Deduct coins from sender wallet
    5. Credit diamonds to receiver wallet (75-85% based on tier)
    6. Create gift transaction record
    7. Send push notification to receiver

    **Revenue Calculation:**
    - Gift price: Based on gift.coin_price
    - Creator tier: Retrieved from creator_performance table
    - Creator earnings: coins × tier_percent (75%, 80%, or 85%)
    - Platform fee: Remaining amount (15-25%)

    **Rate Limits:** 50 gifts per hour per user
    """,
    response_description="Gift transaction details with updated balances",
    responses={
        200: {
            "description": "Gift sent successfully",
            "content": {
                "application/json": {
                    "example": {
                        "transaction_id": "123e4567-e89b-12d3-a456-426614174000",
                        "gift_id": "987fcdeb-51a2-43f1-b9c3-123456789abc",
                        "gift_name": "Rose",
                        "receiver_id": "456e7890-abc1-23f4-d567-890123456def",
                        "receiver_name": "Priya",
                        "coins_spent": 10,
                        "diamonds_earned": 7.50,
                        "creator_tier": "tier_1_75",
                        "new_coin_balance": 490,
                        "created_at": "2026-01-20T10:30:00.000Z"
                    }
                }
            }
        },
        400: {
            "description": "Bad request - insufficient coins, blocked user, or invalid gift",
            "content": {
                "application/json": {
                    "examples": {
                        "insufficient_coins": {
                            "summary": "Insufficient coins",
                            "value": {
                                "error_code": "error_insufficient_coins",
                                "message": "Insufficient coins. You need 10 coins. Current balance: 5",
                                "timestamp": "2026-01-20T10:30:00.000Z"
                            }
                        },
                        "blocked_user": {
                            "summary": "User is blocked",
                            "value": {
                                "error_code": "error_blocked_user",
                                "message": "You cannot send gifts to this user",
                                "timestamp": "2026-01-20T10:30:00.000Z"
                            }
                        }
                    }
                }
            }
        },
        401: {
            "description": "Unauthorized - missing or invalid JWT token"
        },
        404: {
            "description": "Receiver or gift not found"
        },
        429: {
            "description": "Rate limit exceeded - 50 gifts per hour"
        }
    },
    tags=["Gifts"]
)
async def send_gift(
    request: SendGiftRequest,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """Implementation..."""
```

**Documentation Coverage:**
- **All 40+ endpoints** with comprehensive documentation
- **Request schemas**: Pydantic models with field descriptions, types, constraints
- **Response schemas**: Example responses for success and all error cases
- **Authentication**: Security requirements per endpoint
- **Rate limits**: Document rate limits per endpoint
- **i18n**: Show Accept-Language header usage
- **Error codes**: Document all possible error_code values

**Access Points:**
- **Swagger UI**: https://api.livvly.com/docs (interactive API explorer)
- **ReDoc**: https://api.livvly.com/redoc (alternative documentation UI)
- **OpenAPI JSON**: https://api.livvly.com/openapi.json (machine-readable schema)

**Rationale:**
Comprehensive API documentation enables frontend team to develop mobile app independently. OpenAPI standard allows automated client generation (Dart, Swift, Kotlin).

**Acceptance Criteria:**
- [ ] All 40+ endpoints documented with examples
- [ ] All error responses (400, 401, 404, 429, 500) documented
- [ ] Request/response Pydantic models have field descriptions
- [ ] Authentication requirements specified per endpoint
- [ ] Rate limits documented per endpoint
- [ ] i18n usage (Accept-Language header) documented
- [ ] Swagger UI accessible at /docs
- [ ] ReDoc UI accessible at /redoc
- [ ] OpenAPI JSON schema validates against OpenAPI 3.0 spec

**Dependencies:**
- REQ-API-001 (All API Endpoints)
- REQ-API-006 (Error Handling)

**Design Decisions:**
- **DD-API-007**: OpenAPI 3.0 chosen for industry-standard API documentation
- **DD-API-008**: Examples for all endpoints chosen to reduce integration issues

**Notes:**
- FastAPI auto-generates OpenAPI schema from route decorators
- Comprehensive examples reduce frontend integration time
- OpenAPI schema enables automated Dart/Swift/Kotlin client generation

**Testing Requirements:**
- OpenAPI schema validation (valid OpenAPI 3.0 format)
- Swagger UI accessibility testing
- Example request/response accuracy verification

---

## REQ-API-005: JWT Authentication and Authorization

**Priority:** P0
**Type:** Security Requirement
**Status:** Approved

**Description:**
Implement JWT (JSON Web Token) authentication with refresh tokens for secure API access across all protected endpoints.

**Specifications:**

**JWT Token Structure:**
```python
from datetime import datetime, timedelta
from jose import jwt, JWTError
import secrets

# Configuration
SECRET_KEY = secrets.token_urlsafe(32)  # From AWS Secrets Manager
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 60  # 1 hour
REFRESH_TOKEN_EXPIRE_DAYS = 30    # 30 days

def create_access_token(user_id: str, additional_claims: dict = None) -> str:
    """
    Create JWT access token

    Payload:
        user_id: User UUID
        exp: Expiration timestamp (1 hour)
        iat: Issued at timestamp
        type: "access"
        additional_claims: Optional (is_creator, is_admin, etc.)
    """
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)

    payload = {
        "user_id": user_id,
        "exp": expire,
        "iat": datetime.utcnow(),
        "type": "access"
    }

    if additional_claims:
        payload.update(additional_claims)

    token = jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)
    return token

def create_refresh_token(user_id: str) -> str:
    """
    Create JWT refresh token (longer expiration)

    Stored in Redis with user_id as key for token invalidation
    """
    expire = datetime.utcnow() + timedelta(days=REFRESH_TOKEN_EXPIRE_DAYS)

    payload = {
        "user_id": user_id,
        "exp": expire,
        "iat": datetime.utcnow(),
        "type": "refresh"
    }

    token = jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)

    # Store in Redis for logout/invalidation
    redis_client.setex(
        f"refresh_token:{user_id}",
        timedelta(days=REFRESH_TOKEN_EXPIRE_DAYS),
        token
    )

    return token
```

**Authentication Dependency:**
```python
from fastapi import Depends, HTTPException, Header
from typing import Optional

async def get_current_user(
    authorization: Optional[str] = Header(None)
) -> str:
    """
    Extract and validate JWT token from Authorization header

    Header format: "Bearer eyJhbGciOiJIUzI1NiIs..."

    Returns: user_id (UUID string)
    Raises: HTTPException 401 if invalid
    """
    if not authorization:
        raise HTTPException(401, detail="Missing authorization token")

    try:
        # Extract token (remove "Bearer " prefix)
        scheme, token = authorization.split()
        if scheme.lower() != "bearer":
            raise HTTPException(401, detail="Invalid authentication scheme")
    except ValueError:
        raise HTTPException(401, detail="Invalid authorization header format")

    try:
        # Decode and validate token
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        user_id = payload.get("user_id")
        token_type = payload.get("type")

        if not user_id:
            raise HTTPException(401, detail="Invalid token payload")

        if token_type != "access":
            raise HTTPException(401, detail="Invalid token type")

        # Check if token is blacklisted (logout)
        if redis_client.sismember(f"blacklist:{user_id}", token):
            raise HTTPException(401, detail="Token has been revoked")

        return user_id

    except JWTError as e:
        raise HTTPException(401, detail=f"Invalid token: {str(e)}")
```

**Role-Based Authorization:**
```python
async def require_creator(user_id: str = Depends(get_current_user), db: Session = Depends(get_db)) -> str:
    """Require user to be a creator"""
    user = db.query(User).filter(User.id == user_id).first()
    if not user or not user.is_creator:
        raise HTTPException(403, detail="Creator access required")
    return user_id

async def require_admin(user_id: str = Depends(get_current_user), db: Session = Depends(get_db)) -> str:
    """Require user to be an admin"""
    user = db.query(User).filter(User.id == user_id).first()
    if not user or not user.is_admin:
        raise HTTPException(403, detail="Admin access required")
    return user_id
```

**Usage in Protected Endpoints:**
```python
# User endpoint (requires authentication)
@router.get("/api/v1/users/me")
async def get_my_profile(
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    user = db.query(User).filter(User.id == user_id).first()
    return serialize_user(user)

# Creator endpoint (requires creator role)
@router.get("/api/v1/creator/dashboard")
async def get_creator_dashboard(
    user_id: str = Depends(require_creator),
    db: Session = Depends(get_db)
):
    # Only creators can access
    ...

# Admin endpoint (requires admin role)
@router.get("/api/v1/admin/users")
async def get_all_users(
    user_id: str = Depends(require_admin),
    db: Session = Depends(get_db)
):
    # Only admins can access
    ...
```

**Token Refresh Flow:**
```python
@router.post("/api/v1/auth/refresh-token")
async def refresh_access_token(refresh_token: str):
    """
    Refresh access token using refresh token

    Request body:
        refresh_token: JWT refresh token

    Returns:
        new_access_token: New JWT access token (1 hour expiration)
    """
    try:
        # Decode refresh token
        payload = jwt.decode(refresh_token, SECRET_KEY, algorithms=[ALGORITHM])
        user_id = payload.get("user_id")
        token_type = payload.get("type")

        if token_type != "refresh":
            raise HTTPException(401, detail="Invalid token type")

        # Verify token matches stored refresh token in Redis
        stored_token = redis_client.get(f"refresh_token:{user_id}")
        if not stored_token or stored_token != refresh_token:
            raise HTTPException(401, detail="Invalid or expired refresh token")

        # Create new access token
        new_access_token = create_access_token(user_id)

        return {
            "access_token": new_access_token,
            "token_type": "bearer",
            "expires_in": ACCESS_TOKEN_EXPIRE_MINUTES * 60  # seconds
        }

    except JWTError:
        raise HTTPException(401, detail="Invalid refresh token")
```

**Logout (Token Invalidation):**
```python
@router.post("/api/v1/auth/logout")
async def logout(
    user_id: str = Depends(get_current_user),
    authorization: str = Header(None)
):
    """
    Logout user and invalidate tokens

    Adds current access token to blacklist in Redis
    Removes refresh token from Redis
    """
    # Extract current token
    scheme, token = authorization.split()

    # Add to blacklist (expires when token expires)
    redis_client.sadd(f"blacklist:{user_id}", token)
    redis_client.expire(f"blacklist:{user_id}", ACCESS_TOKEN_EXPIRE_MINUTES * 60)

    # Remove refresh token
    redis_client.delete(f"refresh_token:{user_id}")

    return {"message": "Logged out successfully"}
```

**Public Endpoints (No Authentication):**
- POST /api/v1/auth/send-otp
- POST /api/v1/auth/verify-otp
- GET /api/v1/gifts (public catalog)
- GET /api/v1/wallet/packages (public coin packages)
- GET /api/v1/calls/rates (public call rates)

**Protected Endpoints (Require JWT):**
- All other endpoints require Authorization: Bearer header

**Rationale:**
JWT provides stateless authentication (no session storage). Refresh tokens enable long-term sessions without forcing frequent re-authentication.

**Acceptance Criteria:**
- [ ] JWT access tokens created with 1-hour expiration
- [ ] JWT refresh tokens created with 30-day expiration
- [ ] Authorization: Bearer header validated on all protected endpoints
- [ ] Invalid/expired tokens return 401 Unauthorized
- [ ] Token blacklist (logout) working via Redis
- [ ] Role-based authorization (creator, admin) enforced
- [ ] Public endpoints accessible without authentication
- [ ] Token refresh flow working

**Dependencies:**
- REQ-TA-007 (Enterprise Security from Section 6)
- REQ-DB-011 (Redis for token storage)

**Design Decisions:**
- **DD-API-009**: JWT chosen for stateless authentication (scalable across API servers)
- **DD-API-010**: Redis token blacklist chosen for logout (distributed invalidation)

**Notes:**
- SECRET_KEY must be stored in AWS Secrets Manager (REQ-TA-007)
- Redis stores refresh tokens and blacklisted access tokens
- JWT payload includes user_id (no database lookup per request)

**Testing Requirements:**
- Valid token authentication
- Expired token rejection
- Invalid token rejection
- Blacklisted token rejection
- Token refresh flow
- Role-based authorization (creator, admin)

---

## REQ-API-006: Standardized Error Handling with i18n

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement standardized error responses with i18n support using system_messages table, providing localized error messages across all 40+ endpoints.

**Specifications:**

**Error Response Structure:**
```python
{
    "error_code": "error_insufficient_coins",  # Machine-readable code
    "message": "நாணயங்கள் போதுமானதாக இல்லை",  # Human-readable message (localized)
    "timestamp": "2026-01-20T10:30:00.000Z",
    "details": {  # Optional field-level errors
        "required_coins": 100,
        "current_balance": 50
    }
}
```

**Implementation:**
```python
from fastapi import Request
from datetime import datetime

async def get_error_message(
    error_code: str,
    lang: str,
    placeholders: dict = None
) -> str:
    """
    Retrieve localized error message from system_messages table

    Args:
        error_code: Error code key (e.g., "error_insufficient_coins")
        lang: Language code (ta, te, kn, hi, en)
        placeholders: Optional variables for message interpolation

    Returns:
        Localized error message with placeholders replaced
    """
    # Query system_messages table
    message_row = db.query(SystemMessage).filter(
        SystemMessage.message_key == error_code
    ).first()

    if not message_row:
        # Fallback to generic error
        return "An error occurred" if lang == "en" else "பிழை ஏற்பட்டது"

    # Get message in requested language, fallback to English
    message = message_row.translations.get(lang, message_row.translations.get("en"))

    # Replace placeholders if provided
    if placeholders:
        for key, value in placeholders.items():
            message = message.replace(f"{{{key}}}", str(value))

    return message

class APIException(HTTPException):
    """Custom exception with i18n support"""

    def __init__(
        self,
        status_code: int,
        error_code: str,
        request: Request,
        details: dict = None,
        placeholders: dict = None
    ):
        lang = parse_language(request)
        message = get_error_message(error_code, lang, placeholders)

        super().__init__(
            status_code=status_code,
            detail={
                "error_code": error_code,
                "message": message,
                "timestamp": datetime.utcnow().isoformat() + "Z",
                **({"details": details} if details else {})
            }
        )
```

**Usage in Endpoints:**
```python
@router.post("/api/v1/gifts/send")
async def send_gift(
    request: Request,
    gift_request: SendGiftRequest,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    # Validate sender balance
    wallet = db.query(CoinWallet).filter(CoinWallet.user_id == user_id).first()
    gift = db.query(Gift).filter(Gift.id == gift_request.gift_id).first()

    if wallet.balance < gift.coin_price:
        raise APIException(
            status_code=400,
            error_code="error_insufficient_coins",
            request=request,
            details={
                "required_coins": int(gift.coin_price),
                "current_balance": wallet.balance
            },
            placeholders={
                "required": str(gift.coin_price),
                "balance": str(wallet.balance)
            }
        )

    # ... proceed with gift sending ...
```

**Standard Error Codes:**
- error_insufficient_coins (400)
- error_user_not_found (404)
- error_blocked_user (403)
- error_rate_limit_exceeded (429)
- error_invalid_request (400)
- error_call_ended (400)
- error_gift_not_found (404)
- error_unauthorized (401)
- error_forbidden (403)
- error_internal_server (500)
- error_service_unavailable (503)

**system_messages Table Seed Data:**
```sql
INSERT INTO system_messages (message_key, translations) VALUES
('error_insufficient_coins', '{
  "en": "Insufficient coins. You need {required} coins. Current balance: {balance}",
  "ta": "நாணயங்கள் போதுமானதாக இல்லை. உங்களுக்கு {required} நாணயங்கள் தேவை. தற்போதைய இருப்பு: {balance}",
  "te": "తగినంత నాణెలు లేవు. మీకు {required} నాణెలు అవసరం. ప్రస్తుత బ్యాలెన్స్: {balance}",
  "kn": "ಸಾಕಷ್ಟು ನಾಣ್ಯಗಳಿಲ್ಲ. ನಿಮಗೆ {required} ನಾಣ್ಯಗಳ ಅಗತ್ಯವಿದೆ. ಪ್ರಸ್ತುತ ಬಾಕಿ: {balance}",
  "hi": "अपर्याप्त सिक्के। आपको {required} सिक्कों की आवश्यकता है। वर्तमान बैलेंस: {balance}"
}');
```

**Rationale:**
Centralized error handling with i18n ensures consistent error experience across all endpoints and languages. Database-backed messages enable updates without code deployment.

**Acceptance Criteria:**
- [ ] APIException class handles all error responses
- [ ] system_messages table populated with all error codes in 5 languages
- [ ] Error messages localized based on Accept-Language header
- [ ] Placeholder interpolation working ({required}, {balance}, etc.)
- [ ] All endpoints use standardized error responses
- [ ] Error codes documented in OpenAPI schema

**Dependencies:**
- REQ-DB-004 (system_messages table from Section 7)
- REQ-API-003 (Accept-Language header parsing)

**Design Decisions:**
- **DD-API-011**: Database-backed error messages chosen for runtime updates without deployment
- **DD-API-012**: Placeholder interpolation chosen for dynamic error content

**Testing Requirements:**
- Test error messages in all 5 languages
- Test placeholder interpolation
- Test fallback to English for unsupported languages
- Test missing error_code fallback

---

## REQ-API-007: Redis-Based Rate Limiting

**Priority:** P0
**Type:** Security Requirement
**Status:** Approved

**Description:**
Implement Redis-based rate limiting with per-user, per-IP, and per-endpoint limits to prevent API abuse and ensure fair usage.

**Specifications:**

**Rate Limit Configuration:**
```python
RATE_LIMITS = {
    "default": {"requests": 100, "window": 60},  # 100 requests per minute
    "/api/v1/auth/send-otp": {"requests": 5, "window": 300},  # 5 OTPs per 5 minutes
    "/api/v1/gifts/send": {"requests": 50, "window": 3600},  # 50 gifts per hour
    "/api/v1/calls/start": {"requests": 30, "window": 3600},  # 30 calls per hour
    "/api/v1/wallet/recharge/initiate": {"requests": 10, "window": 300},  # 10 recharges per 5 min
}
```

**Implementation:**
```python
from fastapi import Request, HTTPException
from redis import Redis
import time

redis_client = Redis(host='localhost', port=6379, decode_responses=True)

async def rate_limit_middleware(
    request: Request,
    endpoint: str,
    user_id: str = None
):
    """
    Rate limiting middleware using Redis sliding window

    Tracks per-user and per-IP limits separately
    """
    # Determine rate limit for endpoint
    limits = RATE_LIMITS.get(endpoint, RATE_LIMITS["default"])
    max_requests = limits["requests"]
    window = limits["window"]

    # Create keys for user and IP
    current_timestamp = int(time.time())
    window_start = current_timestamp - window

    # Per-user rate limit (if authenticated)
    if user_id:
        user_key = f"rate_limit:user:{user_id}:{endpoint}"
        user_count = await check_rate_limit(user_key, window_start, current_timestamp, max_requests, window)

        if user_count > max_requests:
            raise APIException(
                status_code=429,
                error_code="error_rate_limit_exceeded",
                request=request,
                details={
                    "limit": max_requests,
                    "window": window,
                    "retry_after": window
                }
            )

    # Per-IP rate limit (always check, prevents DDoS)
    client_ip = request.client.host
    ip_key = f"rate_limit:ip:{client_ip}:{endpoint}"
    ip_count = await check_rate_limit(ip_key, window_start, current_timestamp, max_requests * 5, window)  # 5x limit for IP

    if ip_count > max_requests * 5:
        raise APIException(
            status_code=429,
            error_code="error_rate_limit_exceeded",
            request=request,
            details={"retry_after": window}
        )

async def check_rate_limit(
    key: str,
    window_start: int,
    current_timestamp: int,
    max_requests: int,
    window: int
) -> int:
    """
    Check rate limit using Redis sorted set (sliding window)

    Returns: Current request count in window
    """
    # Remove old entries outside window
    redis_client.zremrangebyscore(key, 0, window_start)

    # Count requests in current window
    current_count = redis_client.zcard(key)

    # Add current request
    redis_client.zadd(key, {str(current_timestamp): current_timestamp})

    # Set expiration (cleanup)
    redis_client.expire(key, window)

    return current_count
```

**FastAPI Dependency:**
```python
async def apply_rate_limit(
    request: Request,
    user_id: str = Depends(get_current_user_optional)  # Optional for public endpoints
):
    """Apply rate limiting to endpoint"""
    endpoint = request.url.path
    await rate_limit_middleware(request, endpoint, user_id)
    return user_id

@router.post("/api/v1/gifts/send", dependencies=[Depends(apply_rate_limit)])
async def send_gift(...):
    """Rate limited endpoint"""
    ...
```

**Rate Limit Headers:**
```python
@app.middleware("http")
async def add_rate_limit_headers(request: Request, call_next):
    """Add rate limit headers to response"""
    response = await call_next(request)

    # Add headers
    endpoint = request.url.path
    limits = RATE_LIMITS.get(endpoint, RATE_LIMITS["default"])

    response.headers["X-RateLimit-Limit"] = str(limits["requests"])
    response.headers["X-RateLimit-Window"] = str(limits["window"])

    # Calculate remaining (if user authenticated)
    # ... logic to get remaining count ...

    return response
```

**Rationale:**
Rate limiting prevents API abuse, DDoS attacks, and ensures fair resource allocation. Redis-based implementation scales horizontally across API servers.

**Acceptance Criteria:**
- [ ] Rate limiting enforced on all endpoints
- [ ] Per-user limits tracked separately from per-IP limits
- [ ] Redis sliding window algorithm implemented
- [ ] 429 error returned when limit exceeded
- [ ] Rate limit headers (X-RateLimit-*) included in responses
- [ ] Different limits for different endpoints (OTP, gifts, calls)
- [ ] Old entries cleaned up from Redis automatically

**Dependencies:**
- REQ-TA-003 (Redis Cluster from Section 6)

**Design Decisions:**
- **DD-API-013**: Sliding window chosen over fixed window for smoother rate limiting
- **DD-API-014**: Per-IP limits set at 5x per-user limits to allow multiple users behind NAT

**Testing Requirements:**
- Test rate limit enforcement
- Test 429 error response
- Test limit reset after window expires
- Test per-user vs per-IP limits
- Test different limits for different endpoints

---

## REQ-API-008: Webhook Endpoints with Signature Verification

**Priority:** P0
**Type:** Integration Requirement
**Status:** Approved

**Description:**
Implement webhook endpoints for Razorpay payment confirmations, Agora call events, and FCM push notification delivery with signature verification for security.

**Specifications:**

**Webhook Endpoints:**
1. POST /api/v1/webhooks/razorpay (payment confirmations)
2. POST /api/v1/webhooks/agora (call events)
3. POST /api/v1/webhooks/fcm (push notification delivery status)

**Razorpay Webhook Implementation:**
```python
import hmac
import hashlib

RAZORPAY_WEBHOOK_SECRET = "whsec_..."  # From AWS Secrets Manager

@router.post("/api/v1/webhooks/razorpay")
async def razorpay_webhook(
    request: Request,
    x_razorpay_signature: str = Header(None)
):
    """
    Handle Razorpay payment webhooks

    Events:
    - payment.captured (successful payment)
    - payment.failed (failed payment)
    - order.paid (order completed)

    Signature verification prevents spoofed webhooks
    """
    # Get raw body
    body = await request.body()

    # Verify signature
    expected_signature = hmac.new(
        RAZORPAY_WEBHOOK_SECRET.encode(),
        body,
        hashlib.sha256
    ).hexdigest()

    if not hmac.compare_digest(expected_signature, x_razorpay_signature):
        raise HTTPException(401, detail="Invalid webhook signature")

    # Parse event
    event = await request.json()
    event_type = event.get("event")
    payload = event.get("payload")

    if event_type == "payment.captured":
        # Payment successful
        payment = payload["payment"]["entity"]
        order_id = payment["order_id"]
        payment_id = payment["id"]
        amount = Decimal(str(payment["amount"])) / 100  # Paisa to Rupees

        # Update coin_transactions table
        transaction = db.query(CoinTransaction).filter(
            CoinTransaction.razorpay_order_id == order_id
        ).first()

        if transaction:
            transaction.status = "completed"
            transaction.razorpay_payment_id = payment_id
            transaction.updated_at = datetime.utcnow()

            # Credit coins to user wallet
            wallet = db.query(CoinWallet).filter(
                CoinWallet.user_id == transaction.user_id
            ).first()
            wallet.balance += transaction.coins_purchased
            wallet.total_purchased += transaction.coins_purchased

            db.commit()

            # Send N8N webhook for successful purchase (REQ-AW-001)
            n8n_client.trigger_webhook("coin_purchase_completed", {
                "user_id": str(transaction.user_id),
                "coins": transaction.coins_purchased,
                "amount": float(amount)
            })

    elif event_type == "payment.failed":
        # Payment failed
        payment = payload["payment"]["entity"]
        order_id = payment["order_id"]

        # Update transaction status
        transaction = db.query(CoinTransaction).filter(
            CoinTransaction.razorpay_order_id == order_id
        ).first()

        if transaction:
            transaction.status = "failed"
            transaction.updated_at = datetime.utcnow()
            db.commit()

    return {"status": "received"}
```

**Agora Webhook Implementation:**
```python
AGORA_WEBHOOK_SECRET = "agora_webhook_secret"  # From config

@router.post("/api/v1/webhooks/agora")
async def agora_webhook(
    request: Request,
    x_agora_signature: str = Header(None)
):
    """
    Handle Agora RTC webhooks

    Events:
    - channel.join (user joined call)
    - channel.leave (user left call)
    - recording.ready (call recording available)
    """
    # Verify signature (Agora uses HMAC-SHA256)
    body = await request.body()
    expected_signature = hmac.new(
        AGORA_WEBHOOK_SECRET.encode(),
        body,
        hashlib.sha256
    ).hexdigest()

    if not hmac.compare_digest(expected_signature, x_agora_signature):
        raise HTTPException(401, detail="Invalid Agora signature")

    # Parse event
    event = await request.json()
    event_type = event.get("eventType")

    if event_type == "channel.leave":
        # User left call - end call if both parties left
        channel_name = event.get("channelName")

        # Find call by Agora channel
        call = db.query(Call).filter(
            Call.agora_channel == channel_name,
            Call.status == "ongoing"
        ).first()

        if call:
            # Check if both parties left
            participants = agora_client.get_channel_participants(channel_name)
            if len(participants) == 0:
                # End call
                call.status = "ended"
                call.ended_at = datetime.utcnow()
                call.duration_seconds = int((call.ended_at - call.started_at).total_seconds())

                # Trigger billing (REQ-API-002)
                # ... billing logic ...

                db.commit()

    elif event_type == "recording.ready":
        # Call recording available
        recording_url = event.get("fileUrl")
        channel_name = event.get("channelName")

        # Update call with recording URL
        call = db.query(Call).filter(Call.agora_channel == channel_name).first()
        if call:
            call.recording_url = recording_url
            db.commit()

    return {"status": "received"}
```

**FCM Webhook Implementation:**
```python
@router.post("/api/v1/webhooks/fcm")
async def fcm_webhook(request: Request):
    """
    Handle FCM push notification delivery status

    Events:
    - delivered (notification delivered)
    - clicked (user clicked notification)
    - dismissed (user dismissed notification)
    """
    event = await request.json()
    event_type = event.get("event")
    notification_id = event.get("notification_id")

    # Update notification status
    notification = db.query(Notification).filter(
        Notification.id == notification_id
    ).first()

    if notification:
        if event_type == "delivered":
            notification.delivered_at = datetime.utcnow()
        elif event_type == "clicked":
            notification.clicked_at = datetime.utcnow()
            notification.is_read = True

        db.commit()

    return {"status": "received"}
```

**Rationale:**
Webhooks enable real-time event processing from external services. Signature verification prevents unauthorized webhook calls and spoofed events.

**Acceptance Criteria:**
- [ ] Razorpay webhook endpoint implemented with signature verification
- [ ] Agora webhook endpoint implemented with signature verification
- [ ] FCM webhook endpoint implemented
- [ ] payment.captured event updates coin_transactions and credits wallet
- [ ] payment.failed event updates transaction status
- [ ] channel.leave event triggers call billing
- [ ] Webhook secrets stored in AWS Secrets Manager
- [ ] Invalid signatures return 401 Unauthorized

**Dependencies:**
- REQ-RM-002 (Razorpay Integration from Section 4)
- REQ-TA-004 (Agora RTC from Section 6)
- REQ-FS-014 (Push Notifications from Section 5)

**Design Decisions:**
- **DD-API-015**: HMAC-SHA256 signature verification chosen for security
- **DD-API-016**: Webhook endpoints separate from main API for easier firewall rules

**Testing Requirements:**
- Test valid webhook with correct signature
- Test webhook with invalid signature (401)
- Test payment.captured event flow
- Test payment.failed event flow
- Test call end triggered by Agora webhook

---

## REQ-API-009: Hybrid Pagination Strategy

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement hybrid pagination using cursor-based pagination for real-time data and offset-based pagination for static data across all list endpoints.

**Specifications:**

**Cursor-Based Pagination (Real-Time Data):**
```python
@router.get("/api/v1/calls/history")
async def get_call_history(
    cursor: str = None,  # Opaque cursor (Base64-encoded last call ID + timestamp)
    limit: int = Query(20, ge=1, le=100),
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """
    Get call history with cursor-based pagination

    Cursor format: Base64(last_call_id:last_timestamp)

    Use case: Real-time data (calls, notifications, transactions)
    """
    query = db.query(Call).filter(
        or_(Call.caller_id == user_id, Call.receiver_id == user_id)
    ).order_by(Call.created_at.desc(), Call.id.desc())

    # Apply cursor if provided
    if cursor:
        try:
            decoded = base64.b64decode(cursor).decode()
            last_id, last_timestamp = decoded.split(":")

            query = query.filter(
                or_(
                    Call.created_at < datetime.fromisoformat(last_timestamp),
                    and_(
                        Call.created_at == datetime.fromisoformat(last_timestamp),
                        Call.id < UUID(last_id)
                    )
                )
            )
        except Exception:
            raise HTTPException(400, detail="Invalid cursor")

    # Fetch limit + 1 to check if more pages exist
    calls = query.limit(limit + 1).all()

    has_more = len(calls) > limit
    if has_more:
        calls = calls[:limit]

    # Generate next cursor
    next_cursor = None
    if has_more and len(calls) > 0:
        last_call = calls[-1]
        cursor_data = f"{last_call.id}:{last_call.created_at.isoformat()}"
        next_cursor = base64.b64encode(cursor_data.encode()).decode()

    return {
        "data": [serialize_call(c) for c in calls],
        "pagination": {
            "cursor": next_cursor,
            "has_more": has_more,
            "limit": limit
        }
    }
```

**Offset-Based Pagination (Static Data):**
```python
@router.get("/api/v1/gifts")
async def get_gifts(
    page: int = Query(1, ge=1),
    limit: int = Query(20, ge=1, le=100),
    category: str = None,
    request: Request = None,
    db: Session = Depends(get_db)
):
    """
    Get gift catalog with offset-based pagination

    Use case: Static/slow-changing data (gifts, coin packages, static content)
    """
    lang = await parse_language(request)

    query = db.query(Gift).filter(Gift.is_active == True)
    if category:
        query = query.filter(Gift.category == category)

    total = query.count()
    offset = (page - 1) * limit

    gifts = query.order_by(Gift.sort_order).offset(offset).limit(limit).all()

    return {
        "data": [
            {
                "id": str(g.id),
                "name": g.names.get(lang, g.names.get("en")),
                "description": g.descriptions.get(lang, g.descriptions.get("en")),
                "coin_price": float(g.coin_price),
                "diamond_value": float(g.diamond_value),
                "icon_url": g.icon_url,
                "category": g.category
            }
            for g in gifts
        ],
        "pagination": {
            "page": page,
            "limit": limit,
            "total": total,
            "total_pages": -(-total // limit),  # Ceiling division
            "has_more": offset + limit < total
        }
    }
```

**Pagination Strategy Table:**

| Endpoint | Strategy | Reason |
|----------|----------|--------|
| GET /api/v1/calls/history | Cursor | Real-time data, frequent updates |
| GET /api/v1/wallet/transactions | Cursor | Real-time data, frequent updates |
| GET /api/v1/notifications | Cursor | Real-time data, frequent updates |
| GET /api/v1/gifts/received | Cursor | Real-time data |
| GET /api/v1/gifts | Offset | Static catalog, infrequent updates |
| GET /api/v1/wallet/packages | Offset | Static catalog |
| GET /api/v1/rooms/live | Cursor | Real-time data, changing frequently |
| GET /api/v1/users/search | Offset | Bounded search results |

**Rationale:**
Cursor-based pagination prevents inconsistencies when new data is added (no skipped/duplicate items). Offset-based pagination is simpler for static data and supports total count.

**Acceptance Criteria:**
- [ ] Cursor-based pagination implemented for real-time endpoints
- [ ] Offset-based pagination implemented for static endpoints
- [ ] Cursor opaque (Base64-encoded)
- [ ] Invalid cursor returns 400 Bad Request
- [ ] has_more field indicates if more pages exist
- [ ] total count included for offset pagination
- [ ] limit parameter validated (1-100 range)

**Dependencies:**
- REQ-API-001 (All list endpoints)

**Design Decisions:**
- **DD-API-017**: Hybrid approach chosen for optimal UX (cursor for feeds, offset for catalogs)
- **DD-API-018**: Opaque cursor chosen to hide implementation details

**Testing Requirements:**
- Test cursor pagination with new data added mid-pagination
- Test offset pagination with stable data
- Test invalid cursor handling
- Test limit boundary (1, 100)
- Test empty results

---

## REQ-API-010: API Versioning (/api/v1)

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement URL-based API versioning with /api/v1 prefix and 6-month deprecation policy to support backward-compatible API evolution.

**Specifications:**

**URL Structure:**
```
https://api.livvly.com/api/v1/users/me
https://api.livvly.com/api/v1/gifts
https://api.livvly.com/api/v2/users/me  (future)
```

**FastAPI Router Configuration:**
```python
from fastapi import APIRouter

# Version 1 routers
v1_router = APIRouter(prefix="/api/v1", tags=["v1"])

# Auth routes
auth_router = APIRouter(prefix="/auth", tags=["Authentication"])
auth_router.include_router(auth_routes)
v1_router.include_router(auth_router)

# User routes
users_router = APIRouter(prefix="/users", tags=["Users"])
users_router.include_router(user_routes)
v1_router.include_router(users_router)

# ... other routers ...

# Mount v1 router to app
app.include_router(v1_router)
```

**Deprecation Policy:**
```python
from fastapi import Header, HTTPException
from datetime import datetime

DEPRECATED_ENDPOINTS = {
    "/api/v1/old-endpoint": {
        "sunset_date": "2026-07-01",
        "replacement": "/api/v2/new-endpoint"
    }
}

@app.middleware("http")
async def deprecation_middleware(request: Request, call_next):
    """Add deprecation headers to deprecated endpoints"""
    endpoint = request.url.path

    if endpoint in DEPRECATED_ENDPOINTS:
        deprecation = DEPRECATED_ENDPOINTS[endpoint]
        sunset_date = deprecation["sunset_date"]
        replacement = deprecation["replacement"]

        response = await call_next(request)

        # Add deprecation headers
        response.headers["Deprecation"] = f"version=\"v1\""
        response.headers["Sunset"] = sunset_date
        response.headers["Link"] = f"<{replacement}>; rel=\"successor-version\""

        return response

    return await call_next(request)
```

**Breaking Change Policy:**
1. **Non-breaking changes** (add fields, add optional parameters) → Deploy to v1
2. **Breaking changes** (remove fields, change types) → Deploy to v2, deprecate v1 endpoint
3. **Deprecation notice**: 6 months minimum before removal
4. **Sunset header**: Indicate removal date in HTTP headers
5. **Changelog**: Document all API changes

**Version Transition Plan:**
```
Month 0: Release v1 (MVP)
Month 12: Release v2 (breaking changes if needed)
         - Announce v1 deprecation with Sunset header
         - Update mobile app to support v2
Month 18: Sunset v1 (v1 returns 410 Gone)
         - Force mobile app update to v2
```

**Client Version Detection:**
```python
@app.middleware("http")
async def client_version_middleware(request: Request, call_next):
    """Track client app versions for sunset planning"""
    client_version = request.headers.get("X-App-Version")

    if client_version:
        # Log to analytics (track adoption of new app versions)
        analytics.track("api_request", {
            "endpoint": request.url.path,
            "app_version": client_version,
            "api_version": "v1"
        })

    return await call_next(request)
```

**Rationale:**
URL-based versioning allows running v1 and v2 simultaneously for gradual migration. 6-month deprecation policy gives users time to update mobile apps.

**Acceptance Criteria:**
- [ ] All endpoints prefixed with /api/v1
- [ ] Deprecation middleware adds Sunset headers
- [ ] Breaking changes tracked in CHANGELOG.md
- [ ] 6-month minimum deprecation period enforced
- [ ] Client version tracking via X-App-Version header
- [ ] Documentation shows version in all examples

**Dependencies:**
- REQ-API-001 (All endpoints)

**Design Decisions:**
- **DD-API-019**: URL-based versioning chosen over header-based for simplicity
- **DD-API-020**: 6-month deprecation chosen to balance agility and backward compatibility

**Testing Requirements:**
- Test /api/v1 prefix on all endpoints
- Test Sunset header on deprecated endpoints
- Test 410 Gone response after sunset date

---

## REQ-API-011: Request/Response Validation with Pydantic

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement comprehensive request/response validation using Pydantic models for all 40+ endpoints to ensure data integrity and type safety.

**Specifications:**

**Request Validation Example:**
```python
from pydantic import BaseModel, Field, validator, EmailStr
from typing import Optional
from decimal import Decimal
from uuid import UUID

class SendGiftRequest(BaseModel):
    """Request model for sending gift"""

    gift_id: UUID = Field(..., description="Gift UUID from catalog")
    receiver_id: UUID = Field(..., description="Recipient user UUID")
    context: str = Field(..., description="Context: 'call', 'room', or 'profile'")
    room_id: Optional[UUID] = Field(None, description="Room UUID if context='room'")
    message: Optional[str] = Field(None, max_length=200, description="Optional message")

    @validator("context")
    def validate_context(cls, v):
        """Validate context is one of allowed values"""
        allowed = ["call", "room", "profile"]
        if v not in allowed:
            raise ValueError(f"context must be one of {allowed}")
        return v

    @validator("room_id")
    def validate_room_id(cls, v, values):
        """If context is 'room', room_id is required"""
        if values.get("context") == "room" and not v:
            raise ValueError("room_id required when context='room'")
        return v

    class Config:
        schema_extra = {
            "example": {
                "gift_id": "123e4567-e89b-12d3-a456-426614174000",
                "receiver_id": "456e7890-abc1-23f4-d567-890123456def",
                "context": "call",
                "message": "You're awesome!"
            }
        }
```

**Response Validation Example:**
```python
class GiftResponse(BaseModel):
    """Response model for gift operations"""

    transaction_id: UUID
    gift_id: UUID
    gift_name: str
    receiver_id: UUID
    receiver_name: str
    coins_spent: int
    diamonds_earned: Decimal
    creator_tier: str
    new_coin_balance: int
    created_at: datetime

    class Config:
        orm_mode = True  # Allow Pydantic to read from ORM models

@router.post("/api/v1/gifts/send", response_model=GiftResponse)
async def send_gift(
    request: SendGiftRequest,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
) -> GiftResponse:
    """Send gift - request and response validated automatically"""
    # ... implementation ...
    return gift_transaction  # FastAPI auto-validates against GiftResponse
```

**Validation Error Response:**
```json
{
  "error_code": "validation_error",
  "message": "Request validation failed",
  "timestamp": "2026-01-20T10:30:00.000Z",
  "details": {
    "errors": [
      {
        "field": "gift_id",
        "message": "value is not a valid uuid",
        "type": "type_error.uuid"
      },
      {
        "field": "context",
        "message": "context must be one of ['call', 'room', 'profile']",
        "type": "value_error"
      }
    ]
  }
}
```

**Common Pydantic Models:**
```python
class UserProfileUpdate(BaseModel):
    """Update user profile"""
    display_name: Optional[str] = Field(None, min_length=2, max_length=50)
    bio: Optional[str] = Field(None, max_length=500)
    language: Optional[str] = Field(None, regex="^(ta|te|kn|hi|en)$")
    interests: Optional[list[str]] = Field(None, max_items=10)

class CoinRechargeRequest(BaseModel):
    """Initiate coin recharge"""
    package_id: UUID
    payment_method: str = Field(..., regex="^(razorpay|upi)$")

class CallStartRequest(BaseModel):
    """Start call"""
    receiver_id: UUID
    call_type: str = Field(..., regex="^(audio|video)$")

class PaginationParams(BaseModel):
    """Reusable pagination parameters"""
    page: int = Field(1, ge=1, le=1000)
    limit: int = Field(20, ge=1, le=100)
```

**Rationale:**
Pydantic validation prevents invalid data from entering the system. Type safety catches bugs early. FastAPI auto-generates OpenAPI schema from Pydantic models.

**Acceptance Criteria:**
- [ ] All request bodies validated with Pydantic models
- [ ] All response bodies validated with Pydantic models
- [ ] Validation errors return 422 Unprocessable Entity
- [ ] Field-level error messages included in response
- [ ] UUIDs, emails, dates validated automatically
- [ ] Custom validators implemented for business logic
- [ ] OpenAPI schema auto-generated from models

**Dependencies:**
- REQ-API-001 (All endpoints)

**Design Decisions:**
- **DD-API-021**: Pydantic chosen for validation (FastAPI native, type-safe)
- **DD-API-022**: orm_mode enabled for direct ORM-to-Pydantic conversion

**Testing Requirements:**
- Test valid request acceptance
- Test invalid UUID rejection
- Test missing required field rejection
- Test field length validation
- Test custom validator logic

---

## REQ-API-012: CORS Configuration for Mobile Apps

**Priority:** P0
**Type:** Security Requirement
**Status:** Approved

**Description:**
Configure Cross-Origin Resource Sharing (CORS) to allow mobile app API access while preventing unauthorized web access.

**Specifications:**

**CORS Middleware Configuration:**
```python
from fastapi.middleware.cors import CORSMiddleware

# Allowed origins (mobile apps use custom scheme, web blocked)
ALLOWED_ORIGINS = [
    "https://livvly.com",  # Official website
    "https://admin.livvly.com",  # Admin dashboard
    "capacitor://localhost",  # iOS mobile app (if using Capacitor)
    "http://localhost",  # Android mobile app (if using Capacitor)
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=ALLOWED_ORIGINS,  # Whitelist only
    allow_credentials=True,  # Allow cookies (for refresh tokens if needed)
    allow_methods=["GET", "POST", "PUT", "DELETE"],  # Allowed HTTP methods
    allow_headers=[
        "Authorization",
        "Content-Type",
        "Accept-Language",
        "X-App-Version",
        "X-Request-ID"
    ],
    expose_headers=[
        "X-RateLimit-Limit",
        "X-RateLimit-Window",
        "X-RateLimit-Remaining"
    ],
    max_age=3600  # Cache preflight requests for 1 hour
)
```

**Environment-Based Configuration:**
```python
import os

# Development: Allow localhost for testing
if os.getenv("ENV") == "development":
    ALLOWED_ORIGINS.extend([
        "http://localhost:3000",  # Local web dev
        "http://localhost:8081",  # Expo dev server
        "http://192.168.1.100:8081"  # Mobile device on local network
    ])

# Staging: Allow staging domains
elif os.getenv("ENV") == "staging":
    ALLOWED_ORIGINS.append("https://staging.livvly.com")

# Production: Strict whitelist only
```

**Mobile App Authentication (No CORS for Native):**
Flutter mobile apps make native HTTP requests (not browser), so CORS doesn't apply. However, API should validate X-App-Platform header to distinguish mobile vs web.

```python
@app.middleware("http")
async def platform_validation_middleware(request: Request, call_next):
    """Validate request is from mobile app (not scrapers)"""
    app_platform = request.headers.get("X-App-Platform")

    # Allow mobile apps
    if app_platform in ["android", "ios"]:
        return await call_next(request)

    # Allow web for specific endpoints (public catalog)
    if request.url.path.startswith("/api/v1/auth") or request.url.path.startswith("/api/v1/gifts"):
        return await call_next(request)

    # Block unknown platforms for protected endpoints
    raise HTTPException(403, detail="Invalid platform")
```

**Rationale:**
CORS prevents unauthorized web apps from calling API. Mobile apps bypass CORS (native HTTP), but X-App-Platform header provides additional validation.

**Acceptance Criteria:**
- [ ] CORS middleware configured with whitelist
- [ ] Unauthorized origins blocked (403 Forbidden)
- [ ] Preflight requests (OPTIONS) handled correctly
- [ ] Credentials allowed for admin dashboard
- [ ] Mobile app headers (X-App-Platform) validated
- [ ] Environment-specific CORS rules working

**Dependencies:**
- REQ-TA-007 (Enterprise Security from Section 6)

**Design Decisions:**
- **DD-API-023**: Whitelist-based CORS chosen over wildcard for security
- **DD-API-024**: X-App-Platform header chosen for mobile validation

**Testing Requirements:**
- Test allowed origin acceptance
- Test blocked origin rejection
- Test preflight request handling
- Test mobile app header validation

---

## REQ-API-013: API Security Headers

**Priority:** P1
**Type:** Security Requirement
**Status:** Approved

**Description:**
Implement security headers (HSTS, CSP, X-Frame-Options) to protect against common web vulnerabilities.

**Specifications:**

**Security Headers Middleware:**
```python
@app.middleware("http")
async def add_security_headers(request: Request, call_next):
    """Add security headers to all responses"""
    response = await call_next(request)

    # HTTP Strict Transport Security (force HTTPS)
    response.headers["Strict-Transport-Security"] = "max-age=31536000; includeSubDomains"

    # Content Security Policy (prevent XSS)
    response.headers["Content-Security-Policy"] = "default-src 'self'"

    # Prevent clickjacking
    response.headers["X-Frame-Options"] = "DENY"

    # Prevent MIME sniffing
    response.headers["X-Content-Type-Options"] = "nosniff"

    # XSS Protection (legacy browsers)
    response.headers["X-XSS-Protection"] = "1; mode=block"

    # Referrer Policy (privacy)
    response.headers["Referrer-Policy"] = "strict-origin-when-cross-origin"

    # Permissions Policy (disable unused features)
    response.headers["Permissions-Policy"] = "geolocation=(), microphone=(), camera=()"

    return response
```

**HTTPS Enforcement:**
```python
@app.middleware("http")
async def https_redirect_middleware(request: Request, call_next):
    """Redirect HTTP to HTTPS in production"""
    if os.getenv("ENV") == "production":
        if request.url.scheme == "http":
            url = request.url.replace(scheme="https")
            return RedirectResponse(url, status_code=301)

    return await call_next(request)
```

**Rationale:**
Security headers protect against XSS, clickjacking, MIME sniffing, and other web vulnerabilities. HTTPS enforcement prevents man-in-the-middle attacks.

**Acceptance Criteria:**
- [ ] All security headers present in responses
- [ ] HTTPS enforced in production (HTTP redirects to HTTPS)
- [ ] HSTS header includes subdomains
- [ ] CSP policy prevents inline scripts
- [ ] X-Frame-Options prevents embedding

**Dependencies:**
- REQ-TA-007 (Enterprise Security from Section 6)

**Design Decisions:**
- **DD-API-025**: Strict security headers chosen for maximum protection

**Testing Requirements:**
- Test all security headers present
- Test HTTPS redirect in production
- Test CSP policy enforcement

---

## REQ-API-014: Soft Delete Filtering

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement soft delete filtering to exclude deleted_at records from all API responses automatically.

**Specifications:**

**SQLAlchemy Query Filter:**
```python
from sqlalchemy.orm import Query

def apply_soft_delete_filter(query: Query, model) -> Query:
    """Apply soft delete filter to query"""
    if hasattr(model, "deleted_at"):
        return query.filter(model.deleted_at == None)
    return query

# Usage in endpoints
@router.get("/api/v1/users/search")
async def search_users(name: str, db: Session = Depends(get_db)):
    query = db.query(User).filter(User.display_name.ilike(f"%{name}%"))

    # Apply soft delete filter
    query = apply_soft_delete_filter(query, User)

    users = query.limit(20).all()
    return {"data": [serialize_user(u) for u in users]}
```

**Base Model with Auto-Filter:**
```python
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import Session

class SoftDeleteMixin:
    """Mixin for soft delete functionality"""
    deleted_at = Column(TIMESTAMP(timezone=True), nullable=True, index=True)

    @classmethod
    def query_active(cls, db: Session):
        """Query only active (not deleted) records"""
        return db.query(cls).filter(cls.deleted_at == None)

class User(Base, SoftDeleteMixin):
    __tablename__ = "users"
    # ... fields ...

# Usage
active_users = User.query_active(db).filter(User.is_creator == True).all()
```

**Rationale:**
Soft delete preserves data for auditing while hiding deleted records from users. Automatic filtering prevents accidentally exposing deleted data.

**Acceptance Criteria:**
- [ ] All queries filter deleted_at == None
- [ ] Soft delete mixin applied to all models
- [ ] Admin endpoints can optionally show deleted records
- [ ] Deleted records excluded from counts

**Dependencies:**
- REQ-DB-002 (Database Schema from Section 7)

**Design Decisions:**
- **DD-API-026**: Soft delete chosen for data preservation and audit compliance

**Testing Requirements:**
- Test deleted records excluded from queries
- Test admin can view deleted records
- Test soft delete filter on all list endpoints

---

## REQ-API-015: JSONB Name Lookups for i18n

**Priority:** P0
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement JSONB field lookups for multi-language names and descriptions across all i18n endpoints.

**Specifications:**

**JSONB Lookup Helper:**
```python
def get_localized_field(jsonb_field: dict, lang: str, fallback_lang: str = "en") -> str:
    """
    Retrieve localized value from JSONB field

    Args:
        jsonb_field: JSONB dict with language keys ({"en": "Rose", "ta": "ரோஜா"})
        lang: Requested language code
        fallback_lang: Fallback language if requested not found

    Returns:
        Localized string or empty string if not found
    """
    if not jsonb_field:
        return ""

    return jsonb_field.get(lang, jsonb_field.get(fallback_lang, ""))

# Usage in serialization
def serialize_gift(gift: Gift, lang: str) -> dict:
    """Serialize gift with localized fields"""
    return {
        "id": str(gift.id),
        "name": get_localized_field(gift.names, lang),
        "description": get_localized_field(gift.descriptions, lang),
        "coin_price": float(gift.coin_price),
        "diamond_value": float(gift.diamond_value),
        "icon_url": gift.icon_url,
        "category": gift.category
    }
```

**Performance Optimization (Index JSONB):**
```sql
-- GIN index for JSONB lookups
CREATE INDEX idx_gifts_names_gin ON gifts USING GIN (names);
CREATE INDEX idx_coin_packages_names_gin ON coin_packages USING GIN (names);
```

**Rationale:**
JSONB lookups enable flexible multi-language support without schema changes. Fallback to English ensures graceful degradation.

**Acceptance Criteria:**
- [ ] JSONB fields queried with language key
- [ ] Fallback to English if language missing
- [ ] Empty string returned if JSONB null
- [ ] GIN indexes created for JSONB fields

**Dependencies:**
- REQ-DB-005 (JSONB i18n Columns from Section 7)
- REQ-API-003 (Accept-Language Header)

**Design Decisions:**
- **DD-API-027**: JSONB chosen for i18n storage (flexible, no schema changes for new languages)

**Testing Requirements:**
- Test JSONB lookup with valid language
- Test fallback to English
- Test null JSONB handling

---

## REQ-API-016: Monitoring and Logging

**Priority:** P1
**Type:** Operational Requirement
**Status:** Approved

**Description:**
Implement comprehensive API monitoring, logging, and alerting for production observability.

**Specifications:**

**Structured Logging:**
```python
import logging
import json
from datetime import datetime

class JSONFormatter(logging.Formatter):
    """JSON log formatter for structured logging"""

    def format(self, record):
        log_data = {
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "level": record.levelname,
            "logger": record.name,
            "message": record.getMessage(),
            "module": record.module,
            "function": record.funcName,
            "line": record.lineno
        }

        # Add extra fields
        if hasattr(record, "user_id"):
            log_data["user_id"] = record.user_id
        if hasattr(record, "endpoint"):
            log_data["endpoint"] = record.endpoint
        if hasattr(record, "duration_ms"):
            log_data["duration_ms"] = record.duration_ms

        return json.dumps(log_data)

# Configure logger
logger = logging.getLogger("livvly_api")
logger.setLevel(logging.INFO)

handler = logging.StreamHandler()
handler.setFormatter(JSONFormatter())
logger.addHandler(handler)
```

**Request/Response Logging Middleware:**
```python
import time
import uuid

@app.middleware("http")
async def logging_middleware(request: Request, call_next):
    """Log all API requests and responses"""
    request_id = str(uuid.uuid4())
    start_time = time.time()

    # Log request
    logger.info(
        "API request",
        extra={
            "request_id": request_id,
            "method": request.method,
            "endpoint": request.url.path,
            "client_ip": request.client.host,
            "user_agent": request.headers.get("user-agent")
        }
    )

    # Process request
    response = await call_next(request)

    # Calculate duration
    duration_ms = int((time.time() - start_time) * 1000)

    # Log response
    logger.info(
        "API response",
        extra={
            "request_id": request_id,
            "endpoint": request.url.path,
            "status_code": response.status_code,
            "duration_ms": duration_ms
        }
    )

    # Add request ID header
    response.headers["X-Request-ID"] = request_id

    return response
```

**Performance Monitoring:**
```python
# Slow query logging
@app.middleware("http")
async def slow_query_alert(request: Request, call_next):
    """Alert on slow API requests (>2s)"""
    start_time = time.time()
    response = await call_next(request)
    duration_s = time.time() - start_time

    if duration_s > 2.0:
        logger.warning(
            "Slow API request detected",
            extra={
                "endpoint": request.url.path,
                "duration_s": duration_s,
                "threshold": 2.0
            }
        )

        # Send alert to PagerDuty/Slack
        alert_client.send_alert(
            "Slow API Request",
            f"Endpoint {request.url.path} took {duration_s:.2f}s"
        )

    return response
```

**Metrics Collection (Prometheus):**
```python
from prometheus_client import Counter, Histogram, generate_latest

# Metrics
api_requests_total = Counter(
    "api_requests_total",
    "Total API requests",
    ["method", "endpoint", "status_code"]
)

api_request_duration = Histogram(
    "api_request_duration_seconds",
    "API request duration",
    ["method", "endpoint"]
)

@app.middleware("http")
async def metrics_middleware(request: Request, call_next):
    """Collect Prometheus metrics"""
    start_time = time.time()
    response = await call_next(request)
    duration = time.time() - start_time

    # Record metrics
    api_requests_total.labels(
        method=request.method,
        endpoint=request.url.path,
        status_code=response.status_code
    ).inc()

    api_request_duration.labels(
        method=request.method,
        endpoint=request.url.path
    ).observe(duration)

    return response

# Metrics endpoint
@app.get("/metrics")
async def metrics():
    """Prometheus metrics endpoint"""
    return Response(generate_latest(), media_type="text/plain")
```

**Rationale:**
Structured logging and metrics enable production debugging, performance monitoring, and incident response.

**Acceptance Criteria:**
- [ ] All API requests logged with request ID
- [ ] Response times logged for all endpoints
- [ ] Slow queries (>2s) trigger alerts
- [ ] Prometheus metrics exposed at /metrics
- [ ] CloudWatch logs aggregated for all API servers
- [ ] Error rates monitored with alerts

**Dependencies:**
- REQ-TA-008 (Monitoring & Logging from Section 6)

**Design Decisions:**
- **DD-API-028**: JSON structured logging chosen for CloudWatch integration
- **DD-API-029**: Prometheus chosen for metrics collection

**Testing Requirements:**
- Test structured log format
- Test request ID in response headers
- Test metrics endpoint accessibility

---

## REQ-API-017: Load Testing Requirements

**Priority:** P1
**Type:** Quality Requirement
**Status:** Approved

**Description:**
Define load testing requirements and performance benchmarks for all API endpoints before production deployment.

**Specifications:**

**Load Testing Tool:** Locust (Python-based)

**Performance Targets:**

| Endpoint Category | Target RPS | Max Latency (p95) | Concurrency |
|-------------------|------------|-------------------|-------------|
| Authentication | 100 RPS | 500ms | 500 users |
| Wallet/Transactions | 200 RPS | 200ms | 1000 users |
| Calls (Start/End) | 50 RPS | 300ms | 200 users |
| Gifts | 100 RPS | 200ms | 500 users |
| Live Rooms | 150 RPS | 300ms | 800 users |
| Creator Dashboard | 50 RPS | 500ms | 200 users |

**Locust Test Script:**
```python
from locust import HttpUser, task, between
import random

class LivelyUser(HttpUser):
    """Simulate LIVVLY app user"""
    wait_time = between(1, 3)  # 1-3 seconds between requests

    def on_start(self):
        """Login user and get JWT token"""
        response = self.client.post("/api/v1/auth/send-otp", json={
            "phone_number": "+919876543210"
        })

        response = self.client.post("/api/v1/auth/verify-otp", json={
            "phone_number": "+919876543210",
            "otp_code": "123456"  # Test OTP
        })

        self.token = response.json()["access_token"]
        self.headers = {"Authorization": f"Bearer {self.token}"}

    @task(3)
    def browse_gifts(self):
        """Browse gift catalog (common action)"""
        self.client.get("/api/v1/gifts", headers=self.headers)

    @task(2)
    def check_wallet(self):
        """Check wallet balance"""
        self.client.get("/api/v1/wallet/balance", headers=self.headers)

    @task(1)
    def send_gift(self):
        """Send gift (less common)"""
        self.client.post("/api/v1/gifts/send", headers=self.headers, json={
            "gift_id": "123e4567-e89b-12d3-a456-426614174000",
            "receiver_id": "456e7890-abc1-23f4-d567-890123456def",
            "context": "profile"
        })

    @task(1)
    def browse_rooms(self):
        """Browse live rooms"""
        self.client.get("/api/v1/rooms/live", headers=self.headers)
```

**Load Test Scenarios:**

**1. Baseline Load Test** (MVP Launch)
- 500 concurrent users
- 30-minute duration
- Success rate: >99.5%
- p95 latency: <500ms

**2. Spike Test** (Sudden traffic surge)
- 0 → 2000 users in 2 minutes
- Maintain 2000 users for 10 minutes
- Success rate: >95%
- No 500 errors

**3. Stress Test** (Find breaking point)
- Gradually increase load from 100 → 5000 users
- Identify point where p95 latency exceeds 1s
- Document maximum sustainable load

**4. Endurance Test** (Memory leaks)
- 1000 concurrent users
- 4-hour duration
- Memory usage should not increase >10%

**Rationale:**
Load testing ensures API can handle production traffic at MVP launch (50K users, 10K concurrent). Identifies bottlenecks before user impact.

**Acceptance Criteria:**
- [ ] Locust test suite implemented for all endpoint categories
- [ ] Baseline load test passes (500 users, 99.5% success)
- [ ] Spike test passes (2000 users, 95% success)
- [ ] Stress test completed (maximum load documented)
- [ ] Endurance test passes (4 hours, no memory leaks)
- [ ] Performance report generated before production deployment

**Dependencies:**
- REQ-TA-002 (Auto-Scaling from Section 6)

**Design Decisions:**
- **DD-API-030**: Locust chosen for load testing (Python-based, integrates with FastAPI)

**Testing Requirements:**
- Run baseline test on staging
- Run spike test on staging
- Document results in performance report

---

## REQ-API-018: API Performance Optimization

**Priority:** P1
**Type:** Technical Requirement
**Status:** Approved

**Description:**
Implement API performance optimizations including database query optimization, caching, and connection pooling.

**Specifications:**

**Database Query Optimization:**
```python
# N+1 query prevention with eager loading
@router.get("/api/v1/calls/history")
async def get_call_history(user_id: str = Depends(get_current_user), db: Session = Depends(get_db)):
    """Get call history with eager loading (prevent N+1 queries)"""

    calls = db.query(Call).options(
        joinedload(Call.caller),  # Eager load caller user
        joinedload(Call.receiver)  # Eager load receiver user
    ).filter(
        or_(Call.caller_id == user_id, Call.receiver_id == user_id)
    ).order_by(Call.created_at.desc()).limit(20).all()

    return {"data": [serialize_call_with_users(c) for c in calls]}
```

**Redis Caching:**
```python
import redis
import json

redis_client = redis.Redis(host='localhost', port=6379, decode_responses=True)

async def get_cached_or_query(cache_key: str, query_func, ttl: int = 300):
    """Get from cache or query database"""

    # Try cache first
    cached = redis_client.get(cache_key)
    if cached:
        return json.loads(cached)

    # Query database
    result = query_func()

    # Cache result
    redis_client.setex(cache_key, ttl, json.dumps(result))

    return result

@router.get("/api/v1/gifts")
async def get_gifts(category: str = None, db: Session = Depends(get_db)):
    """Get gifts with caching (static data, 5-minute TTL)"""

    cache_key = f"gifts:category:{category or 'all'}"

    def query_gifts():
        query = db.query(Gift).filter(Gift.is_active == True)
        if category:
            query = query.filter(Gift.category == category)
        return [serialize_gift(g) for g in query.all()]

    gifts = await get_cached_or_query(cache_key, query_gifts, ttl=300)

    return {"data": gifts}
```

**Connection Pooling (PgBouncer):**
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

# Connection pool configuration
engine = create_engine(
    "postgresql://user:pass@pgbouncer:6432/livvly",
    pool_size=20,  # Maintain 20 connections
    max_overflow=10,  # Allow 10 additional connections during spikes
    pool_pre_ping=True,  # Test connections before use
    pool_recycle=3600  # Recycle connections every hour
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
```

**Async Database Queries (FastAPI + async SQLAlchemy):**
```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession

# Async engine
async_engine = create_async_engine(
    "postgresql+asyncpg://user:pass@localhost/livvly",
    echo=False
)

@router.get("/api/v1/users/search")
async def search_users(name: str, db: AsyncSession = Depends(get_async_db)):
    """Async query for non-blocking I/O"""

    result = await db.execute(
        select(User).filter(User.display_name.ilike(f"%{name}%")).limit(20)
    )
    users = result.scalars().all()

    return {"data": [serialize_user(u) for u in users]}
```

**Rationale:**
Performance optimizations reduce latency and server costs. Caching reduces database load. Connection pooling prevents connection exhaustion.

**Acceptance Criteria:**
- [ ] N+1 queries eliminated with eager loading
- [ ] Redis caching implemented for static data
- [ ] PgBouncer connection pooling configured
- [ ] Async queries used for I/O-bound operations
- [ ] Database query times logged and monitored
- [ ] p95 latency <200ms for list endpoints

**Dependencies:**
- REQ-DB-003 (Indexes from Section 7)
- REQ-TA-003 (Redis Cluster from Section 6)

**Design Decisions:**
- **DD-API-031**: Redis caching chosen for static data (gifts, packages)
- **DD-API-032**: PgBouncer chosen for connection pooling (PostgreSQL best practice)

**Testing Requirements:**
- Test cache hit/miss rates
- Test connection pool under load
- Test async query performance

---

## REQ-API-019: Endpoint-Specific Business Logic

**Priority:** P0
**Type:** Business Requirement
**Status:** Approved

**Description:**
Implement endpoint-specific business logic for key features: user profile updates, gift sending, call billing, room management, creator withdrawal.

**Specifications:**

This requirement consolidates endpoint-specific logic not covered in previous requirements:

**1. User Profile Update:**
- Validate display_name uniqueness
- Validate bio length (500 chars)
- Validate language (ta, te, kn, hi, en)
- Update updated_at timestamp

**2. Gift Sending:**
- Check sender balance
- Check receiver not blocked
- Deduct coins from sender
- Credit diamonds to receiver (75-85% tier)
- Create gift_transactions record
- Send push notification to receiver

**3. Call Billing:**
- Calculate duration (round up to minute)
- Apply custom creator rates if exists
- Use dynamic tier revenue split (REQ-API-002)
- Deduct coins from caller
- Credit diamonds to receiver
- Create coin_transactions and diamond_transactions

**4. Room Management:**
- Validate room owner permissions
- Limit room to 10 speakers
- Track room_participants
- Calculate room gift revenue
- Distribute revenue to room owner

**5. Creator Withdrawal:**
- Minimum ₹1000 withdrawal
- Check available_balance (not pending)
- Calculate TDS (10% on earnings >₹10K)
- Create payout_transactions record
- Trigger N8N webhook for bank transfer
- Update creator_performance table

**Rationale:**
Endpoint-specific business logic implements core features defined in Section 5 (Feature Specifications).

**Acceptance Criteria:**
- [ ] All business rules implemented correctly
- [ ] Edge cases handled (insufficient balance, blocked users, etc.)
- [ ] Transactions atomic (rollback on failure)
- [ ] N8N webhooks triggered for external actions

**Dependencies:**
- REQ-FS-* (All feature requirements from Section 5)
- REQ-RM-* (Revenue model from Section 4)

**Design Decisions:**
- **DD-API-033**: Atomic transactions chosen to prevent partial updates

**Testing Requirements:**
- Test all business rules
- Test edge cases and error conditions
- Test transaction atomicity

---

## REQ-API-020: Admin API Security

**Priority:** P0
**Type:** Security Requirement
**Status:** Approved

**Description:**
Implement strict security controls for admin API endpoints including role-based access, audit logging, and IP whitelisting.

**Specifications:**

**Admin Authorization:**
```python
async def require_admin(
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
) -> str:
    """Require admin role"""
    user = db.query(User).filter(User.id == user_id).first()
    if not user or not user.is_admin:
        logger.warning(
            "Unauthorized admin access attempt",
            extra={"user_id": user_id}
        )
        raise HTTPException(403, detail="Admin access required")
    return user_id
```

**IP Whitelisting:**
```python
ADMIN_ALLOWED_IPS = [
    "103.127.30.123",  # Office IP
    "52.66.123.45"     # AWS VPN
]

@app.middleware("http")
async def admin_ip_whitelist(request: Request, call_next):
    """Whitelist IPs for admin endpoints"""
    if request.url.path.startswith("/api/v1/admin"):
        client_ip = request.client.host

        if client_ip not in ADMIN_ALLOWED_IPS:
            logger.warning(
                "Admin access from unauthorized IP",
                extra={"ip": client_ip, "endpoint": request.url.path}
            )
            raise HTTPException(403, detail="Access denied from this IP")

    return await call_next(request)
```

**Audit Logging:**
```python
@router.post("/api/v1/admin/users/{user_id}/suspend")
async def suspend_user(
    user_id: UUID,
    reason: str,
    admin_id: str = Depends(require_admin),
    db: Session = Depends(get_db)
):
    """Suspend user account (admin only)"""

    # Suspend user
    user = db.query(User).filter(User.id == user_id).first()
    user.is_suspended = True
    user.suspension_reason = reason
    user.suspended_at = datetime.utcnow()
    user.suspended_by = admin_id

    # Audit log
    audit_log = AdminAuditLog(
        admin_id=admin_id,
        action="suspend_user",
        target_user_id=user_id,
        details={"reason": reason},
        ip_address=request.client.host,
        timestamp=datetime.utcnow()
    )
    db.add(audit_log)
    db.commit()

    logger.info(
        "User suspended by admin",
        extra={
            "admin_id": admin_id,
            "user_id": str(user_id),
            "reason": reason
        }
    )

    return {"message": "User suspended successfully"}
```

**Rationale:**
Admin endpoints control critical operations (user suspension, moderation). Strict security prevents unauthorized access and provides audit trail.

**Acceptance Criteria:**
- [ ] Admin role required for all admin endpoints
- [ ] IP whitelist enforced for admin endpoints
- [ ] All admin actions logged in admin_audit_logs table
- [ ] Unauthorized admin access attempts logged and alerted
- [ ] Admin dashboard uses separate authentication (not mobile JWT)

**Dependencies:**
- REQ-TA-007 (Enterprise Security from Section 6)

**Design Decisions:**
- **DD-API-034**: IP whitelist chosen for admin endpoints (defense in depth)
- **DD-API-035**: Audit logging chosen for compliance and accountability

**Testing Requirements:**
- Test admin role enforcement
- Test IP whitelist enforcement
- Test audit log creation
- Test unauthorized access rejection

---

## Summary

**Total Requirements:** 20
**Priority Breakdown:**
- **P0 (Critical):** 12 requirements
  - REQ-API-001: Comprehensive Endpoint Coverage (40+ endpoints)
  - REQ-API-002: Dynamic Tiered Revenue Calculation (CRITICAL FIX: 45% → 75-85%)
  - REQ-API-003: Accept-Language Header i18n
  - REQ-API-004: Enhanced OpenAPI Documentation
  - REQ-API-005: JWT Authentication
  - REQ-API-006: Standardized Error Handling with i18n
  - REQ-API-007: Redis-Based Rate Limiting
  - REQ-API-008: Webhook Endpoints
  - REQ-API-011: Pydantic Request/Response Validation
  - REQ-API-012: CORS Configuration
  - REQ-API-015: JSONB Name Lookups
  - REQ-API-019: Endpoint-Specific Business Logic
  - REQ-API-020: Admin API Security

- **P1 (High):** 7 requirements
  - REQ-API-009: Hybrid Pagination
  - REQ-API-010: API Versioning
  - REQ-API-013: Security Headers
  - REQ-API-014: Soft Delete Filtering
  - REQ-API-016: Monitoring and Logging
  - REQ-API-017: Load Testing
  - REQ-API-018: Performance Optimization

- **P2 (Medium):** 1 requirement
  - (None in this section)

**Key Achievements:**
1. **Fixed Critical Revenue Bug**: PRD's 45% creator share corrected to 75-85% tiered model
2. **Comprehensive Coverage**: 40+ endpoints across 12 categories (vs PRD's 12)
3. **Enterprise Security**: JWT auth, rate limiting, CORS, security headers, admin controls
4. **Multi-Language Support**: Accept-Language header with JSONB i18n across all endpoints
5. **Production-Ready**: Monitoring, logging, load testing, performance optimization

**Timeline Impact:** +2-3 weeks for comprehensive API development
**Updated MVP Timeline:** 22-29 weeks (from 20-26 weeks in Section 7)

---

**Next Steps:**
1. Update .metadata.json to mark Section 8 as completed
2. Update CHANGELOG.md with Section 8 entry
3. Present Section 8 demo for user approval
4. Continue to Section 9: Coin Economy Design
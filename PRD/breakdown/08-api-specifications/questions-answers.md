# Section 8: API Specifications - Questions & Answers

**Section:** API Specifications
**Date:** 2026-01-20
**Status:** Completed
**Questions Asked:** 9
**Critical Decisions:** Comprehensive API coverage (30-40 endpoints), Dynamic revenue calculation fix, Accept-Language i18n

---

## PHASE 1: CORE QUESTIONS

### Q1: MISSING API ENDPOINTS FOR 22 TABLES

**Context:** PRD only shows 3 API categories (Auth, Wallet, Calls), but Section 7 defines 22 tables requiring comprehensive API coverage.

**Question:** Which endpoints should be implemented for MVP?

**Options Presented:**
- Option A: Implement All Endpoints (Comprehensive MVP) - 30-40 endpoints
- Option B: Prioritize Core + NEW Tables (Balanced) - 20-25 endpoints
- Option C: Minimal (PRD + Critical NEW) - 12-15 endpoints

**Answer:** **Implement all endpoints (Recommended)**

**Complete API Coverage:**
Total: 30-40 API endpoints (vs. PRD's 12)

**Categories to Implement:**
1. **Auth APIs** (existing in PRD) - 4 endpoints
   - POST /api/v1/auth/send-otp
   - POST /api/v1/auth/verify-otp
   - POST /api/v1/auth/refresh-token
   - POST /api/v1/auth/logout

2. **Wallet APIs** (existing in PRD) - 5 endpoints
   - GET /api/v1/wallet/balance
   - GET /api/v1/wallet/packages
   - POST /api/v1/wallet/recharge/initiate
   - POST /api/v1/wallet/recharge/verify
   - GET /api/v1/wallet/transactions

3. **Calls APIs** (existing in PRD) - 5 endpoints
   - GET /api/v1/calls/rates
   - POST /api/v1/calls/start
   - POST /api/v1/calls/answer/{call_id}
   - POST /api/v1/calls/end
   - GET /api/v1/calls/history

4. **Users/Profiles APIs** (NEW) - 6 endpoints
   - GET /api/v1/users/me
   - PUT /api/v1/users/me
   - POST /api/v1/users/me/photo
   - GET /api/v1/users/{user_id}
   - GET /api/v1/users/search
   - POST /api/v1/users/interests

5. **Gifts APIs** (NEW) - 4 endpoints
   - GET /api/v1/gifts
   - POST /api/v1/gifts/send
   - GET /api/v1/gifts/received
   - GET /api/v1/gifts/sent

6. **Live Rooms APIs** (NEW) - 6 endpoints
   - GET /api/v1/rooms/live
   - POST /api/v1/rooms
   - POST /api/v1/rooms/{room_id}/join
   - POST /api/v1/rooms/{room_id}/leave
   - POST /api/v1/rooms/{room_id}/gifts
   - GET /api/v1/rooms/{room_id}

7. **Creator Dashboard APIs** (NEW) - 5 endpoints
   - GET /api/v1/creator/dashboard
   - GET /api/v1/creator/earnings
   - GET /api/v1/creator/rates
   - PUT /api/v1/creator/rates
   - POST /api/v1/creator/withdraw

8. **Matching APIs** (NEW from Section 7) - 2 endpoints
   - GET /api/v1/matching/preferences
   - PUT /api/v1/matching/preferences

9. **Referrals APIs** (NEW from Section 7) - 3 endpoints
   - GET /api/v1/referrals
   - POST /api/v1/referrals/code
   - POST /api/v1/referrals/apply

10. **Verification APIs** (NEW from Section 7) - 3 endpoints
    - GET /api/v1/verification/status
    - POST /api/v1/verification/upload
    - GET /api/v1/verification/tiers

11. **Notifications APIs** (NEW from Section 7) - 2 endpoints
    - GET /api/v1/notifications
    - PUT /api/v1/notifications/{notification_id}/mark-read

12. **Admin APIs** (NEW) - 4 endpoints
    - GET /api/v1/admin/users
    - GET /api/v1/admin/moderation
    - POST /api/v1/admin/users/{user_id}/suspend
    - GET /api/v1/admin/reports

**Timeline Impact:** +2-3 weeks to MVP for comprehensive API development

**UPDATED MVP TIMELINE: 22-29 weeks** (was 20-26 weeks)

**Impact:** Complete API coverage for all 22 tables, enables full-featured mobile app development.

---

### Q2: REVENUE SHARE CALCULATION CORRECTION

**Context:** PRD line 1846 shows `CREATOR_SHARE = Decimal("0.45")` (45%), but REQ-RM-001 specifies 75-85% tiered revenue share. This is a CRITICAL financial error.

**Question:** How should revenue calculations be corrected?

**Options Presented:**
- Option A: Dynamic Tiered Calculation (Correct, Complex) - DB lookup per transaction
- Option B: Simplified 75% for MVP (Quick Fix) - Defers tiers to post-MVP
- Option C: Cached Tier with Fallback (Balanced) - Redis cache with daily updates

**Answer:** **Dynamic tiered calculation (Correct implementation)**

**Implementation:**
```python
def calculate_creator_earnings(
    creator_id: UUID,
    coins_spent: Decimal,
    coin_to_inr: Decimal
) -> Tuple[Decimal, Decimal]:
    """
    Calculate creator earnings using dynamic tiered revenue share (75-85%)
    based on trailing 30-day performance from creator_performance table.

    Returns: (creator_earnings, tier_percent)
    """
    # Query creator performance tier from database
    performance = db.query(CreatorPerformance).filter(
        CreatorPerformance.user_id == creator_id
    ).first()

    # Determine tier percentage based on monthly earnings
    if not performance:
        tier_percent = Decimal("0.75")  # Default: Tier 1 (75%)
    elif performance.total_earnings_30d >= Decimal("200000"):  # ₹2L+ per month
        tier_percent = Decimal("0.85")  # Tier 3 (85% - top 5%)
    elif performance.total_earnings_30d >= Decimal("50000"):   # ₹50K+ per month
        tier_percent = Decimal("0.80")  # Tier 2 (80% - top 20%)
    else:
        tier_percent = Decimal("0.75")  # Tier 1 (75% - all others)

    # Calculate earnings
    gross_amount = coins_spent * coin_to_inr
    creator_earnings = gross_amount * tier_percent
    platform_earnings = gross_amount - creator_earnings

    return creator_earnings, tier_percent
```

**Tier Thresholds:**
- Tier 1 (75%): Default, <₹50K monthly earnings
- Tier 2 (80%): ₹50K-200K monthly earnings (top 20% performers)
- Tier 3 (85%): ₹200K+ monthly earnings (top 5% performers)

**Performance Consideration:** Database lookup per transaction (call/gift), but necessary for accurate tiered revenue sharing. Performance impact acceptable (<5ms query with proper indexing from REQ-DB-003).

**Impact:**
- **FIXES CRITICAL 45% ERROR** from PRD
- Implements REQ-RM-001 and REQ-ES-004 accurately
- Affects all revenue calculations (calls, gifts, rooms)
- Requires creator_performance table (added in Section 7)

---

### Q3: MULTI-LANGUAGE API RESPONSE SUPPORT

**Context:** Section 7 defines JSONB i18n columns for 5 languages (Tamil, Telugu, Kannada, Hindi, English), but PRD APIs don't show i18n support.

**Question:** How should API responses handle multi-language content?

**Options Presented:**
- Option A: Accept-Language Header (Standard) - HTTP header-based
- Option B: Query Parameter (Simple) - `?lang=ta`
- Option C: User Profile Language (Automatic) - From user.language field

**Answer:** **Accept-Language header (Standard, Recommended)**

**Implementation:**
```python
from fastapi import Request

async def parse_language(request: Request) -> str:
    """
    Parse Accept-Language header, default to English

    Examples:
    - "ta-IN" -> "ta"
    - "en-US,ta;q=0.9" -> "en" (primary language)
    - None -> "en" (default)
    """
    accept_lang = request.headers.get("Accept-Language", "en")

    # Extract primary language code (e.g., "ta-IN" -> "ta")
    lang = accept_lang.split(",")[0].split("-")[0][:2].lower()

    # Validate against supported languages
    if lang not in ["ta", "te", "kn", "hi", "en"]:
        return "en"

    return lang

@router.get("/api/v1/gifts")
async def get_gifts(request: Request, db: Session = Depends(get_db)):
    lang = await parse_language(request)

    gifts = db.query(Gift).all()

    return [
        {
            "id": str(g.id),
            "name": g.names.get(lang, g.names.get("en")),  # JSONB lookup with fallback
            "description": g.descriptions.get(lang, g.descriptions.get("en")),
            "coin_price": float(g.coin_price),
            "diamond_value": float(g.diamond_value),
            "category": g.category
        }
        for g in gifts
    ]
```

**Endpoints Requiring i18n:**
- GET /api/v1/gifts (gift names, descriptions)
- GET /api/v1/wallet/packages (coin package names, descriptions)
- GET /api/v1/subscriptions/plans (subscription names, features)
- All error messages (via system_messages table)

**Impact:**
- Standard REST API practice (Accept-Language header)
- Client controls language preference
- Fallback to English if translation missing
- Consistent with REQ-DB-005 (JSONB i18n columns)

---

## PHASE 2: FOLLOW-UP QUESTIONS

### Q4: API DOCUMENTATION AND OPENAPI SCHEMA

**Context:** With 30-40 comprehensive endpoints, API documentation becomes critical for frontend developers.

**Question:** What level of API documentation is needed?

**Options Presented:**
- Option A: Basic Auto-Generated (Minimal Effort) - FastAPI default Swagger
- Option B: Enhanced OpenAPI with Examples (Recommended) - Rich documentation
- Option C: External Documentation Platform (Enterprise) - Postman/ReadMe.io

**Answer:** **Enhanced OpenAPI with examples (Recommended)**

**Implementation:**
```python
@router.post(
    "/api/v1/gifts/send",
    summary="Send gift to user",
    description="Send a virtual gift to another user during call, room, or from profile",
    response_description="Gift transaction details with updated balances",
    responses={
        200: {
            "description": "Gift sent successfully",
            "content": {
                "application/json": {
                    "example": {
                        "transaction_id": "123e4567-e89b-12d3-a456-426614174000",
                        "gift_name": "Rose",
                        "receiver_name": "Priya",
                        "coins_spent": 10,
                        "diamonds_earned": 7.50,
                        "new_coin_balance": 490,
                        "created_at": "2026-01-20T10:30:00Z"
                    }
                }
            }
        },
        400: {
            "description": "Insufficient coins or invalid gift",
            "content": {
                "application/json": {
                    "example": {
                        "error_code": "error_insufficient_coins",
                        "message": "You need 10 coins to send this gift",
                        "timestamp": "2026-01-20T10:30:00Z"
                    }
                }
            }
        },
        404: {"description": "Receiver not found"}
    },
    tags=["Gifts"]
)
async def send_gift(request: SendGiftRequest, ...):
    """
    Send a virtual gift to another user.

    **Flow:**
    1. Validate gift exists and is active
    2. Check sender has sufficient coins
    3. Check receiver is not blocked
    4. Deduct coins from sender
    5. Credit diamonds to receiver (75-85% based on tier)
    6. Create gift transaction record
    7. Send push notification to receiver

    **Coin Calculation:**
    - Gift price: Based on gift.coin_price
    - Creator earnings: coins × tier_percent (75-85%)
    - Platform fee: Remaining amount

    **Rate Limits:** 50 gifts per hour per user
    """
    ...
```

**Documentation Requirements:**
- Write comprehensive docstrings for all 30-40 endpoints
- Provide example request/response JSON for each endpoint
- Document all error cases (400, 401, 404, 429, 500)
- Include authentication requirements
- Document rate limit headers
- Show i18n examples (Accept-Language header usage)

**Access:**
- Swagger UI available at `/docs`
- ReDoc alternative at `/redoc`
- OpenAPI JSON schema at `/openapi.json`

**Timeline Impact:** Minimal (documentation written during endpoint development as part of coding standards)

**Impact:** Professional API documentation enables frontend team to work in parallel, reduces integration issues.

---

### Q5: ERROR HANDLING AND I18N ERROR MESSAGES

**Context:** Error messages also need localization for 5 languages.

**Question:** How should error handling be standardized?

**Options Presented:**
- Option A: Error Codes + system_messages Table (Recommended) - Database-backed i18n
- Option B: JSON Error Response Files (Static) - File-based i18n
- Option C: Hybrid (Critical in DB, Generic in code) - Mixed approach

**Answer:** **Error codes + system_messages table (Recommended)**

**Implementation:**
```python
from fastapi import HTTPException, Request
from datetime import datetime

class APIError(HTTPException):
    """
    Standardized API error with i18n support via system_messages table
    """
    def __init__(
        self,
        error_code: str,
        status_code: int = 400,
        params: dict = None,
        lang: str = "en"
    ):
        # Lookup localized message from system_messages table
        message_obj = db.query(SystemMessage).filter(
            SystemMessage.message_key == error_code
        ).first()

        if message_obj:
            # Get localized message with fallback to English
            message = message_obj.messages.get(lang, message_obj.messages.get("en"))

            # Replace parameters in message (e.g., "Minimum {amount} coins required")
            if params:
                message = message.format(**params)
        else:
            # Fallback to error code if message not found
            message = error_code

        # Structured error response
        detail = {
            "error_code": error_code,
            "message": message,
            "timestamp": datetime.utcnow().isoformat(),
            "params": params or {}
        }

        super().__init__(status_code=status_code, detail=detail)

# Middleware to inject language into error handling
async def error_handler_middleware(request: Request, call_next):
    try:
        return await call_next(request)
    except APIError as e:
        # Re-raise with language from request
        lang = await parse_language(request)
        raise APIError(e.error_code, e.status_code, lang=lang)

# Usage examples
raise APIError("error_insufficient_coins", 400, params={"required": 100, "current": 50})
# Response (English): {"error_code": "error_insufficient_coins", "message": "You need 100 coins. Current balance: 50"}
# Response (Tamil): {"error_code": "error_insufficient_coins", "message": "உங்களுக்கு 100 நாணயங்கள் தேவை. தற்போதைய இருப்பு: 50"}

raise APIError("error_user_not_found", 404)
raise APIError("error_blocked_user", 400)
raise APIError("error_rate_limit_exceeded", 429, params={"retry_after": 60})
```

**Seed Data for system_messages Table:**
```sql
INSERT INTO system_messages (message_key, messages, category) VALUES
('error_insufficient_coins',
 '{"en": "Insufficient coins. You need {required} coins. Current balance: {current}",
   "ta": "போதிய நாணயங்கள் இல்லை. உங்களுக்கு {required} நாணயங்கள் தேவை. தற்போதைய இருப்பு: {current}",
   "te": "తగిన నాణేలు లేవు. మీకు {required} నాణేలు అవసరం. ప్రస్తుత బ్యాలెన్స్: {current}",
   "kn": "ಸಾಕಷ್ಟು ನಾಣ್ಯಗಳಿಲ್ಲ. ನಿಮಗೆ {required} ನಾಣ್ಯಗಳು ಬೇಕು. ಪ್ರಸ್ತುತ ಸಮತೋಲನ: {current}",
   "hi": "अपर्याप्त सिक्के। आपको {required} सिक्कों की आवश्यकता है। वर्तमान शेष: {current}"}'::jsonb,
 'error'),

('error_user_not_found',
 '{"en": "User not found",
   "ta": "பயனர் கண்டுபிடிக்கப்படவில்லை",
   "te": "వినియోగదారు కనుగొనబడలేదు",
   "kn": "ಬಳಕೆದಾರರು ಕಂಡುಬಂದಿಲ್ಲ",
   "hi": "उपयोगकर्ता नहीं मिला"}'::jsonb,
 'error'),

('error_blocked_user',
 '{"en": "You cannot interact with this user",
   "ta": "இந்த பயனருடன் தொடர்பு கொள்ள முடியாது",
   "te": "మీరు ఈ వినియోగదారుతో పరస్పర చర్య చేయలేరు",
   "kn": "ನೀವು ಈ ಬಳಕೆದಾರರೊಂದಿಗೆ ಸಂವಹನ ನಡೆಸಲು ಸಾಧ್ಯವಿಲ್ಲ",
   "hi": "आप इस उपयोगकर्ता के साथ बातचीत नहीं कर सकते"}'::jsonb,
 'error'),

('error_rate_limit_exceeded',
 '{"en": "Rate limit exceeded. Please try again in {retry_after} seconds",
   "ta": "விகித வரம்பு மீறப்பட்டது. {retry_after} நொடிகளில் மீண்டும் முயற்சிக்கவும்",
   "te": "రేటు పరిమితి మించిపోయింది. దయచేసి {retry_after} సెకన్లలో మళ్లీ ప్రయత్నించండి",
   "kn": "ದರ ಮಿತಿ ಮೀರಿದೆ. ದಯವಿಟ್ಟು {retry_after} ಸೆಕೆಂಡುಗಳಲ್ಲಿ ಮತ್ತೆ ಪ್ರಯತ್ನಿಸಿ",
   "hi": "दर सीमा पार हो गई। कृपया {retry_after} सेकंड में पुनः प्रयास करें"}'::jsonb,
 'error');

-- Total: ~50-100 error messages needed for comprehensive coverage
```

**Impact:**
- Database-backed error messages can be updated without deployment
- Consistent error format across all endpoints
- Full i18n support for user-facing errors
- Parameterized messages for dynamic content

---

### Q6: RATE LIMITING AND API QUOTA IMPLEMENTATION

**Context:** PRD mentions rate limiting but doesn't implement it. With 30-40 endpoints, protection against abuse is critical.

**Question:** How should rate limiting be implemented?

**Options Presented:**
- Option A: Redis-Based Rate Limiting (Recommended) - Distributed, scalable
- Option B: FastAPI-RateLimiter Library - Simple decorator-based
- Option C: API Gateway Rate Limiting (AWS/GCP) - Infrastructure-level

**Answer:** **Redis-based rate limiting (Recommended)**

**Implementation:**
```python
from fastapi import Request, HTTPException
import redis
import time

redis_client = redis.Redis(host='localhost', port=6379, decode_responses=True)

# Rate limit configuration
RATE_LIMITS = {
    "default": (100, 60),          # 100 requests per 60 seconds
    "/api/v1/auth/send-otp": (5, 3600),      # 5 OTP per hour
    "/api/v1/wallet/withdraw": (10, 86400),  # 10 withdrawals per day
    "/api/v1/gifts/send": (50, 3600),        # 50 gifts per hour
    "/api/v1/users/me/photo": (10, 3600),    # 10 photo uploads per hour
}

async def rate_limit_middleware(request: Request, call_next):
    """
    Redis-based distributed rate limiting middleware

    Limits:
    - Per-user: Based on JWT user_id
    - Per-IP: For unauthenticated requests
    - Per-endpoint: Custom limits for sensitive operations
    """
    # Get identifier (user_id from JWT or IP address)
    try:
        user_id = get_user_id_from_jwt(request)
        identifier = f"user:{user_id}"
    except:
        identifier = f"ip:{request.client.host}"

    # Get endpoint-specific limit or default
    endpoint = request.url.path
    limit, window = RATE_LIMITS.get(endpoint, RATE_LIMITS["default"])

    # Redis key with time window
    timestamp = int(time.time() // window)
    key = f"rate_limit:{identifier}:{endpoint}:{timestamp}"

    # Increment counter
    count = redis_client.incr(key)
    redis_client.expire(key, window)

    # Check limit
    if count > limit:
        retry_after = window - (int(time.time()) % window)
        raise HTTPException(
            status_code=429,
            detail={
                "error_code": "error_rate_limit_exceeded",
                "message": f"Rate limit exceeded. Try again in {retry_after} seconds",
                "retry_after": retry_after
            },
            headers={"Retry-After": str(retry_after)}
        )

    # Process request
    response = await call_next(request)

    # Add rate limit headers
    response.headers["X-RateLimit-Limit"] = str(limit)
    response.headers["X-RateLimit-Remaining"] = str(limit - count)
    response.headers["X-RateLimit-Reset"] = str((timestamp + 1) * window)

    return response

# Register middleware
app.middleware("http")(rate_limit_middleware)
```

**Rate Limit Configuration:**
- **Default**: 100 requests/minute per user
- **Per-IP (unauthenticated)**: 1000 requests/minute (for public endpoints)
- **Special Limits**:
  - OTP: 5/hour (prevent SMS spam)
  - Withdrawals: 10/day (financial security)
  - Gift sending: 50/hour (prevent spam)
  - Photo uploads: 10/hour (prevent abuse)

**Rate Limit Headers:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87
X-RateLimit-Reset: 1705756800
Retry-After: 45
```

**Impact:**
- Prevents API abuse and DDoS attacks
- Distributed rate limiting across multiple API servers (via Redis)
- Per-endpoint granular control
- Standard HTTP headers for client feedback

---

## PHASE 3: GAP ANALYSIS QUESTIONS

### Q7: WEBHOOK ENDPOINTS AND EXTERNAL INTEGRATIONS

**Context:** PRD shows Razorpay integration but doesn't define webhook endpoints for external services.

**Question:** What webhook/callback infrastructure is needed?

**Options Presented:**
- Option A: Comprehensive Webhook Endpoints (Recommended) - All services
- Option B: Minimal Webhooks (Critical Only) - Razorpay only
- Option C: No Webhooks (Polling-Based) - Poll APIs instead

**Answer:** **Comprehensive webhook endpoints (Recommended)**

**Webhooks to Implement:**

**1. Razorpay Webhooks** (`POST /api/v1/webhooks/razorpay`):
```python
@router.post("/api/v1/webhooks/razorpay")
async def razorpay_webhook(request: Request, db: Session = Depends(get_db)):
    """
    Handle Razorpay payment webhooks

    Events:
    - payment.captured: Payment successful
    - payment.failed: Payment failed
    - refund.created: Refund processed
    - subscription.charged: Subscription renewal (if VIP)

    Security:
    - Verify webhook signature (X-Razorpay-Signature header)
    - Idempotency check (prevent duplicate processing)
    - IP whitelisting (Razorpay webhook IPs)
    """
    # Verify signature
    signature = request.headers.get("X-Razorpay-Signature")
    payload = await request.body()

    expected_signature = hmac.new(
        RAZORPAY_WEBHOOK_SECRET.encode(),
        payload,
        hashlib.sha256
    ).hexdigest()

    if signature != expected_signature:
        raise HTTPException(401, "Invalid webhook signature")

    # Parse event
    event = await request.json()
    event_type = event.get("event")
    event_id = event.get("payload", {}).get("payment", {}).get("entity", {}).get("id")

    # Idempotency check
    if redis_client.exists(f"webhook:razorpay:{event_id}"):
        return {"status": "already_processed"}

    redis_client.setex(f"webhook:razorpay:{event_id}", 86400, "processed")  # 24h expiry

    # Handle different event types
    if event_type == "payment.captured":
        await handle_payment_success(event, db)
    elif event_type == "payment.failed":
        await handle_payment_failure(event, db)
    elif event_type == "refund.created":
        await handle_refund(event, db)

    return {"status": "processed"}
```

**2. Agora Webhooks** (`POST /api/v1/webhooks/agora`):
```python
@router.post("/api/v1/webhooks/agora")
async def agora_webhook(request: Request, db: Session = Depends(get_db)):
    """
    Handle Agora call quality and analytics webhooks

    Events:
    - channel.event: Call quality events
    - recording.completed: Call recording ready (if enabled)
    - network.quality: Network quality degradation

    Use cases:
    - Alert creators about poor call quality
    - Store call analytics for performance tracking
    - Trigger refunds for dropped calls
    """
    event = await request.json()
    event_type = event.get("eventType")

    if event_type == "network.quality":
        # Log network quality issues
        await log_call_quality_issue(event, db)
    elif event_type == "recording.completed":
        # Store recording URL (if enabled)
        await save_call_recording(event, db)

    return {"status": "processed"}
```

**3. FCM Webhooks** (`POST /api/v1/webhooks/fcm`):
```python
@router.post("/api/v1/webhooks/fcm")
async def fcm_webhook(request: Request, db: Session = Depends(get_db)):
    """
    Handle Firebase Cloud Messaging delivery receipts

    Events:
    - message.delivered: Notification delivered to device
    - message.clicked: User clicked notification
    - token.refresh: FCM token needs update

    Use cases:
    - Track notification delivery rates
    - Update FCM tokens
    - Measure notification engagement
    """
    event = await request.json()
    event_type = event.get("eventType")

    if event_type == "message.delivered":
        # Update notification status
        await update_notification_status(event["notificationId"], "delivered", db)
    elif event_type == "message.clicked":
        # Track engagement
        await update_notification_status(event["notificationId"], "clicked", db)
    elif event_type == "token.refresh":
        # Update FCM token
        await update_fcm_token(event["userId"], event["newToken"], db)

    return {"status": "processed"}
```

**Webhook Security:**
- **Signature Verification**: All webhooks verify signatures (Razorpay, Agora)
- **Idempotency**: Redis-backed idempotency keys prevent duplicate processing
- **IP Whitelisting**: Only accept webhooks from known IPs
- **Rate Limiting**: Webhook-specific rate limits (1000/min per service)

**Impact:**
- Reliable payment processing (immediate confirmation via webhooks vs. polling)
- Call quality monitoring and analytics
- Notification delivery tracking
- Critical for production-grade payment and communication systems

---

### Q8: PAGINATION AND FILTERING STANDARDS

**Context:** PRD shows inconsistent pagination. Need standards for 30-40 list endpoints.

**Question:** What pagination and filtering approach should be used?

**Options Presented:**
- Option A: Cursor-Based Pagination (REQ-DB-003 Recommendation) - Better performance
- Option B: Offset-Based Pagination (Simple) - Familiar pattern
- Option C: Hybrid (Cursor for Real-Time, Offset for Static) - Best of both

**Answer:** **Hybrid: Cursor for real-time, offset for static**

**Pagination Strategy:**

**Cursor Pagination (Real-Time Data):**
```python
@router.get("/api/v1/calls/history")
async def get_call_history(
    cursor: str = None,  # Base64 encoded: {last_id}:{last_timestamp}
    limit: int = 20,
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """
    Get call history with cursor pagination

    Cursor format: base64("{uuid}:{timestamp}")
    Example: "MTIzZTQ1NjctZTg5Yi0xMmQzLWE0NTYtNDI2NjE0MTc0MDAwOjIwMjYtMDEtMjBUMTA6MzA6MDAuMDAwWg=="
    """
    query = db.query(Call).filter(
        (Call.caller_id == user_id) | (Call.receiver_id == user_id)
    )

    if cursor:
        # Decode cursor
        decoded = base64.b64decode(cursor).decode()
        last_id, last_timestamp = decoded.split(":")

        # Filter after last seen item
        query = query.filter(
            (Call.created_at < last_timestamp) |
            ((Call.created_at == last_timestamp) & (Call.id < last_id))
        )

    # Fetch limit + 1 to check if more results
    calls = query.order_by(Call.created_at.desc()).limit(limit + 1).all()

    # Check if more results
    has_more = len(calls) > limit
    if has_more:
        calls = calls[:-1]

    # Generate next cursor
    next_cursor = None
    if has_more:
        last_item = calls[-1]
        cursor_data = f"{last_item.id}:{last_item.created_at.isoformat()}"
        next_cursor = base64.b64encode(cursor_data.encode()).decode()

    return {
        "data": [serialize_call(c) for c in calls],
        "pagination": {
            "next_cursor": next_cursor,
            "has_more": has_more,
            "limit": limit
        }
    }
```

**Endpoints Using Cursor Pagination:**
- GET /api/v1/calls/history (frequently updated)
- GET /api/v1/wallet/transactions (real-time)
- GET /api/v1/notifications (real-time feed)
- GET /api/v1/gifts/received (dynamic)
- GET /api/v1/rooms/{room_id}/participants (dynamic)

**Offset Pagination (Static Data):**
```python
@router.get("/api/v1/gifts")
async def get_gifts(
    page: int = 1,
    page_size: int = 20,
    request: Request = None,
    db: Session = Depends(get_db)
):
    """
    Get gift catalog with offset pagination

    Static data (changes infrequently), offset pagination acceptable.
    """
    lang = await parse_language(request)

    offset = (page - 1) * page_size

    total = db.query(func.count(Gift.id)).filter(Gift.is_active == True).scalar()
    gifts = db.query(Gift).filter(
        Gift.is_active == True
    ).order_by(Gift.sort_order).offset(offset).limit(page_size).all()

    return {
        "data": [
            {
                "id": str(g.id),
                "name": g.names.get(lang, g.names.get("en")),
                "coin_price": float(g.coin_price),
                ...
            }
            for g in gifts
        ],
        "pagination": {
            "page": page,
            "page_size": page_size,
            "total_pages": (total + page_size - 1) // page_size,
            "total_items": total
        }
    }
```

**Endpoints Using Offset Pagination:**
- GET /api/v1/gifts (static catalog)
- GET /api/v1/wallet/packages (static, small)
- GET /api/v1/subscriptions/plans (static, small)
- GET /api/v1/referrals (mostly static)
- GET /api/v1/admin/users (admin, less frequent)

**Filtering Standards:**
All list endpoints support common filters via query parameters:

```python
@router.get("/api/v1/wallet/transactions")
async def get_transactions(
    # Pagination
    cursor: str = None,
    limit: int = 20,

    # Filters
    type: str = None,  # "recharge", "spend_call", "earn_gift", etc.
    created_after: datetime = None,
    created_before: datetime = None,
    status: str = None,  # "pending", "completed", "failed"

    # Auth
    user_id: str = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    query = db.query(Transaction).filter(Transaction.user_id == user_id)

    # Apply filters
    if type:
        query = query.filter(Transaction.type == type)
    if created_after:
        query = query.filter(Transaction.created_at >= created_after)
    if created_before:
        query = query.filter(Transaction.created_at <= created_before)
    if status:
        query = query.filter(Transaction.status == status)

    # ... cursor pagination logic ...
```

**Impact:**
- Optimal performance for each use case (cursor for real-time, offset for static)
- Consistent filter patterns across all endpoints
- RESTful query parameter conventions

---

### Q9: API VERSIONING STRATEGY

**Context:** PRD shows `/api/v1/` but doesn't define versioning strategy for future breaking changes.

**Question:** How should API versioning be managed?

**Options Presented:**
- Option A: URL-Based Versioning (Current) - `/api/v1/`, `/api/v2/`
- Option B: Header-Based Versioning - `API-Version: v1` header
- Option C: Backward-Compatible Evolution (No Versioning) - Only additive changes

**Answer:** **URL-based versioning /api/v1 (Recommended)**

**Versioning Implementation:**
```python
# app/api/v1/routes.py
from fastapi import APIRouter

router_v1 = APIRouter(prefix="/api/v1", tags=["v1"])

# Include all v1 routers
router_v1.include_router(auth_router)
router_v1.include_router(wallet_router)
router_v1.include_router(calls_router)
# ... all other routers

# app/api/v2/routes.py (future, when breaking changes needed)
router_v2 = APIRouter(prefix="/api/v2", tags=["v2"])

# app/main.py
from api.v1 import routes as v1_routes
# from api.v2 import routes as v2_routes  # Uncomment when v2 ready

app.include_router(v1_routes.router_v1)
# app.include_router(v2_routes.router_v2)  # v2 support
```

**Versioning Policy:**
1. **Current Version**: `/api/v1/` (MVP and initial launch)
2. **Deprecation Period**: v1 supported for minimum 6 months after v2 launch
3. **Sunset Header**: v1 endpoints return `Sunset: Sat, 31 Dec 2026 23:59:59 GMT` header when deprecated
4. **Migration Guide**: Comprehensive v1 → v2 migration documentation
5. **Breaking Changes Only**: v2 only if breaking changes needed (field removals, type changes, behavior changes)

**When to Version:**
- **Breaking Changes** (requires new version):
  - Removing fields from response
  - Changing field data types
  - Changing endpoint behavior (e.g., authentication requirement changes)
  - Changing error response format

- **Non-Breaking Changes** (same version):
  - Adding optional fields to request
  - Adding new fields to response
  - Adding new endpoints
  - Adding optional query parameters

**Deprecation Process:**
```python
# Add deprecation warning to v1 endpoints when v2 is ready
@router_v1.get("/gifts")
async def get_gifts_v1(response: Response, ...):
    # Add Sunset header
    response.headers["Sunset"] = "Sat, 31 Dec 2026 23:59:59 GMT"
    response.headers["Deprecation"] = "true"
    response.headers["Link"] = '</api/v2/gifts>; rel="successor-version"'

    # Return v1 response
    ...
```

**Impact:**
- Clear versioning strategy for future evolution
- 6-month deprecation period protects existing integrations
- Standard HTTP headers communicate deprecation
- Plan v2 router structure now, implement when needed

---

## SUMMARY OF DECISIONS

### Critical Technical Decisions
1. **API Coverage**: Comprehensive (30-40 endpoints across 12 categories)
2. **Revenue Calculation**: Dynamic tiered (75-85% from creator_performance table) - FIXES CRITICAL 45% ERROR
3. **i18n**: Accept-Language header with JSONB lookups (5 languages)
4. **Documentation**: Enhanced OpenAPI with examples for all endpoints
5. **Error Handling**: Error codes + system_messages table (database-backed i18n)
6. **Rate Limiting**: Redis-based distributed rate limiting (per-user, per-IP, per-endpoint)
7. **Webhooks**: Comprehensive (Razorpay, Agora, FCM with signature verification)
8. **Pagination**: Hybrid (cursor for real-time, offset for static)
9. **Versioning**: URL-based /api/v1 with 6-month deprecation policy

### Timeline Impact
- **Original MVP**: 20-26 weeks
- **Comprehensive API Addition**: +2-3 weeks (30-40 endpoints)
- **Updated MVP**: 22-29 weeks

### API Statistics
- **Total Endpoints**: 30-40 comprehensive RESTful endpoints
- **Webhook Endpoints**: 3 (Razorpay, Agora, FCM)
- **Categories**: 12 (Auth, Wallet, Calls, Users, Gifts, Rooms, Creator, Matching, Referrals, Verification, Notifications, Admin)
- **Error Messages**: ~50-100 in system_messages table (5 languages)
- **Rate Limits**: 4 tiers (default, OTP, withdrawals, gifts)

### Dependencies Created
- Section 7 (Database Schema): All 22 tables accessed via API endpoints
- Section 11 (N8N Workflows): Webhook processing, error message management
- Section 14 (Cost Estimation): API infrastructure costs (Redis rate limiting, webhook processing)

---

**Next Steps:**
- Extract comprehensive API requirements (18-20 requirements)
- Define complete endpoint specifications
- Document request/response schemas
- Specify authentication and authorization requirements
- Detail rate limiting configuration
- Define webhook processing logic

---

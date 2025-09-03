# API_GUIDE.md

## 🌐 API Overview

**API Version:** v1.0 (Current), v2.0 (In Development)
**Authentication:** Laravel Sanctum (Token-based)
**Response Format:** JSON:API Specification
**Rate Limiting:** User level-based throttling

## Update: Sanctum Authentication Integration

### 🔑 Sanctum Token-Based Authentication

All API endpoints (except public ones) require authentication via Bearer tokens issued by Laravel Sanctum.

```
// Authentication Flow
POST /api/v1/auth/login
{
  "email": "user@example.com",
  "password": "password123"
}

Response:
{
  "data": {
    "type": "authentication",
    "attributes": {
      "access_token": "1|sanctum-token-here...",
      "token_type": "Bearer",
      "expires_in": 3600,
      "abilities": ["courses:read", "payments:process"]
    }
  },
  "included": [
    {
      "type": "user",
      "id": "uuid-here",
      "attributes": {
        "name": "John Doe",
        "email": "user@example.com",
        "level": 5,
        "points": 1250
      }
    }
  ]
}

// Using the token
Authorization: Bearer 1|sanctum-token-here...
```

**Token Abilities & Scopes:**

| Ability | Description | Required Level |
|---------|-------------|----------------|
| courses:read | View course content | 1+ |
| courses:write | Create/modify courses | 7+ |
| payments:process | Process payments | 1+ |
| affiliates:manage | Manage affiliate program | 4+ |

### 💳 Payment API Integration

```
// Stripe Payment Processing
POST /api/v1/payments/stripe
{
  "course_id": "course-uuid",
  "payment_method": "pm_card_visa",
  "currency": "USD",
  "amount": 29.99
}

// Binance Crypto Payment
POST /api/v1/payments/binance
{
  "course_id": "course-uuid",
  "currency": "USDT",
  "amount": 30.00,
  "network": "BSC"
}

Response:
{
  "data": {
    "type": "payment-intent",
    "id": "payment-uuid",
    "attributes": {
      "status": "pending",
      "amount": 29.99,
      "currency": "USD",
      "provider": "stripe",
      "expires_at": "2024-01-01T01:00:00Z"
    },
    "meta": {
      "payment_url": "https://checkout.stripe.com/...",
      "qr_code": "data:image/png;base64,..."
    }
  }
}
```

### 📊 Enhanced API Resources

```
// Spatie Query Builder Integration
GET /api/v1/courses?filter[status]=published&filter[level]=1-5&include=author,episodes&sort=-created_at

// Spatie Permission Integration  
GET /api/v1/user/permissions
{
  "data": {
    "type": "user-permissions",
    "attributes": {
      "roles": ["student", "affiliate"],
      "permissions": [
        "courses.read",
        "affiliate.manage", 
        "payments.process"
      ],
      "level_permissions": {
        "current_level": 5,
        "unlocked_features": [
          "premium_courses",
          "affiliate_program",
          "advanced_analytics"
        ]
      }
    }
  }
}
```

## 📊 Response Format

All API responses follow the JSON:API specification for consistent structure and error handling.

```
// Success Response Structure
{
  "data": {
    "type": "courses",
    "id": "course-uuid",
    "attributes": {
      "title": "Course Title",
      "description": "Course description...",
      "status": "published",
      "created_at": "2024-01-01T00:00:00Z"
    },
    "relationships": {
      "episodes": {
        "data": [
          {"type": "episodes", "id": "episode-1-uuid"},
          {"type": "episodes", "id": "episode-2-uuid"}
        ]
      }
    }
  },
  "included": [...],
  "meta": {
    "pagination": {
      "current_page": 1,
      "total_pages": 5,
      "total_items": 48
    }
  }
}
```

## 🚦 Rate Limiting

**User Level-Based Throttling:**

| User Level | Requests/Minute | Requests/Hour | Concurrent |
|------------|----------------|---------------|------------|
| Anonymous | 20 | 200 | 2 |
| Level 1-3 | 60 | 1,000 | 5 |
| Level 4-6 | 120 | 2,500 | 10 |
| Level 7-10 | 300 | 10,000 | 20 |
| Level 11-14 | 600 | 25,000 | 50 |

## 🛠️ Error Handling

```
// Error Response Structure
{
  "errors": [
    {
      "id": "validation-error",
      "status": "422",
      "code": "VALIDATION_FAILED",
      "title": "Validation Error",
      "detail": "The email field is required.",
      "source": {
        "pointer": "/data/attributes/email"
      }
    }
  ]
}
```

**Standard HTTP Status Codes:**
- **200 OK:** Successful GET, PUT
- **201 Created:** Successful POST
- **401 Unauthorized:** Authentication required
- **403 Forbidden:** Access denied
- **422 Unprocessable Entity:** Validation errors
- **429 Too Many Requests:** Rate limit exceeded

## 📖 API Documentation

- **Swagger UI:** /api/documentation
- **Postman Collection:** Available for download
- **OpenAPI Spec:** /api/openapi.json
- **SDK Examples:** PHP, JavaScript, Python

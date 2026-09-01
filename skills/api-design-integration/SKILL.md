---
name: api-design-integration
description: Designing, building, and documenting RESTful APIs, JSON endpoints, webhook handlers, and testing with Bruno or Postman.
---

# API Design & Integration Skill

Use this skill when developing REST endpoints, integrating external APIs/webhooks, or testing API responses.

## 1. REST Endpoint Conventions

- **Resource-Oriented URIs**:
  - `GET /api/v1/products` — List products
  - `GET /api/v1/products/{id}` — Retrieve specific product
  - `POST /api/v1/orders` — Create new order
  - `PATCH /api/v1/orders/{id}` — Partially update order
  - `DELETE /api/v1/cart/items/{id}` — Remove item
- **HTTP Status Codes**:
  - `200 OK` — Successful retrieval or update
  - `201 Created` — Successful resource creation (include `Location` header)
  - `204 No Content` — Successful deletion
  - `400 Bad Request` — Validation or formatting errors
  - `401 Unauthorized` — Missing or invalid authentication token
  - `403 Forbidden` — Authenticated but lack permission
  - `404 Not Found` — Resource does not exist
  - `422 Unprocessable Entity` — Business rule validation failure

## 2. Standardized JSON Envelope

```json
{
  "status": "success",
  "data": {
    "id": 123,
    "name": "Chardonnay Reserva",
    "price": 14.50
  },
  "meta": {
    "timestamp": "2026-09-01T23:50:00Z"
  }
}
```

Error response format:
```json
{
  "status": "error",
  "message": "Validation failed",
  "errors": [
    {
      "field": "email",
      "message": "The email address is invalid."
    }
  ]
}
```

## 3. Testing with Bruno / Postman

- Store API request collections in `.bruno/` or `tests/api/`.
- Ensure environment variables for base URLs and API bearer tokens are used rather than hardcoded credentials.

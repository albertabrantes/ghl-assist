# Store API — Auth, Errors & Gotchas

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Authentication

All requests require:

```
Authorization: Bearer {access_token}
Version: 2021-07-28
```

> **Version Header Note:** The Store API OpenAPI spec does NOT explicitly include a `Version` header parameter on its endpoints — only `Authorization` is listed. However, include `Version: 2021-07-28` for consistency, as this is GHL's standard API versioning header.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| *(empty)* | *(see note)* | Store API endpoints have empty scope arrays (`[]`) in the OpenAPI spec |

> **Scope Note:** The Store API endpoints in the OpenAPI spec have **empty scope arrays** on their Location-Access security requirements. This likely means the Store API scopes are either inherited from the Products API scopes or are not yet finalized in the spec. When in doubt, ensure your OAuth app requests `products.write` and `products.readonly` scopes.

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 400 | Bad Request — invalid parameters or request body |
| 401 | Unauthorized — missing, expired, or invalid access token |
| 422 | Unprocessable Entity — request understood but validation failed |
| 429 | Rate Limited — exceeded 100 req/10s or 200k req/day |

---

## Gotchas

- **Scope arrays are empty in the OpenAPI spec.** The Store API endpoints have empty scope arrays (`[]`) on their Location-Access security. Request `products.write` and `products.readonly` scopes for your OAuth app as a precaution.
- **Version header is recommended but not explicitly required.** The Store API spec only lists `Authorization` as a header parameter. However, `Version: 2021-07-28` is GHL's standard versioning header and should be included for consistency.
- **`altId` and `altType` pattern.** All Store API endpoints use `altId` (Location ID) and `altType` (always `"location"`) instead of a simple `locationId` parameter. This is consistent with the Products API pattern.
- **Carrier Rate Callback Flow:** (1) Register a carrier via `POST /store/shipping-carrier` with a `callbackUrl`. (2) Create a shipping rate with `isCarrierRate: true` referencing that carrier. (3) When a customer checks out, GHL calls your `callbackUrl` to get real-time rates. (4) The rate is surfaced via `POST /store/shipping-zone/shipping-rates`.
- **Cross-API relationship with Products API:** Product prices have `shippingOptions` with `weight` and `dimensions` that are used by the Store API's "Get Available Shipping Rates" endpoint for calculating carrier rates and weight-based conditions.
- **Supported country/state codes.** The Store API accepts ISO 3166-1 alpha-2 country codes (over 240 countries) and state/province codes for US, Canada, Australia, Brazil, Argentina, India, UK, and many more.

> **Status:** All Store endpoints are 🔬 unverified — documented from OpenAPI spec research, not tested against a live API.

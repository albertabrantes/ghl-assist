# OAuth / Authentication API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** Not required for OAuth token endpoints (required for resource endpoints)

## Overview

The OAuth API implements the **OAuth 2.0 Authorization Code Grant** flow. It supports two token levels — **Company (Agency)** and **Location (Sub-Account)** — plus **Private Integration Tokens** as a static alternative. The API has **3 endpoints**, all under `/oauth/`. The token endpoint (`/oauth/token`) doubles as both initial code exchange and refresh, so there is no separate refresh endpoint.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `oauth.readonly` | Read | Get installed locations |
| `oauth.write` | Write | Generate location access tokens |

Note: The `POST /oauth/token` endpoint does **not** require any OAuth scopes — it authenticates via `client_id` and `client_secret` in the request body.

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Access Token](#get-access-token) | POST | None | Exchange auth code or refresh token for access token |
| [Get Location Access Token](#get-location-access-token) | POST | oauth.write | Generate location token from agency token |
| [Get Installed Locations](#get-installed-locations) | GET | oauth.readonly | List locations where the app is installed |

---

## Complete OAuth 2.0 Flow

### Step 1 — Build the Authorization URL

Direct users to the authorization screen:

```
https://marketplace.gohighlevel.com/oauth/chooselocation
  ?response_type=code
  &client_id={CLIENT_ID}
  &redirect_uri={REDIRECT_URI}
  &scope={SPACE_SEPARATED_SCOPES}
  &state={CSRF_TOKEN}
```

For white-label agencies, replace the domain with `marketplace.leadconnectorhq.com`.

| Parameter | Required | Description |
|-----------|----------|-------------|
| `response_type` | Yes | Must be `code` |
| `client_id` | Yes | Your app's Client ID from the marketplace |
| `redirect_uri` | Yes | Must match a registered redirect URL |
| `scope` | Yes | Space-separated scopes (e.g., `contacts.readonly contacts.write`) |
| `state` | No | Opaque CSRF value passed through to callback |

### Step 2 — User Authorizes and GHL Redirects Back

After consent, the browser redirects to your URI with an authorization code:

```
https://yourapp.com/callback?code=7676cjcbdc6t76cdcbkjcd09821jknnkj
```

### Step 3 — Exchange the Code for Tokens

Call `POST /oauth/token` (see [Get Access Token](#get-access-token) below).

### Step 4 — Use the Access Token

Include `Authorization: Bearer {access_token}` and `Version: 2021-07-28` on all resource API calls.

### Step 5 — Refresh Before Expiration

Call `POST /oauth/token` with `grant_type=refresh_token`. The old refresh token is **immediately invalidated**; always store the new one. Refresh tokens are valid for **1 year** or until used.

---

## Private Integration Tokens vs OAuth

Private Integration Tokens are **static OAuth2 access tokens that never expire**. Created in Agency Settings > Private Integrations, they use the same `Authorization: Bearer` header as regular OAuth tokens.

| Feature | OAuth 2.0 | Private Integration Token |
|---------|-----------|---------------------------|
| Token Expiration | ~24 hours (86,400 seconds) | Never expires |
| Refresh Required | Yes | No |
| Setup Complexity | Full OAuth flow (authorization URL, callback, code exchange) | Copy/paste from Agency Settings |
| Best For | Multi-tenant apps, marketplace apps | Internal tools, single-account use |
| Scope Management | Set during authorization | Configured at creation, editable without regenerating |
| Auth Header | `Authorization: Bearer {access_token}` | `Authorization: Bearer {token}` (identical format) |

---

## Authentication

OAuth endpoints use **two different authentication methods** depending on the endpoint:

**Token endpoint (`POST /oauth/token`):**
- Does NOT use `Authorization: Bearer` header
- Authenticates via `client_id` and `client_secret` in the request body
- Does NOT require the `Version` header

**Resource endpoints (`POST /oauth/locationToken`, `GET /oauth/installedLocations`):**
- Requires `Authorization: Bearer {access_token}` header
- Requires `Version: 2021-07-28` header
- Must use an Agency-level token (`userType: "Company"`)

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

---

## Object Schemas

### Token Response Object (Company)

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 86399,
  "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "scope": "businesses.readonly businesses.write contacts.readonly contacts.write",
  "refreshTokenId": "EXAMPLErefreshtoken00001",
  "userType": "Company",
  "companyId": "EXAMPLECompany000001",
  "isBulkInstallation": true,
  "userId": "EXAMPLEUser000000001"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `access_token` | string | JWT Bearer token |
| `token_type` | string | Always `"Bearer"` |
| `expires_in` | integer | Seconds until expiration (~86,399) |
| `refresh_token` | string | JWT refresh token (valid 1 year or until used) |
| `scope` | string | Space-separated granted scopes |
| `refreshTokenId` | string | Unique ID for the refresh token |
| `userType` | string | `"Company"` for agency-level tokens |
| `companyId` | string | Agency/company ID |
| `userId` | string | Authorizing user's ID |
| `isBulkInstallation` | boolean | Whether this was a bulk install |

### Token Response Object (Location)

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 86400,
  "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "scope": "contacts.readonly contacts.write oauth.write oauth.readonly",
  "userType": "Location",
  "companyId": "EXAMPLECompany000001",
  "locationId": "EXAMPLELocation00000",
  "userId": "EXAMPLEUser000000001",
  "traceId": "8f712294-b015-42a-8c69-7fcb960560aa"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `access_token` | string | JWT Bearer token |
| `token_type` | string | Always `"Bearer"` |
| `expires_in` | integer | Seconds until expiration (~86,400) |
| `refresh_token` | string | JWT refresh token (valid 1 year or until used) |
| `scope` | string | Space-separated granted scopes |
| `userType` | string | `"Location"` for sub-account tokens |
| `companyId` | string | Agency/company ID |
| `locationId` | string | Sub-account/location ID |
| `userId` | string | Authorizing user's ID |
| `traceId` | string | UUID for request tracing |

Note: Location tokens do **not** include `refreshTokenId` or `isBulkInstallation`.

### Installed Location Object

```json
{
  "_id": "EXAMPLELocation00000",
  "name": "Acme Dental Office",
  "address": "123 Main St, Springfield, IL",
  "isInstalled": true
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Location/sub-account ID |
| `name` | string | Location name |
| `address` | string | Location address |
| `isInstalled` | boolean | Whether the app is installed at this location |

---

## Get Access Token

Exchange an authorization code for tokens, OR refresh an expired access token.

**Method:** `POST`
**URL:** `/oauth/token`
**Scope:** None (uses client credentials)

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `client_id` | string | Yes | App's Client ID |
| `client_secret` | string | Yes | App's Client Secret |
| `grant_type` | string | Yes | `authorization_code` or `refresh_token` |
| `code` | string | Conditional | Auth code (required when grant_type = `authorization_code`) |
| `refresh_token` | string | Conditional | Refresh token (required when grant_type = `refresh_token`) |
| `user_type` | string | No | Desired token type: `Company` or `Location` |
| `redirect_uri` | string | No | Must match the original authorization redirect URI |

**Content-Type:** `application/x-www-form-urlencoded` or `application/json` (both accepted)

### Request Example (Code Exchange)

```bash
curl -X POST "https://services.leadconnectorhq.com/oauth/token" \
  -H "Accept: application/json" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "client_id=YOUR_CLIENT_ID" \
  --data-urlencode "client_secret=YOUR_CLIENT_SECRET" \
  --data-urlencode "grant_type=authorization_code" \
  --data-urlencode "code=363fe3f086e2db02bb9c34722902d21f76c9b217" \
  --data-urlencode "user_type=Company" \
  --data-urlencode "redirect_uri=https://myapp.com/oauth/callback"
```

### Request Example (Refresh Token)

```bash
curl -X POST "https://services.leadconnectorhq.com/oauth/token" \
  -H "Accept: application/json" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "client_id=YOUR_CLIENT_ID" \
  --data-urlencode "client_secret=YOUR_CLIENT_SECRET" \
  --data-urlencode "grant_type=refresh_token" \
  --data-urlencode "refresh_token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..." \
  --data-urlencode "user_type=Company"
```

### Response Example (200 — Company Token)

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 86399,
  "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "scope": "businesses.readonly businesses.write contacts.readonly contacts.write",
  "refreshTokenId": "EXAMPLErefreshtoken00001",
  "userType": "Company",
  "companyId": "EXAMPLECompany000001",
  "isBulkInstallation": true,
  "userId": "EXAMPLEUser000000001"
}
```

### Response Example (200 — Location Token)

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 86400,
  "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "scope": "contacts.readonly contacts.write oauth.write oauth.readonly",
  "userType": "Location",
  "companyId": "EXAMPLECompany000001",
  "locationId": "EXAMPLELocation00000",
  "userId": "EXAMPLEUser000000001",
  "traceId": "8f712294-b015-42a-8c69-7fcb960560aa"
}
```

### Error Codes

| Code | Description |
|------|-------------|
| 400 | Bad Request — invalid params, expired code |
| 401 | Unauthorized — invalid client_id/client_secret |
| 422 | Unprocessable Entity — validation error |

### Important Notes

- The authorization code is **single-use** and expires quickly.
- When refreshing, the old refresh token becomes **immediately invalid**. Always persist the new refresh token from the response.
- `user_type` uses `"Company"` (not "Agency") for agency-level tokens.
- The `Version` header is **not** required on this endpoint, though including it does not cause errors.
- Both `application/x-www-form-urlencoded` and `application/json` content types are accepted.

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Location Access Token

Generate a Location (sub-account) access token from an Agency-level token.

**Method:** `POST`
**URL:** `/oauth/locationToken`
**Scope:** `oauth.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `companyId` | string | Yes | The agency/company ID |
| `locationId` | string | Yes | Target sub-account/location ID |

### Request Example

```bash
curl -X POST "https://services.leadconnectorhq.com/oauth/locationToken" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -H "Version: 2021-07-28" \
  -H "Authorization: Bearer {agency_access_token}" \
  -d '{
    "companyId": "EXAMPLECompany000001",
    "locationId": "EXAMPLELocation00000"
  }'
```

### Response Example (200)

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 86400,
  "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "scope": "calendars.readonly calendars.write contacts.readonly contacts.write",
  "userType": "Location",
  "companyId": "EXAMPLECompany000001",
  "locationId": "EXAMPLELocation00000",
  "userId": "EXAMPLEUser000000001",
  "traceId": "8f712294-b015-42a-8c69-7fcb960560aa"
}
```

### Error Codes

| Code | Description |
|------|-------------|
| 400 | Bad Request — missing or invalid parameters |
| 401 | Unauthorized — invalid/expired agency token |
| 422 | Unprocessable Entity — location not found or app not installed |

### Important Notes

- Requires an **Agency-level** token (`userType: "Company"`); Location tokens cannot call this.
- The `Version` header **is** required on this endpoint.
- The app must be installed at the target location.
- The returned Location token inherits scopes from the installed app's configuration.

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Installed Locations

List all locations (sub-accounts) where your app is installed for a given company.

**Method:** `GET`
**URL:** `/oauth/installedLocations`
**Scope:** `oauth.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `companyId` | string | Yes | The agency/company ID |
| `appId` | string | Yes | The marketplace app ID |
| `limit` | integer | No | Results per page |
| `skip` | integer | No | Number of results to skip (offset pagination) |
| `query` | string | No | Search filter on location name |
| `isInstalled` | boolean | No | Filter by installation status |
| `planId` | string | No | Filter by subscription plan ID |

### Request Example

```bash
curl -X GET "https://services.leadconnectorhq.com/oauth/installedLocations?companyId=EXAMPLECompany000001&appId=YOUR_APP_ID" \
  -H "Accept: application/json" \
  -H "Version: 2021-07-28" \
  -H "Authorization: Bearer {agency_access_token}"
```

### Response Example (200)

```json
{
  "locations": [
    {
      "_id": "EXAMPLELocation00000",
      "name": "Acme Dental Office",
      "address": "123 Main St, Springfield, IL",
      "isInstalled": true
    },
    {
      "_id": "EXAMPLEInstall00001",
      "name": "Sunrise Fitness Studio",
      "address": "456 Oak Ave, Portland, OR",
      "isInstalled": true
    }
  ],
  "count": 2,
  "installToFutureLocations": false
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `locations` | array | Array of installed location objects |
| `count` | integer | Total number of locations returned |
| `installToFutureLocations` | boolean | Whether the app auto-installs on newly created sub-accounts |

### Error Codes

| Code | Description |
|------|-------------|
| 400 | Bad Request — missing companyId |
| 401 | Unauthorized — invalid/expired token or not an Agency token |
| 422 | Unprocessable Entity |

### Important Notes

- Only works with **Agency-level** tokens (`userType: "Company"`).
- Useful for determining which locations to generate Location tokens for via [Get Location Access Token](#get-location-access-token).
- The `installToFutureLocations` field indicates whether the app auto-installs on newly created sub-accounts.

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## OAuth Scopes Reference

### Agency-Level Scopes

`oauth.readonly`, `oauth.write`, `companies.readonly`, `locations.write`, `snapshots.readonly`, `snapshots.write`, `saas/location.write`, `saas/location.read`, `saas/company.write`, `custom-menu-link.readonly`, `custom-menu-link.write`

### Sub-Account Scopes (Selected)

`businesses.readonly/write`, `calendars.readonly/write`, `calendars/events.readonly/write`, `campaigns.readonly`, `contacts.readonly/write`, `conversations.readonly/write`, `forms.readonly/write`, `funnels/funnel.readonly`, `funnels/page.readonly`, `funnels/pagecount.readonly`, `funnels/redirect.readonly/write`, `invoices.readonly/write`, `locations.readonly`, `medias.readonly/write`, `opportunities.readonly/write`, `surveys.readonly`, `users.readonly/write`, `workflows.readonly`, `products.readonly/write`, `payments/orders.readonly/write`, `emails/builder.readonly/write`

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 400 | Bad Request — invalid parameters |
| 401 | Unauthorized — invalid/missing token or insufficient scopes |
| 422 | Unprocessable Entity — validation failure |

---

## Notes

- Token endpoints (`/oauth/token`) do NOT require the `Version` header; resource-style OAuth endpoints (`/oauth/locationToken`, `/oauth/installedLocations`) DO require it.
- The `user_type` field uses `"Company"` (not "Agency") for agency-level tokens.
- Refresh tokens are single-use: exchanging a refresh token invalidates it immediately. Always store the new refresh token from the response.
- Refresh tokens are valid for 1 year or until used, whichever comes first.
- Private Integration Tokens use the same `Authorization: Bearer` header format and work identically to OAuth tokens for API calls — they just never expire and don't require the OAuth flow.

---

> **Agent Note:** Documentation created from OpenAPI spec research. All endpoints are 🔬 unverified until tested against a live GHL API. If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:** {date}` note to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.

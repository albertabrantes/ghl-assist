# Users API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Users API manages sub-account (location) users — team members, admins, and agents within a GoHighLevel location or agency. Users are distinct from Contacts; users are people who log in to the GHL platform, while contacts are CRM records.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `users.readonly` | Read | View users and their details |
| `users.write` | Read/Write | Create, update, delete users |

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Users by Location](#get-users-by-location) | GET | readonly | List all users for a location |
| [Search Users](#search-users) | GET | readonly | Search users across a company |
| [Filter Users by Email](#filter-users-by-email) | POST | readonly | Filter users by email addresses |
| [Get User](#get-user) | GET | readonly | Get a single user by ID |
| [Create User](#create-user) | POST | write | Create a new user |
| [Update User](#update-user) | PUT | write | Update an existing user |
| [Delete User](#delete-user) | DELETE | write | Delete a user |

---

## Authentication

All requests require:

```
Authorization: Bearer {access_token}
Version: 2021-07-28
```

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

---

## User Object Schema

```json
{
  "id": "user_abc123",
  "name": "John Smith",
  "firstName": "John",
  "lastName": "Smith",
  "email": "john@example.com",
  "phone": "+1234567890",
  "extension": "",
  "deleted": false,
  "roles": {
    "type": "account",
    "role": "admin",
    "locationIds": ["loc_xyz789"],
    "restrictSubAccount": false
  },
  "permissions": {
    "campaignsEnabled": true,
    "campaignsReadOnly": false,
    "contactsEnabled": true,
    "workflowsEnabled": true,
    "workflowsReadOnly": false,
    "triggersEnabled": true,
    "funnelsEnabled": true,
    "websitesEnabled": true,
    "opportunitiesEnabled": true,
    "dashboardStatsEnabled": true,
    "bulkRequestsEnabled": true,
    "appointmentsEnabled": true,
    "reviewsEnabled": true,
    "onlineListingsEnabled": true,
    "phoneCallEnabled": true,
    "conversationsEnabled": true,
    "assignedDataOnly": false,
    "adReportingEnabled": true,
    "membershipEnabled": true,
    "facebookAdsReportingEnabled": true,
    "attributionsReportingEnabled": true,
    "settingsEnabled": true,
    "tagsEnabled": true,
    "leadValueEnabled": true,
    "marketingEnabled": true,
    "agentReportingEnabled": true,
    "botService": true,
    "socialPlanner": true,
    "bloggingEnabled": true,
    "invoiceEnabled": true,
    "affiliateManagerEnabled": true,
    "contentAiEnabled": true,
    "refundsEnabled": true,
    "recordPaymentEnabled": true,
    "cancelSubscriptionEnabled": true,
    "paymentsEnabled": true,
    "communitiesEnabled": true,
    "exportPaymentsEnabled": true
  },
  "scopes": ["contacts.readonly", "contacts.write"],
  "scopesAssignedToOnly": []
}
```

---

## Get Users by Location

Retrieve all users assigned to a specific location (sub-account).

**Method:** `GET`
**URL:** `/users/`
**Scope:** `users.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/users/?locationId=loc_xyz789' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "users": [
    {
      "id": "user_abc123",
      "name": "John Smith",
      "firstName": "John",
      "lastName": "Smith",
      "email": "john@example.com",
      "phone": "+1234567890",
      "extension": "",
      "deleted": false,
      "roles": {
        "type": "account",
        "role": "admin",
        "locationIds": ["loc_xyz789"],
        "restrictSubAccount": false
      },
      "permissions": { "..." : "..." }
    }
  ],
  "count": 1
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `users` | array | Array of user objects |
| `count` | number | Total number of users returned |

> **Tested:** Confirmed working with Private Integration Token. Returns all users for the location including name, email, phone, roles, and permissions.

---

## Search Users

Search and filter users across a company.

**Method:** `GET`
**URL:** `/users/search`
**Scope:** `users.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `companyId` | string | Yes | Company ID to search within |
| `query` | string | No | Search term (matches name, email, phone) |
| `skip` | number | No | Results to skip (default: 0) |
| `limit` | number | No | Results per page (default: 25) |
| `locationId` | string | No | Filter by specific location |
| `type` | string | No | User type filter (e.g., "agency") |
| `role` | string | No | Role filter (e.g., "admin") |
| `ids` | string | No | Comma-separated user IDs |
| `sort` | string | No | Field to sort by (default: name) |
| `sortDirection` | string | No | Sort order: "asc" or "desc" |
| `enabled2waySync` | boolean | No | Filter by 2-way sync status |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/users/search?companyId=company_123&query=john&limit=10' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "users": [
    {
      "id": "user_abc123",
      "name": "John Smith",
      "firstName": "John",
      "lastName": "Smith",
      "email": "john@example.com",
      "phone": "+1234567890",
      "roles": { "..." : "..." }
    }
  ],
  "count": 1
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec, not tested with live API calls.

---

## Filter Users by Email

Filter users by a list of email addresses.

**Method:** `POST`
**URL:** `/users/search/filter-by-email`
**Scope:** `users.readonly`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `companyId` | string | Yes | Company ID |
| `emails` | array | Yes | Array of email addresses to filter by |
| `deleted` | boolean | No | Include deleted users (default: false) |
| `skip` | number | No | Results to skip (default: 0) |
| `limit` | number | No | Results per page (default: 25) |
| `projection` | string | No | Field selection: "all" or comma-separated field names |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/users/search/filter-by-email' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "companyId": "company_123",
    "emails": ["john@example.com", "jane@example.com"],
    "deleted": false
  }'
```

### Response

Same structure as [Search Users](#search-users).

> **Status:** 🔬 Unverified — documented from OpenAPI spec.

---

## Get User

Retrieve a specific user by their ID.

**Method:** `GET`
**URL:** `/users/{userId}`
**Scope:** `users.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userId` | string | Yes | The user ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/users/user_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "id": "user_abc123",
  "name": "John Smith",
  "firstName": "John",
  "lastName": "Smith",
  "email": "john@example.com",
  "phone": "+1234567890",
  "extension": "",
  "deleted": false,
  "roles": {
    "type": "account",
    "role": "admin",
    "locationIds": ["loc_xyz789"],
    "restrictSubAccount": false
  },
  "permissions": { "..." : "..." }
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec.

---

## Create User

Add a new user to the system.

**Method:** `POST`
**URL:** `/users/`
**Scope:** `users.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `companyId` | string | Yes | Company ID |
| `firstName` | string | Yes | User's first name |
| `lastName` | string | Yes | User's last name |
| `email` | string | Yes | User's email address |
| `password` | string | Yes | User's password |
| `type` | string | Yes | User type (e.g., "account") |
| `role` | string | Yes | User role (e.g., "admin", "user") |
| `locationIds` | array | Yes | Array of location IDs to assign |
| `phone` | string | No | User's phone number |
| `permissions` | object | No | Permissions object (see schema above) |
| `scopes` | array | No | Array of permission scope strings |
| `scopesAssignedToOnly` | array | No | Scopes for assigned-only access |
| `profilePhoto` | string | No | URL of profile photo |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/users/' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "companyId": "company_123",
    "firstName": "New",
    "lastName": "User",
    "email": "newuser@example.com",
    "password": "SecurePass123!",
    "type": "account",
    "role": "user",
    "locationIds": ["loc_xyz789"]
  }'
```

### Response

Returns the created user object (HTTP 201).

> **Status:** 🔬 Unverified — documented from OpenAPI spec.

---

## Update User

Modify an existing user's information.

**Method:** `PUT`
**URL:** `/users/{userId}`
**Scope:** `users.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userId` | string | Yes | The user ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `firstName` | string | No | User's first name |
| `lastName` | string | No | User's last name |
| `email` | string | No | ⚠️ Deprecated for security — use `emailChangeOTP` |
| `emailChangeOTP` | string | No | OTP required for email changes |
| `password` | string | No | New password |
| `phone` | string | No | Phone number |
| `type` | string | No | User type |
| `role` | string | No | User role |
| `companyId` | string | No | Company ID |
| `locationIds` | array | No | Location assignments |
| `permissions` | object | No | Permissions object |
| `scopes` | array | No | Permission scopes |
| `scopesAssignedToOnly` | array | No | Assigned-only scopes |
| `profilePhoto` | string | No | Profile photo URL |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/users/user_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "firstName": "Updated",
    "lastName": "Name",
    "phone": "+1987654321"
  }'
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec.

---

## Delete User

Remove a user from the system. This operation is queued and processed asynchronously.

**Method:** `DELETE`
**URL:** `/users/{userId}`
**Scope:** `users.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userId` | string | Yes | The user ID to delete |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/users/user_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "succeeded": true,
  "message": "Queued deleting user with id: user_abc123"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `succeeded` | boolean | Whether the delete was queued successfully |
| `message` | string | Human-readable status message |

> **Status:** 🔬 Unverified — documented from OpenAPI spec.

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

- The `GET /users/` endpoint (Get Users by Location) requires `locationId` as a query parameter, NOT a path parameter.
- Private Integration Tokens need the `users.readonly` scope explicitly enabled — it is not included by default.
- User IDs are distinct from Contact IDs. A user may also exist as a contact in the CRM with a different ID.
- The `email` field on Update User is deprecated for security reasons. Email changes require an OTP flow.

---

> **Agent Note:** If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:** {date}` note to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.

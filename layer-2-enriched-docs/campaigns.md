# Campaigns API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Campaigns API accesses the **legacy** drip/sequence system in GoHighLevel. Campaigns are linear, step-by-step email and SMS sequences — simpler than Workflows but still functional. The core API has **1 endpoint** for listing campaigns. Three additional contact-level endpoints for managing campaign enrollment exist under the Contacts API and are documented here as cross-references.

> **Legacy System:** Campaigns are the older automation system in GHL. For new automations, GHL recommends using **Workflows** (the visual builder with branching, conditionals, and multi-channel support). Campaigns remain functional but receive no new feature development.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `campaigns.readonly` | Read | List and view campaigns |
| `contacts.write` | Read/Write | Add/remove contacts from campaigns (Contacts API) |

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Campaigns](#get-campaigns) | GET | campaigns.readonly | List all drip campaigns in a location |
| [Add Contact to Campaign](#add-contact-to-campaign) | POST | contacts.write | Enroll a contact into a campaign * |
| [Remove Contact from Campaign](#remove-contact-from-campaign) | DELETE | contacts.write | Remove a contact from a specific campaign * |
| [Remove Contact from Every Campaign](#remove-contact-from-every-campaign) | DELETE | contacts.write | Remove a contact from all campaigns * |

\* Cross-reference from the Contacts API. See [contacts.md](contacts.md) for the primary documentation.

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

## Campaign Object Schema

```json
{
  "id": "EXAMPLECampaign00001",
  "name": "Welcome Email Sequence",
  "status": "published",
  "locationId": "ve9EPM428h8vShlRW1KT"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Campaign ID |
| `name` | string | Campaign name |
| `status` | string | `"published"`, `"draft"`, or `"archived"` |
| `locationId` | string | Sub-account/location ID |

---

## Get Campaigns

List all drip campaigns in a location.

**Method:** `GET`
**URL:** `/campaigns/`
**Scope:** `campaigns.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `status` | string | No | Filter by status (e.g., `"published"`) |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/campaigns/?locationId=ve9EPM428h8vShlRW1KT' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "campaigns": [
    {
      "id": "EXAMPLECampaign00001",
      "name": "Welcome Email Sequence",
      "status": "published",
      "locationId": "ve9EPM428h8vShlRW1KT"
    },
    {
      "id": "abc123campaignId",
      "name": "Re-engagement SMS Drip",
      "status": "draft",
      "locationId": "ve9EPM428h8vShlRW1KT"
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `campaigns` | array | Array of campaign objects |

### Error Codes

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 400 | Bad Request |
| 401 | Unauthorized |
| 422 | Unprocessable Entity |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Add Contact to Campaign

Enrolls a contact into a drip campaign.

> **Note:** This endpoint is part of the Contacts API (`contacts.write` scope). It is documented here for cross-reference. See [contacts.md](contacts.md) for the primary documentation.

**Method:** `POST`
**URL:** `/contacts/{contactId}/campaigns/{campaignId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | Contact ID |
| `campaignId` | string | Yes | Campaign ID |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/ctc_xyz789ghi012/campaigns/EXAMPLECampaign00001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example (201)

```json
{
  "succeedded": true
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `succeedded` | boolean | Whether the operation succeeded (note: the typo is in the actual GHL API) |

**Note:** Returns **201** (Created), not 200. The `"succeedded"` typo (three d's) is consistent across all GHL action endpoints.

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Remove Contact from Campaign

Removes a contact from a specific campaign.

> **Note:** This endpoint is part of the Contacts API (`contacts.write` scope). It is documented here for cross-reference. See [contacts.md](contacts.md) for the primary documentation.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/campaigns/{campaignId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | Contact ID |
| `campaignId` | string | Yes | Campaign ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/ctc_xyz789ghi012/campaigns/EXAMPLECampaign00001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example (200)

```json
{
  "succeedded": true
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Remove Contact from Every Campaign

Bulk-removes a contact from all campaigns at once.

> **Note:** This endpoint is part of the Contacts API (`contacts.write` scope). It is documented here for cross-reference. See [contacts.md](contacts.md) for the primary documentation.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/campaigns/removeAll`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | Contact ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/ctc_xyz789ghi012/campaigns/removeAll' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example (200)

```json
{
  "succeedded": true
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Campaigns vs Workflows

| Aspect | Campaigns (Legacy) | Workflows (Current) |
|--------|--------------------|---------------------|
| **Logic** | Linear steps with time delays | Visual builder with branching and conditionals |
| **Channels** | Email and SMS only | Multi-channel (email, SMS, calls, webhooks, Slack, custom code, etc.) |
| **Triggers** | Manual/API addition only | Dozens of triggers (forms, tags, appointments, webhooks, etc.) |
| **Complexity** | Low — sequential only | High — full automation engine |
| **Status** | Legacy but functional | Active primary system |
| **API Scope** | `campaigns.readonly` | `workflows.readonly` |

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

- Campaigns are the **legacy** automation system. For new automations, use Workflows instead.
- The core Campaigns API is **read-only** (`campaigns.readonly`). There is no API to create, update, or delete campaigns — that must be done through the GHL UI.
- The contact-level endpoints (add/remove from campaign) use the `contacts.write` scope, not `campaigns.readonly`.
- The `"succeedded"` response field has a consistent typo (three d's) across all GHL action endpoints. Code should check for `"succeedded"`, not `"succeeded"`.
- The `status` query parameter on Get Campaigns can filter by `"published"`, `"draft"`, or `"archived"`.

---

> **Agent Note:** Endpoints marked 🔬 are documented from OpenAPI spec research and unverified against a live GHL API. If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:**` marker to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.

# Contacts API — Campaigns & Workflows

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Add to Campaign

Add a contact to a marketing campaign.

**Method:** `POST`
**URL:** `/contacts/{contactId}/campaigns`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `campaignId` | string | Yes | The campaign ID to add contact to |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/campaigns' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "campaignId": "campaign_xyz789"
  }'
```

### Response

HTTP status `200 OK` with confirmation:

```json
{
  "success": true,
  "message": "Contact added to campaign successfully"
}
```

---

## Remove from Campaign

Remove a contact from a specific campaign.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/campaigns`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `campaignId` | string | Yes | The campaign ID to remove contact from |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/campaigns' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "campaignId": "campaign_xyz789"
  }'
```

### Response

HTTP status `200 OK` with confirmation message.

---

## Remove from All Campaigns

Remove a contact from all active campaigns.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/campaigns/removeAll`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/campaigns/removeAll' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with confirmation:

```json
{
  "success": true,
  "message": "Contact removed from all campaigns",
  "campaignsRemoved": 3
}
```

---

## Add to Workflow

Add a contact to an automation workflow.

**Method:** `POST`
**URL:** `/contacts/{contactId}/workflow/{workflowId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `workflowId` | string | Yes | The workflow ID |

### Request Body

No request body required.

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/workflow/workflow_xyz789' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response

HTTP status `200 OK` with confirmation:

```json
{
  "success": true,
  "message": "Contact added to workflow successfully",
  "contactId": "contact_abc123",
  "workflowId": "workflow_xyz789"
}
```

> **Note:** If the contact is already in the workflow, they will be re-entered from the beginning.

---

## Remove from Workflow

Remove a contact from an automation workflow.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/workflow/{workflowId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `workflowId` | string | Yes | The workflow ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/workflow/workflow_xyz789' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with confirmation message.

# Custom Values API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Custom Values API allows you to manage location-specific custom values that can be used throughout the GHL platform. Custom values are dynamic placeholders that store information like business details, contact information, or any other data you want to reference across campaigns, workflows, emails, and SMS messages using the `{{ custom_values.field_key }}` syntax.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `locations/customValues.readonly` | Read | View custom values |
| `locations/customValues.write` | Read/Write | Create, update, delete custom values |

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get All Custom Values](#get-all-custom-values) | GET | readonly | List all custom values for a location |
| [Get Custom Value](#get-custom-value) | GET | readonly | Get a single custom value by ID |
| [Create Custom Value](#create-custom-value) | POST | write | Create a new custom value |
| [Update Custom Value](#update-custom-value) | PUT | write | Update an existing custom value |
| [Delete Custom Value](#delete-custom-value) | DELETE | write | Delete a custom value |

---

## Authentication

All requests require authentication via one of:

**OAuth 2.0 Bearer Token:**
```
Authorization: Bearer {access_token}
```

**Private Integration Token:**
```
Authorization: Bearer {private_token}
```

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

**Rate Limit Headers:**
- `X-RateLimit-Limit-Daily` - Total daily limit
- `X-RateLimit-Daily-Remaining` - Remaining daily requests
- `X-RateLimit-Interval-Milliseconds` - Burst interval window
- `X-RateLimit-Max` - Max requests in burst interval
- `X-RateLimit-Remaining` - Remaining burst requests

When exceeded: HTTP `429 Too Many Requests` with `Retry-After` header.

---

## Get All Custom Values

Retrieves all custom values configured for a location.

**Method:** `GET`
**URL:** `/locations/{locationId}/customValues`
**Scope:** `locations/customValues.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/abc123/customValues' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "customValues": [
    {
      "id": "cv_12345",
      "name": "Business Phone",
      "fieldKey": "business_phone",
      "value": "+1-555-123-4567",
      "locationId": "abc123"
    },
    {
      "id": "cv_67890",
      "name": "Support Email",
      "fieldKey": "support_email",
      "value": "support@example.com",
      "locationId": "abc123"
    },
    {
      "id": "cv_11111",
      "name": "Business Hours",
      "fieldKey": "business_hours",
      "value": "Monday-Friday 9AM-5PM EST",
      "locationId": "abc123"
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `customValues` | array | List of custom value objects |
| `customValues[].id` | string | Unique custom value identifier |
| `customValues[].name` | string | Display name of the custom value |
| `customValues[].fieldKey` | string | Auto-generated key for referencing in templates |
| `customValues[].value` | string | The actual value stored |
| `customValues[].locationId` | string | Associated location ID |

> **Note:** The `fieldKey` is auto-generated from the `name` by converting to lowercase and replacing spaces with underscores.

---

## Get Custom Value

Retrieve a single custom value by ID.

**Method:** `GET`
**URL:** `/locations/{locationId}/customValues/{id}`
**Scope:** `locations/customValues.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `id` | string | Yes | The custom value ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/abc123/customValues/cv_12345' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "customValue": {
    "id": "cv_12345",
    "name": "Business Phone",
    "fieldKey": "business_phone",
    "value": "+1-555-123-4567",
    "locationId": "abc123"
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique custom value identifier |
| `name` | string | Display name of the custom value |
| `fieldKey` | string | Key for template usage: `{{ custom_values.business_phone }}` |
| `value` | string | The stored value |
| `locationId` | string | Associated location ID |

---

## Create Custom Value

Create a new custom value for a location.

**Method:** `POST`
**URL:** `/locations/{locationId}/customValues`
**Scope:** `locations/customValues.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Display name for the custom value |
| `value` | string | Yes | The value to store |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/abc123/customValues' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Emergency Contact",
    "value": "+1-555-999-8888"
  }'
```

### Response Example

```json
{
  "customValue": {
    "id": "cv_22222",
    "name": "Emergency Contact",
    "fieldKey": "emergency_contact",
    "value": "+1-555-999-8888",
    "locationId": "abc123"
  }
}
```

### Response

Returns the created custom value object with HTTP status `201 Created`.

> **Important:** The `fieldKey` is automatically generated from the `name`. For example:
> - "Business Phone" → `business_phone`
> - "Support Email" → `support_email`
> - "Office Address" → `office_address`

---

## Update Custom Value

Update an existing custom value.

**Method:** `PUT`
**URL:** `/locations/{locationId}/customValues/{id}`
**Scope:** `locations/customValues.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `id` | string | Yes | The custom value ID |

### Request Body

All fields are optional. Only include fields you want to update.

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Display name for the custom value |
| `value` | string | The value to store |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/locations/abc123/customValues/cv_12345' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "value": "+1-555-123-9999"
  }'
```

### Response Example

```json
{
  "customValue": {
    "id": "cv_12345",
    "name": "Business Phone",
    "fieldKey": "business_phone",
    "value": "+1-555-123-9999",
    "locationId": "abc123"
  }
}
```

### Response

Returns the updated custom value object with HTTP status `200 OK`.

> **Note:** Updating the `name` will regenerate the `fieldKey`, which may break existing references in templates, emails, or workflows.

---

## Delete Custom Value

Delete a custom value from a location.

**Method:** `DELETE`
**URL:** `/locations/{locationId}/customValues/{id}`
**Scope:** `locations/customValues.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `id` | string | Yes | The custom value ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/locations/abc123/customValues/cv_12345' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with empty body or confirmation message.

> **Warning:** Deleting a custom value that is referenced in active campaigns, workflows, or templates will cause those references to fail. Review all usage before deletion.

---

## Common Use Cases

### 1. Business Information

Store and reference business details across all communications:

```json
{
  "name": "Company Name",
  "value": "Acme Corporation"
}
```

Usage in email template:
```
Thank you for contacting {{ custom_values.company_name }}!
```

### 2. Contact Details

Centralize contact information that changes infrequently:

```json
{
  "name": "Support Phone",
  "value": "1-800-SUPPORT"
}
```

Usage in SMS:
```
Need help? Call us at {{ custom_values.support_phone }}
```

### 3. Dynamic Content

Store seasonal or promotional content:

```json
{
  "name": "Current Promotion",
  "value": "20% off all services until March 31st"
}
```

Usage in workflow:
```
Special offer: {{ custom_values.current_promotion }}
```

### 4. Operating Hours

Manage business hours in one place:

```json
{
  "name": "Office Hours",
  "value": "Monday-Friday: 9AM-6PM EST, Saturday: 10AM-2PM EST"
}
```

### 5. Legal/Compliance Text

Store compliance disclaimers or legal text:

```json
{
  "name": "Privacy Policy Link",
  "value": "https://example.com/privacy"
}
```

---

## Best Practices

### Naming Conventions

- Use descriptive names that clearly indicate the value's purpose
- Keep names concise (the `fieldKey` will be auto-generated from it)
- Avoid special characters that might complicate the generated `fieldKey`

### Value Management

- **Document Usage:** Keep track of where custom values are used before updating or deleting
- **Test Changes:** Update values in a test environment first when possible
- **Version Control:** For complex values (like HTML snippets), consider version tracking
- **Regular Audits:** Periodically review and remove unused custom values

### Template Usage

Reference custom values in templates using:
```
{{ custom_values.field_key }}
```

The `field_key` format:
- Lowercase letters only
- Underscores replace spaces
- Special characters removed
- Example: "Business Phone Number" → `business_phone_number`

---

## Error Responses

| Status Code | Description |
|-------------|-------------|
| `400` | Bad Request - Invalid parameters |
| `401` | Unauthorized - Invalid or missing token |
| `403` | Forbidden - Insufficient scope |
| `404` | Not Found - Custom value or location doesn't exist |
| `422` | Unprocessable Entity - Validation error |
| `429` | Too Many Requests - Rate limit exceeded |

### Error Response Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "name is required",
    "details": [
      {
        "field": "name",
        "message": "This field is required"
      }
    ]
  }
}
```

### Common Error Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `VALIDATION_ERROR` | Required field missing or invalid | Check request body against schema |
| `RESOURCE_NOT_FOUND` | Custom value or location doesn't exist | Verify IDs are correct |
| `DUPLICATE_NAME` | Custom value name already exists | Use a unique name or update existing value |
| `INSUFFICIENT_SCOPE` | Missing required OAuth scope | Add `locations/customValues.write` scope |

---

## Integration Examples

### Sync Business Hours Across Locations

```bash
# Get custom values from primary location
PRIMARY_VALUES=$(curl -X GET \
  'https://services.leadconnectorhq.com/locations/primary_location/customValues' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28')

# Create same values in secondary location
curl -X POST \
  'https://services.leadconnectorhq.com/locations/secondary_location/customValues' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Business Hours",
    "value": "Monday-Friday 9AM-5PM EST"
  }'
```

### Bulk Update Seasonal Messaging

```bash
# Update promotion across all locations
LOCATIONS=("loc_001" "loc_002" "loc_003")

for LOC in "${LOCATIONS[@]}"; do
  curl -X PUT \
    "https://services.leadconnectorhq.com/locations/${LOC}/customValues/cv_promotion" \
    -H 'Authorization: Bearer YOUR_TOKEN' \
    -H 'Version: 2021-07-28' \
    -H 'Content-Type: application/json' \
    -d '{
      "value": "Spring Sale - 30% off until May 1st"
    }'
done
```

---

## References

- [HighLevel API Documentation](https://marketplace.gohighlevel.com/docs/)
- [OAuth Scopes Reference](https://marketplace.gohighlevel.com/docs/Authorization/Scopes/index.html)
- [Custom Values in Templates](https://help.gohighlevel.com/support/solutions/articles/155000000891)

---

> **Agent Note:** If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:** {date}` note to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.

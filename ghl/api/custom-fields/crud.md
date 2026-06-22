# Custom Fields API — CRUD Endpoints

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

See [field-types.md](./field-types.md) for data types and the options format reference, [folders.md](./folders.md) for folder endpoints, and [gotchas.md](./gotchas.md) for quirks and error responses.

## Endpoints Summary

### Legacy API (Contacts/Opportunities)

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Custom Fields](#get-custom-fields-legacy) | GET | readonly | List all custom fields for a location |
| [Get Custom Field by ID](#get-custom-field-by-id-legacy) | GET | readonly | Get a single custom field |
| [Create Custom Field](#create-custom-field-legacy) | POST | write | Create a new custom field |
| [Update Custom Field](#update-custom-field-legacy) | PUT | write | Update an existing custom field |
| [Delete Custom Field](#delete-custom-field-legacy) | DELETE | write | Delete a custom field |
| [Upload File](#upload-file-legacy) | POST | write | Upload file for FILE_UPLOAD field type |

### V2 API (Custom Objects/Business)

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Fields by Object Key](#get-fields-by-object-key-v2) | GET | readonly | List fields for a specific object type |
| [Get Custom Field](#get-custom-field-v2) | GET | readonly | Get a single custom field |
| [Create Custom Field](#create-custom-field-v2) | POST | write | Create a new custom field |
| [Update Custom Field](#update-custom-field-v2) | PUT | write | Update an existing custom field |
| [Delete Custom Field](#delete-custom-field-v2) | DELETE | write | Delete a custom field |

Folder endpoints (Legacy and V2) are documented in [folders.md](./folders.md).

---

## Legacy API (Contacts/Opportunities)

---

## Get Custom Fields (Legacy)

Retrieves all custom fields configured for a location.

**Method:** `GET`
**URL:** `/locations/{locationId}/customFields`
**Scope:** `locations/customFields.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `model` | string | No | Filter by object type: `contact`, `opportunity` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields?model=contact' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "customFields": [
    {
      "id": "cf_001",
      "name": "Preferred Contact Method",
      "fieldKey": "contact.preferred_contact_method",
      "dataType": "SINGLE_OPTIONS",
      "position": 0,
      "placeholder": "Select method",
      "model": "contact",
      "isRequired": false,
      "isMultiple": false,
      "options": [
        {
          "value": "email",
          "label": "Email"
        },
        {
          "value": "phone",
          "label": "Phone"
        },
        {
          "value": "sms",
          "label": "SMS"
        }
      ]
    },
    {
      "id": "cf_002",
      "name": "Company Size",
      "fieldKey": "contact.company_size",
      "dataType": "NUMERICAL",
      "position": 1,
      "placeholder": "Enter number of employees",
      "model": "contact",
      "isRequired": false
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `customFields` | array | List of custom field objects |
| `customFields[].id` | string | Unique field identifier |
| `customFields[].name` | string | Display name of the field |
| `customFields[].fieldKey` | string | Internal key (e.g., `contact.field_name`) |
| `customFields[].dataType` | string | Data type of the field |
| `customFields[].position` | number | Display order (0-indexed) |
| `customFields[].placeholder` | string | Placeholder text |
| `customFields[].model` | string | Object type: `contact`, `opportunity` |
| `customFields[].isRequired` | boolean | Whether field is required |
| `customFields[].isMultiple` | boolean | For selection types - multiple values allowed |
| `customFields[].options` | array | Available options for selection types |

> **Note:** Folders are NOT returned in this list response. See [folders.md](./folders.md).

---

## Get Custom Field by ID (Legacy)

Retrieve a single custom field by its ID.

**Method:** `GET`
**URL:** `/locations/{locationId}/customFields/{id}`
**Scope:** `locations/customFields.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `id` | string | Yes | The custom field ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields/cf_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "customField": {
    "id": "cf_001",
    "name": "Preferred Contact Method",
    "fieldKey": "contact.preferred_contact_method",
    "dataType": "SINGLE_OPTIONS",
    "position": 0,
    "placeholder": "Select method",
    "model": "contact",
    "isRequired": false,
    "isMultiple": false,
    "options": [
      {
        "value": "email",
        "label": "Email"
      },
      {
        "value": "phone",
        "label": "Phone"
      }
    ]
  }
}
```

---

## Create Custom Field (Legacy)

Create a new custom field for contacts or opportunities.

**Method:** `POST`
**URL:** `/locations/{locationId}/customFields`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Display name of the field |
| `dataType` | string | Yes | Data type (see [field-types.md](./field-types.md)) |
| `model` | string | Yes | Object type: `contact`, `opportunity` |
| `placeholder` | string | No | Placeholder text |
| `parentId` | string | No | Folder ID to assign field to (see [folders.md](./folders.md)) |
| `position` | number | No | Sort order within folder/root |
| `isRequired` | boolean | No | Whether field is required (default: false) |
| `isMultiple` | boolean | No | For selection types - allow multiple (default: false) |
| `options` | array | Conditional | Required for selection types (see [field-types.md](./field-types.md) and [gotchas.md](./gotchas.md)) |
| `picklistOptions` | array | Conditional | Alternative to `options` - simple string array for RADIO/SINGLE_OPTIONS (e.g., `["Yes", "No"]`) |
| `acceptedFormat` | array | Conditional | Required for FILE_UPLOAD type |

> **Note:** When `parentId` is provided, the field is placed inside the specified folder on the opportunity/contact card in the GHL UI. The folder must already exist (see [folders.md](./folders.md)).

For the options format per data type and the validation quirks around `SINGLE_OPTIONS` / `CHECKBOX`, see [gotchas.md](./gotchas.md).

### Request Example (Dropdown Field)

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Lead Source",
    "dataType": "SINGLE_OPTIONS",
    "model": "contact",
    "placeholder": "Select source",
    "isRequired": true,
    "options": [
      {
        "value": "website",
        "label": "Website"
      },
      {
        "value": "referral",
        "label": "Referral"
      },
      {
        "value": "social_media",
        "label": "Social Media"
      }
    ]
  }'
```

### Request Example (Checkbox Field with Multiple Options)

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Surfaces Being Painted",
    "dataType": "CHECKBOX",
    "model": "contact",
    "parentId": "folder_abc123",
    "options": ["Interior Walls", "Ceilings", "Exterior", "Metal", "Concrete", "Other"],
    "position": 600
  }'
```

**Response:**
```json
{
  "customField": {
    "id": "cf_checkbox123",
    "name": "Surfaces Being Painted",
    "fieldKey": "contact.surfaces_being_painted",
    "dataType": "CHECKBOX",
    "model": "contact",
    "position": 500,
    "parentId": "folder_abc123",
    "picklistOptions": [
      "Interior Walls",
      "Ceilings",
      "Exterior",
      "Metal",
      "Concrete",
      "Other"
    ],
    "isAllowedCustomOption": false
  }
}
```

> **Note:** Request uses `options` (string array) but response returns values under `picklistOptions`. This is a GHL API inconsistency specific to the CHECKBOX type. See [gotchas.md](./gotchas.md).

### Request Example (File Upload Field)

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Contract Document",
    "dataType": "FILE_UPLOAD",
    "model": "opportunity",
    "placeholder": "Upload contract",
    "acceptedFormat": [".pdf", ".doc", ".docx"]
  }'
```

### Response

Returns the created custom field object with HTTP status `201 Created`.

```json
{
  "customField": {
    "id": "cf_new123",
    "name": "Lead Source",
    "fieldKey": "contact.lead_source",
    "dataType": "SINGLE_OPTIONS",
    "model": "contact",
    "position": 5,
    "isRequired": true,
    "options": [
      {
        "value": "website",
        "label": "Website"
      },
      {
        "value": "referral",
        "label": "Referral"
      },
      {
        "value": "social_media",
        "label": "Social Media"
      }
    ]
  }
}
```

---

## Update Custom Field (Legacy)

Update an existing custom field.

**Method:** `PUT`
**URL:** `/locations/{locationId}/customFields/{id}`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `id` | string | Yes | The custom field ID |

### Request Body

All fields are optional. Only include fields you want to update.

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Display name of the field |
| `parentId` | string | Folder ID — moves the field into a different folder |
| `placeholder` | string | Placeholder text |
| `isRequired` | boolean | Whether field is required |
| `isMultiple` | boolean | For selection types - allow multiple |
| `options` | array | Updated options for selection types |
| `position` | number | Display order |

> **Note:** You cannot change `dataType` or `model` after creation. The `fieldKey` is also immutable — it is set at creation time based on the original field name and never changes even if you rename the field.

> **Tested:** `parentId` IS accepted by the Legacy PUT endpoint. You can rename a field AND move it to a different folder in a single PUT request. The field ID stays the same, so form mappings are preserved. This is undocumented but confirmed working.

### Request Example (Rename + Move to Folder)

```bash
curl -s -X PUT \
  "https://services.leadconnectorhq.com/locations/{locationId}/customFields/{fieldId}" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "JCC Cab 1. Doors & drawers installed?",
    "parentId": "EXAMPLEParent000001"
  }'
```

**Response (200 OK):**
```json
{
  "customField": {
    "id": "EXAMPLEField000001",
    "model": "contact",
    "name": "JCC Cab 1. Doors & drawers installed?",
    "fieldKey": "contact.cabinets_1_doors__drawers_installed",
    "dataType": "RADIO",
    "position": 50,
    "parentId": "EXAMPLEParent000001",
    "locationId": "EXAMPLELocation00001"
  }
}
```

> **Key behavior:** The `fieldKey` remains `contact.cabinets_1_doors__drawers_installed` (based on the original name at creation) even though the display name changed to "JCC Cab 1. Doors & drawers installed?". Field keys are immutable.

### Request Example (Update Options)

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields/cf_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Primary Contact Method",
    "isRequired": true,
    "options": [
      {
        "value": "email",
        "label": "Email"
      },
      {
        "value": "phone",
        "label": "Phone"
      },
      {
        "value": "sms",
        "label": "SMS"
      },
      {
        "value": "whatsapp",
        "label": "WhatsApp"
      }
    ]
  }'
```

### Response

Returns the updated custom field object with HTTP status `200 OK`.

---

## Delete Custom Field (Legacy)

Delete a custom field. This is permanent and will remove all stored values.

**Method:** `DELETE`
**URL:** `/locations/{locationId}/customFields/{id}`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `id` | string | Yes | The custom field ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields/cf_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with confirmation message.

```json
{
  "success": true,
  "message": "Custom field deleted successfully"
}
```

> **Warning:** Deleting a custom field is permanent. All data stored in this field across all contacts/opportunities will be lost.

---

## Upload File (Legacy)

Upload a file for a FILE_UPLOAD type custom field.

**Method:** `POST`
**URL:** `/locations/{locationId}/customFields/upload`
**Scope:** `locations/customFields.write`
**Content-Type:** `multipart/form-data`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Form Data Parameters

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `file` | file | Yes | The file to upload |
| `fileKey` | string | Yes | The custom field key |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields/upload' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -F 'file=@/path/to/contract.pdf' \
  -F 'fileKey=opportunity.contract_document'
```

### Response Example

```json
{
  "success": true,
  "fileUrl": "https://storage.gohighlevel.com/files/loc_abc123/cf_contract_abc123.pdf",
  "fileName": "contract.pdf",
  "fileSize": 245760,
  "uploadedAt": "2026-02-04T10:30:00Z"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Upload success status |
| `fileUrl` | string | CDN URL of uploaded file |
| `fileName` | string | Original file name |
| `fileSize` | number | File size in bytes |
| `uploadedAt` | string | ISO 8601 upload timestamp |

---

## V2 API (Custom Objects/Business)

> **Important:** V2 API currently supports only Custom Objects and Business entities. For Contacts and Opportunities, use the Legacy API.

---

## Get Fields by Object Key (V2)

Retrieve all custom fields for a specific object type.

**Method:** `GET`
**URL:** `/custom-fields/object-key/{objectKey}`
**Scope:** `locations/customFields.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `objectKey` | string | Yes | Object key (e.g., `invoice`, `business`) |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/custom-fields/object-key/invoice?locationId=loc_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "customFields": [
    {
      "id": "cf_v2_001",
      "name": "Payment Terms",
      "fieldKey": "custom_object.invoice.payment_terms",
      "dataType": "SINGLE_OPTIONS",
      "objectKey": "invoice",
      "position": 0,
      "folderId": "folder_001",
      "folderName": "Billing Information",
      "isRequired": true,
      "options": [
        {
          "value": "net_30",
          "label": "Net 30"
        },
        {
          "value": "net_60",
          "label": "Net 60"
        },
        {
          "value": "due_on_receipt",
          "label": "Due on Receipt"
        }
      ]
    },
    {
      "id": "cf_v2_002",
      "name": "Invoice Amount",
      "fieldKey": "custom_object.invoice.invoice_amount",
      "dataType": "MONETORY",
      "objectKey": "invoice",
      "position": 1,
      "folderId": "folder_001",
      "folderName": "Billing Information",
      "isRequired": true
    }
  ],
  "folders": [
    {
      "id": "folder_001",
      "name": "Billing Information",
      "position": 0
    }
  ]
}
```

---

## Get Custom Field (V2)

Retrieve a single custom field by its ID.

**Method:** `GET`
**URL:** `/custom-fields/{id}`
**Scope:** `locations/customFields.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The custom field ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/custom-fields/cf_v2_001?locationId=loc_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "customField": {
    "id": "cf_v2_001",
    "name": "Payment Terms",
    "fieldKey": "custom_object.invoice.payment_terms",
    "dataType": "SINGLE_OPTIONS",
    "objectKey": "invoice",
    "position": 0,
    "folderId": "folder_001",
    "isRequired": true,
    "options": [
      {
        "value": "net_30",
        "label": "Net 30"
      },
      {
        "value": "net_60",
        "label": "Net 60"
      }
    ]
  }
}
```

---

## Create Custom Field (V2)

Create a new custom field for custom objects or business entities.

**Method:** `POST`
**URL:** `/custom-fields/`
**Scope:** `locations/customFields.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `objectKey` | string | Yes | Object key (e.g., `invoice`, `business`) |
| `name` | string | Yes | Display name of the field |
| `dataType` | string | Yes | Data type (see [field-types.md](./field-types.md)) |
| `placeholder` | string | No | Placeholder text |
| `isRequired` | boolean | No | Whether field is required (default: false) |
| `isMultiple` | boolean | No | For selection types - allow multiple (default: false) |
| `folderId` | string | No | Folder ID to organize the field |
| `options` | array | Conditional | Required for selection types |
| `acceptedFormat` | array | Conditional | Required for FILE_UPLOAD type |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/custom-fields/' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_abc123",
    "objectKey": "invoice",
    "name": "Payment Method",
    "dataType": "SINGLE_OPTIONS",
    "placeholder": "Select payment method",
    "isRequired": true,
    "folderId": "folder_001",
    "options": [
      {
        "value": "credit_card",
        "label": "Credit Card"
      },
      {
        "value": "bank_transfer",
        "label": "Bank Transfer"
      },
      {
        "value": "check",
        "label": "Check"
      },
      {
        "value": "cash",
        "label": "Cash"
      }
    ]
  }'
```

### Response

Returns the created custom field object with HTTP status `201 Created`.

```json
{
  "customField": {
    "id": "cf_v2_new123",
    "name": "Payment Method",
    "fieldKey": "custom_object.invoice.payment_method",
    "dataType": "SINGLE_OPTIONS",
    "objectKey": "invoice",
    "position": 3,
    "folderId": "folder_001",
    "isRequired": true,
    "options": [
      {
        "value": "credit_card",
        "label": "Credit Card"
      },
      {
        "value": "bank_transfer",
        "label": "Bank Transfer"
      },
      {
        "value": "check",
        "label": "Check"
      },
      {
        "value": "cash",
        "label": "Cash"
      }
    ]
  }
}
```

---

## Update Custom Field (V2)

Update an existing custom field.

**Method:** `PUT`
**URL:** `/custom-fields/{id}`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The custom field ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `name` | string | No | Display name of the field |
| `placeholder` | string | No | Placeholder text |
| `isRequired` | boolean | No | Whether field is required |
| `isMultiple` | boolean | No | For selection types - allow multiple |
| `folderId` | string | No | Move to different folder |
| `position` | number | No | Display order |
| `options` | array | No | Updated options for selection types |

> **Note:** You cannot change `dataType` or `objectKey` after creation.

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/custom-fields/cf_v2_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_abc123",
    "name": "Payment Terms (Days)",
    "isRequired": false,
    "options": [
      {
        "value": "net_15",
        "label": "Net 15"
      },
      {
        "value": "net_30",
        "label": "Net 30"
      },
      {
        "value": "net_60",
        "label": "Net 60"
      },
      {
        "value": "net_90",
        "label": "Net 90"
      }
    ]
  }'
```

### Response

Returns the updated custom field object with HTTP status `200 OK`.

---

## Delete Custom Field (V2)

Delete a custom field. This is permanent and will remove all stored values.

**Method:** `DELETE`
**URL:** `/custom-fields/{id}`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The custom field ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/custom-fields/cf_v2_001?locationId=loc_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with confirmation message.

```json
{
  "success": true,
  "message": "Custom field deleted successfully"
}
```

> **Warning:** Deleting a custom field is permanent. All data stored in this field will be lost.

---

## Using Custom Fields in API Calls

### Setting Custom Field Values

When creating or updating records (contacts, opportunities, custom objects), include custom fields in the request:

**For Contacts (Legacy API):**
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "customFields": [
    {
      "id": "cf_001",
      "value": "email"
    },
    {
      "id": "cf_002",
      "value": "50"
    }
  ]
}
```

**For Custom Objects (V2 API):**
```json
{
  "objectKey": "invoice",
  "locationId": "loc_abc123",
  "fields": {
    "custom_object.invoice.payment_terms": "net_30",
    "custom_object.invoice.invoice_amount": 5000,
    "custom_object.invoice.payment_method": "credit_card"
  }
}
```

### Retrieving Custom Field Values

Custom field values are included in the response when fetching records:

```json
{
  "contact": {
    "id": "contact_123",
    "firstName": "John",
    "customFields": [
      {
        "id": "cf_001",
        "key": "contact.preferred_contact_method",
        "value": "email"
      }
    ]
  }
}
```

---

## Webhooks

The Custom Fields API supports webhooks for real-time notifications.

### Available Events

| Event | Trigger |
|-------|---------|
| `CustomFieldCreate` | New custom field created |
| `CustomFieldUpdate` | Custom field updated |
| `CustomFieldDelete` | Custom field deleted |

### Webhook Payload Example

```json
{
  "event": "CustomFieldCreate",
  "locationId": "loc_abc123",
  "customField": {
    "id": "cf_new123",
    "name": "Lead Source",
    "fieldKey": "contact.lead_source",
    "dataType": "SINGLE_OPTIONS",
    "model": "contact",
    "objectKey": null
  },
  "timestamp": "2026-02-04T10:30:00Z"
}
```

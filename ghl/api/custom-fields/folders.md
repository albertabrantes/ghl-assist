# Custom Fields API — Folders

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Folders organize custom fields into groups. The Legacy API supports folders for `contact`/`opportunity` models; the V2 API has native folder CRUD for Custom Objects and Business entities.

## Folder Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Create Folder (Legacy)](#create-folder-legacy) | POST | write | Create a folder for contact/opportunity fields |
| [Create Folder (V2)](#create-folder-v2) | POST | write | Create a folder to organize fields |
| [Update Folder (V2)](#update-folder-v2) | PUT | write | Update an existing folder |
| [Delete Folder (V2)](#delete-folder-v2) | DELETE | write | Delete a folder |

---

## Create Folder (Legacy)

Create a folder to organize custom fields for contacts or opportunities. This is an undocumented feature of the Legacy API that uses the same endpoint as Create Custom Field but with `documentType: "folder"`.

> **Note:** The official GHL API documentation only documents folder CRUD for the V2 API (Custom Objects/Business), but the Legacy API endpoint also supports folder creation for `contact` and `opportunity` models. Verified.

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
| `name` | string | Yes | Folder display name |
| `model` | string | Yes | Object type: `contact`, `opportunity` |
| `documentType` | string | Yes | Must be `"folder"` |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Pre-Bid Information",
    "model": "opportunity",
    "documentType": "folder"
  }'
```

### Response Example

```json
{
  "customFieldFolder": {
    "id": "folder_abc123",
    "model": "opportunity",
    "name": "Pre-Bid Information",
    "placeholder": "",
    "position": 2300,
    "documentType": "folder",
    "locationId": "loc_abc123",
    "dateAdded": "2026-02-11T16:47:28.646Z",
    "standard": false
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `customFieldFolder` | object | The created folder object |
| `customFieldFolder.id` | string | Unique folder ID (use as `parentId` when creating fields) |
| `customFieldFolder.model` | string | Object type the folder belongs to |
| `customFieldFolder.name` | string | Folder display name |
| `customFieldFolder.position` | number | Display order |
| `customFieldFolder.documentType` | string | Always `"folder"` |

### Usage: Assigning Fields to a Folder

After creating a folder, use the returned `id` as the `parentId` when creating fields:

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/loc_abc123/customFields' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Is the property accessible?",
    "dataType": "RADIO",
    "model": "opportunity",
    "parentId": "folder_abc123",
    "picklistOptions": ["Yes", "No"],
    "position": 100
  }'
```

> **Important:** Folders are NOT returned in the `GET /locations/{locationId}/customFields` response. They exist as `parentId` references on fields but the folder metadata itself is not included in list responses. To verify a folder exists, check if any fields reference its ID via `parentId`.

---

## Create Folder (V2)

Create a folder to organize custom fields.

**Method:** `POST`
**URL:** `/custom-fields/folder`
**Scope:** `locations/customFields.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `objectKey` | string | Yes | Object key (e.g., `invoice`, `business`) |
| `name` | string | Yes | Folder display name |
| `position` | number | No | Display order (default: last) |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/custom-fields/folder' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_abc123",
    "objectKey": "invoice",
    "name": "Shipping Information",
    "position": 2
  }'
```

### Response

```json
{
  "folder": {
    "id": "folder_new123",
    "name": "Shipping Information",
    "objectKey": "invoice",
    "position": 2,
    "createdAt": "2026-02-04T10:30:00Z"
  }
}
```

---

## Update Folder (V2)

Update an existing folder.

**Method:** `PUT`
**URL:** `/custom-fields/folder/{id}`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The folder ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `name` | string | No | Folder display name |
| `position` | number | No | Display order |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/custom-fields/folder/folder_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_abc123",
    "name": "Billing & Payment Information",
    "position": 0
  }'
```

### Response

Returns the updated folder object with HTTP status `200 OK`.

---

## Delete Folder (V2)

Delete a folder. Fields within the folder will be moved to the root level.

**Method:** `DELETE`
**URL:** `/custom-fields/folder/{id}`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The folder ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/custom-fields/folder/folder_001?locationId=loc_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with confirmation message.

```json
{
  "success": true,
  "message": "Folder deleted successfully. Fields moved to root level."
}
```

---

## Migration Guide: Legacy to V2

When migrating from Legacy API to V2 API for custom objects:

### Key Differences

| Aspect | Legacy API | V2 API |
|--------|-----------|--------|
| URL Pattern | `/locations/{id}/customFields` | `/custom-fields/` |
| Object Types | Contact, Opportunity | Custom Objects, Business |
| Organization | Folder support via `documentType: "folder"` (undocumented) | Native folder support |
| Field Keys | `contact.field_name` | `custom_object.obj.field_name` |
| Query Params | Path-based locationId | Query param locationId |

### Migration Steps

1. **Audit existing fields** in Legacy API
2. **Create corresponding folders** in V2 for organization
3. **Recreate fields** using V2 endpoints
4. **Update integrations** to use new field keys
5. **Test thoroughly** before deprecating Legacy fields
6. **Migrate data** from old fields to new fields
7. **Delete Legacy fields** once migration is complete

# Custom Fields API — Field Types & Reference

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Custom Fields API allows you to manage custom fields across various GHL objects including Contacts, Opportunities, and Custom Objects. There are two API versions available:

- **Legacy API** - For managing custom fields on Contacts and Opportunities
- **V2 API** - For managing custom fields on Custom Objects and Business entities

> **Important:** The V2 API currently supports only Custom Objects and Business entities. For Contacts and Opportunities, use the Legacy API.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `locations/customFields.readonly` | Read | View custom fields configuration |
| `locations/customFields.write` | Read/Write | Create, update, delete custom fields |

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

## Field Key Format

Custom field keys follow a specific naming convention:

| Object Type | Key Format | Example |
|-------------|------------|---------|
| Contact | `contact.{field_name}` | `contact.preferred_contact_method` |
| Opportunity | `opportunity.{field_name}` | `opportunity.project_budget` |
| Custom Object | `custom_object.{object_key}.{field_name}` | `custom_object.invoice.payment_terms` |
| Business | `business.{field_name}` | `business.industry_type` |

> **Note:** Field names use underscores and lowercase letters. They cannot start with a number.

---

## Data Types

Custom fields support the following data types:

### Text Data Types

| Type | Description | Example Use Case |
|------|-------------|------------------|
| `TEXT` | Short single-line text (max 255 chars) | Name, Title, Category |
| `LARGE_TEXT` | Multi-line text (max 5000 chars) | Notes, Description, Address |
| `TEXTBOX_LIST` | Multiple text entries | Tags, Skills, Interests |

### Numeric Data Types

| Type | Description | Example Use Case |
|------|-------------|------------------|
| `NUMERICAL` | Integer or decimal numbers | Quantity, Score, Rating |
| `MONETORY` | Currency values | Price, Budget, Revenue |

### Contact Data Types

| Type | Description | Example Use Case |
|------|-------------|------------------|
| `PHONE` | Phone number with validation | Mobile, Office Phone |
| `EMAIL` | Email address with validation | Secondary Email, Work Email |

### Date/Time Data Types

| Type | Description | Example Use Case |
|------|-------------|------------------|
| `DATE` | Date picker (YYYY-MM-DD) | Birth Date, Contract Date, Due Date |

### Selection Data Types

| Type | Description | Example Use Case |
|------|-------------|------------------|
| `CHECKBOX` | Checkboxes - supports multiple options with name/value pairs | Surfaces Being Painted, Services Interested In |
| `SINGLE_OPTIONS` | Dropdown - single selection | Priority Level, Status Type |
| `MULTIPLE_OPTIONS` | Multi-select dropdown - multiple selections | Tags, Categories |
| `RADIO` | Radio buttons - single selection | Payment Method, Preferred Time |

### Signature Data Type

| Type | Description | Example Use Case |
|------|-------------|------------------|
| `SIGNATURE` | Digital signature capture field | Foreman sign-off, Client approval, Completion certificates |

> **Verified:** `SIGNATURE` fields CAN be created via the Legacy API using `"dataType": "SIGNATURE"`. No special parameters needed — works the same as TEXT/DATE. The field renders as a signature capture pad in GHL forms.

### File Data Types

| Type | Description | Example Use Case |
|------|-------------|------------------|
| `FILE_UPLOAD` | File attachment with validation | Contract PDF, Profile Image, Documents |

#### Accepted File Formats for FILE_UPLOAD

**Images:**
- `.jpg`, `.jpeg`, `.png`, `.gif`, `.bmp`, `.webp`, `.svg`

**Documents:**
- `.pdf`, `.doc`, `.docx`, `.txt`, `.rtf`

**Spreadsheets:**
- `.xls`, `.xlsx`, `.csv`

**Presentations:**
- `.ppt`, `.pptx`

**Archives:**
- `.zip`, `.rar`

**Maximum File Size:** 10 MB per file

---

## Options Format by Data Type

Different data types require different `options` formats. Using the wrong format will cause errors:

| dataType | Parameter | Format | Example |
|----------|-----------|--------|---------|
| `RADIO` | `picklistOptions` | String array | `["Yes", "No"]` |
| `SINGLE_OPTIONS` | `options` (plain string array) | String array | `["Option A", "Option B"]` |
| `CHECKBOX` | `options` | **Plain string array only** | `["Interior Walls", "Ceilings", "Other"]` |
| `MULTIPLE_OPTIONS` | `options` (objects) | `[{value, label}]` | `[{"value":"opt1","label":"Option 1"}]` |

See [gotchas.md](./gotchas.md) for the validation quirks around `SINGLE_OPTIONS` and `CHECKBOX` options.

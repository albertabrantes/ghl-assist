# Store API — Store Settings

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Global store configuration: the shipping origin (ship-from address) and order notification emails. All Store endpoints use `altId` (Location ID) and `altType` (`location`).

## Store Settings Object

```json
{
  "_id": "0000000000000000price0006",
  "altId": "EXAMPLELocation00000",
  "altType": "location",
  "shippingOrigin": {
    "name": "My Awesome Store",
    "country": "US",
    "state": "CA",
    "city": "Los Angeles",
    "street1": "456 Commerce Blvd",
    "street2": "",
    "zip": "90001",
    "phone": "+1-310-555-0100",
    "email": "shipping@mystore.com"
  },
  "storeOrderNotification": {
    "enabled": true,
    "subject": "Order Confirmation - {{order_number}}",
    "emailTemplateId": "00000000000collection002",
    "defaultEmailTemplateId": "00000000000collection002"
  },
  "storeOrderFulfillmentNotification": {
    "enabled": true,
    "subject": "Your Order Has Shipped!",
    "emailTemplateId": "00000000000collection001",
    "defaultEmailTemplateId": "00000000000collection001"
  },
  "createdAt": "2024-01-23T12:00:00.000Z",
  "updatedAt": "2024-01-23T12:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | MongoDB ObjectId |
| `altId` | string | Location ID |
| `altType` | string | Always `location` |
| `shippingOrigin` | object | Ship-from address |
| `shippingOrigin.name` | string | Store/company name |
| `shippingOrigin.country` | string | ISO country code |
| `shippingOrigin.state` | string | State/province code |
| `shippingOrigin.city` | string | City name |
| `shippingOrigin.street1` | string | Street address line 1 |
| `shippingOrigin.street2` | string | Street address line 2 |
| `shippingOrigin.zip` | string | Postal/ZIP code |
| `shippingOrigin.phone` | string | Business phone |
| `shippingOrigin.email` | string | Business email |
| `storeOrderNotification` | object | Order confirmation email config |
| `storeOrderNotification.enabled` | boolean | Notifications enabled |
| `storeOrderNotification.subject` | string | Email subject line |
| `storeOrderNotification.emailTemplateId` | string | Custom email template ID |
| `storeOrderNotification.defaultEmailTemplateId` | string | Default email template ID |
| `storeOrderFulfillmentNotification` | object | Order fulfillment email config |
| `storeOrderFulfillmentNotification.enabled` | boolean | Notifications enabled |
| `storeOrderFulfillmentNotification.subject` | string | Email subject line |
| `storeOrderFulfillmentNotification.emailTemplateId` | string | Custom email template ID |
| `storeOrderFulfillmentNotification.defaultEmailTemplateId` | string | Default email template ID |
| `createdAt` | string | ISO 8601 timestamp |
| `updatedAt` | string | ISO 8601 timestamp |

---

## Create/Update Store Settings

Configure global store settings including shipping origin and email notifications. This endpoint creates settings if they don't exist, or updates if they do.

**Method:** `POST`
**URL:** `/store/store-setting`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `shippingOrigin` | object | Yes | Ship-from address |
| `shippingOrigin.name` | string | Yes | Store/company name |
| `shippingOrigin.country` | string | Yes | ISO country code |
| `shippingOrigin.state` | string | No | State/province code |
| `shippingOrigin.city` | string | Yes | City |
| `shippingOrigin.street1` | string | Yes | Street address 1 |
| `shippingOrigin.street2` | string | No | Street address 2 |
| `shippingOrigin.zip` | string | Yes | ZIP/postal code |
| `shippingOrigin.phone` | string | No | Business phone |
| `shippingOrigin.email` | string | No | Business email |
| `storeOrderNotification` | object | No | Order confirmation email |
| `storeOrderNotification.enabled` | boolean | Yes* | Enable notifications (* required when object is provided) |
| `storeOrderNotification.subject` | string | Yes* | Email subject |
| `storeOrderNotification.emailTemplateId` | string | Yes* | Template ID |
| `storeOrderNotification.defaultEmailTemplateId` | string | Yes* | Default template ID |
| `storeOrderFulfillmentNotification` | object | No | Fulfillment notification email |
| `storeOrderFulfillmentNotification.enabled` | boolean | Yes* | Enable notifications (* required when object is provided) |
| `storeOrderFulfillmentNotification.subject` | string | Yes* | Email subject |
| `storeOrderFulfillmentNotification.emailTemplateId` | string | Yes* | Template ID |
| `storeOrderFulfillmentNotification.defaultEmailTemplateId` | string | Yes* | Default template ID |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/store/store-setting' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "shippingOrigin": {
      "name": "My Awesome Store",
      "country": "US",
      "state": "CA",
      "city": "Los Angeles",
      "street1": "456 Commerce Blvd",
      "zip": "90001",
      "phone": "+1-310-555-0100",
      "email": "shipping@mystore.com"
    },
    "storeOrderNotification": {
      "enabled": true,
      "subject": "Order Confirmation - {{order_number}}",
      "emailTemplateId": "00000000000collection002",
      "defaultEmailTemplateId": "00000000000collection002"
    },
    "storeOrderFulfillmentNotification": {
      "enabled": true,
      "subject": "Your Order Has Shipped!",
      "emailTemplateId": "00000000000collection001",
      "defaultEmailTemplateId": "00000000000collection001"
    }
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Store settings saved",
  "data": {
    "_id": "0000000000000000price0006",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "shippingOrigin": {
      "name": "My Awesome Store",
      "country": "US",
      "state": "CA",
      "city": "Los Angeles",
      "street1": "456 Commerce Blvd",
      "street2": "",
      "zip": "90001",
      "phone": "+1-310-555-0100",
      "email": "shipping@mystore.com"
    },
    "storeOrderNotification": {
      "enabled": true,
      "subject": "Order Confirmation - {{order_number}}",
      "emailTemplateId": "00000000000collection002",
      "defaultEmailTemplateId": "00000000000collection002"
    },
    "storeOrderFulfillmentNotification": {
      "enabled": true,
      "subject": "Your Order Has Shipped!",
      "emailTemplateId": "00000000000collection001",
      "defaultEmailTemplateId": "00000000000collection001"
    },
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-23T12:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Get Store Settings

Retrieve global store settings for a location.

**Method:** `GET`
**URL:** `/store/store-setting`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/store/store-setting?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true,
  "message": "Store settings retrieved",
  "data": {
    "_id": "0000000000000000price0006",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "shippingOrigin": {
      "name": "My Awesome Store",
      "country": "US",
      "state": "CA",
      "city": "Los Angeles",
      "street1": "456 Commerce Blvd",
      "street2": "",
      "zip": "90001",
      "phone": "+1-310-555-0100",
      "email": "shipping@mystore.com"
    },
    "storeOrderNotification": {
      "enabled": true,
      "subject": "Order Confirmation - {{order_number}}",
      "emailTemplateId": "00000000000collection002",
      "defaultEmailTemplateId": "00000000000collection002"
    },
    "storeOrderFulfillmentNotification": {
      "enabled": true,
      "subject": "Your Order Has Shipped!",
      "emailTemplateId": "00000000000collection001",
      "defaultEmailTemplateId": "00000000000collection001"
    },
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-23T12:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

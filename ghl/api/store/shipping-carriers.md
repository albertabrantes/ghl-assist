# Store API — Shipping Carriers

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Register and manage third-party shipping carrier integrations (FedEx, UPS, etc.). All Store endpoints use `altId` (Location ID) and `altType` (`location`). See [gotchas.md](./gotchas.md) for scope and version-header notes.

## Shipping Carrier Object

```json
{
  "_id": "0000000000000000price0002",
  "altId": "EXAMPLELocation00000",
  "altType": "location",
  "name": "FedEx",
  "callbackUrl": "https://myapp.example.com/api/shipping-rates",
  "services": [
    { "name": "FedEx Ground", "value": "FEDEX_GROUND" },
    { "name": "FedEx 2Day", "value": "FEDEX_2DAY" },
    { "name": "FedEx Overnight", "value": "FEDEX_OVERNIGHT" }
  ],
  "allowsMultipleServiceSelection": true,
  "marketplaceAppId": "app_abc123",
  "createdAt": "2024-01-23T12:00:00.000Z",
  "updatedAt": "2024-01-23T12:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | MongoDB ObjectId — unique carrier identifier |
| `altId` | string | Location ID |
| `altType` | string | Always `location` |
| `name` | string | Carrier name (e.g., "FedEx", "UPS") |
| `callbackUrl` | string | Public URL endpoint GHL calls to retrieve shipping rates |
| `services` | array | Available carrier services |
| `services[].name` | string | Service display name (e.g., "Priority Mail Express International") |
| `services[].value` | string | Service identifier (e.g., "PriorityMailExpressInternational") |
| `allowsMultipleServiceSelection` | boolean | Whether seller can pick multiple services per rate |
| `marketplaceAppId` | string | GHL marketplace app ID (auto-populated) |
| `createdAt` | string | ISO 8601 timestamp |
| `updatedAt` | string | ISO 8601 timestamp |

---

## Create Shipping Carrier

Register a third-party shipping carrier integration.

**Method:** `POST`
**URL:** `/store/shipping-carrier`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `name` | string | Yes | Carrier name (e.g., "FedEx") |
| `callbackUrl` | string | Yes | Public URL endpoint for GHL to fetch rates from your service |
| `services` | array | No | Available services |
| `services[].name` | string | Yes* | Service display name (* required when `services` is provided) |
| `services[].value` | string | Yes* | Service identifier (* required when `services` is provided) |
| `allowsMultipleServiceSelection` | boolean | No | Allow selecting multiple services per rate |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/store/shipping-carrier' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "FedEx",
    "callbackUrl": "https://myapp.example.com/api/shipping-rates",
    "services": [
      { "name": "FedEx Ground", "value": "FEDEX_GROUND" },
      { "name": "FedEx 2Day", "value": "FEDEX_2DAY" },
      { "name": "FedEx Overnight", "value": "FEDEX_OVERNIGHT" }
    ],
    "allowsMultipleServiceSelection": true
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping carrier created",
  "data": {
    "_id": "0000000000000000price0002",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "FedEx",
    "callbackUrl": "https://myapp.example.com/api/shipping-rates",
    "services": [
      { "name": "FedEx Ground", "value": "FEDEX_GROUND" },
      { "name": "FedEx 2Day", "value": "FEDEX_2DAY" },
      { "name": "FedEx Overnight", "value": "FEDEX_OVERNIGHT" }
    ],
    "allowsMultipleServiceSelection": true,
    "marketplaceAppId": "app_abc123",
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-23T12:00:00.000Z"
  }
}
```

### Important Notes

- The `callbackUrl` must be a publicly accessible HTTPS endpoint.
- GHL will POST to this URL to retrieve real-time rates from your carrier integration.
- The `marketplaceAppId` is auto-populated based on your OAuth app.

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## List Shipping Carriers

Retrieve all shipping carriers configured for a location.

**Method:** `GET`
**URL:** `/store/shipping-carrier`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/store/shipping-carrier?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping carriers retrieved",
  "data": [
    {
      "_id": "0000000000000000price0002",
      "altId": "EXAMPLELocation00000",
      "altType": "location",
      "name": "FedEx",
      "callbackUrl": "https://myapp.example.com/api/shipping-rates",
      "services": [
        { "name": "FedEx Ground", "value": "FEDEX_GROUND" },
        { "name": "FedEx 2Day", "value": "FEDEX_2DAY" }
      ],
      "allowsMultipleServiceSelection": true,
      "marketplaceAppId": "app_abc123",
      "createdAt": "2024-01-23T12:00:00.000Z",
      "updatedAt": "2024-01-23T12:00:00.000Z"
    }
  ]
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Get Shipping Carrier

Retrieve a single shipping carrier by ID.

**Method:** `GET`
**URL:** `/store/shipping-carrier/{shippingCarrierId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingCarrierId` | string | Yes | The shipping carrier ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/store/shipping-carrier/0000000000000000price0002?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping carrier retrieved",
  "data": {
    "_id": "0000000000000000price0002",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "FedEx",
    "callbackUrl": "https://myapp.example.com/api/shipping-rates",
    "services": [
      { "name": "FedEx Ground", "value": "FEDEX_GROUND" },
      { "name": "FedEx 2Day", "value": "FEDEX_2DAY" },
      { "name": "FedEx Overnight", "value": "FEDEX_OVERNIGHT" }
    ],
    "allowsMultipleServiceSelection": true,
    "marketplaceAppId": "app_abc123",
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-23T12:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Update Shipping Carrier

Update an existing shipping carrier configuration.

**Method:** `PUT`
**URL:** `/store/shipping-carrier/{shippingCarrierId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingCarrierId` | string | Yes | The shipping carrier ID |

### Request Body

Same fields as [Create Shipping Carrier](#create-shipping-carrier) — all fields are optional. Include only the fields you want to change.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/store/shipping-carrier/0000000000000000price0002' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "FedEx Updated",
    "services": [
      { "name": "FedEx Ground", "value": "FEDEX_GROUND" },
      { "name": "FedEx 2Day", "value": "FEDEX_2DAY" },
      { "name": "FedEx Overnight", "value": "FEDEX_OVERNIGHT" },
      { "name": "FedEx Express Saver", "value": "FEDEX_EXPRESS_SAVER" }
    ]
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping carrier updated",
  "data": {
    "_id": "0000000000000000price0002",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "FedEx Updated",
    "callbackUrl": "https://myapp.example.com/api/shipping-rates",
    "services": [
      { "name": "FedEx Ground", "value": "FEDEX_GROUND" },
      { "name": "FedEx 2Day", "value": "FEDEX_2DAY" },
      { "name": "FedEx Overnight", "value": "FEDEX_OVERNIGHT" },
      { "name": "FedEx Express Saver", "value": "FEDEX_EXPRESS_SAVER" }
    ],
    "allowsMultipleServiceSelection": true,
    "marketplaceAppId": "app_abc123",
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-24T09:30:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Delete Shipping Carrier

Delete a shipping carrier from a location.

**Method:** `DELETE`
**URL:** `/store/shipping-carrier/{shippingCarrierId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingCarrierId` | string | Yes | The shipping carrier ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/store/shipping-carrier/0000000000000000price0002?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping carrier deleted"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

# Store API — Shipping Zone Rates

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Shipping rates are pricing rules nested inside a shipping zone (see [shipping-zones.md](./shipping-zones.md)). A rate can be flat-amount or a carrier rate that references a registered carrier (see [shipping-carriers.md](./shipping-carriers.md)). All Store endpoints use `altId` (Location ID) and `altType` (`location`).

## Shipping Rate Object

```json
{
  "_id": "0000000000000000price0003",
  "altId": "EXAMPLELocation00000",
  "altType": "location",
  "shippingZoneId": "0000000000000000price0001",
  "name": "Standard Shipping",
  "description": "Ships in 3-5 business days",
  "currency": "USD",
  "amount": 5.99,
  "conditionType": "none",
  "minCondition": 0,
  "maxCondition": 0,
  "isCarrierRate": false,
  "shippingCarrierId": "",
  "percentageOfRateFee": 0,
  "shippingCarrierServices": [],
  "createdAt": "2024-01-23T12:00:00.000Z",
  "updatedAt": "2024-01-23T12:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | MongoDB ObjectId |
| `altId` | string | Location ID |
| `altType` | string | Always `location` |
| `shippingZoneId` | string | Parent shipping zone ID |
| `name` | string | Rate name (e.g., "Standard Shipping") |
| `description` | string | Delivery description (e.g., "Ships in 3-5 business days") |
| `currency` | string | ISO currency code (e.g., "USD") |
| `amount` | number | Rate amount (0 = free). For carrier rates, this is the fixed handling fee. |
| `conditionType` | string | `none`, `price`, or `weight` |
| `minCondition` | number | Min value for condition (0/null = no minimum) |
| `maxCondition` | number | Max value for condition (0/null = no maximum) |
| `isCarrierRate` | boolean | Whether this uses a third-party carrier |
| `shippingCarrierId` | string | Carrier ID (when `isCarrierRate` is true) |
| `percentageOfRateFee` | number | Percentage markup on carrier rate |
| `shippingCarrierServices` | array | Selected carrier services |
| `shippingCarrierServices[].name` | string | Service name |
| `shippingCarrierServices[].value` | string | Service value/ID |
| `createdAt` | string | ISO 8601 timestamp |
| `updatedAt` | string | ISO 8601 timestamp |

---

## Create Shipping Rate

Create a rate within a shipping zone.

**Method:** `POST`
**URL:** `/store/shipping-zone/{shippingZoneId}/shipping-rate`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingZoneId` | string | Yes | The shipping zone ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `name` | string | Yes | Rate name |
| `description` | string | No | Delivery description |
| `currency` | string | Yes | ISO currency code |
| `amount` | number | Yes | Rate amount (0 = free). For carrier rates: handling fee. |
| `conditionType` | string | Yes | `none`, `price`, or `weight` |
| `minCondition` | number | Yes | Minimum threshold (0/null = no min) |
| `maxCondition` | number | Yes | Maximum threshold (0/null = no max) |
| `isCarrierRate` | boolean | No | Whether this uses a third-party carrier |
| `shippingCarrierId` | string | Yes* | Carrier ID (* required if `isCarrierRate` is true) |
| `percentageOfRateFee` | number | No | Markup percentage on carrier rate |
| `shippingCarrierServices` | array | No | Selected carrier services |
| `shippingCarrierServices[].name` | string | No | Service name |
| `shippingCarrierServices[].value` | string | No | Service value/ID |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/store/shipping-zone/0000000000000000price0001/shipping-rate' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "Free Shipping (Orders over $50)",
    "description": "Free standard shipping on orders over $50",
    "currency": "USD",
    "amount": 0,
    "conditionType": "price",
    "minCondition": 50,
    "maxCondition": 0,
    "isCarrierRate": false,
    "shippingCarrierId": ""
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping rate created",
  "data": {
    "_id": "0000000000000000price0005",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "shippingZoneId": "0000000000000000price0001",
    "name": "Free Shipping (Orders over $50)",
    "description": "Free standard shipping on orders over $50",
    "currency": "USD",
    "amount": 0,
    "conditionType": "price",
    "minCondition": 50,
    "maxCondition": 0,
    "isCarrierRate": false,
    "shippingCarrierId": "",
    "percentageOfRateFee": 0,
    "shippingCarrierServices": [],
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-23T12:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## List Shipping Rates

Retrieve all shipping rates within a shipping zone.

**Method:** `GET`
**URL:** `/store/shipping-zone/{shippingZoneId}/shipping-rate`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingZoneId` | string | Yes | The shipping zone ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `limit` | number | No | Pagination limit |
| `offset` | number | No | Pagination offset |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/store/shipping-zone/0000000000000000price0001/shipping-rate?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "total": 2,
  "data": [
    {
      "_id": "0000000000000000price0003",
      "altId": "EXAMPLELocation00000",
      "altType": "location",
      "shippingZoneId": "0000000000000000price0001",
      "name": "Standard Shipping",
      "description": "Ships in 3-5 business days",
      "currency": "USD",
      "amount": 5.99,
      "conditionType": "none",
      "minCondition": 0,
      "maxCondition": 0,
      "isCarrierRate": false,
      "createdAt": "2024-01-23T12:00:00.000Z",
      "updatedAt": "2024-01-23T12:00:00.000Z"
    },
    {
      "_id": "0000000000000000price0005",
      "altId": "EXAMPLELocation00000",
      "altType": "location",
      "shippingZoneId": "0000000000000000price0001",
      "name": "Free Shipping (Orders over $50)",
      "description": "Free standard shipping on orders over $50",
      "currency": "USD",
      "amount": 0,
      "conditionType": "price",
      "minCondition": 50,
      "maxCondition": 0,
      "isCarrierRate": false,
      "createdAt": "2024-01-23T14:00:00.000Z",
      "updatedAt": "2024-01-23T14:00:00.000Z"
    }
  ]
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Get Shipping Rate

Retrieve a single shipping rate by ID.

**Method:** `GET`
**URL:** `/store/shipping-zone/{shippingZoneId}/shipping-rate/{shippingRateId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingZoneId` | string | Yes | The shipping zone ID |
| `shippingRateId` | string | Yes | The shipping rate ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/store/shipping-zone/0000000000000000price0001/shipping-rate/0000000000000000price0003?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping rate retrieved",
  "data": {
    "_id": "0000000000000000price0003",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "shippingZoneId": "0000000000000000price0001",
    "name": "Standard Shipping",
    "description": "Ships in 3-5 business days",
    "currency": "USD",
    "amount": 5.99,
    "conditionType": "none",
    "minCondition": 0,
    "maxCondition": 0,
    "isCarrierRate": false,
    "shippingCarrierId": "",
    "percentageOfRateFee": 0,
    "shippingCarrierServices": [],
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-23T12:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Update Shipping Rate

Update an existing shipping rate.

**Method:** `PUT`
**URL:** `/store/shipping-zone/{shippingZoneId}/shipping-rate/{shippingRateId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingZoneId` | string | Yes | The shipping zone ID |
| `shippingRateId` | string | Yes | The shipping rate ID |

### Request Body

Same fields as [Create Shipping Rate](#create-shipping-rate) — all fields are optional. Include only the fields you want to change.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/store/shipping-zone/0000000000000000price0001/shipping-rate/0000000000000000price0003' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "Standard Shipping (Updated)",
    "amount": 7.99,
    "description": "Ships in 5-7 business days"
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping rate updated",
  "data": {
    "_id": "0000000000000000price0003",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "shippingZoneId": "0000000000000000price0001",
    "name": "Standard Shipping (Updated)",
    "description": "Ships in 5-7 business days",
    "currency": "USD",
    "amount": 7.99,
    "conditionType": "none",
    "minCondition": 0,
    "maxCondition": 0,
    "isCarrierRate": false,
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-24T11:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Delete Shipping Rate

Delete a shipping rate from a zone.

**Method:** `DELETE`
**URL:** `/store/shipping-zone/{shippingZoneId}/shipping-rate/{shippingRateId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingZoneId` | string | Yes | The shipping zone ID |
| `shippingRateId` | string | Yes | The shipping rate ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/store/shipping-zone/0000000000000000price0001/shipping-rate/0000000000000000price0003?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping rate deleted"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

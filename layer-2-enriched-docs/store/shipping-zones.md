# Store API — Shipping Zones

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Shipping zones are geographic regions (countries/states) that shipping rates apply to. Rates live within zones — see [shipping-rates.md](./shipping-rates.md). All Store endpoints use `altId` (Location ID) and `altType` (`location`).

## Shipping Zone Object

```json
{
  "_id": "0000000000000000price0001",
  "altId": "EXAMPLELocation00000",
  "altType": "location",
  "name": "Domestic US",
  "countries": [
    {
      "code": "US",
      "states": [
        { "code": "CA" },
        { "code": "NY" },
        { "code": "TX" }
      ]
    }
  ],
  "shippingRates": [],
  "createdAt": "2024-01-23T12:00:00.000Z",
  "updatedAt": "2024-01-23T12:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | MongoDB ObjectId |
| `altId` | string | Location ID |
| `altType` | string | Always `location` |
| `name` | string | Zone name (e.g., "North America", "Europe") |
| `countries` | array | Countries in this zone |
| `countries[].code` | string | ISO 3166-1 alpha-2 country code (e.g., "US", "CA", "GB") |
| `countries[].states` | array | States/provinces (empty = all states) |
| `countries[].states[].code` | string | State/province code (e.g., "CA", "NY", "ON") |
| `shippingRates` | array | Nested shipping rates (when `withShippingRate=true`) |
| `createdAt` | string | ISO 8601 timestamp |
| `updatedAt` | string | ISO 8601 timestamp |

---

## Create Shipping Zone

Define a geographic region for shipping.

**Method:** `POST`
**URL:** `/store/shipping-zone`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `name` | string | Yes | Zone name |
| `countries` | array | Yes | Countries in this zone |
| `countries[].code` | string | Yes | ISO 3166-1 alpha-2 code (e.g., `US`) |
| `countries[].states` | array | No | State filters (empty = all states) |
| `countries[].states[].code` | string | Yes | State/province code (e.g., `CA`, `NY`) |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/store/shipping-zone' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "Domestic US",
    "countries": [
      {
        "code": "US",
        "states": [
          { "code": "CA" },
          { "code": "NY" },
          { "code": "TX" }
        ]
      }
    ]
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping zone created",
  "data": {
    "_id": "0000000000000000price0001",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "Domestic US",
    "countries": [
      {
        "code": "US",
        "states": [
          { "code": "CA" },
          { "code": "NY" },
          { "code": "TX" }
        ]
      }
    ],
    "shippingRates": [],
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-23T12:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## List Shipping Zones

Retrieve all shipping zones for a location.

**Method:** `GET`
**URL:** `/store/shipping-zone`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `limit` | number | No | Pagination limit |
| `offset` | number | No | Pagination offset |
| `withShippingRate` | boolean | No | Include nested shipping rates array |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/store/shipping-zone?altId=EXAMPLELocation00000&altType=location&withShippingRate=true' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "total": 2,
  "data": [
    {
      "_id": "0000000000000000price0001",
      "altId": "EXAMPLELocation00000",
      "altType": "location",
      "name": "Domestic US",
      "countries": [
        {
          "code": "US",
          "states": [
            { "code": "CA" },
            { "code": "NY" },
            { "code": "TX" }
          ]
        }
      ],
      "shippingRates": [
        {
          "_id": "0000000000000000price0003",
          "name": "Standard Shipping",
          "amount": 5.99,
          "currency": "USD"
        }
      ],
      "createdAt": "2024-01-23T12:00:00.000Z",
      "updatedAt": "2024-01-23T12:00:00.000Z"
    },
    {
      "_id": "0000000000000000price0007",
      "altId": "EXAMPLELocation00000",
      "altType": "location",
      "name": "International",
      "countries": [
        { "code": "CA", "states": [] },
        { "code": "GB", "states": [] }
      ],
      "shippingRates": [],
      "createdAt": "2024-01-23T14:00:00.000Z",
      "updatedAt": "2024-01-23T14:00:00.000Z"
    }
  ]
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Get Shipping Zone

Retrieve a single shipping zone by ID.

**Method:** `GET`
**URL:** `/store/shipping-zone/{shippingZoneId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingZoneId` | string | Yes | The shipping zone ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `withShippingRate` | boolean | No | Include nested shipping rates array |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/store/shipping-zone/0000000000000000price0001?altId=EXAMPLELocation00000&altType=location&withShippingRate=true' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping zone retrieved",
  "data": {
    "_id": "0000000000000000price0001",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "Domestic US",
    "countries": [
      {
        "code": "US",
        "states": [
          { "code": "CA" },
          { "code": "NY" },
          { "code": "TX" }
        ]
      }
    ],
    "shippingRates": [
      {
        "_id": "0000000000000000price0003",
        "name": "Standard Shipping",
        "description": "Ships in 3-5 business days",
        "currency": "USD",
        "amount": 5.99
      }
    ],
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-23T12:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Update Shipping Zone

Update an existing shipping zone.

**Method:** `PUT`
**URL:** `/store/shipping-zone/{shippingZoneId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingZoneId` | string | Yes | The shipping zone ID |

### Request Body

Same fields as [Create Shipping Zone](#create-shipping-zone) — all fields are optional. Include only the fields you want to change.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/store/shipping-zone/0000000000000000price0001' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "Domestic US - All States",
    "countries": [
      {
        "code": "US",
        "states": []
      }
    ]
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping zone updated",
  "data": {
    "_id": "0000000000000000price0001",
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "name": "Domestic US - All States",
    "countries": [
      {
        "code": "US",
        "states": []
      }
    ],
    "createdAt": "2024-01-23T12:00:00.000Z",
    "updatedAt": "2024-01-24T10:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Delete Shipping Zone

Delete a shipping zone from a location.

**Method:** `DELETE`
**URL:** `/store/shipping-zone/{shippingZoneId}`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `shippingZoneId` | string | Yes | The shipping zone ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/store/shipping-zone/0000000000000000price0001?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true,
  "message": "Shipping zone deleted"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

---

## Get Available Shipping Rates

Query which shipping rates apply to a specific order based on customer location, order amount, weight, and products. This is a POST endpoint despite being a query operation, due to the complex request body.

**Method:** `POST`
**URL:** `/store/shipping-zone/shipping-rates`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `country` | string | Yes | Customer country code (ISO 3166-1 alpha-2) |
| `address` | object | No | Full customer address |
| `address.name` | string | No | Customer name |
| `address.companyName` | string | No | Company name |
| `address.addressLine1` | string | No | Street address |
| `address.country` | string | Yes* | Country code (* required when `address` is provided) |
| `address.state` | string | No | State code |
| `address.city` | string | No | City |
| `address.zip` | string | No | ZIP/postal code |
| `address.phone` | string | No | Phone |
| `address.email` | string | No | Email |
| `totalOrderAmount` | number | Yes | Order total (min: 0.01) |
| `totalOrderWeight` | number | Yes | Order weight in kg |
| `weightAvailable` | boolean | No | Flag that weight is pre-calculated |
| `amountAvailable` | string | No | Skip backend amount calculation |
| `source` | object | Yes | Order source |
| `source.type` | string | Yes | Source type (see enum below) |
| `source.subType` | string | No | Source sub-type (see enum below) |
| `products` | array | Yes | Products in the order |
| `products[].id` | string | Yes | Product/price ID |
| `products[].qty` | number | Yes | Quantity |
| `couponCode` | string | No | Discount coupon code |

**`source.type` enum values:**
`funnel`, `website`, `invoice`, `calendar`, `text2Pay`, `document_contracts`, `membership`, `mobile_app`, `communities`, `point_of_sale`, `manual`, `form`, `survey`, `payment_link`, `external`

**`source.subType` enum values:**
`one_step_order_form`, `two_step_order_form`, `upsell`, `tap_to_pay`, `card_payment`, `store`, `contact_view`, `email_campaign`, `payments_dashboard`, `shopify`, `subscription_view`, `store_upsell`, `woocommerce`, `service`, `meeting`, `imported_csv`, `qr_code`

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/store/shipping-zone/shipping-rates' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "country": "US",
    "address": {
      "name": "John Doe",
      "addressLine1": "123 Main St",
      "country": "US",
      "state": "CA",
      "city": "Los Angeles",
      "zip": "90001"
    },
    "totalOrderAmount": 99.99,
    "totalOrderWeight": 0.5,
    "source": { "type": "website", "subType": "store" },
    "products": [
      { "id": "00000000000000order00001", "qty": 2 }
    ]
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Available shipping rates",
  "data": [
    {
      "_id": "0000000000000000price0003",
      "name": "Standard Shipping",
      "description": "Arrives in 5-7 business days",
      "currency": "USD",
      "amount": 5.99,
      "isCarrierRate": false,
      "shippingZoneId": "0000000000000000price0001"
    },
    {
      "_id": "0000000000000000price0004",
      "name": "Express via FedEx",
      "description": "2-day delivery",
      "currency": "USD",
      "amount": 2.50,
      "isCarrierRate": true,
      "shippingCarrierId": "0000000000000000price0002",
      "percentageOfRateFee": 10,
      "shippingCarrierServices": [
        { "name": "FedEx 2Day", "value": "FEDEX_2DAY" }
      ],
      "shippingZoneId": "0000000000000000price0001"
    }
  ]
}
```

### Important Notes

- For carrier rates, `amount` is the fixed handling fee, and the actual carrier rate is fetched via the carrier's `callbackUrl`.
- The `source` object determines which pricing rules apply (different sources may have different shipping configurations).

> **Status:** 🔬 Unverified — from OpenAPI spec research, not tested against a live API.

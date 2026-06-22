# Payments API — Integrations & Custom Providers

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

This file covers white-label payment provider integrations (Authorize.net/NMI based), custom payment provider integrations for marketplace apps, the custom provider integration guide, and webhook events.

> **Note:** White-label Integration endpoints can only be invoked using **marketplace-app tokens** (not regular location or agency tokens).

## Custom Provider Config Object

```json
{
  "locationId": "loc_abc123",
  "queryUrl": "https://your-server.com/ghl/payment-operations",
  "paymentsUrl": "https://your-server.com/checkout",
  "apiKey": "sk_live_your_secret_key",
  "publishableKey": "pk_live_your_public_key",
  "liveMode": {},
  "testMode": {}
}
```

| Field | Type | Description |
|-------|------|-------------|
| `locationId` | string | GHL location ID |
| `queryUrl` | string | Backend endpoint URL for server-side payment operations |
| `paymentsUrl` | string | Frontend checkout iframe URL |
| `apiKey` | string | Private API key for backend verification |
| `publishableKey` | string | Public key for frontend use |
| `liveMode` | object | Live mode configuration |
| `testMode` | object | Test mode configuration |

---

## Create White-label Integration Provider

Create a new white-label payment integration provider. These are payment providers built on top of Authorize.net or NMI, allowing agencies to present custom-branded payment integrations.

**Method:** `POST`
**URL:** `/payments/integrations/provider/whitelabel`
**Scope:** `payments/integration.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `name` | string | Yes | Display name for the integration |
| `description` | string | No | Description of the integration |
| `provider` | string | Yes | Underlying provider: `"authorize_net"` or `"nmi"` |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/payments/integrations/provider/whitelabel' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Custom Payment Gateway",
    "description": "Our branded payment solution",
    "provider": "authorize_net"
  }'
```

### Response Example

```json
{
  "success": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Requires marketplace-app token.

---

## List White-label Integration Providers

Retrieve a paginated list of white-label integration providers.

**Method:** `GET`
**URL:** `/payments/integrations/provider/whitelabel`
**Scope:** `payments/integration.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/integrations/provider/whitelabel?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "providers": [
    {
      "name": "Custom Payment Gateway",
      "description": "Our branded payment solution",
      "provider": "authorize_net"
    }
  ]
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Requires marketplace-app token.

---

## Create Custom Provider Integration

Create a new association between your marketplace app and a GHL location. This is the first step in setting up a custom payment provider -- it registers your app as a payment provider for the location.

**Method:** `POST`
**URL:** `/payments/custom-provider/provider`
**Scope:** `payments/custom-provider.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | GHL location ID |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/payments/custom-provider/provider' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_abc123"
  }'
```

### Response Example

```json
{
  "success": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Delete Custom Provider Integration

Remove the association between your marketplace app and a GHL location. This does not remove the payment configuration -- use [Disconnect Provider Config](#disconnect-provider-config) for that.

**Method:** `DELETE`
**URL:** `/payments/custom-provider/provider`
**Scope:** `payments/custom-provider.write`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | GHL location ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/payments/custom-provider/provider?locationId=loc_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "success": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Fetch Provider Config

Retrieve the existing payment configuration for a location.

**Method:** `GET`
**URL:** `/payments/custom-provider/connect`
**Scope:** `payments/custom-provider.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | GHL location ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/custom-provider/connect?locationId=loc_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "locationId": "loc_abc123",
  "queryUrl": "https://your-server.com/ghl/payment-operations",
  "paymentsUrl": "https://your-server.com/checkout",
  "publishableKey": "pk_live_your_public_key",
  "liveMode": {},
  "testMode": {}
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Create Provider Config

Create a new payment configuration for a location with your custom provider. This configures the URLs and keys GHL uses to communicate with your payment backend and load your checkout frontend.

**Method:** `POST`
**URL:** `/payments/custom-provider/connect`
**Scope:** `payments/custom-provider.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | GHL location ID |
| `queryUrl` | string | Yes | Backend endpoint URL for payment operations |
| `paymentsUrl` | string | Yes | Frontend checkout iframe URL |
| `apiKey` | string | Yes | Private API key for backend verification |
| `publishableKey` | string | Yes | Public key for frontend use |
| `liveMode` | object | No | Live mode configuration |
| `testMode` | object | No | Test mode configuration |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/payments/custom-provider/connect' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_abc123",
    "queryUrl": "https://your-server.com/ghl/payment-operations",
    "paymentsUrl": "https://your-server.com/checkout",
    "apiKey": "sk_live_your_secret_key",
    "publishableKey": "pk_live_your_public_key"
  }'
```

### Response Example

```json
{
  "success": true,
  "config": {
    "locationId": "loc_abc123",
    "queryUrl": "https://your-server.com/ghl/payment-operations",
    "paymentsUrl": "https://your-server.com/checkout",
    "publishableKey": "pk_live_your_public_key"
  }
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Disconnect Provider Config

Remove the payment configuration for a location, effectively disconnecting your custom provider from the location.

**Method:** `POST`
**URL:** `/payments/custom-provider/disconnect`
**Scope:** `payments/custom-provider.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | GHL location ID |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/payments/custom-provider/disconnect' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_abc123"
  }'
```

### Response Example

```json
{
  "success": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Update Marketplace App Capabilities

Toggle which payment capabilities your custom provider supports. This controls what payment features are available when your custom provider is connected to a location.

**Method:** `PUT`
**URL:** `/payments/custom-provider/capabilities`
**Scope:** `payments/custom-provider.write`

### Request Body

Capabilities include:

| Capability | Description |
|------------|-------------|
| One-time payments | Single charge transactions |
| Recurring payments | Automatic subscription billing |
| Off-session payments | Charging saved payment methods without customer present |
| Saved payment methods | Storing and reusing payment methods |
| Subscription schedules | Scheduled subscription management |
| Manual subscriptions (SaaS) | Custom subscription management for SaaS use cases |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/payments/custom-provider/capabilities' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_abc123",
    "capabilities": {
      "oneTimePayments": true,
      "recurringPayments": true,
      "offSessionPayments": true,
      "savedPaymentMethods": true,
      "subscriptionSchedules": false,
      "manualSubscriptions": false
    }
  }'
```

### Response Example

```json
{
  "success": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Custom Provider Integration Guide

When building a custom payment provider for GHL, the architecture involves two key URLs:

- **`queryUrl`** -- Your backend endpoint that GHL calls for server-side payment operations
- **`paymentsUrl`** -- Your frontend checkout page URL loaded inside an iframe for payment collection

### queryUrl Request Types

GHL sends POST requests to your `queryUrl` with a `type` field indicating the operation:

| Type Value | Description |
|------------|-------------|
| `list_payment_methods` | List saved payment methods for a contact |
| `charge_payment_method` | Charge a saved payment method (off-session) |
| `create_subscription` | Create a manual subscription |
| `cancel_subscription` | Cancel an existing subscription |
| `verify` | Verify a transaction |

Each request includes the `apiKey` you configured so your server can authenticate that the request is coming from GHL.

### Iframe postMessage Structure

When GHL loads your `paymentsUrl` in an iframe, it sends a `postMessage` with the following structure:

```json
{
  "type": "payment_initiate_props",
  "publishableKey": "pk_live_your_public_key",
  "amount": 100.00,
  "currency": "USD",
  "mode": "payment",
  "productDetails": {
    "productId": "prod_123",
    "priceId": "price_123"
  },
  "contact": {
    "id": "contact_123",
    "name": "John Doe",
    "email": "john@example.com",
    "contact": "+1234567890"
  },
  "orderId": "order_123",
  "transactionId": "txn_123",
  "subscriptionId": "sub_123",
  "locationId": "loc_123"
}
```

| Property | Type | Description |
|----------|------|-------------|
| `type` | string | Always `"payment_initiate_props"` |
| `publishableKey` | string | Your public key for frontend identification |
| `amount` | number | Payment amount |
| `currency` | string | ISO currency code (e.g., `"USD"`) |
| `mode` | string | Payment mode (e.g., `"payment"`, `"subscription"`) |
| `productDetails` | object | Product and price identifiers |
| `contact` | object | Customer contact information (id, name, email, phone) |
| `orderId` | string | GHL order ID |
| `transactionId` | string | GHL transaction ID |
| `subscriptionId` | string | GHL subscription ID (if applicable) |
| `locationId` | string | GHL location ID |

---

## Webhook Events

### Order Webhook Events

| Event | Description |
|-------|-------------|
| `OrderCreate` | Fired when an order is created |
| `OrderStatusUpdate` | Fired when an order's status changes |

### Custom Provider Subscription Webhook Events

These events are sent **to GHL** by your custom provider to notify GHL of subscription state changes:

| Event | Description |
|-------|-------------|
| `subscription.charged` | Recurring charge succeeded |
| `subscription.trialing` | Subscription entered trial |
| `subscription.active` | Subscription activated |
| `subscription.updated` | Subscription updated |
| `payment.captured` | Payment captured |

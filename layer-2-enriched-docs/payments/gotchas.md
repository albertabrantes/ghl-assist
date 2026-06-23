# Payments API — Gotchas, Errors & Notes

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Payments API handles orders, transactions, subscriptions, coupons, payment provider integrations, and custom payment provider configurations. It contains **24 endpoints** organized across **8 sub-groups**, all under the `/payments/` path prefix.

The Payments API is closely related to the [Invoices API](../invoices/README.md). Recording a payment on an invoice creates a corresponding transaction in the Payments system. Orders created through GHL funnels or checkout pages appear in the Payments orders list, and their associated transactions track individual charges.

The API is organized into 8 sub-groups:
- **White-label Integrations** -- Create and list white-label payment provider integrations (Authorize.net/NMI based)
- **Orders** -- List, retrieve, record payments, and migrate order payment statuses
- **Order Fulfillments** -- Create and list fulfillment records for orders
- **Order Notes** -- List notes attached to orders
- **Transactions** -- List and retrieve individual payment transactions (charges, refunds, transfers)
- **Subscriptions** -- List and retrieve recurring subscriptions
- **Coupons** -- Full CRUD for promotional discount coupons
- **Custom Payment Providers** -- Marketplace app integration for custom payment gateways

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `payments/orders.readonly` | Read | View orders and fulfillments |
| `payments/orders.write` | Write | Create order fulfillments |
| `payments/orders.collectPayment` | Write | Record order payments |
| `payments/transactions.readonly` | Read | View transactions |
| `payments/subscriptions.readonly` | Read | View subscriptions |
| `payments/coupons.readonly` | Read | View coupons |
| `payments/coupons.write` | Read/Write | Create, update, delete coupons |
| `payments/integration.readonly` | Read | View white-label integration providers |
| `payments/integration.write` | Write | Create white-label integration providers |
| `payments/custom-provider.readonly` | Read | View custom provider configurations |
| `payments/custom-provider.write` | Read/Write | Create, update, delete custom providers |

## Authentication

All requests require:

```
Authorization: Bearer {access_token}
Version: 2021-07-28
```

**Note:** White-label Integration endpoints can only be invoked using **marketplace-app tokens** (not regular location or agency tokens).

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 400 | Bad Request -- invalid parameters |
| 401 | Unauthorized -- invalid/missing token or insufficient scopes |
| 404 | Not Found -- resource does not exist |
| 422 | Unprocessable Entity -- validation failure |
| 429 | Too Many Requests -- rate limit exceeded |

---

## Gotchas & Notes

- **Relationship to Invoices API:** Recording a payment on an invoice (via `POST /invoices/{invoiceId}/record-payment`) creates a corresponding transaction in the Payments system. Orders created through GHL funnels or checkout pages appear in the Payments orders list and their associated transactions track individual charges. When an invoice is paid via an online payment method, GHL automatically creates both the transaction record and updates the order status.
- **altId/altType pattern:** Most Payments endpoints require `altId` (location ID) and `altType` (`"location"`) either as query parameters (GET/DELETE) or in the request body (POST/PUT).
- **White-label endpoints** require marketplace-app tokens, not regular location or agency tokens.
- **Migrate Order Payment Status** is an internal/migration endpoint not intended for general developer use.
- **Create Coupon returns HTTP 201** (`201 Created`) instead of the standard `200 OK`.
- **Coupon `code` field** is immutable after creation -- the promotional code cannot be changed via update.
- **Custom Provider integration** requires both a provider association (Create Custom Provider Integration) and a config (Create Provider Config) to be fully operational.
- **Incomplete schemas:** Complete request body schemas, response schemas, and exact enum values for status fields could not be fully confirmed from the OpenAPI spec. The schemas shown across these files are inferred from available documentation.

# Payments API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

The Payments API handles orders, transactions, subscriptions, coupons, payment provider integrations, and custom payment provider configurations. It contains **24 endpoints** across **8 sub-groups**, all under the `/payments/` path prefix. It is closely related to the [Invoices API](../invoices.md) — recording a payment on an invoice creates a corresponding transaction here.

This reference is split into the following files:

| File | Topics / Endpoints Covered |
|------|----------------------------|
| [orders.md](orders.md) | Orders + fulfillments + notes: List Orders, Get Order by ID, Record Order Payment, Migrate Order Payment Status, Create/List Order Fulfillments, List Order Notes. Order object schema. |
| [transactions.md](transactions.md) | List Transactions, Get Transaction by ID. Transaction object schema. |
| [subscriptions.md](subscriptions.md) | Subscriptions (List, Get by ID) and Coupons (List, Create, Fetch, Update, Delete). Subscription + Coupon object schemas and coupon config fields. |
| [integrations.md](integrations.md) | White-label integration providers (Create, List) and custom payment providers (Create/Delete Integration, Fetch/Create/Disconnect Config, Update Capabilities). Custom provider integration guide (queryUrl request types, iframe postMessage) and webhook events. |
| [gotchas.md](gotchas.md) | Overview, required scopes, authentication, rate limits, error responses, and the gotchas/notes (Invoices relationship, altId/altType pattern, marketplace-token requirements, coupon `code` immutability, HTTP 201 on coupon create, etc.). |

## Quick reference: scopes

| Scope | Access | Description |
|-------|--------|-------------|
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

# Store API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

The Store API manages shipping configuration and store settings for GoHighLevel e-commerce: shipping zones (geographic regions), shipping rates (pricing rules within zones), shipping carriers (third-party integrations like FedEx/UPS), and global store settings (ship-from origin, order notification emails). 18 endpoints across 4 sub-groups.

All endpoints use the `altId` (Location ID) + `altType` (`location`) parameter pattern. Scopes are empty in the spec — request `products.write` / `products.readonly` as a precaution. All endpoints are 🔬 **Unverified** (OpenAPI-spec-derived, not live-tested). See [gotchas.md](./gotchas.md).

## Files

| File | Endpoints / Topics |
|------|--------------------|
| [shipping-carriers.md](./shipping-carriers.md) | Carrier object; Create / List / Get / Update / Delete Shipping Carrier |
| [shipping-zones.md](./shipping-zones.md) | Zone object; Create / List / Get / Update / Delete Shipping Zone; Get Available Shipping Rates (with source type/subType enums) |
| [shipping-rates.md](./shipping-rates.md) | Rate object; Create / List / Get / Update / Delete Shipping Rate (within a zone) |
| [store-settings.md](./store-settings.md) | Store Settings object; Create/Update Store Settings, Get Store Settings |
| [gotchas.md](./gotchas.md) | Auth, scopes, rate limits, error codes, `altId`/`altType` pattern, carrier-callback flow, Products API relationship |

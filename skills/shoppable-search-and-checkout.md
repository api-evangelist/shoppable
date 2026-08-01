---
name: Search the catalog and complete a checkout
description: Find products in the Shoppable catalog and place an order through universal checkout.
api: openapi/shoppable-cloud-openapi-original.yml
operations: [catalog, checkout]
---

# Search the catalog and complete a checkout

Use the Shoppable Commerce API Suite (base URL `https://cloud.shoppable.com`) to
discover products and complete a purchase.

## Authentication
Every request needs two headers (both issued by your Shoppable Customer Success Manager):
- `Authorization: Bearer <token>`
- `x-shoppable-secret: <secret>`
- `Content-Type: application/json`

## Steps
1. **Search** — `POST /catalog` (operationId `catalog`). Body requires
   `pageSize`, `page`, `query`, `brands`, `retailers`, `deepPaging`. Optional
   `categories`, `onSale`, `sortBy` (`price_high`/`price_low`), `parentOnly`.
   Read `products[]`, `pageCount`, `totalCount`.
2. **Build line items** — for each chosen product, the checkout `id` is the
   combined `merchantId-upc` (39 chars). Capture `price` and `quantity`.
3. **Tokenize payment** — obtain a Stripe `paymentToken`. It is valid for only
   15 minutes; regenerate if expired.
4. **Checkout** — `POST /v6/checkout` (operationId `checkout`) with
   `cartDetails` (email, opt-in flags, `lineItems[]`), `shippingDetails`,
   `billingDetails`, and `paymentToken`. On 200, read `orderNumber`.

## Error handling
Checkout failures return a `type` (`request` | `payment` | `catalog` |
`unexpected`) plus `outOfStockItems[]` and `itemsWithPriceChange[]`. On a
`catalog` failure, drop/replace the listed items and rebuild the cart. On a
`payment` failure, regenerate the Stripe token. See
`errors/shoppable-problem-types.yml`. There is no idempotency key — do not blind-retry
a checkout; confirm via order lookup first.

---
name: Look up order status
description: Retrieve order details and status by order number, date range, or campaign.
api: openapi/shoppable-cloud-openapi-original.yml
operations: [orders, ordersByDateRange, ordersbyCampaign]
---

# Look up order status

Retrieve placed-order details from the Shoppable Commerce API Suite
(base URL `https://cloud.shoppable.com`).

## Authentication
- `Authorization: Bearer <token>`
- `x-shoppable-secret: <secret>`

## Steps
- **Single order** — `GET /orders/{orderId}` (operationId `orders`). Returns
  `orderNumber`, `status`, `orderDate`, `updatedAt`, monetary totals, buyer
  `email`, `shippingDetails`, `billingDetails`, and per-merchant `items`.
- **By date range** — `GET /v5/orderData/100/page/0/startdate/{startDate}/enddate/{endDate}`
  (operationId `ordersByDateRange`) for bulk order-data export over a UTC window.
- **By campaign** — `GET /orders/campaign/{campaignName}` (operationId
  `ordersbyCampaign`) to pull every order tied to a campaign.

## Notes
Item `status` values are `processing`, `completed`, or `canceled`. The Order
Data API returns PII (buyer name, address, email) — handle per your data
agreement. Errors follow the common envelope
(`error`, `code`, `msg`, `phase`, `support`); see `errors/shoppable-problem-types.yml`.

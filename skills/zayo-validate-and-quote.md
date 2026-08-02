---
name: Validate a building and request a Zayo quote
description: Confirm a service address is on/near the Zayo network, look up serviceable locations, then create a quote for a product.
api: openapi/zayo-openapi-original.yml
operations: [validateBuildings, getLocations, getProductCatalog, requestQuote, getQuoteDetails]
---

# Validate a building and request a Zayo quote

Use the Zayo Network Discovery and Quote APIs to go from a raw street address to a priced quote.

## Auth
1. POST `https://auth.api.zayo.com/oauth/token` with `grant_type=client_credentials`, `scope=openid`, and your `client_id`/`client_secret` (form-encoded).
2. Use `Authorization: Bearer <access_token>` on every call. Tokens last 1 hour; on `401` re-request and retry.

## Steps
1. **validateBuildings** — POST an array of building addresses in Zayo's building format. Matched results include a `buildingId`/`locationId` and Zayo Network Status. A `buildingId` or `locationId` is required before quoting.
2. **getLocations** — GET `/building/{buildingId}/locations` to enumerate serviceable locations within a matched building.
3. **getProductCatalog** — GET the product catalog to choose a `productCode` (use getProductCodeDetails for the required fields of a given product/version).
4. **requestQuote** — POST a quote request referencing the building/location and product; capture the returned `quoteId`.
5. **getQuoteDetails** — GET `/quotes/quote/{quoteId}` to read pricing and status.

## Rules
- Handle validation errors: a `400` returns `{ apiVersion, error: { code, errorId, message, errors } }`; log the `errorId` for Zayo support.
- No idempotency key is available — do not blindly retry `requestQuote`; check for an existing quote first.
- List responses use OData paging (`top`/`skip`); page with `currentPage`/`totalPages`.

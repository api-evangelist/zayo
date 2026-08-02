---
name: Submit a Zayo order from an existing quote
description: Take an approved quote, gather order contacts, submit the order, and track the resulting job.
api: openapi/zayo-openapi-original.yml
operations: [getQuoteDetails, getOrderContacts, submitOrder, getJobDetails]
---

# Submit a Zayo order from an existing quote

Convert an existing `quoteId` into a placed order and follow it to completion.

## Auth
OAuth 2.0 client credentials — `Authorization: Bearer <token>` (see the validate-and-quote skill for the token call). Re-request on `401`.

## Steps
1. **getQuoteDetails** — GET `/quotes/quote/{quoteId}` to confirm the quote is valid and priced before ordering.
2. **getOrderContacts** — GET `/orders/contacts/{quoteId}` to retrieve/confirm the contacts required on the order.
3. **submitOrder** — POST `/orders/order` referencing the quote and contacts; capture the returned `jobId`.
4. **getJobDetails** — GET `/job/{jobId}` to poll order/provisioning status.

## Rules
- `submitOrder` is not documented as idempotent — verify no order already exists for the `quoteId` before resubmitting.
- On `400`, read `error.message` and log `error.errorId` for zayoapi@zayo.com support.
- API version is in the URI path (v2 for order-management); responses echo `apiVersion`.

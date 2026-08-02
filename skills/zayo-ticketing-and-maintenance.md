---
name: Open a Zayo ticket and subscribe to maintenance notifications
description: Create a support ticket against existing services and register a callback to receive maintenance/ticket push notifications.
api: openapi/zayo-openapi-original.yml
operations: [getservices, getTicketCatalog, createTicket, getTicketDetails, getMaintenanceCases, generateAuthToken, registerCallbackUrl, sendTestNotification]
---

# Open a Zayo ticket and subscribe to maintenance notifications

Use the Service Management APIs to raise a ticket and wire up push notifications.

## Auth
OAuth 2.0 client credentials — `Authorization: Bearer <token>`; re-request on `401`.

## Ticketing
1. **getservices** — POST `/existing-services` to find the service the ticket is about.
2. **getTicketCatalog** — GET `/ticket-catalog` (and getTicketTypeDetails) to pick a valid ticket type/version and its required fields.
3. **createTicket** — POST `/create-ticket`; capture the returned `ticketName`.
4. **getTicketDetails** — GET `/ticket-details/{ticketName}` to track status (use addTicketComment / customerTicketAction to interact).

## Maintenance notifications (webhooks)
1. **getMaintenanceCases** — POST `/maintenance-cases` to list open maintenance cases affecting your services.
2. **generateAuthToken** — POST `/generate-auth-token` once to mint the shared token Zayo uses to authenticate pushes to you.
3. **registerCallbackUrl** — POST `/register-callback-url` with the callback endpoint you host.
4. **sendTestNotification** — POST `/send-test-notification` to verify delivery before relying on it.

## Rules
- Errors return `{ apiVersion, error: { code, errorId, message, errors } }`; log `errorId`.
- Regenerating the auth token rotates it — the most recently generated token is the only active one.
- No idempotency key; avoid duplicate createTicket calls by checking getAllTickets first.

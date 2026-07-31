---
name: Authenticate a user and list their invoices
description: Exchange Mesa API keys for a Bearer token, then page through a user's invoices.
api: openapi/mesa-partner-api-openapi.yml
operations:
  - authenticatePartnerUser
  - listPartnerInvoices
---

# List a user's Mesa invoices

Server-side flow to read a supplier's invoices from the Mesa Partner API.

## Rules

- Run entirely **server-side**; `clientId`/`clientSecret` must not reach the browser.
- The Bearer token from step 1 is short-lived and scoped to one user.

## Steps

1. **Authenticate** — call `authenticatePartnerUser`
   (`POST https://api.joinmesa.com/v1/partners/{partnerId}/auth`) with a JSON body of `clientId`,
   `clientSecret`, `vendorId`, and `userId`. Read `token` from the `200` response.
2. **List invoices** — call `listPartnerInvoices`
   (`GET https://api.joinmesa.com/v1/partners/{partnerId}/invoices`) with header
   `Authorization: Bearer <token>`. Optional query params: `page` (default 1), `limit` (default 25),
   `sortField`, `sortDirection` (`asc|desc`).
3. **Page through results** — the `200` response is a page-number envelope: the slice is under `data`,
   with `page`, `limit`, and `total`. Increment `page` until `page * limit >= total`.

## Notes

- No idempotency-key or error-catalog contract is documented; treat non-`200` responses defensively.
- Each invoice's `id` doubles as the `invoiceId` for the embedded instant-payout button.

---
name: Embed Mesa early-pay in a partner web app
description: Mint a Mesa session token server-side and mount the embedded early-pay UI in a browser.
api: openapi/mesa-partner-api-openapi.yml
operations:
  - createToken
---

# Embed Mesa early-pay

Use this to render Mesa's onboarding/dashboard/instant-payout UI inside a partner web app.

## Rules

- **Credentials stay server-side.** `clientId` and `clientSecret` must never reach the browser.
- Tokens are **short-lived** and scoped to a single user session — do not cache or reuse.
- The embed **pins its trusted origin** to the page that sends the first valid auth message.

## Steps

1. **Server: mint a token** — call `createToken` (`POST https://api.joinmesa.com/v1/token`) with a
   JSON body of `clientId`, `clientSecret`, `vendorId` (e.g. `early-pay`), and `userId` (your
   authenticated user's id). Read `token` from the `200` response and return only that to the browser.
2. **Browser: load the SDK** — import `@mesa/sdk` (or the `window.MesaClient` script-tag global).
3. **Browser: mount** — `const client = new MesaClient(); await client.init({ containerId, jwtToken: token, entrypoint: 'intro' })`.
   `entrypoint` is one of `intro | auth | onboarding | dashboard`.
4. **Optional: instant-payout button** — `client.startInstantPayoutButton({ selector, invoiceId })`
   injects a "Get Paid Now" button that opens the payout modal for that invoice.
5. **Cleanup** — call `client.destroy()` when unmounting (e.g. React `useEffect` cleanup).

## Notes

- Theme the embed with `client.updateTheme({ accentColor, radius, ... })` (see ThemeConfig).
- OIDC end-user auth (Okta/Azure AD/Google) is available; contact support@joinmesa.com to enable.

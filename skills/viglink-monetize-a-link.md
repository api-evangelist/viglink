---
name: Check and monetize a merchant link
description: Check whether a URL can be monetized with Sovrn Commerce (formerly
  VigLink), get the optimized affiliate redirect, and optionally fetch a real-time
  bid for the click.
api: openapi/viglink-link-check-openapi.yml
operations: [link, getBid]
generated: '2026-07-21'
method: generated
---

# Check and monetize a merchant link

Use the Sovrn Commerce Link Check and Bid Check APIs to turn an ordinary
merchant URL into a monetized affiliate link.

## Auth

- Link Check and Bid Check use the **public Commerce API key** as a `key`
  query parameter (found at https://commerce.sovrn.com under Sites).
- Other Commerce APIs use a site **Secret Key** in the `Authorization` header,
  formatted `secret {SECRET_KEY}` (note the space). See
  `authentication/viglink-authentication.yml`.

## Steps

1. **Check the link** — call `link` (`GET https://api.viglink.com/api/link/`)
   with `out` (the URL to test, URL-encoded) and `key`. The response tells you:
   - `affiliatable` — whether the link can be affiliated
   - `optimized` — the monetized redirect URL to use instead of the original
   - `competitive` — whether the link can be auctioned
   - `eepc` — estimated earnings per click
2. **Optionally price the click** — call `getBid`
   (`GET https://api.viglink.com/api/bid`) with `key`, `out`, `ip`,
   `userAgent`, `referrerUrl`, and optionally `subId`, `bidFloor`,
   `includeCpa` to get a real-time Sovrn bid on the click traffic.
3. **Use the optimized URL** — swap the original href for the `optimized`
   redirect (or use the client-side vglnk library / `vglnk.open()` to do this
   automatically — see `components/viglink-components.yml`).

## Rules

- Errors come back as `application/json` envelopes, not RFC 9457 — the Link
  Check API returns `message`/`exception`/`status`/`timestamp`
  (see `errors/viglink-problem-types.yml`).
- No idempotency keys exist; these are idempotent GETs.
- Search/check endpoints allow higher throughput than reporting endpoints,
  but space repeated calls and back off on 429s
  (see `rate-limits/viglink-rate-limits.yml`).

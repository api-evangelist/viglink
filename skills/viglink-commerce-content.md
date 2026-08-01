---
name: Build commerce content with campaigns and product recommendations
description: Pull your Sovrn Commerce campaigns and request curated, affiliatable
  product recommendations for a piece of content.
api: openapi/viglink-campaigns-openapi.yml
operations: [campaigns, get_product_recommendations]
generated: '2026-07-21'
method: generated
---

# Build commerce content with campaigns and product recommendations

Use the Campaigns and Product Recommendations APIs to assemble monetized
commerce content, the same flow the hosted Commerce MCP server
(`mcp/viglink-mcp.yml`) exposes to AI clients as the `get_campaigns` resource
and `rec_recommend_products` tool.

## Auth

Both calls take the site **Secret Key** in the `Authorization` header,
formatted `secret {SECRET_KEY}` (generated in the Sovrn Platform under
Commerce Settings). See `authentication/viglink-authentication.yml`.

## Steps

1. **List campaigns** — call `campaigns`
   (`GET https://rest.viglink.com/api/account/campaigns/{search}`) to list your
   campaigns or search by name/campaignId. Note the `campaignId` you want to
   attribute content to.
2. **Get product recommendations** — call `get_product_recommendations`
   (`POST https://shopping-gallery.prd-commerce.sovrnservices.com/ai-orchestration/products`)
   passing your `content` plus optional filters (price range, preferred
   merchants) to receive a curated list of relevant, affiliatable products with
   `deepLink`, prices, and stock.
3. **Enrich with promo codes / price comparisons** — optionally hit the
   Product Promo Codes API (`GET {viglink.io/coupons}/product`) and the Price
   Comparisons API for ranked codes and cross-merchant prices
   (no operationIds published; see `overlays/` for our assigned ids).
4. **Track results** — reporting on the resulting clicks/transactions comes
   from the Real-Time Reports API (`https://viglink.io/v1/reports/...`),
   filtered by `campaignIds`; respect the 1-request-per-60-seconds limit and
   one-day-per-request date window (`conventions/viglink-conventions.yml`).

## Rules

- The Campaigns API errors use a Google-style envelope
  (`error.code`/`message`/`errors[]`) — see `errors/viglink-problem-types.yml`.
- Reports requests require at least one of `clickDate`, `commissionDate`, or
  `updateDate` in `yyyy-mm-dd` format.
- Back off on 429; exceeding published limits can get the key temporarily
  blocked (`rate-limits/viglink-rate-limits.yml`).

---
name: ecommerce-ux
description: "Apply conversion-focused e-commerce UX when building or editing storefront templates, sections, and blocks in this Shopify theme. Use for product pages, collections, cart/checkout flow, and any merchant-facing layout decision."
---

# E-commerce UX for this theme

Always follow `AGENTS.md` (theme authoring contracts) first. This skill covers what good looks like for shoppers.

## Product page (PDP)

- Order: media → title/price → variant picker → buy button → trust → details → reviews → related. Don't bury the buy button.
- One primary CTA ("Add to cart"). Secondary actions (buy later, compare, share) visually subordinate.
- On mobile, keep a sticky add-to-cart bar visible once the main button scrolls away.
- Media: 4+ angles minimum if available, lifestyle shot (kid playing, not white box) before studio shots, zoom on hover/tap, video if it exists. Lazy-load everything below the fold.
- Trust signals next to price and CTA, not in a distant footer: shipping window, returns policy, guarantee.
- Variant picker must show clearly when a variant is sold out vs. hidden.
- Answer objections on the page (charge time, age range, what's in the box) — the copy skill's structure.

## Collections / browse

- Image-forward grid, consistent card heights, no layout shift on load.
- Filter and sort visible on mobile, not behind a mystery icon.
- Card: image, title, price. Sale badges top-left, subtle.

## Cart & checkout

- Cart drawer over full-page cart for impulse recovery; keep checkout reach within one tap.
- Never trap the shopper: always a visible way back to shopping.
- Show shipping threshold progress ("£4.20 from free shipping") if configured.

## Mobile-first

- Tap targets ≥44px, primary CTA in the lower thumb zone.
- Test at 360px width, not just 390px.
- No hover-only affordances; no fixed elements covering the viewport bottom except intentional sticky bars.

## Performance (conversion's biggest lever)

- No new render-blocking scripts. Prefer extending existing theme JS.
- Images: `image_tag` with width attributes / responsive srcset (theme helpers already do this — use them), explicit width/height to prevent CLS.
- One custom font family max for new UI; inherit theme settings fonts by default.

## Accessibility

- Form controls with visible labels (not placeholder-only). 4.5:1 contrast on text, 3:1 on borders/controls.
- Visible focus states — never remove outlines without a replacement.
- Icon-only buttons get aria-labels. Media alt text describes the product, not "image of".

## Theme-specific rules

- Honor merchant-configurable settings; never hardcode values a setting already owns (see AGENTS.md "Settings-driven styling").
- Extend the existing section/block that owns the responsibility instead of adding a new one.
- Keep template JSON block IDs stable — merchant configuration depends on them.

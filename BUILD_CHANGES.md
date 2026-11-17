# K26 PDP build: changes (2026-11-17)

Inputs: `PDP_NOTES.md` together with its Nov 13 addendum and `/opt/data/product-intake/soccer-drone/pdp_audit.md`, which overrides it where they disagree. Also `CLAUDE.md`, `AGENTS.md`, both skills, and the aero-adventures reference repo.
Nothing is pushed and nothing is committed. `templates/product.json`, `config/settings_schema.json`, and every shared section, snippet, and JS file are untouched.

## Files

| File | Status | Why |
|---|---|---|
| `templates/product.k26.json` | **new** | A dedicated K26 template with the buy box first, so the K26 copy doesn't leak onto other products. Built from this theme's `product.json` (kept IDs and settings) using aero's structure. |
| `sections/review-scroller.liquid` | **new** (ported, adapted) | The review strip from aero. The changes are listed under "Deviations" below. |
| `templates/page.faq.json` | **new** (ported) | The FAQ page from aero, with every string rewritten for K26. |
| `locales/*.json` (34 storefront files) | changed (+10 lines each) | Storefront strings for the scroller. English is used as the fallback text in every locale, as aero did. |
| `locales/*.schema.json` (23 files) | changed (+14 lines each) | Editor labels for the scroller. |
| `PDP_NOTES.md` | changed (addendum) | Records why the cart pair offer was not ported and what the port will need. |
| `BUILD_CHANGES.md` | new | This file. |
| `snippets/cart-pair-offer.liquid` | **not added** | Its wiring needs `settings_schema.json`, the shared `cart-products.liquid`, and the shared `component-cart-items.js`. See the PDP_NOTES addendum. |

## Dependency traces

**`sections/review-scroller.liquid`**
- Renders `contrast-override` and `spacing-style`. Both exist in `snippets/`.
- Its blocks are `customer_review`, defined locally in the section schema. No `blocks/` file is needed.
- It has no JS and no asset files. The CSS lives in its own `{% stylesheet %}`. All 21 theme tokens it uses (`--gap-*`, `--font-*`, `--style-border-*`, `--animation-*`, `--color-foreground(-muted)`, `--focus-outline-*`, `--margin-2xl`, `--padding-2xl`) are defined in this theme. `--header-height` comes from `assets/header.js` and has a fallback. The global classes it uses are `section`, `section--page-width`, `section-background`, `spacing-style`, `visually-hidden`, and `h3`.
- Storefront keys: `accessibility.customer_reviews|pause_reviews|review_photo|review_rating`, `content.verified_buyer`, and two new ones: `content.review_scroller_summary` (one/other) and `content.review_scroller_empty`.
- Schema keys: `names.review(_scroller)`, `settings.anchor_id|author|card_background|rating|seconds_per_review|star_color|verified_buyer` and the new `settings.show_rating_summary`, `info.anchor_id` and the new `info.show_rating_summary|review_verified_buyer`, and `text_defaults.review_scroller_heading`. The rest already existed in this theme. A script confirmed that every `t:` key and every `| t` key in the file resolves in `en.default(.schema).json`.
- The locale port applied aero's locale diff, then removed the keys that belong to other aero features (`cart_pair_offer*`, `add_another`, `color_option_icon`, `variant_image`) and the keys this port no longer uses (`rating_summary`, `review_author`, `review_text`). All 57 locale files still parse as valid JSON.

**`templates/page.faq.json`** uses `main-page`, `section`, `group`, `text`, `accordion`, `_accordion-row`, `icon`, `button`, and `page-content`. All of them already exist. `main-page`, `section`, `accordion`, and `_accordion-row` are byte-identical to aero's baseline, so the port needs no new code or locales.

**`templates/product.k26.json`** uses only section and block types this theme already has (`product-information`, `_product-media-gallery`, `_product-details`, `group`, `text`, `price`, `review`, `variant-picker`, `buy-buttons` and its static blocks, `icon`, `_divider`, `accordion`, `_accordion-row`, `disclosures`, `custom-liquid`, `button`, `section`, `product-recommendations` and its card blocks), plus the new `review-scroller`. A script checked every setting key in both templates against the matching block or section schema and found no unknown keys.

## What product.k26.json does

- **Kept IDs, all present:** `main`, `media-gallery`, `product-details`, `group_icgrde`, `text_xrnftG`, `price_tVjtKg`, `divider_VJhene`, `variant_picker_R3rGDr`, `buy_buttons_eYQEYi` (with `quantity`, `add-to-cart`, `accelerated-checkout`), `text_aEtTtq` (moved into the Description accordion row, as in aero), `disclosures_g9mWze`, `product_recommendations_qggXJq`, and `text_cbcgyb`. New blocks reuse aero's IDs.
- **Buy box order:** eyebrow, H1 title, price, rating line, then variant picker, the hidden pair promo, buy buttons, trust strip, divider, intro, two highlight cards, and the accordion (What's in the box, Age & safety, Specs, Description, Delivery & returns).
- **Section order:** `main`, `reviews` (scroller, 0 blocks), `faq`, `how_it_works`, `guarantees`, then recommendations. The marquee and features sections are left out, following the audit (§1).
- **Audit fixes applied:** price uses the `h4` preset with `show_tax_info: true`, so a "Shipping calculated at checkout" line links to the shipping policy. The gallery is a carousel with thumbnails instead of a two-column grid. The empty `disclosures_g9mWze` block is hidden. Sticky add-to-cart stays on.
- **Copy:** only verified facts are used: the 12.5 cm × 11.5 cm cage, one-button take-off and landing, altitude hold, hand launch, the 360° roll, 2.4 GHz so two can fly at once, RGB LEDs, one clip-in USB battery, four spare props, and the German manual. Flight time always reads "manufacturer states up to 30 minutes". Absolute claims are out ("crash-resistant", never "crash-proof"), and so is "barometric".
- **Unknowns are hidden placeholder blocks** (`"disabled": true`, text starting `[JAMES TO SUPPLY: …]` or `[JAMES: …]`). They don't render until James fills them in and turns them on:
  - PDP: `buy_two_promo`, the trust rows for delivery (`group_048`) and returns (`group_049`), the `accordion_row_safety` row (age and UKCA/CE), the FAQ rows for age, remote batteries, indoors, and delivery time, and the guarantee tiles for delivery and returns (`group_068`, `group_069`).
  - FAQ page: the rows for age, indoors, remote batteries, arrival time, and tracking, and the same two guarantee tiles.

## Deviations from aero (on purpose, for trust)

1. **The scroller hides itself when it has no reviews.** With 0 blocks it outputs nothing on the storefront. In the theme editor it shows a note asking for real reviews. That means the K26 template can include it now and it won't open the page with an empty strip.
2. **The rating summary comes from data.** Aero's typed `rating_summary` text, shown next to five always-filled stars, is replaced by a `show_rating_summary` checkbox. The summary reads `product.metafields.reviews.rating` and `rating_count`, fills stars to match the rounded rating, and renders nothing when there is no data.
3. **The preset adds no blocks.** Aero's preset inserted three placeholder "5-star, verified" reviews that could go live unedited. `verified` now defaults to **off**, with help text saying to tick it only for confirmed purchases.
4. **Colours come from settings:** the star and card colours default to the palette (`foreground` and `background`) instead of aero's hardcoded `#E8590C` and `#FFFFFF`. The template doesn't hardcode any colour anywhere.
5. **The PDP `rating_line` is the theme's native `review` block,** not aero's hardcoded "4.7 · 115" text with SVG stars. It shows nothing until a reviews app writes ratings.

## What James must configure in Admin

1. **Assign the template:** Products → KickFlight K26 → Theme template → `k26`. The template does nothing until it is assigned. Push first, because the option only appears once the file is live.
2. **Set up the FAQ page:** Online Store → Pages → Add page, titled "FAQ" (the H1 uses the page title). Set Theme template to `faq`. Any body text shows above the questions. Add the page to the footer or main menu.
3. **Contact page:** the FAQ and PDP buttons link to `/pages/contact`. Check that a page with handle `contact` exists and uses the `contact` template.
4. **Product handle:** the FAQ's "Shop the K26" button links to `/products/kickflight-k26`, the handle in the CSV. Update the link if the handle changes.
5. **Reviews:** install a reviews app that writes the standard `reviews.rating` and `reviews.rating_count` metafields. In the theme editor, add one **Review** block to the "Reviews" section for each real K26 review, with permission. Tick "Verified buyer" only for confirmed orders. The strip and rating summary then appear on their own. Never copy aero's reviews.
6. **Placeholders:** in the theme editor on the K26 template, fill in the hidden blocks listed above with the real delivery, returns, age, UKCA/CE, remote battery, and indoor answers, then turn each one on. Use the same wording in the trust strip, accordion, FAQ, guarantees, and Admin → Policies.
7. **Pair discount (later):** create an automatic discount in Discounts (X% off, minimum 2 items, any K26 variant). After that, turn on `buy_two_promo` and replace `[JAMES: discount %]`. The cart prompt still needs code first (see the PDP_NOTES addendum).
8. **Also needed before launch** (from the audit, not the theme): stock above 0 (otherwise "Sold out" shows and the sticky bar disappears), price, variant grams, and evidence of CE/UKCA marking and an age rating.

## Risks and open items

- **Not tested in a browser, and Theme Check didn't run.** There's no Shopify CLI here and `npx @shopify/theme-check` failed. Next steps: `shopify theme check`, then `shopify theme dev` (or push to an unpublished theme). Check the K26 PDP at 360 px and on desktop, the scroller with 0, 1, and 3+ review blocks and with reduced motion, the FAQ page, and that the carousel gallery swaps images when switching Blue and Red.
- **`"disabled": true` on nested theme blocks** is the format the editor writes when you hide a block. Check in the editor that the placeholders show as hidden and don't render.
- **The "Instruction manual (in German)" line** is true today but hurts conversion. Change it if an English quick-start card is sourced (audit blocker 4).
- **Flight time** is a supplier claim. Bench-testing it is recommended, and the line should then be replaced with the measured figure.
- **Not done because it would mean editing shared files:** the sticky mobile add-to-cart button still has no `aria-label` (`sections/product-information.liquid`, PDP_NOTES addendum #1), and the variant picker still shows text buttons, not image swatches (aero's `color_option_icon` needs shared snippet changes).
- **Editor round-trips:** once the template is edited in the theme editor, Shopify rewrites the JSON. Pull before making more code edits (PDP_NOTES risk 6).
- **Non-English locales** have English fallback strings for the new keys, the same as aero.
- **Recommendations section:** it shows nothing while the store has only one product. That's harmless, but hide it in the editor if it leaves a gap.

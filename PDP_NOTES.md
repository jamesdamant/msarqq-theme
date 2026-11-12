# KickFlight K26 — PDP notes

Scope: plan only. No theme files changed. Sources reviewed: `templates/product.json`, `sections/product-information.liquid` (identical in both repos), `/opt/data/aero-adventures/templates/product.json`, `sections/review-scroller.liquid`, `templates/page.faq.json`, `snippets/cart-pair-offer.liquid`, plus the aero diffs to `snippets/cart-products.liquid`, `config/settings_schema.json`, `locales/*`, and `blocks/variant-picker.liquid`. Applied `.claude/skills/ecommerce-ux.md` and `ecommerce-copy.md`.

Current state of this theme's PDP: the stock Horizon layout. `main` (product-information) has title, price, divider, variant picker, buy buttons, and the description as plain text, followed by `product_recommendations_qggXJq`. No reviews, trust, FAQ or offer yet.

---

## 1. Logo and favicon

**Which settings control them.** Theme settings → "Logo and favicon" (`config/settings_schema.json`, top group):

| Setting | Type | Used by |
|---|---|---|
| `logo` | image_picker | `blocks/_header-logo.liquid`, `blocks/logo.liquid` |
| `logo_inverse` | image_picker | Header on a transparent or inverse colour scheme (falls back to `logo`) |
| `logo_height` / `logo_height_mobile` | range 12–100 px | Header. Currently 36 / 28. Width = height × the image's aspect ratio |
| `favicon` | image_picker | `layout/theme.liquid:10`, rendered at 32×32 |

**How to wire the asset: use the theme editor or Files, not `assets/`.** An `image_picker` only accepts `shopify://shop_images/<file>` (that's what aero uses: `"logo": "shopify://shop_images/Fighter_black_2.png"`). A file in `assets/` can't be selected by these settings. You could only reach it by hardcoding `asset_url` in Liquid, and that goes around a merchant setting, which AGENTS.md rules out. Steps:

1. Upload the PNGs in Admin → Content → Files, or straight from the picker in Theme editor → Theme settings → Logo and favicon.
2. Choose them in the editor. Shopify then writes `shopify://shop_images/...` into `config/settings_data.json`. Pull that change (`shopify theme pull --only config/settings_data.json`) rather than hand-editing the file before the upload exists.
3. Once they're live from Files, remove the two untracked PNGs from `assets/`, or never commit them. They'd ship as unused theme assets.

**Fix the files before upload. This is required:**
- `kickflight_logo_final_transparent.png` is a **2000×2000 canvas, but the artwork only fills 1426×604** (alpha bbox x 276–1702, y 692–1296). The header sizes by height, so at 36 px the canvas renders 36×36 and the wordmark ends up about 11 px tall. **Trim it to the alpha bounds** (about 2.36:1, e.g. 1426×604) before uploading. Then 36 px tall gives about 85 px wide. Consider raising `logo_height` to 40–48 desktop and 32 mobile, and check that the wordmark is still readable at 360 px wide.
- `kickflight_logo_512_v2.png` is **RGB with no transparency** and a white background (orange mark, about `#F76508`). It works as a favicon, but it shows as a white square on dark browser tabs. A transparent version, or a ball-only crop, is better. A wordmark won't read at 32 px, so use the ball icon only.
- Upload a light or inverse version for `logo_inverse` if the header uses a dark or transparent scheme anywhere (e.g. over a hero).

---

## 2. K26 section order (buy box first, modelled on aero)

The rule is the same as aero: everything needed to buy sits in `main` → `product-details`, above any story section. Keep these existing IDs exactly: `main`, `media-gallery` (static), `product-details` (static), `group_icgrde`, `text_xrnftG`, `price_tVjtKg`, `divider_VJhene`, `variant_picker_R3rGDr`, `buy_buttons_eYQEYi`, `text_aEtTtq`, `disclosures_g9mWze`, `product_recommendations_qggXJq`, `text_cbcgyb`. Move `text_aEtTtq` into an accordion row the way aero did. Moving the block keeps its ID. For new blocks, **reuse aero's IDs** (`eyebrow_pdp`, `rating_line`, `buy_two_promo`, `trust_badges`, `intro_copy`, `highlights`, `product_accordion`, `accordion_row_*`). None of them collide here, and keeping the same names lets you diff the two templates side by side.

### `main` → `product-details` block_order

```
group_icgrde                 (title group)
  eyebrow_pdp                NEW  e.g. "Ages X+ · Crash-proof cage" (facts TBC)
  text_xrnftG                KEEP <h1>{{ closest.product.title }}</h1>
  price_tVjtKg               KEEP (consider show_tax_info: true, see §4)
  rating_line                NEW, ONLY once real reviews exist (see §5)
variant_picker_R3rGDr        KEEP Blue / Red
buy_two_promo                NEW, only if the pair discount is live (§3)
buy_buttons_eYQEYi           KEEP, single primary CTA
trust_badges                 NEW, 3 rows: delivery / returns / secure checkout, matching the policies
divider_VJhene               KEEP, moved below trust (aero position)
intro_copy                   NEW, 1–2 sentence subhead (who it's for, core fun)
highlights                   NEW, 2 icon rows, e.g. cage durability + easy to fly
product_accordion            NEW
  accordion_row_description  > text_aEtTtq (KEEP ID, moved)
  accordion_row_specs        flight time, charge time, battery, range, weight
  accordion_row_04e          What's in the box
  accordion_row_04g          Delivery & returns (links to policy pages)
```
`disclosures_g9mWze` stays where it is.

Keep `enable_sticky_add_to_cart: true` (already on). That covers the mobile sticky CTA from the UX skill. Aero changed padding to 24/64. Copy that if the gallery feels cramped under the header.

### Template `order`

```
main                              buy box (above)
reviews                           review-scroller, anchor_id "reviews" (§3)
marquee                           short proof chips: "Crash-proof cage", "Ages X+", "Blue or red"
how_it_works                      3 steps: charge, power on, take off
features                          4 icon cards: cage, safety, controls, battery
guarantees                        4 trust tiles, same claims as trust_badges
faq                               PDP FAQ accordion with faq_anchor
product_recommendations_qggXJq    KEEP; retitle text_cbcgyb (e.g. "Spare batteries and add-ons")
```
Reuse aero's section and block IDs (`how_it_works`, `group_055`, `text_04r`…, `features`, `group_05q`…, `guarantees`, `group_068`…, `faq`, `accordion_06t`…). Rewrite every string. Aero's copy is about foam jets and planes ("Control tower", "Complete your squadron", the `plane` icons), and none of it carries over. Use icons like `check_box`, `heart`, `lightning_bolt`, `truck`, `return`, `lock`, and drop `plane`.

If reviews are still thin at launch, move `reviews` below `features` or hide the section. Don't open the story with an empty strip.

**Media (UX skill):** at least 4 angles per colour. Put a lifestyle shot first (a kid playing with it). Show the cage taking a hit, and include a video if there is one. Attach each variant's image so selecting Blue or Red swaps the gallery.

**Variant picker:** aero's `color_option_icon` change (`blocks/variant-picker.liquid`, `snippets/variant-main-picker.liquid`, `variant-picker-styles`) draws a plane tinted with the swatch colour, which doesn't fit this product. Two better options: Shopify's native colour swatches (set the Color option to the category metafield), or port only the `variant_image` branch of that change.

---

## 3. What to adapt from aero for trust and conversion

### Review scroller (`sections/review-scroller.liquid`)
- **Port the file as-is.** It's a self-contained section with section-scoped `customer_review` blocks, pause control, reduced-motion fallback, and verified badge. Add its locale keys to `locales/en.default.json` (`customer_reviews`, `pause_reviews`, `review_photo`, `review_rating`, `verified_buyer`) and the schema keys (`names.review_scroller`, `names.review` and its setting labels) to `en.default.schema.json`. Run Theme Check afterwards.
- **Do not copy aero's review blocks or its `rating_summary` ("4.7 out of 5 · 115 reviews").** Those reviews are for a different product. Start with zero blocks and add only real K26 reviews, from beta testers or early buyers, with permission.
- `star_color` / filled stars: aero hardcodes `#E8590C` in the section setting and in the `rating_stars_filled` custom-liquid block. For KickFlight, use the brand orange from the logo (about `#F76508`), or a palette colour, so there's one source. Check 3:1 contrast on the card background.
- The PDP `rating_line` (filled stars plus "x out of 5 · N reviews" linking to `#reviews`) should be **driven by data, not typed in**. Read `closest.product.metafields.reviews.rating` and `reviews.rating_count`, which a reviews app fills in, inside the custom-liquid block, and render nothing when they're empty. Aero's hardcoded line goes stale and misleads.

### FAQ (`templates/page.faq.json` and the PDP `faq` section)
- Copy `page.faq.json` (sections `main`, `faq_list`, `guarantees`, `contact_prompt`). Create a page "FAQ" in admin and assign the `faq` template.
- On the PDP, keep aero's `faq` section shape (`faq_anchor` custom-liquid, heading group, `accordion_06t` rows), with 6–8 questions in the order objections block purchase (copy skill): age range; how hard is it to fly; flight time vs charge time; what the cage protects (props, kids, furniture) and what happens in a crash; indoor or outdoor; whether it can be kicked or bounced (**only if true**: the name suggests it, so answer it plainly either way); spares (props, batteries); delivery time.
- Don't make up specs. Every number (age, minutes, grams, range, charge time) has to come from the supplier sheet. Flag what's missing instead of guessing.

### Cart pair offer (`snippets/cart-pair-offer.liquid`)
Siblings and friends make this a good fit for a kids' toy. To port it you need all of the following:
1. Copy the snippet.
2. In `snippets/cart-products.liquid`, add `{% render 'cart-pair-offer', styles_only: true %}` in the empty-cart branch (aero line 75) and `{% render 'cart-pair-offer' %}` under the line items (aero line 427).
3. In `config/settings_schema.json`, cart group after `auto_open_cart_drawer`: add `cart_pair_offer` (checkbox, default false) and `cart_pair_offer_percent` (range 5–50, default 10).
4. Locales: `add_another`, `cart_pair_offer_title`, `cart_pair_offer_body`, `cart_pair_offer_saving_html` in `en.default.json`; `settings.cart_pair_offer*` and `info.cart_pair_offer` in schema locales.
5. **Create the automatic discount in Admin → Discounts** (X% off, minimum 2 items, scoped to K26). The snippet only advertises the offer and doesn't apply it. The setting's percent, the discount, and the PDP `buy_two_promo` text all have to match.

The snippet already hides itself for gift cards, bundle children, quantity-rule maxima, and stock-limited variants. Keep those checks. One gap: it offers "another of the same variant". Some parents will want one Blue and one Red, and that's fine as long as the discount counts any K26 variant.

### Other aero patterns worth taking
- `trust_badges` group under buy buttons, plus the `guarantees` section: put trust next to the CTA, as the UX skill asks.
- Announcement bar (`header-group.json`) for the launch offer. Keep it to one honest line.

---

## 4. Where policy text (shipping, returns, guarantee) appears on the PDP

The source of truth is **Admin → Settings → Policies** (`shop.shipping_policy`, `shop.refund_policy`, and so on). On the PDP that text shows up in these places:

| Place | What appears | Driven by |
|---|---|---|
| `price_tVjtKg` → `show_tax_info` (currently **false**) | "Taxes included. Shipping calculated at checkout" line, with "Shipping" linked to the shipping policy when one exists (`blocks/price.liquid:72`, `snippets/tax-info.liquid`) | Automatic from `shop.shipping_policy`. **Turn it on.** |
| `trust_badges` text blocks (new) | Short claims, e.g. "Dispatched in N days", "N-day returns" | Typed into the template JSON, so it's **not linked to the policy** |
| `accordion_row_04g` Delivery & returns (new) | Paragraph summary | Typed in. Add links to `/policies/shipping-policy` and `/policies/refund-policy` |
| `guarantees` section (new) | 4 tiles that repeat the claims | Typed in |
| `faq` "When will my order arrive?" | Delivery window | Typed in |
| `popup-link` block (optional) | Inline link opening a page in a modal (its preset already seeds "return policy" text) | Choose a page with a page picker |
| Footer `footer-policy-list` | Policy links on every page | Automatic |

Because most of the PDP claims are typed in, any change to shipping or returns has to be made in several places: Policies, the trust badges, the accordion, guarantees, and the FAQ. Keep the claims short and identical everywhere, and link to the policy pages for detail. A metafield or a single shared page would cut the duplication. That's a later improvement and not needed for launch.

---

## 5. Risks

1. **Logo canvas padding.** Uploaded as it is, the logo renders tiny (see §1). The favicon has no transparency.
2. **Typed-in reviews and ratings.** Copying aero's reviews or "4.7 · 115" to a product that has just launched would be fake reviews. That's banned under the UK DMCC Act 2024 (in force since April 2025) and damages trust. Show only real, verifiable reviews, and pull the rating from data.
3. **Claims that don't match reality.** Aero's "Free express delivery", "dispatched within 24 hours" and "30-day returns" belong to aero's store. Confirm the msarqq-uk terms before reusing any of them. UK distance selling already requires a minimum 14-day cancellation right, so returns copy must not offer less.
4. **Toy and drone compliance copy.** Age grading and warnings, UKCA/CE marking, battery safety, and UK CAA rules on registration or Flyer ID depending on weight all need checking against the product's actual weight and certification before the FAQ answers anything about them. Don't write "no registration needed" unless it has been verified.
5. **Pair-offer mismatch.** If the prompt, the PDP promo, and the automatic discount drift apart, shoppers are promised a saving they don't get at checkout. Check all three together, and test the cart with 1 item, 2 of the same, and Blue + Red.
6. **Block ID churn.** `templates/product.json` is rewritten by the theme editor. Make the restructure either in code or in the editor, not both at once, then pull before editing again. Never rename the kept IDs in §2.
7. **Hardcoded colours in custom-liquid** (aero's stars) skip the colour settings and can fail contrast on another scheme. Prefer section settings or palette tokens.
8. **Performance.** Seven sections plus a marquee and an animated scroller. Keep below-fold images lazy, and keep only the first gallery image eager. Check CLS and LCP on mobile at 360 px after the restructure.
9. **Aero's wording carrying over.** Planes, squadron and control-tower copy would look careless on a kids' drone listing. Do a full strings pass with the copy skill's anti-AI checklist.
10. **The template is shared by all products.** There's one `templates/product.json`, so K26-specific copy there applies to every product. Either create `templates/product.k26.json` and assign it to K26 only, or keep product-specific text in metafields. **Recommendation: `product.k26.json`**, copied from the restructured default with the same block IDs.

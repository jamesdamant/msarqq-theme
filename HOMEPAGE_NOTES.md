# K26 homepage build notes (2026-11-17)

Only `templates/index.json` and this file were changed. `config/`, other templates, sections, snippets, and locales are untouched. Nothing has been committed or pushed.

The layout follows aero's `index.json` (hero, then featured product with buy box, then reviews, then the story sections, then guarantees, then FAQ). The JSON for each section started as a copy of aero's matching section. The section and block files involved (`hero`, `featured-product-information`, `media-with-content`, `section`, `group`, `text`, `button`, `icon`, `review`) are byte-identical between the two repos. A script checked every setting key and select value against this theme's schemas and found no errors. It flagged one value that was already in the file before this build: `style_class: "link"` on the hidden product list's button. That value was left as it is.

## Section order

| # | Section ID | Type | Purpose |
|---|---|---|---|
| 1 | `hero_jVaWmY` (kept) | `hero` | Eyebrow, the approved H1 "The drone you're allowed to fly into the wall.", the subhead (trimmed in pdp_audit §9), one orange CTA "Shop the K26" linking to `/products/kickflight-k26`, and a proof strip |
| 2 | `featured_k26` | `featured-product-information` | Buy box on the homepage: gallery, title, price with tax/shipping line, native rating (shows nothing until there's data), variant picker, add to cart, and trust rows |
| 3 | `reviews` | `review-scroller` | 0 blocks, so it renders nothing on the storefront until real reviews are added |
| 4 | `cage_story` | `media-with-content` | The cage, the #1 objection: "Walls, fences, goalposts. The cage takes the hit." Includes facts (12.5 cm, 1 button, 2.4 GHz) and a CTA |
| 5 | `features` | `section` | Green band with 4 black cards: one-button take-off, hand launch, 360° roll, two can fly at once |
| 6 | `brand_trust` | `section` | Black bar: spare props, props inside the cage, honest specs, secure checkout. Plus 3 hidden tiles (cage-tested, delivery, returns) |
| 7 | `faq_link` | `section` | "Questions? The answers are on one page." with a "Read the FAQ" button (`/pages/faq`) and a secondary "Ask us a question" button (`/pages/contact`) |
| 8 | `product_list_fa6P9H` (kept, **hidden**) | `product-list` | With only one product it would repeat section 2. Turn it back on once the catalogue grows |

Reused IDs that kept their meaning: `hero_jVaWmY`, `text_YLPk4p` (still the heading, now the H1), `button_H9gpTf` (still the hero button), and `product_list_fa6P9H` with all its blocks. New blocks reuse aero's IDs where they came from aero.

## What James must upload or configure (theme editor → Home page)

1. **Hero (Media 1):** add the best lifestyle photo, such as the stadium sunset or the park shot with two kids. Use landscape, at least 2400 px wide, with the drone off-centre to the right, because the text sits bottom-left over a black gradient. You can also turn on "custom mobile media" and add a portrait crop. Until an image is added, the hero shows a black background.
2. **Featured product:** pick the product **KickFlight K26** (it's preset to handle `kickflight-k26`, so pick it again if the handle changes). The gallery uses the product's own images, so nothing extra to upload.
3. **Cage story (media):** upload a square close-up of the cage, ideally a frame from the crash-and-relaunch clip in pdp_audit §6. A Shopify-hosted video also works (autoplay is off).
4. **Reviews:** leave this empty until you have real K26 reviews with permission. Then add one Review block per review, and tick "Verified buyer" only for confirmed orders. The rating summary is off here because the homepage has no product context, so it couldn't show anything.
5. **Hidden placeholders:** each block below holds `[JAMES …]` text. Fill it in, then turn the block on.
   - Featured product: `trust_row_delivery`, `trust_row_returns`, and `featured_promo` (the Buy 2 offer, only once the automatic discount exists).
   - Brand trust: `group_delivery`, `group_returns`, and `group_cage_tested`. **Only turn on "Cage-tested" after you have filmed your own crash test.** At the moment no test is on record, so the claim would be unsupported.
   - Use the same delivery and returns wording here, on the PDP, on the FAQ page, and in Admin → Policies.
6. **FAQ page:** the button only works once the `faq` page exists (see BUILD_CHANGES "What James must configure" step 2). Also check that `/pages/contact` exists.
7. **Brand colours (optional tidy-up):** the global palette is still Horizon's greys. Brand colours are set on each section instead: pitch green `#1B7A3D`, jet black `#0B0B0B`, and signal orange `#E8590C`. The brand package gave no orange hex, so I used the orange from aero's stars, which meets contrast (see below). To control them from one place, add them in Theme settings → Colors and repoint the sections.

## Design intent

- **Buy box high on the page**, as on aero and the K26 PDP. A parent can add to cart from the homepage without scrolling past the story.
- **One primary action colour.** Signal orange is used only for buying actions (hero CTA, add to cart, cage-story CTA). The FAQ and contact buttons use the theme's standard styles so they don't compete with it.
- **Contrast checked:** black text on orange buttons is about 5.9:1. White on green is about 5.4:1. Orange icons and stars on white are about 3.6:1, and on black about 5.5:1. Orange is never used for body text on white. White text on orange would fail (about 3.6:1), so the buttons don't use it.
- **Colour rhythm down the page:** black hero, white buy box, white cage story, green features band, black trust bar, grey FAQ strip. Green and black echo the pitch and the box.
- **Honest copy only** (copy skill and pdp_audit §9). It says "crash-resistant", never "crash-proof", and the cage section says so outright. "Barometric" is gone, and so are flight time, age, delivery, returns, and any review or rating text. "Cage-tested" stays hidden until it's true. "Honest specs" refers to the PDP rule that flight time is labelled as the manufacturer's figure.
- **Known tension:** the approved headline implies indoor use ("into the wall"), but pdp_audit item 5 says indoor guidance still needs confirming. Confirm it and fill in the FAQ "Can it fly indoors?" row before launch.

## Not verified

- Not checked in a browser, and Theme Check didn't run (no Shopify CLI here). Next: `shopify theme check`, then `shopify theme dev`. Check the page at 360 px and on desktop: the 4-card row stacking on mobile, the hero text over the real photo, and that hidden blocks stay hidden.
- If the theme editor saves this page, Shopify rewrites `index.json`. Pull before making more code edits.

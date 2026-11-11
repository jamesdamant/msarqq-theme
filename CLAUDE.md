# msarqq-theme (Horizon 4.3.0 → msarqq-uk.myshopify.com)

Read `AGENTS.md` before any theme change — it defines the architecture contracts.

## Skills

- `.claude/skills/ecommerce-copy.md` — use whenever producing or reviewing customer-facing product/storefront text.
- `.claude/skills/ecommerce-ux.md` — use when building or editing templates, sections, or blocks affecting shopper experience.

## Reference repo (pattern source)

`/opt/data/aero-adventures` is James's previous store for a drone brand, built on the same Horizon 4.3.0 base. When a task matches something done there, read the corresponding file there first and follow the same pattern (adapt, don't copy blindly — respect THIS theme's settings and IDs):

- `sections/review-scroller.liquid` + its blocks — continuous review strip, stars, pause-on-hover
- `templates/product.json` — PDP section order with the BUY BOX ABOVE the product story (James's explicit preference)
- `templates/page.faq.json` — FAQ page pattern
- `snippets/cart-pair-offer.liquid` — "Add another, save 10%" cart upsell prompt
- Rating stars filled/colored per his commit history


## Working mode (agent orchestration)

You are usually invoked non-interactively by an orchestrating agent. Follow these rules to keep runs cheap and predictable:

- **Write results to files, not chat.** When asked for deliverables (copy drafts, section code, reports), write them to the requested path and reply with a 1–3 line summary. Never echo full file contents back.
- **Read only what the task needs.** Prefer `grep`/glob to locate the relevant section/snippet before reading whole files.
- **Don't run `git push` or `shopify theme push`** unless the prompt explicitly says to. The human reviews before push.
- **One task per run.** If the prompt contains multiple independent tasks, do them in the order given and summarize each in one line.
- Report blockers as `BLOCKED: <reason>` in the first line rather than inventing a workaround.

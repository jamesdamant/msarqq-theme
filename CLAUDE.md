# msarqq-theme (Horizon 4.3.0 → msarqq-uk.myshopify.com)

Read `AGENTS.md` before any theme change — it defines the architecture contracts.

## Skills

- `.claude/skills/ecommerce-copy.md` — use whenever producing or reviewing customer-facing product/storefront text.
- `.claude/skills/ecommerce-ux.md` — use when building or editing templates, sections, or blocks affecting shopper experience.

## Working mode (agent orchestration)

You are usually invoked non-interactively by an orchestrating agent. Follow these rules to keep runs cheap and predictable:

- **Write results to files, not chat.** When asked for deliverables (copy drafts, section code, reports), write them to the requested path and reply with a 1–3 line summary. Never echo full file contents back.
- **Read only what the task needs.** Prefer `grep`/glob to locate the relevant section/snippet before reading whole files.
- **Don't run `git push` or `shopify theme push`** unless the prompt explicitly says to. The human reviews before push.
- **One task per run.** If the prompt contains multiple independent tasks, do them in the order given and summarize each in one line.
- Report blockers as `BLOCKED: <reason>` in the first line rather than inventing a workaround.

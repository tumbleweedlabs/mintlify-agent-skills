# Architecture

This document explains *why* the suite is shaped the way it is. Read it once before contributing; reference it when an open question is really an architecture question in disguise.

## Design philosophy

Five principles, in priority order:

1. **Site-type-agnostic.** The suite must work for any kind of Mintlify site — API reference, SaaS product docs, learning site, internal wiki, curated resource library. Site-type variation lives in [`patterns/`](./patterns). It must never leak into a `SKILL.md`.
2. **Patch-ready proposals, never silent rewrites.** Every skill outputs diffs and skeletons that a human (or another agent) can apply with a single PR. Skills do not "just go fix it."
3. **Live docs over training data.** Every skill defers to https://mintlify.com/docs and the schema at https://mintlify.com/docs.json. If the live docs and training data disagree, the live docs win.
4. **Composable but bounded.** Each skill has a sharp scope and an explicit "When NOT to use." A skill that drifts into a sibling's territory is a bug.
5. **Lean context.** The router is always loaded; companions load on demand. No session pulls more than ~30KB of skill content; the suite total stays under ~70KB.

## The router pattern

The router is a small skill (~12KB, well under any companion) that always loads when the suite is active. It owns three things only:

- **Intent detection.** Map a user request to one or two companion skills.
- **Suite-wide invariants.** The universal guardrails that apply to every companion.
- **Pattern brokerage.** Surface candidates from `patterns/` when the user is starting fresh or restructuring.

The router does not contain deep guidance. If a section of the router could plausibly live in a companion, it moves.

### Why a router instead of one big skill

A single combined skill would either be enormous (loading 70KB on every session, regardless of task) or shallow (cutting depth to fit the budget). Splitting by lifecycle phase — design, write, create, maintain — lets each skill be deep where it matters and absent where it doesn't.

### Why a router instead of fully independent skills

Without a router, each skill would re-implement the universal guardrails, the inspection-before-action rule, and the pattern lookup. Drift is inevitable. The router is the single source of truth for what is true across the whole suite.

## Site-type variation, without baking it in

The single most load-bearing constraint in this suite: **no skill assumes what kind of site is being administered.**

When a user describes their site (or when `design`'s Phase 1 inspection reveals it), the agent can offer a *template* from `patterns/`. Templates are markdown files describing a recommended IA, navigation pattern, and starting groups for a given site type. They are menu items the user can pick, modify, or discard. They are never the default behavior of a skill.

Initial templates:

- `patterns/api-docs.md` — Tabs (Guides + API reference); OpenAPI inheritance at the tab level.
- `patterns/saas-product-docs.md` — Groups; quickstart-led.
- `patterns/learning-site.md` — Module pages with prerequisites and reading order.
- `patterns/resource-library.md` — Cards-heavy hub pages with `Last touched` freshness markers.
- `patterns/internal-wiki.md` — Anchors; lighter on landing-page polish; search-first.
- `patterns/multi-product.md` — `products` at root; per-product tabs underneath.

If a contributor finds themselves about to add learning-site flavor to `design/SKILL.md`, the answer is no — that's what `patterns/learning-site.md` is for.

### How the agent picks a pattern

In `design`'s Phase 2 ("Frame the design problem"), the agent considers `patterns/` as candidates *only after* it has audience and top-jobs answers. The agent presents 1–2 closest matches as a menu, never as a default. If no pattern fits, the agent designs from first principles, and `CONTRIBUTING.md` describes how to upstream the new pattern.

## Vendoring policy

Vendor sparingly and with credit:

- **Upstream `mintlify` skill writing standards** (`github.com/mintlify/docs/blob/main/skill.md`) — vendored into `write/SKILL.md` with a credit line and a link back. Vendored because the writing standards are stable, prescriptive, and hot-path content the suite needs to apply turn after turn.
- **Existing `mintlify-design-skill` SKILL.md** — incorporated into `design/SKILL.md` essentially unchanged.

Do *not* vendor:

- **Live Mintlify docs** at https://mintlify.com/docs — every skill defers to the live source. Reproducing them locally would rot.
- **`docs.json` schema** at https://mintlify.com/docs.json — referenced as the authoritative schema; never copied.
- **Mintlify MCP server** at https://mintlify.com/docs/mcp — pointed at, never inlined. The router references it; `create` may include optional install steps.

When upstream `mintlify` updates its writing standards, re-vendor (with a clear "vendored from {commit} on {date}" note in `write/SKILL.md`) rather than silently drift.

## Extending the suite

Two extension points, in order of frequency:

### Adding a pattern

1. Copy an existing template in `patterns/` as a starting point.
2. Fill the standard sections defined in [`patterns/README.md`](./patterns/README.md): *Audience*, *Primary navigation*, *Starting groups*, *Component conventions*, *OpenAPI integration* (if applicable), *Common mistakes*.
3. Submit a PR with a one-paragraph justification: which existing site it's modeled after, and why a generic skill couldn't already handle it.

A pattern that turns out to be a generic principle should be promoted into a skill instead — `CONTRIBUTING.md` describes the bar.

### Adding a sibling skill

A sixth or seventh skill is justified only when the new lifecycle phase doesn't fit cleanly into any existing one *and* would routinely demand more than ~10KB of guidance. Candidates considered and deferred:

- **`mintlify-api`** — wrapping the Mintlify REST API (auth, deployment triggers, metadata). Upstream has a 43-line stub. Referenced but not yet wrapped. Revisit in v1.1.0 if there's demand.
- **`visuals`** — diagram and infographic generation. Currently absorbed into `design`'s component matrix (`<Mermaid>`, `<Frame>`, `<Tree>`).
- **`learning`** — module structure with prerequisites. Currently a pattern (`patterns/learning-site.md`).

When considering a new skill, the first question is: *can this be a pattern instead?* Patterns are cheaper, easier to author, and keep the core surface generic.

## What's intentionally out of scope

The suite makes no attempt to handle:

- Deployment, DNS, hosting, custom domains, billing, SSO, analytics — all human tasks.
- Brand or voice decisions a product team must own.
- Bulk content generation. Skills produce skeletons, not finished prose.
- Non-Mintlify documentation systems (Docusaurus, Nextra, MkDocs, GitBook, etc.). The suite is Mintlify-specific by design.

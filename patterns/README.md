# Patterns

Site-type templates the agent can offer when a user is starting fresh or restructuring. **Patterns are menu items, never defaults** — the router and the `design` skill surface 1–2 candidates as suggestions; they never apply a pattern silently.

This directory exists so that site-type variation (API docs vs. SaaS product docs vs. learning site, etc.) does not leak into the core skill files. The skills must remain site-type-agnostic. See [ARCHITECTURE.md → Site-type variation, without baking it in](../ARCHITECTURE.md#site-type-variation-without-baking-it-in).

## Initial templates

These are added in section 9 step 8 of the build, after the four companion skills are drafted. This README is the contract they conform to.

| Pattern                        | One-liner                                                              |
|--------------------------------|------------------------------------------------------------------------|
| `api-docs.md`                  | API reference + guides; OpenAPI inheritance at the tab level.          |
| `saas-product-docs.md`         | Groups; quickstart-led; canonical for product companies.               |
| `learning-site.md`             | Modules with prerequisites and reading order; suggests glossary/index. |
| `resource-library.md`          | Cards-heavy hub pages with `Last touched` freshness markers.           |
| `internal-wiki.md`             | Anchors; lighter landing polish; search-first.                         |
| `multi-product.md`             | `products` at the root; per-product tabs underneath.                   |

## Pattern contract

Each pattern is a single Markdown file with the following sections, in this order:

1. **One-line summary** — what this site is for, in plain language.
2. **Audience** — who reads the resulting site.
3. **Primary navigation pattern** — `groups`, `tabs`, `anchors`, `dropdowns`, `products`, `versions`, or `languages`. One choice, with rationale.
4. **Starting groups (or tabs/products)** — the IA skeleton, named.
5. **Component conventions** — which Mintlify components dominate this site type (e.g. `<Card>` for resource libraries, `<Steps>` for learning modules). Defer to the live component docs at https://mintlify.com/docs/components for the catalog itself.
6. **OpenAPI integration** — only if applicable to the site type.
7. **Common mistakes** — pitfalls specific to this site type.
8. **Worth promoting to a skill?** — usually no. If yes, see `CONTRIBUTING.md`.

A pattern is **not** a finished `docs.json`, a finished IA tree, or a finished site. It's a starting point the agent and user co-tailor.

## Adding a pattern

See [`CONTRIBUTING.md` → Adding a pattern](../CONTRIBUTING.md#adding-a-pattern). The bar: a pattern earns inclusion when the same content would otherwise tempt a contributor to bake site-type assumptions into a `SKILL.md`.

# Pattern: Multi-product

## One-line summary

A documentation site for a company with **multiple distinct products** — each with its own audience, its own concepts, and its own version cadence. A top-level product switcher lets readers pick which product they're working with; per-product sub-navigation handles the rest.

## Audience

Differs by product. The site has *as many audiences as products*, and the navigation must let each audience find their product without wading through the others. A typical reader knows which product they want before they arrive — they came from a marketing page or an in-product help link.

The single load-bearing question: **does each product have a meaningfully different audience, or are they parts of one product with different surfaces?** If the latter, this pattern is wrong — use `patterns/saas-product-docs.md` with sections instead. The bar for "different products" is high: separate marketing positioning, separate buying motions, separate documentation review chains.

## Primary navigation pattern

`products` at the root, with **`tabs` per product** for the standard "Guides + API reference" split (or `groups` per product if the product has no API surface).

The `products` pattern in `docs.json` provides the visible product switcher in the top nav. Each product is a self-contained mini-site with its own tabs, groups, and OpenAPI references.

`tabs` at the root *without* `products` is wrong for genuinely separate products — it pretends the products are sections of one site, which they aren't. Use `products` and let the switcher do the work.

## Starting structure

```
Products (root navigation pattern)
├── Product A
│   └── Tabs
│       ├── Guides             (groups inside)
│       └── API reference      (auto-generated from product-a/openapi.yaml)
├── Product B
│   └── Tabs
│       ├── Guides
│       └── API reference      (auto-generated from product-b/openapi.yaml)
└── Product C
    └── Groups (no API surface — `groups` instead of `tabs`)
        ├── Get started
        └── …
```

Three structural rules:

1. **Each product has its own root.** Don't share groups across products even if the content is *similar* — it makes the product switcher useless. If two products share a quickstart, write it twice (or, if the duplication is exact, use a snippet).
2. **Each product has its own OpenAPI spec, at its own path.** `product-a/openapi.yaml`, `product-b/openapi.yaml`. Inheritance attaches at the per-product tab level.
3. **Cross-product content lives in a "Platform" or "Company" page** outside the `products` switcher. SSO setup, billing, the company's API key model — these aren't per-product. Put them under an `anchors` section visible from all products.

## Component conventions

- **`<Card>` in `<Columns>` on the global landing page** — link to each product. Each card has the product's icon and a one-sentence description (no marketing prose).
- **`<Tabs>` per product** for the Guides / API reference split (when the product has an API).
- **`<Steps>` for procedures** — same as `patterns/saas-product-docs.md`.
- **`<Banner>` for product-specific announcements**, scoped to the relevant product's pages — not the global site.
- **`<Update>` per product changelog**, kept under each product's "Reference" or "Changelog" group, not in a global changelog.
- **`<CodeGroup>` heavily on API tabs**, per-product.

Keep visual style consistent across products (theme, colors, components). The product switcher is the only thing that should change between sub-sites.

## OpenAPI integration

Each product gets its own spec, attached at the per-product tab level. See `patterns/api-docs.md` for the per-product API tab specifics.

## Common mistakes

- **Reaching for `products` when one is enough.** If the "products" share marketing positioning, share a buying motion, or share most of their docs, they're one product with different surfaces. Use `patterns/saas-product-docs.md` with `tabs`.
- **Cross-linking between products without telling the reader.** If a Product A page links to a Product B page, the reader gets thrown into a different product's nav — surface the jump explicitly with a `<Card>` rather than an inline link.
- **Sharing groups across products.** Each product is a mini-site; share content via snippets if exact, otherwise duplicate. Shared groups confuse the product switcher.
- **One global API reference for all products.** If multiple products have APIs, give each its own OpenAPI spec and reference. A merged spec leaks abstractions.
- **Marketing-style global landing.** Documentation home pages, even multi-product ones, are not landing pages. Lead with the product cards; skip the hero copy.
- **Forgetting the platform-level docs.** SSO, billing, account management, status pages — these are *company-level*, not product-level. They need their own home outside the product switcher.
- **Versioning all products on the same cadence.** Different products release on different cadences. Use per-product `versions` (nested under the product) when needed; don't versionate at the company level.
- **No product-specific search scoping.** Mintlify's search is global by default; on a multi-product site, readers expect "find this in *this* product." If the platform supports it, configure search scoping per product.

## Worth promoting to a skill?

No. The `design` skill already handles `products` as one of the navigation patterns; this pattern is the canonical instantiation. The complexity here isn't a missing skill — it's a series of structural decisions (when to split products, how to handle cross-product content, where platform-level docs live) that this pattern documents.

If a contributor wants to specialize further (e.g., versioned multi-product, or multi-product with a partner ecosystem), prefer adding more patterns rather than promoting this one.

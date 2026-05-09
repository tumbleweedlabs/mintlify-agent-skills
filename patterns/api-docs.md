# Pattern: API documentation

## One-line summary

A reference-led documentation site for an API or SDK, paired with a small set of conceptual guides. The reference is auto-generated from an OpenAPI spec; the guides cover the things the spec can't express (authentication flows, errors, rate limiting, webhooks, end-to-end examples).

## Audience

Developers who already know what an API is. They alternate between *browsing* (looking up the shape of an endpoint) and *reading* (understanding how to authenticate, paginate, or handle a webhook). Treat them as people who want answers fast — search-first, not narrative-first.

## Primary navigation pattern

`tabs` at the root, with two tabs:

- **Guides** — narrative, ordered, hub-first.
- **API reference** — reference, browsed, auto-generated from the spec.

`tabs` is the right call here because the two surfaces have different audiences-of-the-moment (a developer in "learning the API" mode vs. "looking up an endpoint" mode), different reading patterns (linear vs. lookup), and different content shapes (MDX vs. spec-derived).

If the API surface is small enough that auto-generated endpoint pages would be < ~10 entries, consider `groups` with the API as one group instead of `tabs`. Tabs cost a click; don't pay it for a tiny surface.

## Starting groups

```
Tabs
├── Guides
│   ├── Get started        (group, expanded: true)
│   │   ├── Quickstart
│   │   ├── Authentication
│   │   └── Make your first request
│   ├── Concepts           (group, expanded: false)
│   │   ├── Core concepts
│   │   ├── Errors
│   │   ├── Rate limits
│   │   └── Pagination
│   └── Integrations       (group, expanded: false)
│       └── Webhooks
└── API reference          (auto-generated from openapi.yaml)
```

The Guides tab is the *narrative* — promote a quickstart to within one click of the root. The API reference tab is the *catalog* — let OpenAPI generate it; do not hand-author endpoint pages.

## Component conventions

- **`<CodeGroup>` is heavily used.** Most code samples should be available in 2–4 languages (curl + the most likely SDK languages). Defer to https://mintlify.com/docs/components for the catalog.
- **`<Steps>` for any procedure that must be done in order** (auth setup, first request, webhook setup).
- **`<ParamField>` and `<ResponseField>`** in any hand-authored conceptual page that documents data shapes. Match the formatting OpenAPI generates for the reference, so readers don't have to learn two conventions.
- **`<Card>` in `<Columns>`** on the Guides tab landing page — link to the 4–6 most common journeys.
- **Callouts**: `<Warning>` for destructive operations (DELETE endpoints, irreversible state changes), `<Note>` for permission requirements, `<Tip>` for performance recommendations.

Avoid `<Tabs>` inside endpoint pages — `<CodeGroup>` covers the language switch and `<Tabs>` confuses the page hierarchy when nested inside auto-generated content.

## OpenAPI integration

This is the load-bearing decision in this pattern. Get it right and most of the reference takes care of itself.

- **Reference the spec at the tab level**, not per-page: in `docs.json`, set `"openapi": ["openapi.yaml"]` on the API reference tab so all endpoint pages inherit.
- **Place the spec at the repo root or under `/api-reference/`** — match what the rest of the site uses, but a single canonical location prevents drift.
- **The auto-generated pages cover endpoints exhaustively.** Don't hand-author endpoint pages unless the spec genuinely cannot express the workflow (multi-step flows, polling patterns, complex auth handshakes).
- **Conceptual API content lives in the Guides tab**, not next to the auto-generated endpoint list. Auth, errors, rate limits, pagination, webhooks — each is a Guides-tab page that *links into* the reference for specific endpoints.
- **For endpoint-specific extra content** (a long-form example, a migration note), use `description` extensions in the spec rather than a separate MDX file. Keep one source of truth.

## Common mistakes

- **Hand-authoring endpoint pages alongside the auto-generated ones.** Two systems, eventual drift. Always pick one.
- **Putting auth or errors in the API reference tab.** They're conceptual; they belong in Guides. The reference tab is for endpoint shapes.
- **Burying the quickstart.** If a developer can't make a successful API call within two clicks of the home page, the IA is wrong.
- **Inventing custom auth or error components.** Use `<ParamField>`, `<ResponseField>`, `<CodeGroup>`, `<Steps>`. They handle most of it.
- **Versioning via the navigation pattern when only the spec changed.** If the API has multiple versions but the guides are identical, use OpenAPI spec files per version, not the `versions` navigation pattern.
- **Marketing prose on the API reference landing page.** Lead with the base URL, auth model, and a single end-to-end example. No "powerful," no "seamless."

## Worth promoting to a skill?

No. This pattern is a starting point a designer can apply in 10 minutes — there's nothing here that wouldn't dilute the core `design` skill if hoisted into it. The `design` skill's *Component decision matrix* and *OpenAPI integration* sections already contain everything needed to apply this pattern; this file is just the canonical instantiation for an API site.

If a future contributor wants to specialize *more* (e.g., GraphQL-specific defaults, AsyncAPI-specific defaults), prefer adding *more patterns* (`patterns/graphql-docs.md`) rather than promoting this one to a skill.

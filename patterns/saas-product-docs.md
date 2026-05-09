# Pattern: SaaS product documentation

## One-line summary

The canonical product-docs site for a SaaS company: a quickstart-led narrative site organized by what users are trying to *do*, with an optional API reference tab if the product has one. The dominant audience is end users (or admins of end users), not developers building against an API.

## Audience

End users of the product, plus admins setting it up. Some are evaluating the product before buying; most are using it daily. They mostly arrive via Google or in-app help links — assume each page may be the entry point. Job-shaped, not feature-shaped: readers want to *accomplish something*, not browse the feature list.

## Primary navigation pattern

`groups` at the root, with a small set of journey-named groups.

If there's also an API or webhook surface, promote to `tabs` with a "Guides" tab and an "API reference" tab — at that point, the API-docs pattern (`patterns/api-docs.md`) takes over for the reference tab.

If the product has more than one major surface (admin console, end-user app, integrations), don't reach for `tabs` reflexively — try `groups` with section hubs first. Tabs cost a click on every navigation; only pay it when the audiences genuinely diverge.

## Starting groups

```
Groups
├── Get started               (expanded: true)
│   ├── Quickstart
│   ├── Core concepts
│   └── Workspace setup
├── <Primary verb 1>          (expanded: false)
│   └── …
├── <Primary verb 2>          (expanded: false)
│   └── …
├── Integrations              (expanded: false)
│   └── …
├── Administration            (expanded: false)
│   ├── Roles and permissions
│   ├── Billing
│   └── Audit logs
└── Reference                 (expanded: false)
    ├── Glossary
    ├── Limits
    └── Changelog
```

Replace `<Primary verb 1/2>` with the actual top-level user jobs (e.g., "Create campaigns," "Analyze results," "Build dashboards"). Verb-named groups, not noun-named — readers are coming with intent, not browsing a taxonomy.

Keep the count of top-level groups to **5–7**. If you're at 9, two of them probably collapse into one.

## Component conventions

- **`<Steps>` for procedures.** This is the dominant component on a SaaS docs site; every "how do I X" page is a `<Steps>` block.
- **`<Card>` in `<Columns>` on the home page and section hubs.** Link to the 4–6 most-traveled journeys.
- **`<Frame>` around screenshots.** A SaaS docs site without screenshots is suspicious; one with bare unframed PNGs looks unfinished. Always add descriptive alt text.
- **`<CodeGroup>` only for code-relevant pages** (integrations, API examples). Don't pepper code-styling onto pages that aren't code.
- **Callouts**: `<Warning>` before any destructive admin action (deleting a workspace, revoking access), `<Note>` for plan/tier requirements, `<Tip>` for shortcuts.
- **`<Accordion>` for FAQs and edge cases.** Don't let edge cases interrupt the main flow.
- **`<Tabs>` for OS-specific or role-specific instructions.** "Mac / Windows / Linux," "Admin / Member."

Avoid `<Update>` on every page — it loses signal if everything is "updated." Reserve it for genuinely changed-recently sections.

## OpenAPI integration

Optional. Only relevant if the product has an API the user docs reference.

- If yes: promote the root navigation from `groups` to `tabs`, and use the `patterns/api-docs.md` pattern for the reference tab.
- If no: skip this section entirely. Don't fabricate an API surface.

## Common mistakes

- **Naming groups by feature instead of by job.** "Reports" is a feature; "Analyze results" is a job. The user is shopping for the job.
- **Putting administration in front of end-user content.** Admin tasks are usually < 10% of traffic. Put them in their own group, near the bottom.
- **Burying the quickstart.** A new user should be able to start using the product within one click of the home page.
- **One enormous "Features" group with 30 pages.** Split into journey-named groups, even if it feels redundant — `expanded: false` keeps the sidebar clean.
- **Marketing prose on the home page.** Documentation is not a landing page. Avoid "powerful," "seamless," "robust." Lead with what the reader can do here.
- **No section hubs.** Every group with > 5 pages needs an index page that orients the reader. Without it, the sidebar *is* the orientation, and it's a poor one.
- **Skipping screenshots to look more "professional."** A SaaS docs site without UI screenshots is harder to use than one with even mediocre screenshots.

## Worth promoting to a skill?

No. The `design` skill already covers job-shaped grouping, hub pages, and the component matrix. This pattern is the canonical instantiation for a product company — a starting tree to choose from, not a separate skill.

If multi-product is needed, see `patterns/multi-product.md`.

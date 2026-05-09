# Pattern: Internal wiki

## One-line summary

A documentation site for an internal audience — a team, an engineering org, a department. The reader knows the context and the people; they're looking up a specific runbook, decision record, or onboarding step. The site is search-first, low on landing-page polish, and biased toward *completeness* over *narrative shaping*.

## Audience

Employees of the organization that owns the wiki. They're not browsing for inspiration; they're looking for the on-call runbook for a service, the ADR that decided why we use Postgres, the hiring doc for the team they just joined. They've been pointed here by a Slack message, a teammate, or their own search history. Assume they arrive cold to the page they land on, but warm to the org's context.

## Primary navigation pattern

`anchors` at the root, with broad section anchors that stay visible from every page. This is the canonical wiki pattern: persistent jumps to "Engineering," "People," "Runbooks," "ADRs," etc., from anywhere in the site.

`groups` works as a fallback for smaller wikis where anchors would feel heavy. For most internal wikis with > ~30 pages, `anchors` wins.

`tabs` is wrong for an internal wiki — it implies multiple distinct audiences, which a single-team wiki rarely has. Don't reach for tabs.

## Starting groups

```
Anchors (visible from every page)
├── Engineering
│   ├── Services           (groups inside the anchor)
│   ├── Runbooks
│   ├── On-call
│   └── ADRs
├── People
│   ├── Onboarding
│   ├── Team directory
│   └── Career frameworks
├── Operations
│   ├── Tools and access
│   ├── Vendors
│   └── Budgets and approvals
└── Policies
    ├── Security
    ├── Code of conduct
    └── Privacy
```

Each anchor opens a small set of groups underneath. Within an anchor, organize by *what kind of document this is* (runbook, ADR, onboarding doc, policy) rather than by team — team boundaries shift; document types don't.

The "right" top-level anchors depend on the org. The four above are a starting point, not a recommendation.

## Component conventions

- **Search is the primary navigation.** Mintlify's built-in search is doing more work on a wiki than on any other site type. Make sure pages have descriptive `description` frontmatter and useful `keywords`.
- **`<Steps>` for runbooks.** A runbook that isn't a `<Steps>` block is a runbook that won't be followed under stress.
- **`<Warning>` for any step that could cause an incident.** This is the single most important callout on an internal wiki. Don't dilute it with non-emergency uses.
- **`<Note>` for permission requirements** ("You need to be in the `infra` group for this step").
- **`<Accordion>` for "if this fails, try this"** branches inside runbooks.
- **`<Tabs>` for environment-specific instructions** (production / staging / local).
- **`<Update>` to mark recent ADR decisions or runbook changes.** ADRs especially benefit from a date stamp.
- **`<CodeGroup>` for command sequences in different shells / OSes.**
- **`<Frame>` around any UI screenshot, with descriptive alt text.**
- **Skip the polish:** `<Banner>` and `<Card>` in `<Columns>` are less useful here than on user-facing sites. A wiki home page can be a plain ToC.

## OpenAPI integration

Rare. An internal wiki occasionally embeds an internal API reference, but if so it's usually a tiny tab — see `patterns/api-docs.md`. Most wiki-style sites don't have a public API to document.

## Common mistakes

- **Treating it as a marketing surface.** An internal wiki home page with hero copy and `<Card>` grids is a wiki nobody scans. Plain text with anchors works better.
- **No search-first frontmatter.** Without `description` and `keywords`, search results are noise. On a wiki, this is the difference between findable and not.
- **Runbooks that aren't `<Steps>`.** Under incident pressure, paragraphs are unreadable. Always `<Steps>`, always with explicit pre-conditions.
- **ADRs without dates.** An ADR without a date stamp is folklore. Use frontmatter `description` for the date or a `<Update>` block at the top.
- **Mixing public and internal content.** If you find yourself authoring "this is the public version, this is the internal version" — split the wikis. They have different audiences and different review processes.
- **Letting orphaned pages accumulate.** Without `maintain/SKILL.md`'s staleness scan and orphan check, an internal wiki becomes a graveyard. Schedule it.
- **No onboarding hub.** New hires need *one page* that lists what to read, in order, in their first two weeks. Without it, every onboarding is improvised.
- **Burying the on-call runbook.** It should be reachable in two clicks from any anchor.

## Worth promoting to a skill?

No. The structural moves (`anchors`, search-first frontmatter, runbooks-as-`<Steps>`) are all expressible with the `design` and `write` skills. The *content discipline* of a wiki — actually writing the runbooks, keeping them current, retiring stale ADRs — is a writing and maintenance problem, not a Mintlify problem.

If a contributor wants to specialize further (e.g., engineering-org-specific structure with separate sections per service, or a wiki-with-Backstage-integration), prefer adding *more patterns* rather than promoting this one.

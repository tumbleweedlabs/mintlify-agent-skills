# Pattern: Resource library

## One-line summary

A curated collection of external links, tools, papers, repos, or services, organized into browsable hubs. The site's *content* is mostly card-shaped pointers to resources elsewhere; the value is in the curation, the categorization, and the freshness signals.

## Audience

Practitioners or researchers looking for *the right tool / paper / repo for a task they have right now*. They're not reading top-to-bottom — they're scanning a hub for a card whose description matches their need, then clicking out to the linked resource. They return when a new task surfaces.

The single most important question for this audience: **"is this still maintained / accurate / live?"** A resource library that links to four-year-dead repos loses trust fast.

## Primary navigation pattern

`groups` is the canonical choice — categories of resources map cleanly onto groups. Each group is a hub page densely packed with cards.

`anchors` is appropriate as a *secondary* pattern when you want quick jumps from any page to "Submit a resource," "About this library," or "Contributing."

`tabs` is appropriate only when the library splits into genuinely distinct surfaces (e.g., "Tools" vs. "Papers" vs. "Datasets") with different consumption patterns. Don't reach for tabs to organize a single library — `groups` will do.

## Starting groups

```
Groups
├── Browse                         (expanded: true)
│   ├── Library home               (hub, lists categories)
│   ├── <Category 1>               (hub, cards-heavy)
│   ├── <Category 2>               (hub, cards-heavy)
│   └── <Category 3>               (hub, cards-heavy)
├── Featured / This week           (expanded: false, optional)
│   └── …
└── About                          (expanded: false)
    ├── How this is curated
    ├── Submit a resource
    └── Changelog
```

The "Browse" group is the work. Each child page is a category hub: a one-paragraph orientation, then a `<Columns>` of `<Card>`s.

A "Featured" or "This week" group is optional and high-value if maintained — it answers "what's new since I last visited." Don't add it if you can't keep it current; an empty "Featured" group is worse than no Featured group.

## Component conventions

- **`<Card>` in `<Columns>` is the dominant component.** Almost every page is a hub of cards. The card *is* the resource pointer.
- **Card descriptions follow `write/SKILL.md`'s card-description format**: one sentence, no marketing adjectives, **with a freshness or specificity signal** when the link points to an external resource. Examples:
  - `"PyTorch implementation, last commit 2026-04-12"`
  - `"Foundational paper (Vaswani et al., 2017) — still essential"`
  - `"Active community Discord — 12k members, daily activity"`

  This is a format hint, not a mandate. If the site uses a `Last touched` (or similar) frontmatter convention, surface freshness through that mechanism instead of inline.

- **`<Frame>` around any screenshot of a linked tool's UI** — and only if the screenshot adds information the description doesn't.
- **`<Tabs>` for multi-axis categorization** — e.g., a single page that splits "By language" / "By framework" / "By cost." Use sparingly; the primary axis should be the group, not a tab.
- **`<Update>` for the most recent additions to a category page** — a small `<Update label="Added 2026-05-01">` line at the top of the category hub is signal-rich.
- **Avoid `<Steps>`, `<CodeGroup>`** — they don't fit the resource-pointer content shape.

## OpenAPI integration

Not applicable. A resource library doesn't have an API surface to document.

## Common mistakes

- **No freshness signal.** A library that links to dead resources loses credibility fast. Either inline the freshness on each card, or maintain a `Last touched` frontmatter convention and surface staleness automatically via `maintain/SKILL.md`'s scan.
- **Marketing prose in card descriptions.** "A powerful, robust ML framework" tells the reader nothing. "PyTorch — eager-execution PyData library, the de facto research standard, last release 2026-03" does.
- **Categories that overlap.** If a card belongs in two categories, the categories are the wrong shape. Split or merge.
- **No "About" page.** Readers want to know *why* a resource is in the library. A short "How this is curated" page builds trust.
- **Cards without icons.** A `<Card>` without an `icon` looks unfinished in `<Columns>`. Either icon all of them or none of them — consistency matters.
- **One enormous category page.** If a category has > 30 cards, it's two categories. Split before scrolling becomes the dominant interaction.
- **Skipping the global hub.** The home page should be a hub-of-hubs, not a deep narrative or a marketing pitch.
- **No staleness loop.** A resource library *demands* the `maintain/SKILL.md` staleness scan and lychee link-check on a recurring schedule. Without it, the library decays.

## Worth promoting to a skill?

No. This pattern is unusually high-leverage *as a pattern* — it captures a content shape that matters to a specific kind of site without diluting the core skills. But every component move (cards in columns, hub pages, freshness in card descriptions, lychee in CI, recurring staleness scans) is already owned by an existing skill. A "resource library" skill would either restate them or wander into curatorial taste, which isn't Mintlify-specific.

The best amplifier here isn't a new skill — it's making sure `maintain/SKILL.md`'s staleness scan is configured and running, and that `write/SKILL.md`'s card-description format is followed.

# Pattern: Learning site

## One-line summary

A site whose content is *modules to be learned*, not features to be looked up. Readers progress through prerequisites and follow a deliberate reading order. Each module is a self-contained learning unit: explanation, examples, exercises (optional), and a clear sense of *what comes next*.

## Audience

People learning a topic from the ground up. They may be new to the field, switching domains, or refreshing skills they haven't used in years. They want to know **where to start**, **what to read next**, and **whether they've covered the prerequisites for the page they're looking at**. They're easily bounced by a page that assumes more than they know — overshoot the audience, lose them.

## Primary navigation pattern

`groups` is the canonical choice — a learning site is fundamentally one ordered sequence (or a few short branches off a trunk), not a collection of equally-weighted reference surfaces.

`tabs` is appropriate when the site covers two or more *learning tracks* with genuinely different audiences (e.g., "For practitioners" vs. "For researchers"). Don't use tabs for "Beginner / Intermediate / Advanced" — that's a single track with a built-in progression, not separate tracks. Use one group per level instead.

`anchors` is appropriate as a *secondary* pattern inside `groups` when you want a persistent "Glossary," "Cheat sheet," or "Resources" link visible from every page.

## Starting groups

```
Groups
├── Start here                    (expanded: true)
│   ├── Welcome / How to use this site
│   ├── Prerequisites
│   └── Learning path overview
├── <Module 1: foundational>      (expanded: true)
│   ├── Module 1 overview         (hub)
│   ├── Lesson 1.1
│   ├── Lesson 1.2
│   └── Module 1 summary / what's next
├── <Module 2>                    (expanded: false)
│   ├── Module 2 overview         (hub)
│   └── …
├── <Module 3>                    (expanded: false)
│   └── …
└── Reference                     (expanded: false)
    ├── Glossary
    ├── Cheat sheet
    └── Further reading
```

Three rules of thumb:

1. **Each module gets a hub page.** The hub states the module's learning objective, lists prerequisite modules, and ends with a "Next" link. Without the hub, the sidebar is doing the orientation — and the sidebar is a poor orientation device.
2. **Order matters.** Modules should be listable in a sensible reading order; a reader following the sidebar top-to-bottom should be making progress. If two modules are independent, put the more foundational one first.
3. **A glossary or cheat sheet at the bottom is load-bearing.** Learners constantly need to look up a term they encountered three pages ago. Make it one click from any page.

## Component conventions

- **`<Steps>` for procedures inside a lesson** — when the lesson teaches a skill, the skill itself is a sequence.
- **`<Note>` for prerequisite reminders at the top of any lesson that has them.** "This lesson assumes you've completed Module 1."
- **`<Tip>` for "if you've seen this before, skip ahead" notes** — let advanced readers skim without insulting beginners.
- **`<Card>` in `<Columns>` for "What's next" at the end of every module.** 1–3 cards: the next module in sequence, plus a related module if applicable.
- **`<Accordion>` for "common pitfalls" or "deeper dive" content** — keep the main flow scannable; let curious readers expand.
- **`<Mermaid>` for any concept whose mental model is a graph or a flow.** Diagrams are not decoration on a learning site; they're load-bearing.
- **`<CodeGroup>` if the lesson uses code** — even when only one language is shown, it normalizes the formatting.
- **`<Frame>` around screenshots and diagrams.** Always with alt text describing what the image *teaches*, not what it depicts.
- **`<Update>` to mark lessons updated for a syllabus version** — useful when the topic itself moves (e.g., a tool's API changes).

## OpenAPI integration

Rarely applicable. A learning site sometimes references an API in code samples, but the API itself is usually not the subject. If the site teaches an API, that's an API-docs site with educational framing — see `patterns/api-docs.md` first; this pattern second.

## Common mistakes

- **No reading order.** A learning site without a clear "where to start" is a wiki with worse search.
- **Module hubs that just list child pages.** The hub should *teach* the module's purpose, not duplicate the sidebar.
- **Skipping prerequisites.** Every lesson that assumes prior knowledge needs a `<Note>` saying so. Otherwise readers bounce.
- **Treating "advanced" as a separate track.** It's the end of the same track. Use ordering, not tabs.
- **Marketing-style introductions.** "Welcome to the exciting world of..." — a learning site loses trust faster than a product site if the prose feels promotional.
- **No glossary.** Learners need it within two days.
- **Conflating reference content with lesson content.** A reference page (e.g. "all the keyword arguments of `transform()`") doesn't belong inside a lesson. Put references in the Reference group; link from lessons.
- **One enormous module.** If a module has > 8 lessons, it's two modules.
- **Forgetting to mark progress.** No native "I've completed this lesson" component exists in Mintlify, but a "Module summary" page at the end of each module gives the reader a checkpoint.

## Worth promoting to a skill?

No. The structural moves (hub pages, ordered groups, prerequisites callouts, glossary anchor) are all expressible with primitives the `design` skill already covers. The *content discipline* of teaching well is a writing problem, owned by `write` (and ultimately the human writer). A separate "learning" skill would either restate `design` and `write`, or wander into pedagogy that isn't Mintlify-specific.

If a contributor wants to specialize further (e.g., MOOC-style sites with assessments, code playgrounds), prefer adding *more patterns* (`patterns/mooc-site.md`) rather than promoting this one.

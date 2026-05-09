# Contributing

Thanks for considering a contribution. This document is the rulebook: the load-bearing constraint of the suite, how to add patterns and skills, the vendoring policy, and the PR conventions. Read [`ARCHITECTURE.md`](./ARCHITECTURE.md) first for the *why*; this file covers the *how*.

## The load-bearing rule

`SKILL.md` files **must remain site-type-agnostic.** Site-type variation belongs in [`patterns/`](./patterns), never in a skill.

If a PR adds a sentence to any `SKILL.md` that begins with "for a learning site," "for an API reference," "for a resource library," or any equivalent — it will be sent back. The same content can almost always live as a pattern instead.

This is the single hard rule. Most other decisions in this guide derive from it.

## Reporting issues

Open an issue at https://github.com/TumbleweedLabs/mintlify-agent-skills/issues. Include:

- **Which skill or pattern** — `router`, `design`, `write`, `create`, `maintain`, or one of `patterns/*.md`.
- **The user request or scenario** that exposed the problem (anonymized if needed).
- **Actual vs. expected behavior** — what the agent did, what you expected.
- **The agent harness** (Claude Code, Cursor, Copilot, Codex, Windsurf, Claude Agent SDK, custom) and the suite version (`metadata.version` in the relevant `SKILL.md`).
- **Live Mintlify version** if relevant — `mint --version` and a note on whether the live docs at https://mintlify.com/docs have moved on from what the skill assumes.

Bug categories worth flagging explicitly:

- **Site-type leakage.** A skill's prescriptive workflow assumes a particular site type. High-priority — this is the constraint we defend hardest.
- **Schema drift.** A skill references a `docs.json` field, component, or CLI flag that is no longer at https://mintlify.com/docs. Verify against the live docs before filing.
- **MCP behavior drift.** A skill's MCP integration section asserts a tool name, response shape, or behavior that doesn't match the current `https://mcp.mintlify.com` server.

## Adding a pattern

Patterns are the suite's release valve for site-type variation. When a contributor finds themselves about to add site-type-specific guidance to a skill, the answer is almost always "this is a pattern."

**Process:**

1. **Copy an existing template** in [`patterns/`](./patterns) as a starting point. The closest match by audience usually transfers best.
2. **Fill the standard sections** defined in [`patterns/README.md`](./patterns/README.md):
   1. One-line summary
   2. Audience
   3. Primary navigation pattern (with rationale for the choice)
   4. Starting groups / tabs / products
   5. Component conventions
   6. OpenAPI integration (or "not applicable")
   7. Common mistakes
   8. Worth promoting to a skill? (usually no)
3. **Defer to existing skills** for cross-cutting concerns. A pattern should *cite* `skills/design/SKILL.md` for IA primitives, `skills/write/SKILL.md` for prose conventions, `skills/maintain/SKILL.md` for health loops, and `skills/create/SKILL.md` for bootstrap. If the pattern duplicates content from a skill, trim — the skill stays canonical.
4. **Submit a PR** with:
   - The new pattern file at `patterns/<site-type>.md`.
   - A one-line entry added to the table in [`patterns/README.md`](./patterns/README.md).
   - A one-line entry added to the *Initial templates* list in [`skills/router/SKILL.md`](./skills/router/SKILL.md) (Step 3 of the routing workflow).
   - A one-paragraph PR description: which existing site type the pattern is modeled after, and why a generic skill couldn't already handle it.

**The bar for inclusion:** a pattern earns its place when the same content would otherwise tempt a contributor to bake site-type assumptions into a `SKILL.md`. If the pattern restates content already in a skill, it doesn't belong in `patterns/`. If the pattern is so site-specific that nobody else's site would use it, it doesn't belong in `patterns/` either — keep it in your own repo.

## Adding a sibling skill

A sixth (or later) `SKILL.md` is a much higher bar than a pattern. The current five-skill split is deliberate: each skill owns a sharp slice of the lifecycle, and adding a sixth dilutes the routing decision the user (and the router) makes every session.

**Before opening a PR, answer these in writing:**

1. **Can this be a pattern instead?** If the new functionality is shaped like "for this site type, do X," it's a pattern. Patterns are cheaper to author, cheaper to maintain, and easier to evolve.
2. **Is this lifecycle phase genuinely missing?** The current phases are: *intent routing* (router), *initial bootstrap* (create), *information architecture* (design), *prose authorship* (write), *recurring health* (maintain). New skills should fit a phase the existing skills don't already cover.
3. **Will this skill reliably consume more than ~10KB of guidance?** A small skill is better folded into an existing one. The bar for a new skill includes substantive depth.
4. **Does the new skill respect the universal guardrails?** Live docs over training data, no silent rewrites, no invented components, no marketing prose, never use `mint.json`, no deployment changes, site-type-agnostic. If the new skill needs to violate any of these, that's a design problem, not a green light.

Candidates considered and deferred (do not re-open without new evidence):

- **`mintlify-api`** — wrapping the Mintlify REST API (auth, deployment triggers, metadata queries). Upstream has a 43-line stub at [`skills/mintlify-api/SKILL.md`](https://github.com/mintlify/docs/tree/main/skills/mintlify-api) in `github.com/mintlify/docs`. Referenced but not wrapped in v1.0.0. Revisit if there's user demand for ops automation.
- **`visuals`** — diagram and infographic generation. Currently covered by the `design` skill's component matrix (`<Mermaid>`, `<Frame>`, `<Tree>`).
- **`learning`** — module structure with prerequisites and reading order. Currently a pattern (`patterns/learning-site.md`).

If your proposed skill resembles any of the above, the answer is probably "make it a pattern" or "extend an existing skill." If you believe you have a genuinely new lifecycle phase, open an issue *first* to discuss before drafting the PR.

**If the bar is met, the PR includes:**

- The new skill at `<name>/SKILL.md`, matching the structural template (frontmatter → "When to use / When not to use" → operating principles → prescriptive workflow → output contract → guardrails).
- A one-line entry added to the table in `README.md` and to the routing table in `skills/router/SKILL.md`.
- A new section in [`ARCHITECTURE.md → Adding a sibling skill`](./ARCHITECTURE.md#adding-a-sibling-skill) updating the deferred-skill list with rationale.

## Vendoring policy

The suite vendors content from upstream Mintlify in two places:

1. **`skills/write/SKILL.md`** vendors the writing standards from the upstream `mintlify` skill at https://github.com/mintlify/docs/blob/main/skill.md. The vendored sections are *Voice and structure*, *What to avoid*, *Formatting*, *Code examples*, *Page frontmatter*, and *File conventions*. Credit lives both inline (a credit blockquote at the start of *Voice and structure*) and as a *Vendoring notice* footer.
2. **`skills/design/SKILL.md`** is descended from the standalone `TumbleweedLabs/mintlify-design-skill` repository, which has been archived and superseded by this suite.

**Re-vendor on upstream change.** When upstream's writing skill or component reference moves on, refresh the vendored content in `skills/write/SKILL.md` deliberately — don't silently drift. Procedure:

1. Read the current upstream at https://github.com/mintlify/docs/blob/main/skill.md.
2. Diff the vendored sections against the new upstream.
3. Update `skills/write/SKILL.md` with substantive changes only. Light editorial integration is permitted; semantic content should match.
4. Update the *Vendoring notice* footer with the upstream commit SHA and date.
5. Open a PR titled `chore(write): re-vendor upstream writing standards (<short-sha>)`.

**Flag divergence.** If you find a place where the suite's vendored content has drifted from upstream and shouldn't have, file an issue tagged `vendoring` so it can be reconciled in a single PR rather than fixed piecemeal.

**Do not vendor:**

- Live Mintlify documentation at https://mintlify.com/docs — every skill defers to the live source.
- The `docs.json` schema at https://mintlify.com/docs.json — referenced as the authoritative schema, never copied.
- The Mintlify MCP server at https://mintlify.com/docs/ai/mintlify-mcp — referenced, never inlined.

## Testing changes locally

The suite is a collection of `SKILL.md` files; there is no build step and no test runner in this repo. Validation is manual:

1. **Frontmatter parses as YAML.** A malformed `SKILL.md` frontmatter block breaks every harness's discovery. Validate by hand or with a YAML linter before pushing.
2. **Cross-references are valid.** Internal Markdown links between skills, patterns, and root docs (`README.md`, `ARCHITECTURE.md`, `CONTRIBUTING.md`) should resolve. A quick `grep -rn '\.md' .` audit catches most breaks.
3. **Vendored content matches upstream.** If you touched `skills/write/SKILL.md`'s vendored sections, diff against upstream before pushing.
4. **Behavior verification against live Mintlify.** If a change references a CLI flag, schema field, or MCP tool, verify against the live source (`mint --help`, https://mintlify.com/docs.json, the live MCP server). Don't push schema claims you haven't run.

For changes that affect agent behavior, the highest-leverage test is to load the modified suite into your harness of choice and run the canonical sessions described in `README.md → First 60 seconds with a fresh repo`.

A formal CI pipeline (markdown lint, frontmatter schema validation, internal-link check) is on the roadmap — see *Deferred work* below.

## PR conventions

- **One concern per PR.** Don't bundle a pattern addition with a `write` re-vendor; they have different review chains.
- **Conventional commit prefixes.** `feat(write):`, `fix(maintain):`, `chore(patterns):`, `docs(architecture):`. Skills are scoped by directory name; root docs use the document name (`docs(readme):`, `docs(architecture):`, `docs(contributing):`).
- **Don't bump version inside a PR.** The maintainer bumps `metadata.version` in each affected `SKILL.md` at release time, not on every change. If a change is large enough to warrant a minor bump, flag it in the PR description.
- **Update the changelog if one exists.** v1.0.0 ships without one; this section will be revised when a `CHANGELOG.md` exists.
- **Squash-merge.** Keep `main` linear; the per-skill commits during initial drafting were granular for review and won't be the norm post-v1.0.0.

## Versioning

The suite uses semver, applied per-skill in each `SKILL.md`'s `metadata.version` field, and at the suite level via Git tags.

- **Suite tags** — `v1.0.0`, `v1.1.0`, `v2.0.0`. Cut at the maintainer's discretion when a coherent set of changes has accumulated.
- **Per-skill versions** — each `SKILL.md`'s `metadata.version` reflects that skill's own evolution. A patch-level change to `skills/write/SKILL.md` bumps `write` to `1.0.1` without touching the others; a suite-wide breaking change bumps everything to `2.0.0`.

A change is **breaking** when:

- The router's intent → companion table changes mapping (existing user invocations route differently).
- A skill's *Output contract* removes or renames a section.
- The suite's directory layout changes.

A change is a **minor bump** when:

- A new pattern is added.
- A skill gains a new section without removing old ones.
- A new MCP tool is integrated.

Everything else is patch.

## Deferred work

The following are intentionally deferred from v1.0.0:

- **`mintlify-api` skill.** Upstream has a stub for the Mintlify REST API. Wrapping it would add a sixth skill focused on ops automation rather than lifecycle. Revisit when there's demand from real users.
- **Suite CI.** Markdown lint, YAML frontmatter validation, internal-link check, and possibly a "no site-type leakage" lint that flags banned phrases in `SKILL.md` files. Worth setting up; deferred until the right linter set is clear from observed PRs.
- **A formal `CHANGELOG.md`.** Will be added when the first non-initial change ships.
- **Multi-language support for the suite docs.** The suite is English-only. The patterns it produces work for any language Mintlify supports, but the skill files themselves do not have translations.

If you want to take any of the above on, open an issue first to align on scope.

## Code of conduct

Be patient and direct. Disagreement on architecture (e.g., should a pattern become a skill?) is welcome and necessary; rudeness toward contributors isn't. The maintainer reserves the right to close issues or PRs that are off-topic, drive-by, or inflammatory.

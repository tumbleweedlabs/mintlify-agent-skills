---
name: mintlify-maintain
description: Maintain the ongoing health of an existing Mintlify documentation site. Activate when an agent must run `mint broken-links`, `mint validate`, `mint a11y`, `mint update`, or `mint score`; configure or audit a CI link-check (lychee); perform a semantic staleness scan; or open a maintenance PR with proposed flags, archive markers, or `Last touched` updates. Use alongside the `design` skill if a scan reveals structural drift; alongside `write` if it reveals voice or formatting drift. Does not own initial CI setup (that's `create`) or wholesale rewrites.
license: MIT
compatibility:
  platforms:
    - Claude Code
    - Claude Agent SDK
    - GitHub Copilot
    - OpenAI Codex / ChatGPT agents
    - Cursor
    - Windsurf
  runtime:
    - Any agent harness that supports SKILL.md / system-prompt skills
    - Mintlify CLI (`mint`) — required for `broken-links`, `validate`, `a11y`, `update`, `score`
    - Optional: GitHub CLI (`gh`) for opening draft PRs
    - Optional: Mintlify MCP server (https://mintlify.com/docs/mcp) for live doc access
  requires:
    - Read access to `docs.json` and the MDX tree
    - Write access (to a feature branch) for opening maintenance PRs
metadata:
  author: tumbleweedlabs
  version: "1.0.0"
  suite: mintlify-agent-skills
  authoritative_docs: https://mintlify.com/docs
  cli_reference: https://mintlify.com/docs/cli/commands
  schema_reference: https://mintlify.com/docs.json
  scope: ongoing health, link checks, validation, accessibility, CLI migrations, staleness scans, maintenance PRs
  non_scope: initial site bootstrap, IA decisions, prose authorship, deployment, brand changes
---

# Mintlify Maintenance Skill

You are a maintainer for an existing Mintlify documentation site. Your job is to keep the site healthy over time — links resolve, the schema validates, accessibility holds, the toolchain stays current, and stale content gets surfaced for review. You produce **patch-ready proposals** (diffs, flags, draft-PR plans) — never silent rewrites.

> Always consult [mintlify.com/docs](https://mintlify.com/docs) and the CLI reference at [mintlify.com/docs/cli/commands](https://mintlify.com/docs/cli/commands) before running a command whose flags you don't remember. The CLI evolves. Do not improvise flags. If a [Mintlify MCP server](https://mintlify.com/docs/mcp) is available, use it.

This skill does **not** own initial site setup — that's `create`. It does not own IA decisions — that's `design`. It does not own prose — that's `write`.

---

## When to use this skill

Activate when the user (or a parent task) asks for any of:

- "Check broken links," "audit links," "link rot scan"
- "Validate the docs," "what's broken in `docs.json`," "schema check," "OpenAPI validation"
- "Accessibility audit," "a11y check"
- "Update Mintlify," "migrate to the latest schema," "what changed in mint"
- "Agent readiness," "is this site AI-friendly," `mint score`
- "Staleness scan," "what pages haven't been touched in N months," "what's out of date"
- "Open a maintenance PR," "auto-PR for our weekly scan"
- Adding *additional* checks to an existing CI pipeline (recurring lychee, scheduled staleness)

## When NOT to use this skill

Hand off when the task is:

- **Initial CI/CD setup** for a fresh site — that's `create/SKILL.md`. Adding *more* checks to an existing pipeline is fair game here.
- **Restructuring the IA** — that's `design/SKILL.md`. If a scan reveals structural drift (orphaned pages, hub pages with no inbound links, navigation that doesn't match the filesystem), flag it and hand off.
- **Rewriting prose** the staleness scan flagged — that's `write/SKILL.md`. Surface what's stale; don't ghost-write the replacement.
- **Deployment, DNS, hosting changes** — out of scope for the suite.
- **Wholesale rebuilds** — never. Maintenance is incremental.

If the user just wants a one-off "fix this broken link," do it inline; don't run the full workflow.

---

## Operating principles

1. **Run, don't guess.** When the CLI can answer the question, run it. Don't infer the state of links or schema validity from training data.
2. **Surface, don't fix.** Output a list of issues with proposed diffs. The human (or a downstream agent) applies them. Never silently rewrite content the scan flagged.
3. **Idempotent scans.** A staleness scan run twice in a row should produce the same flags unless content changed. Avoid randomness or LLM-tasted "feels stale" judgments without a checkable signal.
4. **Date-aware.** Stale content is defined relative to *today*. Always anchor to the current date in the report and in any `Last touched` updates.
5. **Defer to live docs and the CLI's own help.** If a flag isn't at https://mintlify.com/docs/cli/commands, run `mint <subcommand> --help` rather than guessing.
6. **Patch-ready output.** Every recommendation lands as a diff or a draft-PR plan a human can apply with one click and a `mint validate` run.

---

## Prescriptive workflow

Phases run in order. A full health pass is Phases 1–5. A targeted run (e.g. "just check broken links") can stop after Phase 2.

### Phase 1 — Inspect

1. Read `docs.json` at the repo root. Record:
   - Mintlify version pin or theme reference, if any.
   - Whether `redirects` is in use (and the format).
   - The primary navigation pattern (`groups`, `tabs`, `anchors`, `dropdowns`, `products`, `versions`, `languages`).
   - Any `openapi` references — `mint validate` will check those.
2. Check the existence and state of:
   - `.github/workflows/` — note any existing `lychee`, link-check, or `mint validate` workflows. Do not duplicate what already runs.
   - A `Last touched` (or equivalent) frontmatter convention on representative pages — record the field name; do **not** invent one if absent.
   - A `mint.json` (deprecated). If present, surface a migration step before doing other work.
3. Confirm the Mintlify CLI is installed and what version: `mint --version` (or `mint version`). If the user has `mint update` queued, save the current version for the migration report.

If `docs.json` is missing or unreadable, **stop and ask** — do not fabricate state.

### Phase 2 — Run the CLI checks

Run, in order, scoped to whichever the user asked for. For each command:

1. Capture the full output verbatim.
2. Categorize findings by severity (error, warning, info).
3. Map each finding to a file:line if the CLI provides one; otherwise to a page slug.

| Command                          | Purpose                                                                                  | When to run                                  |
|----------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------|
| `mint broken-links`              | Crawl internal links and surface 404s. Add `--check-anchors` to validate anchor targets. | Always, in any maintenance pass.             |
| `mint validate`                  | Validate `docs.json`, MDX, and any referenced OpenAPI specs.                             | Always, in any maintenance pass.             |
| `mint a11y`                      | Accessibility checks.                                                                    | Always; surface as warnings if not blocking. |
| `mint update`                    | Migrate `docs.json` and MDX usage to the latest schema. Produces large diffs.            | Only when the user asks.                     |
| `mint score [url]`               | Agent-readiness scan (requires `mint login`). Optional but useful for AI-facing sites.   | When user asks for "agent readiness" or AI-friendliness review. |
| `mint analytics …`               | Traffic / search / feedback signals (requires `mint login`).                             | As a staleness *signal*, not a primary check.|

Notes:
- `mint openapi-check` is **deprecated**. Use `mint validate`.
- If a command isn't recognized, the CLI may have moved on. Run `mint --help` and consult https://mintlify.com/docs/cli/commands before improvising. Do **not** invent flags.

### Phase 3 — Configure (or audit) CI link checks

`mint broken-links` covers internal links. **External** links rot too — that's what [lychee](https://github.com/lycheeverse/lychee-action) is for. If the repo has no scheduled link check, recommend one. If it has one, audit for: schedule cadence, scope (paths / globs), failure mode (issue vs. PR vs. nothing).

A starter workflow (verify the action version against https://github.com/lycheeverse/lychee-action — the version pinned below may lag):

```yaml
# .github/workflows/links.yml
name: link-check
on:
  schedule:
    - cron: "0 9 * * 1" # Mondays 09:00 UTC
  workflow_dispatch:

jobs:
  lychee:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: lychee
        uses: lycheeverse/lychee-action@v2
        with:
          args: --no-progress --verbose './**/*.mdx' './**/*.md'
          fail: false
      - name: Open issue on failure
        if: failure()
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: `Link rot detected — ${new Date().toISOString().slice(0, 10)}`,
              body: 'lychee found broken external links. See the workflow run.',
              labels: ['docs', 'link-rot']
            })
```

Notes:
- Pin the action to its **major version tag** (e.g. `@v2`) so security patches arrive without breaking; do not pin to `main`.
- Scope `args` to the docs paths only — running lychee against an entire monorepo is wasteful for a docs site.
- If the site has known noisy responders (rate-limiting CDNs, GitHub anonymous-API limits), add `--accept` codes per the lychee-action README.
- Pair with the `mint broken-links` step in CI; together they cover internal + external.

### Phase 4 — Semantic staleness scan

This is the generic, site-type-agnostic staleness check. Run only when the user asks, or on a configured cadence.

For each MDX page, gather these signals:

| Signal                                                           | Source                                                |
|------------------------------------------------------------------|-------------------------------------------------------|
| Last edit date                                                   | `git log -1 --format=%cI -- <path>`                   |
| Frontmatter `Last touched` (or equivalent) field, if present     | YAML frontmatter; field name from Phase 1 inspection  |
| Linked-resource health                                           | Most recent lychee run, if available                  |
| External-resource version drift                                  | Optional: GitHub releases, PyPI, etc. — only if the page links to versioned resources and the agent has been told to check |
| Low-traffic signal                                               | `mint analytics stats` over a defined window, if the user has enabled analytics                          |

Flag a page as **potentially stale** when **at least two** signals point to it. Suggested defaults (ask if unset; never improvise):

- Last git edit older than 9 months.
- `Last touched` field, if used, older than 9 months.
- Linked external resources have moved, been deprecated, or returned non-2xx in the last lychee run.
- Pageviews in the bottom decile over the last 90 days, if analytics is connected.

**Do not flag based on LLM intuition alone.** The scan must be reproducible. If only one signal points at a page, downgrade to "watch-list" rather than "stale."

For each flagged page, propose one of:

1. **Refresh** — page still relevant, content review needed. Diff: bump `Last touched` to today; leave prose alone.
2. **Archive** — page no longer relevant. Diff: add an `<Update>` callout at top with the archival date; set `archived: true` in frontmatter **only if** the site already uses such a field; surface required `redirects` in `docs.json`.
3. **Delete** — only on explicit user instruction. Diff: file removal + a `redirects` entry to the closest live page.

### Phase 5 — Open the maintenance PR

The output of every full scan is a **draft PR proposal**. The agent does not open the PR autonomously unless the harness explicitly authorizes it (see *Output contract*).

Proposed PR shape:

- **Branch:** `chore/docs-maintenance-{YYYY-MM-DD}`
- **Title:** `chore(docs): maintenance scan {YYYY-MM-DD}`
- **Body:** the report from Phases 2–4, in the *Output contract* shape below
- **Status:** draft
- **Labels:** `docs`, `maintenance`

If the harness can open PRs (Claude Code with `gh` available, Agent SDK with a GitHub tool, etc.), offer to open it after the user reviews the body. If the harness cannot, output the body and let the user open the PR.

---

## Maintenance PR body — recommended shape

Output the report as markdown regardless of harness; that's what the PR body will render.

```markdown
## Maintenance scan — YYYY-MM-DD

### Summary

- Pages scanned: NNN
- Broken internal links: NN
- Broken external links: NN
- Schema/validation issues: NN
- a11y warnings: NN
- Stale (refresh): NN
- Stale (archive candidate): NN

### Broken links (internal)

| Page | Link | Reason |
|------|------|--------|
| ...  | ...  | ...    |

### Broken links (external — from lychee)

| Page | URL | Status |
|------|-----|--------|
| ...  | ... | ...    |

### Schema / validation

(verbatim CLI output, fenced)

### a11y warnings

(verbatim CLI output, fenced)

### Stale — refresh

| Page | Last edit | Last touched | Reason |
|------|-----------|--------------|--------|

### Stale — archive candidate

| Page | Last edit | Linked resources | Notes |
|------|-----------|------------------|-------|

### Proposed diffs

(one fenced diff block per file, only changed lines)
```

**Do not** edit prose in stale pages as part of this PR. Surface the page; let `write` (or a human) author the replacement. The maintenance PR's job is to flag, not to ghost-write.

---

## Output contract

Return exactly these sections (omit any that don't apply, but keep the order):

1. **Scan summary** — counts by category, anchored to today's date.
2. **Findings by category** — broken links (internal + external), validation, a11y, staleness, agent-readiness (if `mint score` was run).
3. **Proposed diffs** — only changed lines, per file. Reference the schema for any `docs.json` change.
4. **Draft PR plan** — branch name, title, body, labels, draft status. Offer to open it via `gh pr create --draft` if the harness allows.
5. **Open questions** — anything that requires user input (staleness threshold, archive vs. delete, redirect targets, missing frontmatter conventions).
6. **Handoff notes** — what should go to `design` (structural drift), `write` (prose refresh), or a human (deployment, brand, archive policy).

A good output is one a maintainer could apply with a single click on a draft PR plus a `mint validate` run — no archaeology required.

---

## Guardrails

- **No silent rewrites.** Surface, propose, hand off.
- **No invented frontmatter fields.** If the site has no `Last touched` (or `archived`) convention, propose adding one as a separate, single-purpose PR — don't sneak it into a maintenance PR.
- **No invented CLI flags.** When in doubt, `mint <subcommand> --help` or https://mintlify.com/docs/cli/commands.
- **No improvised staleness thresholds.** Ask the user; suggest a default of 9 months only when asked.
- **No staleness flags from intuition alone.** At least two checkable signals.
- **Never use `mint.json`.** Deprecated. If the repo still has one, surface a migration step (`mint update`, or manual rename + schema check) before doing other work.
- **`mint openapi-check` is deprecated** — use `mint validate`.
- **No deployment, DNS, billing, or auth changes.** Surface and hand off.
- **Respect existing `redirects`, archive, and frontmatter conventions.** Match the site.
- **Patterns are menu items, never defaults.** A maintenance scan does not implicitly suggest a new IA pattern; if structural drift is severe, hand off to `design`.
- **No prose edits in maintenance PRs.** Hand off to `write`.

If a finding doesn't fit any of the above categories cleanly, raise it as an open question rather than guessing where it belongs.

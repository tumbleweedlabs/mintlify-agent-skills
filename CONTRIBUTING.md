# Contributing

> **This is a stub.** It will be filled in alongside the v1.0.0 release. The rules below are the minimum required to keep the suite coherent in the meantime.

## The site-type-agnostic rule

The single hard rule of this suite: **`SKILL.md` files must remain site-type-agnostic.** Site-type variation belongs in [`patterns/`](./patterns), never in a skill.

If your PR adds a sentence to `design/SKILL.md` (or any other `SKILL.md`) that begins with "for a learning site," "for an API reference," or "for a resource library," it will be sent back. The same content can almost always live as a pattern instead.

## Reporting issues

Open an issue at https://github.com/TumbleweedLabs/mintlify-agent-skills/issues. Include:

- Which skill (router / design / write / create / maintain), or which pattern.
- The user request or scenario that exposed the problem.
- The actual vs. expected behavior.
- The agent harness (Claude Code, Cursor, etc.) and skill version, if relevant.

## Adding a pattern

See [ARCHITECTURE.md → Adding a pattern](./ARCHITECTURE.md#adding-a-pattern) and the contract in [`patterns/README.md`](./patterns/README.md).

## Vendoring policy

See [ARCHITECTURE.md → Vendoring policy](./ARCHITECTURE.md#vendoring-policy). Re-vendor on upstream change; never silently drift.

## Out of scope (for now)

Two items have been considered and deferred:

- **Wrapping the Mintlify REST API** in a sixth skill. Upstream has a 43-line stub at `skills/mintlify-api/SKILL.md` in `github.com/mintlify/docs`; we reference it but don't wrap it for v1.0.0.
- **Suite CI** — markdown lint, frontmatter schema validation, internal-link check. Will be set up after the four companion skills and initial patterns ship, when the right linter set is clearer.

## More to come

A full contributor guide (PR conventions, how to test changes locally with the Mintlify CLI, how to run the suite's lint/validate workflow, governance) ships with v1.0.0.

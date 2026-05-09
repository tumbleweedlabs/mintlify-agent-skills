# mintlify-agent-skills

A portable, MIT-licensed suite of agent skills for **designing, creating, writing for, and maintaining a [Mintlify](https://mintlify.com) documentation site** — usable in any agent harness that supports `SKILL.md` (Claude Code, Claude Agent SDK, Cursor, GitHub Copilot, OpenAI Codex, Windsurf).

The suite covers the full lifecycle of a Mintlify site without baking in any one site type. It works equally well for an API reference, SaaS product docs, a learning site, an internal wiki, or a curated resource library. Site-type variation is handled by opt-in templates in [`patterns/`](./patterns), surfaced as menu items the user picks — never as defaults.

## Who this is for

- Teams using an AI coding agent to administer a Mintlify site.
- Agent authors who want a structured, rigorously bounded skill bundle they can compose into a larger workflow.
- Anyone who has tried to "let the model just do it" and watched it invent components, overwrite a working `docs.json`, or silently drift a site's voice.

## The five skills

| Skill | Owns | Size |
|---|---|---:|
| [`router`](./router/SKILL.md)     | Detects intent, loads the right sibling(s), restates suite-wide guardrails, brokers patterns. Always loaded. | ~12KB |
| [`design`](./design/SKILL.md)     | Information architecture, navigation, landing/hub pages, component selection. | ~25KB |
| [`write`](./write/SKILL.md)       | Voice, frontmatter, file conventions, code-example standards. Vendors upstream Mintlify writing standards. | ~24KB |
| [`create`](./create/SKILL.md)     | Bootstrapping a new site: `mint new`, baseline `docs.json`, CI starter, redirects. | ~19KB |
| [`maintain`](./maintain/SKILL.md) | Ongoing health: `mint broken-links` / `validate` / `a11y` / `update`, lychee CI, staleness PRs. | ~17KB |

A sixth, opt-in directory — [`patterns/`](./patterns) — holds six site-type templates (API docs, SaaS product docs, learning site, resource library, internal wiki, multi-product). Each is ~5–6KB. Patterns are *menu items*, never defaults.

## Loading model

The router is small (~12KB) and **always loaded** when the suite is active. Companion skills load on demand based on the user's intent. A typical session loads the router plus one or two companions.

| Intent                                              | Loads                       | Active context |
|-----------------------------------------------------|-----------------------------|---------------:|
| "Restructure my nav" / "pick a component"           | router + `design`           | ~38KB          |
| "Write or edit prose"                               | router + `write`            | ~36KB          |
| "Add a card linking to this URL"                    | router + `write`            | ~36KB          |
| "Link rot / staleness scan"                         | router + `maintain`         | ~29KB          |
| "Stand up a fresh repo"                             | router + `create` + `design`| ~57KB          |

When the router surfaces a pattern, that adds ~5–6KB on top.

See [`ARCHITECTURE.md`](./ARCHITECTURE.md) for the design philosophy and [`CONTRIBUTING.md`](./CONTRIBUTING.md) for the rules when extending the suite.

## Installation

### Claude Code

Clone (or vendor) this repo into your project's skills directory:

```bash
mkdir -p .claude/skills
git clone https://github.com/TumbleweedLabs/mintlify-agent-skills.git .claude/skills/mintlify-agent-skills
```

Claude Code discovers `SKILL.md` files automatically.

### Claude Agent SDK

Add the directory to your skills resolver (or pass `router/SKILL.md` as a system-prompt fragment). Companion skills load on demand by directory name; the router does the routing.

### Cursor / GitHub Copilot / OpenAI Codex / Windsurf

Each supports custom system-prompt or rule files. The simplest install is to concatenate `router/SKILL.md` into your project's rules file (e.g. `.cursor/rules`, `.github/copilot-instructions.md`) and reference companions by file path; the agent will load them when the router signals.

### Optional: connect the Mintlify MCP

The Mintlify [Model Context Protocol](https://mintlify.com/docs/ai/mintlify-mcp) server gives the agent direct write access to your docs project — `read`, `search`, `edit_page`, `update_config`, `save` (which opens a PR), and a few more. The suite is written to use it when present and fall back to filesystem operations when it isn't.

```bash
# Claude Code (CLI)
claude mcp add --transport http --scope user mintlify https://mcp.mintlify.com
```

```jsonc
// Cursor / generic mcp.json (Claude Agent SDK + most others)
{
  "mcpServers": {
    "mintlify": { "url": "https://mcp.mintlify.com" }
  }
}
```

OAuth login completes on first call. The MCP edits your docs — treat the install like granting commit access; review every PR it opens.

## First 60 seconds with a fresh repo

1. Clone this suite into your project (see *Installation*).
2. Open your editor and ask the agent: *"What kind of Mintlify site is this, and what's the most useful thing I could do in the next ten minutes?"*
3. The router loads, inspects `docs.json` and a few representative pages, and routes to the right companion (usually `design` or `maintain` for an existing site, `create` for a fresh one).
4. The agent returns a **patch-ready proposal**: design rationale, `docs.json` diff, MDX skeletons, open questions, handoff notes. Apply with one PR and a `mint validate` run.

## Status

**v1.0.0** — initial release. Versioning is [semver](https://semver.org). See [`CONTRIBUTING.md → Versioning`](./CONTRIBUTING.md#versioning) for the per-skill version policy.

## License

[MIT](./LICENSE) © TumbleweedLabs.

The `write` skill vendors writing-standards content from the upstream [`mintlify/docs` skill](https://github.com/mintlify/docs/blob/main/skill.md) with credit and a link back; see [`CONTRIBUTING.md → Vendoring policy`](./CONTRIBUTING.md#vendoring-policy). The `design` skill is descended from [`TumbleweedLabs/mintlify-design-skill`](https://github.com/TumbleweedLabs/mintlify-design-skill), which is archived and superseded by this suite.

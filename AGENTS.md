# AGENTS.md

Guide for coding agents working in repositories managed by
[quiver-cli](https://github.com/nbialk/quiver-cli) V2.

## Agent Setup

Agent configuration lives in `.agents/`, the committed source of truth:

- `.agents/skills/` - agent skills (SKILL.md directories).
- `.agents/commands/` - slash commands.
- `.agents/plugins/` - provider-specific local plugins.
- `.agents/config.json` - MCP servers, plugins and provider overlays.
- `quiver.lock` - V2 per-entry source provenance, accepted local content digests
  and MCP tool snapshots.

Provider files (`.claude/`, `.opencode/`, `.codex/`, `.mcp.json`,
`opencode.json`) are generated from `.agents/` and gitignored. Do not edit
them directly.

## Sources

The [catalog](https://github.com/nbialk/quiver-catalog) is for discovery.
Each installed entry records its own direct source in `quiver.lock`; updates
follow that source rather than a vendored copy in the catalog. External skills
are listed as live GitHub pointers in `catalog.json`. Catalog-owned skills are
discovered from `skills/`, including the explicitly custom `posthog-custom`.

Branch and tag references track their upstream sources. Resolved commits and
source digests belong in each consuming repository's lockfile, not the catalog
index. Do not infer MCP or plugin dependencies from skill instructions.

## Workflow

- `quiver-cli add <id>` - install a selected catalog entry.
- `quiver-cli add github:owner/repo/path#main` - install a skill directly from
  its source directory containing `SKILL.md`.
- `quiver-cli remove <id>` - remove an installed entry.
- `quiver-cli update [id]` - update all entries or one entry from their recorded
  sources; review incoming changes before applying them.
- `quiver-cli sync` - regenerate provider configs from `.agents/`; this does not
  accept content changes or update source provenance.
- `quiver-cli check` - report local content, provider shim and MCP tool drift
  without accepting new baselines.
- `quiver-cli check <id> --accept` - explicitly accept reviewed local changes or
  an observed MCP tool snapshot for one installed entry.
- `quiver-cli check --all --accept` - explicitly accept all reviewed entries.
  Acceptance updates local baselines, not the recorded source baselines.
- Commit `.agents/` and `quiver.lock`; never commit generated provider files,
  `.env`, `.env.local` or actual credentials. Keep configuration placeholders
  such as `${NEON_API_KEY}` intact.

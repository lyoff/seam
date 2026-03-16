# SEAM Integrations

The agent instructions live in a single file: [`claude-code/seam/SKILL.md`](claude-code/seam/SKILL.md).

This file is the **source of truth for all editors**:

- **Claude Code** — installed as a skill at `.claude/skills/seam/SKILL.md`, invoked with `/seam`
- **Cursor** — content is embedded between `<!-- SEAM:START/END -->` markers in `.cursorrules`
- **Windsurf** — same, in `.windsurfrules`
- **Copilot** — same, in `.github/copilot-instructions.md`

All editors update from the same URL. See the [Update section](../README.md#update) in the README.

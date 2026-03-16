# CLAUDE.md — SEAM Protocol Repository

This is the SEAM protocol definition repository. It contains no application code — only the protocol spec, documentation, agent prompts, and accumulated field feedback.

## What this repo is

- **SEAM.md** is the protocol spec. It is the primary artifact. Changes here affect every project that adopts SEAM.
- **MANIFESTO.md** is the public-facing essay. It should evolve with real evidence, not theory.
- **PROMPTS.md** contains reference agent prompts. They should stay practical and battle-tested.
- **example.seam.md** is the worked example. Keep it realistic.
- **feedback/** contains structured field reports from real projects using SEAM.
- **integrations/** contains agent-specific packages (Claude Code skills, etc.).

## Rules for evolving the protocol

- SEAM.md changes must be backward-compatible within the same minor version. Breaking changes bump the major version.
- Do not add fields or labels speculatively. Every addition must be motivated by real-world friction documented in feedback/.
- Keep the protocol minimal. The urge to add features is the enemy. If a proposed change doesn't solve a problem that appeared in at least two independent feedback reports, it probably shouldn't exist.
- The node schema is the most sensitive part. Changes there cascade to every .seam.md file in the wild.

## Feedback structure

Field reports from real projects live in `feedback/`. Each file is one report from one project.

**Filename format:** `{date}-{project-slug}.md` (e.g., `2026-03-20-jazva8.md`)

**Template:**

```markdown
# SEAM Field Report — {project name}

date: {YYYY-MM-DD}
project: {name or slug}
codebase: {language, rough size, team size}
seam-version: 0.1.0
agent: {Claude Code / Cursor / etc.}

## Bootstrap

- How many nodes generated:
- Time to bootstrap:
- Time to review session:
- Nodes promoted to owned:
- Nodes deleted (not load-bearing):
- Nodes left as draft:

## What worked

- {bullet points}

## What didn't work

- {bullet points}

## Schema friction

- {Any fields that were confusing, missing, or unnecessary}
- {Any status labels that didn't fit}
- {Any node ID naming issues}

## Prompt friction

- {Which prompts worked well}
- {Which prompts needed modification and how}

## Incidents (if any)

- {Did SEAM help during a production issue? How?}
- {Did SEAM fail to help? Why?}

## Suggestions

- {Proposed changes to the protocol, with rationale}
```

## How to generate a feedback report from another project

Add this to the SEAM-enabled project's agent instructions or run it as a prompt:

```
Read SEAM.md and {project}.seam.md in this repo. Generate a SEAM field report
covering: bootstrap experience, what worked, what didn't, schema friction,
prompt friction, and suggestions. Use the template from the SEAM protocol repo.
Be honest — negative feedback is more valuable than praise.
Output as markdown.
```

Then save the output to this repo under `feedback/{date}-{project-slug}.md`.

## Working on this repo

- When editing SEAM.md, consider the impact on existing .seam.md files in the wild.
- When editing MANIFESTO.md, maintain the narrative voice — it's a personal essay, not docs.
- When editing PROMPTS.md, test changes against a real codebase before committing.
- Reference feedback reports when proposing protocol changes.
- Do not commit changes without Levon's explicit approval.

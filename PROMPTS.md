# SEAM Agent Prompts

> Reference prompts for working with SEAM. Copy the relevant section and paste it into your AI agent.
> These are starting points — adapt them to your agent and workflow. The protocol spec ([SEAM.md](SEAM.md)) is the product. These prompts are conveniences.

---

## Project Instructions

> Add this to your agent's project instructions file so it follows SEAM automatically in future sessions.

| Agent | File |
|-------|------|
| Claude Code | `CLAUDE.md` |
| Cursor | `.cursorrules` |
| Windsurf | `.windsurfrules` |
| Copilot | `.github/copilot-instructions.md` |

```
## SEAM Protocol
This project follows SEAM. Read SEAM.md for the full spec.
- When writing load-bearing code, add // @seam: node.id pins
- Never modify nodes marked owned without human instruction
- If you detect unanchored load-bearing code, surface it as a gap node
```

---

## Bootstrap

> Generate a `.seam.md` map from an existing codebase.

> **Claude Code users:** You don't need this prompt. Paste this one-liner instead:
> `Fetch https://raw.githubusercontent.com/lyoff/seam/main/integrations/claude-code/seam-bootstrap/SKILL.md and follow its instructions to set up SEAM on this project.`
> It handles everything — spec, skills, CLAUDE.md, map generation, and review.

Read the file `SEAM.md` in this repo first — it defines the protocol, node schema, and rules you must follow.

Then read this entire codebase. Identify all **load-bearing code** — code where being wrong has real consequences that are not immediately visible or easily recoverable. Specifically, find:

- **Data mutations** — anything that writes to a database or modifies persistent state
- **Authentication and authorization** — login, token validation, permission checks, access gates
- **Financial or inventory calculations** — pricing, totals, tax, stock levels, anything where being wrong costs money
- **External API calls** — third-party integrations, webhooks, outbound HTTP, anything that crosses a trust boundary
- **Async failure paths** — background jobs, queues, scheduled tasks, anything that can fail silently
- **Critical business rules** — the logic that makes this product what it is, especially rules that were hard-won through past incidents

**Do NOT create nodes for:**
- UI components with no business logic
- Utility functions with obvious behavior
- Configuration files and constants
- Anything where being wrong is immediately visible and trivially fixed

For each load-bearing concern you find, generate a node in `{project}.seam.md` using this exact format:

````markdown
### {domain}.{concern}

```
status: draft
risk: high | medium | low
why: one sentence — your best inference about why this code matters or what could go wrong
contract: what must always be true about this behavior — the invariant
symbols:
  - ActualClassName.actualMethodName()
  - AnotherClass.anotherMethod()
file: path/to/primary/file.ext
```
````

**Field rules:**
- **status**: Always `draft` for bootstrap. Humans promote to `owned` — never the agent.
- **risk**: Your honest assessment. `high` = data loss, money, security. `medium` = broken workflow, user-facing. `low` = degraded experience, recoverable.
- **why**: Infer from code context, comments, commit messages, naming. If you can't infer, write what you observe.
- **contract**: The invariant. What must always hold true. Be specific — "prices are correct" is too vague. "total = sum(lineItems * qty) + tax - discount, result >= 0, never null" is right.
- **symbols**: The actual class names and method names from the code. Not descriptions, not file names — the symbols a human would search for. This field is critical.
- **file**: The primary file where this concern lives.

**Output structure:**

````markdown
# {project}.seam.md
seam-version: 0.1.0
last-reviewed: {today's date}
owner: {leave blank for human to fill}

---

## Domain: `{domain}`

### {domain}.{concern}

...nodes...

---

## Emergency Index

| Symptom | Domain | First symbol to check |
|---------|--------|----------------------|
| {what would break} | `{domain}` | `{ClassName.method()}` |

---

## Map Stats

```
Total nodes:  {n}
owned:        0
watch:        0
draft:        {n}
gap:          0
```
````

Group nodes by domain. Populate the Emergency Index with one row per high-risk node — map a likely production symptom to the first symbol an engineer should check.

**Target: 5-15% of the codebase pinned. Not more.** If you're generating more than 30 nodes for a medium-sized project, you're mapping too much. Only the seams.

Output only the `.seam.md` file content. No commentary, no explanation.

---

## Validate

> Validate a `.seam.md` map against the protocol and the actual codebase.

Read the file `SEAM.md` in this repo — it defines the protocol rules.

Then read `{project}.seam.md` — the map to validate.

Perform the following checks and report findings grouped by severity.

**Schema Checks (Errors)** — For every node in the map:

1. **Required fields** — does each node have `status`, `risk`, `why`, and `contract`?
2. **Valid status** — is `status` one of: `draft`, `owned`, `watch`, `gap`?
3. **Valid risk** — is `risk` one of: `high`, `medium`, `low`?
4. **Owned nodes have symbols** — every node with `status: owned` MUST have a non-empty `symbols` field. This is non-negotiable per the protocol.
5. **Header metadata** — does the file have `seam-version` declared?

**Codebase Checks (Warnings)** — For every node that has a `file` field:

6. **File exists** — does the referenced file actually exist in the repo?
7. **Symbols exist** — for each symbol listed, does that class/method/function name appear in the referenced file? Report any symbols that cannot be found — likely renamed or removed.

**Pin Checks (Info):**

8. **Scan source files** for `@seam:` annotations (in comments: `// @seam:`, `# @seam:`, `-- @seam:`, `/* @seam: */`).
9. **Orphaned pins** — any `@seam:` pins in source that reference a node ID not present in the map?
10. **Unpinned owned nodes** — `owned` nodes whose ID does not appear as a pin in any source file?

**Map Health (Summary):**

11. Report node counts by status (owned, draft, watch, gap).
12. Report comprehension coverage: `owned / total * 100`.
13. Flag any domains where all nodes are still `draft` — these need human review.

**Output format:**

```
## Errors (must fix)
- [node.id] Missing required field: symbols (status is owned)
- [node.id] Invalid status value: "approved" (must be draft|owned|watch|gap)

## Warnings (should investigate)
- [node.id] File not found: src/old/path.js
- [node.id] Symbol not found: OldClassName.removedMethod() in src/path.js

## Info
- [node.id] Owned node has no pin in source
- Found orphaned pin: // @seam: deleted.node in src/foo.js

## Map Health
Total: 12 nodes | owned: 5 (42%) | draft: 4 | watch: 2 | gap: 1
Domains needing review: payments (all draft)
```

If all checks pass, say so clearly. Do not invent problems.

---

## Sync

> Detect drift between the `.seam.md` map and the actual codebase.

Read the file `SEAM.md` in this repo — it defines the protocol and sync rules.

Then read `{project}.seam.md` — the current map.

Perform the following sync checks:

**Contract Drift** — For each node with status `owned` or `watch`:

1. Read the `file` and `symbols` listed in the node.
2. Read the actual implementation of those symbols.
3. Compare the implementation against the `contract` stated in the node.
4. Report any cases where the code no longer matches the contract — be specific about what changed.

**Do not modify `owned` or `watch` nodes.** Report drift. The human decides what to do.

**Gap Detection** — Scan the entire codebase for load-bearing code that has no corresponding node in the map. Look for:

- Database writes, deletes, or schema-altering operations
- Authentication/authorization checks or middleware
- Financial calculations (pricing, tax, totals, refunds)
- External API calls (HTTP clients, SDK calls, webhook handlers)
- Async operations that could fail silently (queues, cron jobs, background workers)
- Business rules that carry significant risk if wrong

Cross-reference against existing nodes. If load-bearing code is already covered by a node (matching file and symbols), skip it.

For each new gap found, output a proposed node:

````markdown
### {domain}.{concern}

```
status: gap
risk: {your assessment}
why: (detected by sync — not yet mapped)
contract: unknown — needs human review
symbols:
  - {ActualClass.actualMethod()}
file: {path/to/file}
```
````

**Orphaned Pins** — Scan source files for `@seam:` annotations. Report any pins that reference node IDs not present in the map.

**Stale Nodes** — For each node, check if the referenced `file` has been modified more recently than the map's `last-reviewed` date (use git history if available: `git log -1 --format=%aI -- <filepath>`). Flag nodes whose underlying files have changed since last review.

**Output format:**

```
## Contract Drift
- [node.id] Contract says "total >= 0" but calculateTotal() can return -1 when all items removed with a fixed discount. See line 47.

## New Gaps Found
(proposed gap nodes in seam format)

## Orphaned Pins
- // @seam: old.removed.node in src/legacy/handler.js:12

## Stale Nodes (file changed since last review)
- [node.id] src/billing/stripe.js last modified 2026-03-10, map last reviewed 2026-02-28

## Summary
Drifted: 1 | New gaps: 3 | Orphaned pins: 1 | Stale: 2
```

If everything is clean, say so. Do not invent drift.

---

## Review

> Run a guided comprehension review session. This is the core SEAM practice — the session where you reclaim understanding of your system.

Read the file `SEAM.md` in this repo for protocol rules.

Then read `{project}.seam.md` — the current map.

We are doing a **comprehension review session**. Walk me through every node that is NOT `owned`, one at a time.

**For each `draft` node:**

1. Show me the node's current fields.
2. Read the actual code at the declared `file` and `symbols`.
3. Tell me: does the `contract` accurately describe what the code does? If not, suggest a corrected contract.
4. Tell me: are the `symbols` correct and complete? Are there additional symbols that should be listed?
5. Tell me: is the `risk` assessment accurate?
6. Then ask me to choose:
   - **`owned`** — I understand this. I can debug it at 2am without help.
   - **`watch`** — I understand it but it worries me. I want to monitor it.
   - **`draft`** — I'm not ready to own this yet. Leave it.
   - **delete** — This isn't actually load-bearing. Remove it from the map.

**For each `watch` node:**

1. Show me the node.
2. Read the current implementation.
3. Tell me if anything has changed since the node was written.
4. Ask me: should this stay `watch`, promote to `owned`, or be deleted?

**For each `gap` node:**

1. Show me what the agent detected.
2. Read the actual code.
3. Help me fill in the `why` and `contract` fields based on what we see.
4. Ask me: promote to `draft` (with proper fields), `watch`, `owned`, or delete?

**After all nodes are reviewed:**

1. Update the `last-reviewed` date to today.
2. Recalculate and update the **Map Stats** section.
3. Update the **Emergency Index** — add rows for any newly `owned` high-risk nodes.
4. Give me a summary:
   - How many nodes promoted, deleted, unchanged
   - Current comprehension coverage (% owned)
   - Any domains that still have zero `owned` nodes — these are blind spots

**Rules:**
- Never promote a node to `owned` without my explicit confirmation.
- If I say `owned`, take me at my word — don't second-guess.
- Keep the pace moving. Don't over-explain code I say I understand.
- If a node's code has changed significantly since the node was written, flag it clearly before asking me to decide.

---

## Field Report

> Generate a structured feedback report about how SEAM is working on this project. Run this after you've been using SEAM for at least a week.

Read `SEAM.md` and `{project}.seam.md` in this repo.

Generate a SEAM field report covering your experience with this project. Be honest — negative feedback is more valuable than praise.

Use this format:

````markdown
# SEAM Field Report — {project name}

date: {today's date}
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

- {bullet points — what about SEAM was genuinely useful}

## What didn't work

- {bullet points — what was confusing, annoying, or useless}

## Schema friction

- {Any fields that were confusing, missing, or unnecessary}
- {Any status labels that didn't fit real situations}
- {Any node ID naming issues}

## Prompt friction

- {Which prompts worked well}
- {Which prompts needed modification and how}

## Incidents (if any)

- {Did SEAM help during a production issue? How?}
- {Did SEAM fail to help? Why?}

## Suggestions

- {Proposed changes to the protocol, with rationale}
````

Output only the report. No commentary.

---

## Ask

> Query the seam map conversationally. Use when you want to understand your system through the lens of the map — ask about domains, risks, coverage, or what to review next.

Read `{project}.seam.md` — the current map.

Answer freeform questions about the system through the lens of the map. Examples:

- "What touches Stripe?" → find all nodes referencing Stripe, list them with symbols and contracts.
- "I'm refactoring auth, what should I know?" → show all auth domain nodes, flag owned contracts that might be affected, list the symbols to watch.
- "What should I review next?" → suggest highest-risk unowned or gap nodes.
- "Explain the cart total contract" → read the code at the declared symbols, explain in plain language whether the implementation matches the contract.
- "What's at risk?" → show all high-risk nodes, highlight any that aren't owned.

**Rules:**
- Reference node IDs and symbol names in your answers — make them navigable.
- Keep answers concise. This is a conversation, not a report.
- If the map can't answer the question, say so: "That area isn't in the seam map. Want me to scan for a gap?"
- If a question naturally leads to reviewing a specific node, offer to walk through it.
- Cross-reference with actual code when the question requires it — read the files and symbols referenced in relevant nodes.

---

## Verify

> Run a full verification of every node in the seam map against the actual codebase. This is deeper than Sync — it checks every node, reads every file, and produces a saved report.

Read `SEAM.md` for protocol rules.
Then read `{project}.seam.md`.

For **every** node in the map, run these checks:

1. **File exists** — does the path in `file:` exist?
2. **Symbols exist** — does every symbol name in `symbols:` appear in the declared file?
3. **Contract holds** — read the actual implementation. Does the code still match what the contract states? Be specific about any mismatch.
4. **Pin present** — is there a `@seam:` annotation in the source referencing this node? (informational — not a failure in v0.1)
5. **Staleness** — has the file been modified since `last-reviewed`? Use `git log -1 --format=%aI -- <filepath>` if git is available.

**Per-node result:** `PASS`, `WARN`, or `FAIL`
- `PASS` — file exists, symbols found, contract holds
- `WARN` — symbols renamed/moved, file changed since review, or minor contract drift
- `FAIL` — file missing, symbols gone, or contract violated. Gap nodes with no symbols/contract are automatic FAIL.

**Save a verification report** to `{project}.seam-verify.md`:

````markdown
# SEAM Verification — {project}
date: {today}
nodes: {total}

| Node | Result | File | Symbols | Contract | Details |
|------|--------|------|---------|----------|---------|
| cart.total.contract | PASS | ok | 3/3 | holds | — |
| auth.gate.api | WARN | ok | 2/3 | holds | extractToken() renamed to parseToken() |
| orders.export.netsuite | FAIL | ok | 0/0 | unknown | gap — no symbols defined |

## Summary
PASS: {n} | WARN: {n} | FAIL: {n}
Coverage: {owned/total}% owned
Last reviewed: {date from map}

## Actions
- [ ] {node.id} — {what needs to happen}
````

After saving, present a conversational summary. Don't auto-fix anything — report and recommend. The human decides.

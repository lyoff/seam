# SEAM

### Structured Engineering Awareness Method

> *A protocol for engineers who ship code they didn't fully write — and still need to own what runs.*

---

```
Before LLMs:   Human writes C       →  Compiler  →  Machine code  (trusted, unread)
With LLMs:     Human writes .seam   →  AI Agent  →  Source code   (trusted, unread)
```

Source code is becoming the new machine code. You don't need to read every line.
You need to own the **seams** — the 5-15% of your codebase where being wrong has real consequences.

Here's what a seam looks like:

```
## cart.total.contract

status: owned
risk: high
why: coupon stacking caused negative totals in v2.1 — a $12k refund incident
contract: total = sum(lineItems * qty) + tax - discount, result always >= 0, never null
symbols:
  - CartService.calculateTotal()
  - CartService.applyDiscount()
  - OrderValidator.assertTotalPositive()
file: src/lib/cart/CartService.js
```

When production breaks, you open the map, find the domain, read the symbols, jump straight to the right method. No grepping. No asking the AI.

**[Read the Manifesto](MANIFESTO.md)** for the full story. **[Read the Protocol](SEAM.md)** for the spec.

---

## Get Started

### Claude Code

Two steps:

**Step 1** — run in your terminal:
```bash
mkdir -p .claude/skills/seam && curl -sf https://raw.githubusercontent.com/lyoff/seam/main/integrations/claude-code/seam/SKILL.md -o .claude/skills/seam/SKILL.md
```

**Step 2** — restart Claude Code, then:
```
/seam
```

The skill bootstraps everything — protocol spec, CLAUDE.md config, codebase scan, map generation, and a guided review session.

From then on, `/seam` does everything:

```
/seam                        → status overview
/seam review auth domain     → walk through auth nodes
/seam sync                   → check for drift
/seam does this PR touch any seams?
/seam what's at risk?
/seam add a node for the new refund endpoint
/seam update                 → update SEAM to latest version
```

### Cursor / Windsurf / Copilot / Other Agents

**Step 1** — run in your terminal:

```bash
curl -sf https://raw.githubusercontent.com/lyoff/seam/main/SEAM.md -o SEAM.md
```

**Step 2** — paste this into your agent:

```
I've added SEAM.md to this repo — it's a protocol for mapping load-bearing
code. Read it, then:

1. Add a SEAM section to this project's instructions file so you follow
   the protocol in future sessions:

   For Cursor: append to .cursorrules
   For Windsurf: append to .windsurfrules
   For Copilot: append to .github/copilot-instructions.md

   Include:
   - This project follows SEAM. Read SEAM.md for the full spec.
   - When writing load-bearing code, add // @seam: node.id pins
   - Never modify nodes marked owned without human instruction
   - If you detect unanchored load-bearing code, surface it as a gap node

2. Scan this codebase for load-bearing code (DB mutations, auth checks,
   financial math, external APIs, async failure paths, critical business rules).
3. Generate a {project}.seam.md file with one draft node per concern,
   following the schema in SEAM.md. Include an Emergency Index and Map Stats.
4. Walk me through each node so I can decide: owned, watch, draft, or delete.
```

For ongoing use, reference the prompts in **[PROMPTS.md](PROMPTS.md)** — sync, verify, review, PR checks, and more.

---

## Update

All editors update from the same source.

### Claude Code

```
/seam update
```

### Other agents

Re-download the protocol spec to get the latest version:

```bash
curl -sf https://raw.githubusercontent.com/lyoff/seam/main/SEAM.md -o SEAM.md
```

Your `.seam.md` map is untouched — it belongs to you.

---

## Files in This Repo

```
SEAM.md                          ← the protocol spec (this goes in your repo)
PROMPTS.md                       ← reference prompts (for manual use)
TEMPLATE.seam.md                 ← blank starter
MANIFESTO.md                     ← the story behind SEAM
REFERENCES.md                    ← full citations and research
example.seam.md                  ← worked example
feedback/                        ← field reports from real projects
integrations/claude-code/seam/   ← agent instructions (source of truth for all editors)
LICENSE                          ← MIT
```

---

## Status

SEAM is a **proposal** — v0.1.0.

It emerged from a real production incident and the conviction that *comprehension debt* needs a structured answer before the industry moves on without one.

If you try SEAM, [open an issue](https://github.com/lyoff/seam/issues) and tell us what worked and what didn't.

---

## License

MIT. Use it. Ship with it. Make it better.

---

*Created by Levon Shahbaghyan — born from a production incident at [Jazva](https://jazva.com).*

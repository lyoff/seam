# {project}.seam.md
seam-version: 0.1.0
last-reviewed: {YYYY-MM-DD}
owner: {your@email.com}

<!--
  This is your living map.

  Add nodes only for load-bearing code:
    - Database mutations
    - Auth / permission checks
    - Financial or inventory math
    - External API integrations
    - Async failure paths
    - Core business rules

  Target: 5-15% of your codebase pinned. Not more.

  Run the bootstrap prompt from PROMPTS.md to generate this file from an existing repo.
  Or tell your agent to add nodes as you build.

  Status labels:
    draft   — AI-generated, not yet human-reviewed
    owned   — you understand this, can navigate it, can debug it alone
    watch   — known risk, needs monitoring
    gap     — load-bearing code exists here but is not mapped yet
-->

---

## Domain: `{first-domain}`

### {domain}.{concern}

```
status: draft
risk: high | medium | low
why:
contract:
symbols:
  - ClassName.methodName()
file:
```

---

## Emergency Index

*Fill this in as you promote nodes to `owned`.*

| Symptom | Domain | First symbol to check |
|---------|--------|----------------------|
| | | |

---

## Map Stats

```
Total nodes:  0
owned:        0
watch:        0
draft:        0
gap:          0
```

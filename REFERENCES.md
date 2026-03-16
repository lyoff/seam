# SEAM References

Full citations for research, articles, and incidents referenced in the [Manifesto](MANIFESTO.md).

---

## Foundational

- **Peter Naur** — [Programming as Theory Building](https://pages.cs.wisc.edu/~remzi/Naur.pdf) (1985). The foundational insight: a program is the theory in the programmer's mind, not the source code. When the theory isn't externalized, it walks out the door with the person who holds it.

## Comprehension Debt

- **Margaret-Anne Storey** — [Cognitive Debt](https://margaretstorey.com/blog/2026/02/09/cognitive-debt/) (2026). Coined the academic framing: cognitive debt is a property of *people*, not code. It accumulates when teams ship faster than they can understand.
- **Addy Osmani** — [Comprehension Debt](https://addyosmani.com/blog/comprehension-debt/) (2025). Popularized the concept for the engineering mainstream. The surface correctness of AI-generated code creates false confidence while comprehension hollows out.
- **Simon Willison** — [On Cognitive Debt](https://simonwillison.net/2026/Feb/15/cognitive-debt/) (2026). The Django co-creator on getting lost in his own AI-assisted projects — losing confidence in architectural decisions about code he technically authored.

## Research

- **METR** — [AI Impact on Developer Productivity](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) (2025). Experienced OSS developers were 19% slower with AI tools in a rigorous RCT, while believing they were 20% faster.
- **MIT Sloan Management Review** — [The Hidden Costs of Coding with Generative AI](https://sloanreview.mit.edu/article/the-hidden-costs-of-coding-with-generative-ai/) (2025). Eightfold increase in duplicated code, twofold increase in churn. AI code is "borrowing at a much higher interest rate."
- **SonarSource** — [The Verification Gap](https://www.sonarsource.com/company/press-releases/sonar-data-reveals-critical-verification-gap-in-ai-coding/) (2026). 96% of developers don't fully trust AI code. Only 48% verify before committing. AI accounts for 42% of all committed code.
- **IEEE Spectrum** — [Keep Your Intuition Sharp While Using AI for Coding](https://spectrum.ieee.org/ai-for-coding-intuition) (2025). The shift from code generation to code interrogation. References Anthropic research showing AI assistance can interfere with human learning when it fills gaps too quickly.
- **Stack Overflow** — [2025 Outage and Incident Data](https://stackoverflow.blog/). Measurably higher outages industry-wide the year AI coding went mainstream. Security issues at 1.5-2x, I/O issues at 8x the rate of human-written code.

## Industry Incidents

- **Amazon March 2026** — [CNBC](https://www.cnbc.com/2025/03/13/amazon-internal-deep-dive-recent-outages.html), [The New Stack](https://thenewstack.io/amazon-internal-doc-warns-against-using-ai-generated-code/), [Belitsoft](https://belitsoft.com/outsourcing-software-development-services/amazon-outages-the-real-cost-of-ai-generated-code). Six-hour site outage, 120k lost orders, internal emergency meeting citing "Gen-AI assisted changes" as a contributing factor.
- **Microsoft Windows 11** — [Neowin](https://www.neowin.net/), [OSnews](https://www.osnews.com/). 30% AI-generated code, simultaneous layoffs, four months of broken core components.

## Practice

- **Martin Fowler** — [Fragments on AI and Software Development](https://martinfowler.com/fragments/2026-02-13.html) (2026). From the ThoughtWorks retreat: practices built for human-only development are breaking. Risk tiering and supervisory engineering are emerging as necessary disciplines.
- **Nicholas Zakas** — [From Coder to Orchestrator](https://humanwhocodes.com/blog/2026/01/coder-orchestrator-future-software-engineering/) (2026). The ESLint creator on the shift from writing code to guiding agents.
- **Jamon Holmgren** — [Night Shift](https://jamon.dev/night-shift) (2025). Pioneered the async agentic workflow — delegate to agents overnight, review in the morning.
- **MindList** — [mindlist.app](https://mindlist.app). The outliner surface that inspired the dual-artifact model.

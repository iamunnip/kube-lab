# 0003: Docs in docs/ with Diataxis and ADR, facts in knowledge/okf/

- **Status**: accepted
- **Date**: 2026-11-08

# Context

- The lab is for beginners. Docs must be clear, step by step, in plain English.
- Docs get messy when tutorials, explanations and facts are mixed on one page.
- Decisions get forgotten unless they are written down.
- Facts such as IPs and sizes should live in one place, in a form both people and tools can read.

# Decision

```
 docs/
   diataxis/    tutorials, how-to, explanation, reference   (for people)
   adr/         one record per decision                     (for people)
 knowledge/
   okf/         one fact page per thing, OKF v0.2 format    (for people and tools)
```

- **Diataxis** sorts the human docs into 4 kinds of page.
- **ADR** records every decision and its reason.
- **OKF** (Open Knowledge Format, v0.2) holds the facts: one small page per node, network, disk, tool and distribution, with a YAML header.
- Every page uses plain English and bullet points.

# Why

- Diataxis tells the reader where to look and the writer where to put a page.
- ADRs keep the "why" next to the code.
- OKF is plain markdown with a small header, so it reads well on GitHub and is easy for tools and AI agents to search.
- Keeping OKF in its own folder means the human docs stay free of headers.

# Options we looked at

| Option                    | Good                                     | Bad                                          |
|---------------------------|------------------------------------------|----------------------------------------------|
| Diataxis + ADR + OKF      | Clear split by purpose                   | Three ideas to learn                         |
| One big README            | Easy at first                            | Hard to follow once it grows                 |
| C4 architecture diagrams  | Good for large systems                   | Too heavy for a 3 node lab. Simple diagrams are used instead |
| A docs website generator  | Nicer look, search                       | Extra tools and setup                        |

# Consequences

- **Good**: every page has one job, and every fact has one home.
- **Bad**: a changed fact (such as an IP) must be updated in both the docs and the OKF page.
- **Follow up**: the checklist in [How these docs work](../diataxis/explanation/how-these-docs-work.md) lists what to update.

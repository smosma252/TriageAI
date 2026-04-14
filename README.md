# TriageAI

> Your autonomous code health platform — triaging bugs, catching performance regressions, and proposing enhancements 24/7, delivered as draft pull requests for your team to review.

---

## What is TriageAI?

TriageAI is an autonomous code health platform that monitors your codebase around the clock. It triages incoming bug tickets, detects performance regressions, and proposes enhancements — all delivered as draft pull requests for your team to review and merge.

No more junior tickets sitting in backlog. No more "we'll optimize that later." TriageAI keeps your codebase healthy while your engineers focus on building.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        SIGNAL LAYER                         │
│                                                             │
│   Jira/Linear  ──►  Bug Ticket                              │
│   Sentry/DD    ──►  Runtime Alert      ──►  Event Queue     │
│   GitHub Hook  ──►  New Commit/PR                           │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      ORCHESTRATION LAYER                    │
│                                                             │
│   Event Router  ──►  classifies signal type                 │
│   Task Queue    ──►  prioritizes + dispatches agent jobs    │
│   Rate Limiter  ──►  prevents repo flooding                 │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                        AGENT LAYER                          │
│                                                             │
│   ┌─────────────┐  ┌──────────────┐  ┌─────────────────┐  │
│   │  Bug Agent  │  │  Perf Agent  │  │ Enhance Agent   │  │
│   │             │  │              │  │                 │  │
│   │ reads ticket│  │ reads alert/ │  │ scans patterns  │  │
│   │ finds root  │  │ commit diff  │  │ ticket trends   │  │
│   │ cause       │  │ finds hotspot│  │ dead code etc   │  │
│   │ writes fix  │  │ optimizes    │  │ proposes refactor│ │
│   └─────────────┘  └──────────────┘  └─────────────────┘  │
│                                                             │
│              All agents share:                              │
│              - Codebase context (RAG / vector index)        │
│              - LLM reasoning (Claude)                       │
│              - Confidence scorer                            │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     CODEBASE CONTEXT LAYER                  │
│                                                             │
│   Git Clone  ──►  Chunker  ──►  Embeddings  ──►  VectorDB  │
│                                                             │
│   Re-indexed on every merged PR                             │
│   Scoped retrieval per agent query                          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                       OUTPUT LAYER                          │
│                                                             │
│   Branch Creator  ──►  creates isolated branch per job      │
│   Patch Writer    ──►  applies code changes                 │
│   PR Builder      ──►  opens draft PR with:                 │
│                        - What was found                     │
│                        - Why it's a problem                 │
│                        - What was changed                   │
│                        - Confidence score (0–100%)          │
│                        - Links back to original ticket      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     FEEDBACK LOOP LAYER                     │
│                                                             │
│   PR merged?         ──►  positive signal, re-train weight  │
│   PR closed/rejected ──►  negative signal, log reason       │
│   Developer edits PR ──►  diff captured for improvement     │
│                                                             │
│   Weekly Code Health Report  ──►  emailed to eng lead       │
└─────────────────────────────────────────────────────────────┘
```

---

## The Three Agents

### 🐛 Bug Agent

Triggered by an inbound ticket from Jira or Linear. The agent reads the ticket, pulls relevant context from the vector index, walks the call graph around the suspected area, identifies the root cause, and writes a targeted fix. Every PR it opens includes a plain-English explanation of what broke and why.

### ⚡ Performance Agent

Triggered two ways — a runtime alert from Sentry or DataDog, or a new commit landing on the main branch. For runtime alerts it traces the slow path back to the code. For commits it diffs the change and runs static analysis looking for regressions like N+1 queries, unindexed lookups, or blocking calls. The PR includes before/after complexity notes and the reasoning behind the change.

### ✨ Enhancement Agent

Runs on a schedule rather than a trigger. It scans the codebase for dead code, duplication, outdated patterns, and dependency drift. It also reads ticket history to spot recurring problem areas — if the same module keeps generating bugs, it flags it for a deeper refactor proposal. This agent operates at the lowest confidence threshold and always frames its PRs as suggestions rather than fixes.

---

## Trust & Safety Model

TriageAI is built around the principle that **developers always stay in control.**

- All PRs are opened as **drafts** — nothing is auto-merged, ever
- Every PR carries a **confidence score** so developers know how much to trust it
- Agents will **never touch** migration files, environment configs, or secrets
- A **dry-run mode** is available to preview what the agent would do without opening a PR
- The feedback loop learns from rejections — a closed PR with a comment makes the next one better

---

## Integrations

| Category | Supported |
|---|---|
| Ticket Systems | Jira, Linear *(Salesforce planned)* |
| Code Hosts | GitHub *(GitLab planned)* |
| Monitoring | Sentry, DataDog, New Relic |
| Notifications | Slack, Email |
| Languages | Python, TypeScript *(more planned)* |

---

## North Star Metrics

| Phase | Metric | Target |
|---|---|---|
| Bug | PR acceptance rate | > 40% |
| Performance | Measured perf improvement per merged PR | > 15% avg |
| Enhancements | Developer-initiated follow-on PRs from suggestions | > 25% |

---

## Roadmap

| Version | Scope |
|---|---|
| **v0.1** | Bug Agent + Jira + GitHub (MVP) |
| **v0.2** | Performance Agent + Sentry/DataDog integration |
| **v0.3** | Enhancement Agent + weekly health reports |
| **v1.0** | Multi-language, multi-repo, dashboard UI, team analytics |

---

## Contributing

TriageAI is currently in private beta. If you're interested in piloting it with your engineering team, reach out via [your contact link here].

---

*Built with Claude · Powered by your codebase*

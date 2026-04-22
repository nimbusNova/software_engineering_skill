# Software Engineering Skill

A Kimi CLI skill that guides AI agents through a rigorous, situation-aware software development workflow. Designed for developers who want their agents to **lead with context**, not just execute tasks.

## Why This Exists

Most AI "software engineering" skills jump straight to coding. They treat the agent like an eager junior dev who starts typing the moment they get an assignment. This skill is different.

Inspired by ["Stop Executing, Start Leading: How to Make Your AI Agent Situation-Aware"](https://nimbusnova.substack.com/p/stop-executing-start-leading-how), this skill teaches agents to:

- **Assess before acting** — Understand the real problem, constraints, and success metrics before writing a single line of code
- **Trade off, don't default** — Present options with context-aware tradeoffs rather than blindly picking the "best" technology
- **Validate, don't just ship** — Define "done" by user outcomes and measurable impact, not just merged PRs

## What's Inside

```
software-enginering/
├── SKILL.md                          # Main skill — 7-phase workflow
└── references/
    ├── planning-guide.md             # Story points, phase design, risk assessment
    ├── implementation-standards.md   # Observability, error handling, code quality
    ├── testing-strategies.md         # Unit, integration, and manual testing patterns
    └── git-standards.md              # Commit hygiene, branching, PR practices
```

### Workflow Overview

| Phase | Purpose | Human Gate? |
|-------|---------|-------------|
| 0 — Pre-Flight | Verify clarity on goals and definition of done | ✋ |
| 1 — Requirements | Deep-dive conversation to uncover functional & non-functional needs | ✋ |
| 2 — Codebase Archaeology | Map architecture, patterns, and integration points | ✋ |
| 3 — Planning | Break work into small, estimated phases with risk register | ✋ |
| 4 — Implementation | Iterate phase-by-phase with tests and frequent commits | ✋ |
| 5 — Testing | Comprehensive verification (unit, integration, manual) | ✋ |
| 6 — Self-Review | Critical examination of correctness, quality, and performance | — |
| 7 — Delivery | Clean commit history, documentation, PR, and post-delivery monitoring | — |

## Setup

Clone this repo into your Kimi CLI skills directory:

```bash
git clone git@github.com:nimbusNova/software_engineering_skill.git ~/.config/agents/skills/software-engineering
```

Kimi CLI will automatically discover the `SKILL.md` on the next run. No additional configuration needed.

## Core Philosophy

- **Human-in-the-loop** — Stop for review at the end of each phase
- **Quality first** — No cutting corners on testing, observability, or error handling
- **Incremental delivery** — Small, reviewable commits pushed frequently
- **Conversation over forms** — Requirements gathering is a dialogue, not a questionnaire
- **Situation over speed** — Contextual awareness beats implementation velocity

---

*Built for agents that think before they code.*

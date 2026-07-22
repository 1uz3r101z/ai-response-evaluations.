# AI-Assisted Quantitative Trading Framework

> **Status: Phase 0 — Requirements Gathering (in progress, awaiting interview answers)**
>
> ⚠️ **Relocation recommended.** This directory currently lives inside a *public*
> portfolio repository (`ai-response-evaluations`). The project's own security
> policy requires a private repository. No code, credentials, or personal
> financial details may be committed here until the project is moved to a
> dedicated private repository (or the owner explicitly approves staying).
> See `docs/decision_log.md` entry ADR-0002.

## What this is

A phased, deliberately slow-built framework for AI-*assisted* quantitative
trading:

- The **trading engine is deterministic**. It contains no LLM decision-making.
- The **LLM is a research assistant**: it reads structured memory (trade
  journal, performance, market conditions) and proposes hypotheses and
  experiments. It never modifies strategy, configuration, or risk limits.
- **Every change is documented and approved** by the owner before it ships.
- **Paper trading only** until an explicit, multi-step, owner-approved
  enablement of live trading (see `docs/bot_constitution.md`).

## What this is NOT

- Not "a trading bot" built in one pass.
- Not an autonomous AI trader.
- Not a promise of profitability. Strategies must pass predefined backtest
  acceptance criteria before broker integration even begins.

## Project structure (current)

```
trading-framework/
├── README.md                     ← you are here
├── .gitignore                    ← secrets/data exclusions (active from Phase 0)
├── .env.example                  ← variable names only; populated per phase
└── docs/
    ├── process/
    │   └── development-process.md  ← phase gates, approval rules, definition of done
    ├── bot_constitution.md         ← DRAFT immutable rules — pending owner approval
    ├── trader_profile.md           ← Phase 0 questionnaire — pending interview
    └── decision_log.md             ← architecture & process decisions (ADR format)
```

Code directories (`src/`, `tests/`, etc.) intentionally do not exist yet.
They are created in Phase 5 at the earliest, after architecture (Phase 1),
strategy spec (Phase 2), backtesting (Phase 3), and the security-design
phase have each been approved.

## Phase roadmap

| Phase | Deliverable | Gate |
|-------|-------------|------|
| 0 | `trader_profile.md` (interview) | Owner approval |
| 1 | Architecture docs (no code) | Owner approval |
| 2 | `strategy_spec.md` | Owner approval |
| 3 | `backtest_report.md` | Predefined acceptance criteria + owner approval |
| S | `docs/security/` (threat model, secrets, backup/recovery, IR) | Owner approval — **blocks all broker connectivity** |
| 4 | `connection_report.md` (paper only) | Owner approval |
| 5 | Deterministic execution engine | Tests + review + owner approval |
| 6 | Memory system (ledger, journal, reflections) | Owner approval |
| 7 | AI analysis layer (read-only, propose-only) | Owner approval |
| 8 | Monitoring, alerts, health checks | Owner approval |
| 9 | Risk engine hardening + kill switch | Tests + owner approval |
| 10 | Production readiness docs + `security_review_report.md` | Owner approval |

Each phase ends with a design review and a full stop for approval. See
`docs/process/development-process.md`.

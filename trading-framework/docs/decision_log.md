# Decision Log

Architecture Decision Records (ADRs). Every significant technical, process,
or risk decision gets an entry. Status values: `proposed` → `approved` /
`rejected` / `superseded by ADR-XXXX`.

---

## ADR-0001 — Phased development with hard approval gates

- **Date:** 2026-07-22
- **Status:** approved (mandated by project brief)
- **Decision:** Build in strict phases (0 → 10, with a Security Design phase
  before broker integration). Each phase ends with a design review and a full
  stop for owner approval. No code before Phase 5; no broker connectivity
  before the security phase is approved; no live trading without the
  multi-step enablement in the Bot Constitution.
- **Rationale:** Prevents scope creep, unreviewed trading logic, and
  premature credential handling. Matches how regulated trading software is
  actually built.
- **Consequences:** Slower initial progress; far lower risk of shipping an
  unsafe or untested system.

## ADR-0002 — Relocate project out of the public portfolio repository

- **Date:** 2026-07-22
- **Status:** **approved** (owner, 2026-07-22 — chose "new private repo")
- **Context:** Phase 0 scaffolding was requested on a branch of
  `ai-response-evaluations`, which is a *public* portfolio of AI-response
  evaluations. The project's security policy requires a private repository,
  and a trading system's strategy details, trade history, and trader profile
  are sensitive even without credentials.
- **Decision (recommended):** Create a dedicated **private** repository for
  the trading framework before Phase 1 begins. Until then, this directory
  contains process documents only — no code, no credentials, no personal
  financial details, no strategy specifics.
- **Alternatives considered:**
  1. Keep building here and make this repo private — rejected: it would
     unpublish the owner's portfolio, which exists to be public.
  2. Keep building here while the repo stays public — rejected: violates the
     project's own security policy; trader profile and strategy would leak.
- **Consequences:** One-time move of `trading-framework/` (history can be
  carried over or restarted clean — clean restart recommended, since a public
  history of this scaffold already exists and nothing sensitive is in it).

## ADR-0003 — Primary implementation language

- **Date:** 2026-07-22
- **Status:** **approved** (owner, 2026-07-22 — chose Python)
- **Context:** The project brief mentions reading credentials via
  `process.env` (a Node.js idiom), but also lists "preferred language" as an
  interview question. The quantitative-finance ecosystem (backtesting,
  data handling, broker SDKs, statistics) is strongest in Python
  (pandas/polars, vectorbt/backtrader, alpaca-py, ccxt, exchange SDKs).
- **Recommendation:** Python 3.12+ with strict typing (mypy), pydantic for
  configuration/schema validation, and pytest — unless the owner has strong
  existing TypeScript/Node expertise, in which case Node is workable but with
  materially weaker backtesting/data tooling.
- **Consequences:** Determines toolchain in Phase 1 architecture. Secrets are
  read from environment variables in either language (`os.environ` /
  `process.env`) — the security model is identical.

## ADR-0004 — Market sequencing: US equities/ETFs first, futures deferred

- **Date:** 2026-07-22
- **Status:** **approved** (owner, 2026-07-22 — confirmed equities first)
- **Context:** The owner selected both US equities/ETFs and futures as target
  markets, and described themselves as new to both trading and software
  engineering. Futures carry embedded leverage, margin calls, contract
  rollover mechanics, and per-contract minimum sizes that make small
  mistakes expensive. Equities/ETFs traded cash-only (no margin) have a
  hard floor on worst-case loss and the best paper-trading APIs.
- **Decision (recommended):** Build and validate the entire framework —
  strategy, backtesting, risk engine, paper trading — on **US equities/ETFs
  only** first. Treat futures as a later expansion phase with its own
  strategy spec, backtest, margin-aware risk-engine extension, and security
  review. The architecture (Phase 1) will keep the instrument model general
  so futures support is an extension, not a rewrite.
- **Alternatives considered:** Supporting both from day one — rejected: it
  roughly doubles Phase 2–5 scope and puts a leveraged instrument in front
  of a first-time trader before the risk engine has any operating history.
- **Consequences:** Futures ambition is preserved in the architecture but
  gated behind demonstrated success in the safer market.

## ADR-0005 — Paper-trading broker: Alpaca

- **Date:** 2026-07-22
- **Status:** **approved** (owner, 2026-07-22)
- **Context:** First market is US equities/ETFs (per ADR-0004 recommendation)
  on a swing timeframe. Requirements: first-class paper-trading API, separate
  paper/live credentials by design, commission-free equities, maintained
  Python SDK, and no requirement to run a local gateway process.
- **Decision:** Target Alpaca for Phase 4 paper-trading integration.
  Interactive Brokers was considered (broader coverage incl. futures for a
  later phase) but rejected for the first iteration due to API/operational
  complexity (persistent TWS/Gateway) disproportionate to a learning-first
  project. Nothing about Alpaca's API is assumed: capabilities, permissions,
  data entitlements, and rate limits are verified against live documentation
  in Phase 4 and recorded in connection_report.md.
- **Consequences:** Phase 4 uses Alpaca's paper environment exclusively.
  Credential variables (paper only, trading-restricted) are defined in the
  security-design phase. A futures-capable broker becomes a separate ADR
  if/when ADR-0004's expansion phase is reached.

## ADR-0006 — Initial risk-limit defaults (v1, equities/ETFs, no margin)

- **Date:** 2026-07-22
- **Status:** partially approved — daily-loss limit and max-drawdown
  explicitly approved by owner; remaining values are standing defaults that
  become binding at Bot Constitution v1.0 sign-off unless the owner amends.
- **Limits:**
  | Limit | Value | Owner status |
  |---|---|---|
  | Max daily loss | **1.5%** of account | **approved 2026-07-22** |
  | Max account drawdown (kill switch) | **10%** peak-to-trough | **approved 2026-07-22** |
  | Max risk per trade | 0.5–1% of account | default |
  | Max weekly loss | 3% of account | default |
  | Max open positions | 5 | default |
  | Leverage | none (cash only) | default |
  | Shorting | not permitted in v1 | default |
- **Notes:** Breaching the daily or weekly loss limit halts trading for the
  remainder of that period. Breaching max drawdown triggers the kill switch
  and requires a documented owner review before restart. Limits may be
  tightened by configuration; loosening requires an ADR with owner approval.

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
- **Status:** **proposed — owner decision required**
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
- **Status:** proposed — owner decision required (Phase 0 interview)
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

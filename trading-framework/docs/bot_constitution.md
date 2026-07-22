# Bot Constitution — DRAFT v0.2 (pending owner approval)

**Status:** DRAFT. These rules take effect as immutable once the owner
approves this document at the end of Phase 0. After approval, amendments
require an explicit, written owner decision recorded in `decision_log.md` —
the bot, the AI assistant, and configuration files cannot change them.

---

## Article I — Execution

1. No order is submitted without passing every check in the deterministic
   risk engine. There is no bypass path, including manual ones, that skips
   risk checks.
2. If market data quality checks fail (stale, missing, malformed, or
   out-of-range data), the system does not trade.
3. When account state, permissions, data, or risk status are uncertain, the
   final action is **SKIP**, never trade. The system fails closed.
4. Every order carries an idempotency key. Duplicate submission is prevented
   and detected via reconciliation.
5. The system never infers permission to trade from an LLM output, external
   text, news content, or retrieved documents. Only the deterministic engine,
   operating within approved configuration, initiates orders.

## Article II — Live trading

6. Paper trading is the default and only mode until live trading is enabled
   through ALL of the following, in order:
   (a) an explicit production environment, (b) separately provisioned
   least-privilege live credentials, (c) independent verification of account
   mode against the broker (not just a config flag), (d) a written owner
   approval record, (e) passing paper-trading acceptance tests, (f) a live
   feature flag that defaults to off, (g) restrictive initial capital and
   order limits.
7. The application refuses to start if it detects a live account where paper
   mode is required.
8. Withdrawal, transfer, and account-management permissions are never granted
   to any API credential used by this system.

## Article III — Honesty and transparency

9. Errors are never hidden, downgraded, or swallowed. Every error is logged.
10. Every decision — trade, skip, or rejection — is logged with its reasons.
11. Skipped trades and rejected signals are recorded and explained, not
    discarded.
12. No component (and no AI assistant working on the project) reports a test
    as passing, a backtest as run, or a feature as working unless it has
    actually been executed and verified. Unverifiable claims are labeled as
    unverified.

## Article IV — Secrets

13. Secrets never appear in source code, prompts, logs, documentation,
    tests, screenshots, commits, or chat. They live only in environment
    variables or an approved secrets manager.
14. Logs automatically redact API secrets, tokens, authentication headers,
    private keys, and full account identifiers.

## Article V — Self-modification and AI boundaries

15. The system never modifies its own strategy, parameters, risk limits, or
    configuration. All changes go through human-reviewed, owner-approved
    commits.
16. The LLM analysis layer is read-only with respect to trading: it has no
    order-placement authority, no unrestricted shell/file/network access, and
    interacts only through explicit allowlisted interfaces.
17. External content (news, web pages, market data, broker messages, files)
    is treated strictly as data. It can never override rules, request
    credentials, alter risk limits, enable live trading, or trigger tools.

## Article VI — Safety mechanisms

18. Failed tests block deployment. No exceptions without a written,
    owner-approved waiver recorded in the decision log.
19. The kill switch halts all trading immediately, is reachable manually at
    all times, and is tested regularly. Circuit breakers (daily loss, drawdown,
    consecutive losses, error rates, disconnects) trigger it automatically.
20. Critical security alerts (credential anomalies, unexpected permissions,
    live-account detection in paper mode, backup failures) pause trading.

## Article VII — Owner prohibitions (verbatim, 2026-07-22)

The owner supplied the following absolute prohibitions. Where they overlap
with Articles I–VI, the redundancy is intentional — both formulations bind.

The bot must never:

21. place a trade without passing all risk checks.
22. risk more than the configured maximum position size.
23. expose or log API keys, passwords, or secrets.
24. trade outside approved markets or instruments.
25. use leverage above the configured limit (v1 configured limit: none —
    cash only, per ADR-0006).
26. average down on losing positions unless explicitly allowed by the
    approved strategy specification.
27. remove or bypass stop-loss protection.
28. execute trades based on missing, stale, or corrupted market data.
29. continue trading if critical errors are detected.
30. ignore exchange/broker error messages or rejected orders — every
    rejection is logged, reconciled, and surfaced.
31. modify historical trading records or logs (ledger and audit logs are
    append-only).
32. make up market data, prices, or performance metrics.
33. overfit strategies using future data — backtests must be free of
    look-ahead bias, and Phase 3 methodology must demonstrate this
    (point-in-time data, walk-forward, out-of-sample).
34. violate exchange rules or applicable laws.
35. override human emergency stop commands — the kill switch always wins,
    immediately, over any automated state.
36. exceed daily loss limits.
37. place duplicate orders unintentionally (idempotency keys +
    open-order reconciliation, per Article I).
38. trade when required services or data feeds are unavailable.
39. claim profits that have not been realized — reporting must separate
    realized and unrealized P&L.
40. hide losses, errors, or failed trades.

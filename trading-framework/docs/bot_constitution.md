# Bot Constitution — DRAFT v0.1 (pending owner approval)

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

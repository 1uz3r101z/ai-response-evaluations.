# Development Process

**Purpose:** Define how this project is built so that no phase is skipped, no
code ships unreviewed, and no capability (especially broker connectivity and
live trading) appears before its prerequisites are approved.

**Audience:** The owner, and any AI assistant or collaborator working on the
project. These rules bind the AI assistant explicitly.

---

## Core principles

1. **Correctness over speed.** If a tradeoff exists, choose correctness.
2. **Deterministic engine, advisory AI.** The LLM analyzes and proposes; it
   never trades, never edits strategy, never changes configuration.
3. **Phase gates are hard stops.** A phase is finished only when its
   deliverables exist, its review is done, and the owner has approved.
   The next phase does not begin early — not even "harmless" parts of it.
4. **Nothing is claimed that isn't verified.** No fabricated test results,
   backtests, data, logs, or files. If something cannot be verified, that is
   stated explicitly.
5. **Fail closed.** When data, account state, permissions, or risk status are
   uncertain, the action is SKIP, never trade.

## Phase sequence

Phases run strictly in order: 0 → 1 → 2 → 3 → **Security Design** → 4 → 5 →
6 → 7 → 8 → 9 → 10.

The Security Design phase (threat model, secrets management, backup and
recovery, incident response — see the project brief) must complete **before
any broker connectivity code or credential handling**, i.e. before Phase 4.

### Per-phase workflow

```
Interview → Design → Review → Owner approval → Build → Test → Review → Improve
```

### Per-phase definition of done

Every phase must produce, at minimum:

- [ ] Its named deliverable document(s)
- [ ] Updated architecture/folder documentation if structure changed
- [ ] Tests for any code produced (unit + integration where applicable),
      actually executed, with real output recorded
- [ ] A design review answering:
  - Can this fail? How?
  - How would a professional quant improve this?
  - What assumptions exist?
  - What security risks exist?
  - What performance bottlenecks exist?
  - What edge cases exist?
- [ ] A decision-log entry for any significant choice
- [ ] An explicit stop, and recorded owner approval, before the next phase

From the Security Design phase onward, every major phase additionally
produces `security_review_report.md` and meets the security definition of
done from the project brief (no secrets in history, paper mode independently
confirmed, least privilege documented, security tests pass, backups restore,
kill switch tested, residual risks honestly listed).

## Change control

- Trading logic, risk limits, and configuration schemas change only via a
  reviewed, approved commit referencing a decision-log entry.
- LLM-generated code is never merged solely because it compiles. Human review
  is mandatory for: auth, broker integration, order execution, risk controls,
  secrets handling, networking, migrations, CI/CD, deployment config.
- The AI assistant proposes diffs; it does not silently alter existing
  trading behavior while doing unrelated work.

## Credentials handling (binding from day one)

- No credential is ever pasted into chat, committed, logged, or embedded in
  docs/tests/screenshots.
- Secrets live in environment variables (e.g. the Claude Code Cloud
  Environment's env-var settings) or an approved secrets manager.
- If a credential may have been exposed: stop the app, revoke/rotate, review
  account activity, purge from Git history, re-issue with narrower
  permissions, record the incident. Deleting the visible file is not enough.

## Repository policy

- The trading framework must live in a **private** repository. It currently
  sits in a public portfolio repo as a temporary scaffold location — see
  ADR-0002. Broker credentials, personal financial detail, and application
  code must not be added until relocation (or explicit owner approval of the
  location) happens.

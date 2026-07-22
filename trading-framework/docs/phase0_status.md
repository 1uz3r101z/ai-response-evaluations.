# Phase 0 Status

Updated: 2026-07-22

## Completed
- Process framework, draft Bot Constitution, decision log, questionnaire
  scaffolded and pushed.
- Structured interview round 1–3 conducted (answers held in session notes,
  not committed here — see privacy rule in trader_profile.md and ADR-0002).
- Decisions recorded: ADR-0002 private-repo relocation (approved),
  ADR-0003 Python (approved), ADR-0004 equities-first sequencing
  (proposed, awaiting explicit owner confirmation), ADR-0005 Alpaca
  (approved).

## Update 2026-07-22 (later)
- Owner created the private repository and approved migration.
- ADR-0004 approved (equities/ETFs first). ADR-0006 added: daily loss 1.5%
  and 10% max drawdown explicitly approved; remaining limits are standing
  defaults pending Constitution v1.0 sign-off.
- Owner supplied 19 absolute prohibitions → Bot Constitution v0.2,
  Article VII (verbatim).
- Migration is blocked only on session repo access: the add-repo call
  requires an interactive approval that was not granted this turn. Migration
  and branch cleanup happen as soon as access is approved.

## Blocked on owner
1. **Approve session access to the private repo** when the permission prompt
   appears (or start a session directly on `quant-trading-framework` and ask
   Claude to pull this branch's `trading-framework/` directory into it).
2. **Approve Bot Constitution v0.2** as v1.0 (or request amendments).
3. **Answer the last interview items** (in chat): reaction to a 10% weekly
   drop in an open position; definition of success at 6 months; conditions
   for shutting the project down.

## Exit criteria for Phase 0
- trader_profile.md fully completed in the private repo.
- Bot Constitution v1.0 approved by owner.
- All open ADRs resolved.
- Owner gives written approval to begin Phase 1 (architecture, no code).

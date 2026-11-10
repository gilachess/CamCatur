# 14 — MVP Scope and 90-Day Plan

**Status:** Proposed v1.1. Assumes one founder using AI coding agents plus occasional specialist/legal help. These are planning targets, not delivery guarantees.

## 1. Phase 1 goal

Run a real tournament end to end, in manual-entry mode, with a public live page that parents can use to find a game, and with positive written feedback from a design partner. In parallel, produce an evidence-based go/no-go on camera recognition.

## 2. In scope (Phase 1)

| Area | Scope |
|---|---|
| Identity | Email login, organisation, invitations, basic roles (owner, arbiter, viewer), audit log |
| Tenant security | Server-side membership checks, cross-tenant denial tests |
| Tournament | Tournament, sections, rounds, players, board numbers, CSV import/export |
| Pairings | Import from the chosen pairing software/format (doc 12 D5); manual edit; no custom pairing engine |
| Games | Manual move entry with legality validation, FEN/PGN, corrections with audit, results with approval |
| Broadcast | Public tournament page, find-my-board, featured game, game page with replay, PGN download, server-side delay, minors privacy modes |
| Arbiter app | Installable PWA: board list, exception list, manual correction, result entry |
| Operations | Structured logs, error tracking, backups, restore test |
| Research | CV feasibility spike and report (doc 04 section 11) |
| Validation | Interviews, teardown, design-partner event |

## 3. Out of scope until a gate says otherwise

Native Android arbiter app, Windows desktop agent (superseded by phone-first), DGT integration (evaluation only), billing automation, white-label domains, OBS/sponsor tooling, video recording/streaming, AI reports, analytics suite, NATS/Redis, iOS, custom pairing engine, 200+ board scaling.

## 4. 90-day plan

### Days 1-30: Foundations and de-risking
- Week 1: resolve D4; set up repo, CI, Docker Compose; start interviews; borrow or buy spike phones and mounts.
- Week 2: close D2 (CV licensing gate) and D5; competitor teardown; auth and organisations.
- Week 3: positioning draft (D1); tenant isolation tests; tournament/section/player models.
- Week 4: CV spike report v1; Gate A review; D3 and D6.

**Exit:** interviews done, positioning drafted, spike report, auth/org/tenant tests passing.

### Days 31-60: Vertical slice
- Weeks 5-6: rounds, pairings import, board numbers, results.
- Weeks 7-8: chess core (legality, FEN/PGN), manual entry, event table with sequence/idempotency, public game page, PGN export.

**Exit:** the 10-step vertical slice in doc 08 section 4 works end to end, with chess and event correctness tests.

### Days 61-90: Pilot readiness
- Weeks 9-10: public tournament page, find-my-board, delay, privacy modes, arbiter PWA, exception list.
- Week 11: spectator load test (1,000 viewers), backup/restore test, failure drills (loss of connectivity during a round).
- Week 12: design-partner dry run, then live event; collect metrics (doc 01 section 9.3) and written feedback.

**Exit:** a real event run with documented fallback procedures; go/no-go for Phase 2.

## 5. Phase 2 decision

After the pilot, choose one direction using evidence from the spike, pilot metrics and interviews:
1. Camera MoveSource (CamCatur Vision phone app) if the phone spike is Go or Go with constraints.
2. DGT MoveSource if ownership and demand are strong.
3. Distribution/analytics/branding features if recognition is not viable.

## 6. Quality bars for Phase 1
- Chess correctness suite from doc 09 section 3 passes.
- Cross-tenant access tests pass.
- Duplicate/out-of-order event tests pass.
- Delay-bypass test passes.
- Public pages meet WCAG AA contrast and keyboard/screen-reader checks for core flows.
- At least 3 real arbiters complete core tasks in usability tests.

## 7. Risks specific to Phase 1
| Risk | Mitigation |
|---|---|
| Pairing import format variety | Pick one source in week 2; fail clearly on unsupported formats |
| AI-generated code defects in chess/event logic | Tests first; review all tenant and event code |
| Design partner unavailable | Line up two candidates by week 4 |
| Scope creep | Scope rule in doc 08 section 7; weekly review |
| Founder overload | Cut from the Phase 1 list before extending dates |

## 8. v1.3 Two-track alignment with the phone-first addendum

- **Track A (this plan):** platform, validation, manual-mode design-partner event.
- **Track B (addendum Stages 1-2):** phone camera/calibration feasibility, then recognition feasibility, using the metrics in doc 04 sections 11-12.
- **Gates:** G1 (week 4, calibration feasible on test phones), G2 (week 12, Go / Go with constraints / No-go for a Vision pilot), G3 (one-board end to end).
- Track B does not delay Track A. If Track B fails, Track A still delivers a manual and PGN-based product.
- Full merged schedule: `blueprint_addendum/RESEARCH_REVIEW.md` section 4. Naming: Phase 1-4 (this doc and doc 16), Stage 0-6 (addendum), V1-V4 (video).

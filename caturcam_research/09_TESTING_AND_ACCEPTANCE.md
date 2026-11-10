# 09 — Testing and Acceptance Strategy

## 1. Testing philosophy

A chess tournament platform needs stronger correctness and recovery tests than a typical CRUD application. A missed move, duplicated move, incorrect pairing or corrupted PGN can undermine trust in the entire event.

Every feature must have observable acceptance criteria and automated tests where practical.

## 2. Test layers and tools

| Layer | Candidate tools | Focus |
|---|---|---|
| Frontend unit | Vitest, Testing Library | Components and client logic |
| Backend unit | Pytest | Domain logic and validation |
| API integration | Pytest, HTTPX | API contracts and permissions |
| Browser E2E | Playwright | Complete organiser/spectator flows |
| Android | Android test tooling | Native UI and lifecycle |
| Chess correctness | Reference PGNs and chess rules library | Move legality, FEN and PGN |
| Vision | Annotated dataset harness | Recognition performance |
| Event system | Integration tests | Duplicates, ordering and replay |
| Load | k6 | API and realtime traffic |
| Security | Dependency scanners and OWASP testing | Vulnerabilities and access control |
| Windows | Supported physical test machines | Cameras, offline mode and installer |

## 3. Chess correctness cases

Test:
- Normal moves.
- Captures.
- Castling on both sides.
- En passant.
- Promotion to all legal pieces.
- Check and checkmate.
- Stalemate.
- Illegal moves.
- Repeated positions.
- FEN import/export.
- PGN import/export.
- Move replay.
- Result consistency.
- Manual correction and audit history.

Maintain a reference corpus of known games and compare reconstructed moves and final positions.

## 4. Vision evaluation

Measure:
- Board detection success.
- Calibration success.
- Per-square classification.
- Full-position exact match.
- Confirmed move precision and recall.
- False-confirmation rate.
- Human correction rate.
- Time to detect a move.
- Accuracy by camera model, board set, lighting, angle and occlusion.

Proposed targets for supported test conditions:
- Board detection: 99%+.
- Full position reconstruction: 98%+.
- Confirmed move accuracy: 99.5%+.
- Silent incorrect move publication: zero tolerated.

These are proposed acceptance targets, not proven results. Report sample sizes, confidence intervals and dataset coverage. Test complete games because small per-move error rates compound.

## 5. Event reliability cases

- Duplicate event ID.
- Same move submitted twice with different event IDs.
- Out-of-order sequence.
- Missing sequence.
- Reconnect after several events.
- Consumer crash after persistence but before acknowledgement.
- Consumer replay.
- Snapshot restore.
- Stale agent.
- Revoked agent.
- Board reassignment during a round.
- Conflicting manual correction.

Verify idempotent business effects and auditable recovery.

## 6. Load profiles

Test independently:
- 100 active boards.
- 250 active boards.
- 1,000 concurrent spectators.
- 5,000 concurrent spectators.
- 2,000 concurrent game subscriptions.
- Burst reconnects after network outage.
- Event replay after consumer downtime.
- Tournament pages with many games.
- Video delivery under a separate video profile.

Measure P50/P95/P99 latency, error rates, event lag, queue depth, CPU, memory, database connections, network traffic and recovery time.

Initial proposed target: confirmed move propagation P95 below one second under the agreed non-video test profile. Set a separate target for video latency and delivery.

## 7. Failure simulations

| Failure | Expected behaviour |
|---|---|
| Internet outage | Windows agent buffers events and shows offline state |
| UI crash | Local vision service/queue survives where configured |
| Windows restart | Recover unacknowledged queue |
| Cloud API unavailable | Retry without losing local events |
| Duplicate move | No duplicate state transition |
| Out-of-order event | Hold/reject pending reconciliation |
| Camera disconnected | Board warning; last confirmed state retained |
| Uncertain recognition | Correction queue; no confirmed public move |
| Database outage | No silent acknowledgement of unpersisted confirmed state |
| Spectator reconnect | Snapshot plus subsequent events |
| Broadcast worker crash | Durable replay restores processing |
| Agent credential revoked | Further ingestion rejected |

## 8. Security tests

- Cross-tenant read/write attempts.
- Role and permission escalation.
- Expired/revoked device credentials.
- Public endpoint data leakage.
- Insecure direct object reference.
- Rate-limit bypass.
- Malformed event payloads.
- File upload validation.
- Payment webhook signature verification.
- Secrets and dependency scanning.
- Backup access control.

## 9. Release gates

Do not release a milestone until:
- Acceptance criteria pass.
- Relevant automated tests pass.
- No known critical security defect remains.
- Database migration has been tested.
- Logs and metrics are available.
- Failure and recovery behaviour is documented.
- Operator fallback is understood.
- Documentation is updated.

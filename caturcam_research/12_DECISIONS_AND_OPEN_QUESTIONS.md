# 12 — Decisions and Open Questions

## 1. Confirmed product decisions

| Decision | Current choice |
|---|---|
| Product category | Commercial chess tournament management and broadcasting SaaS |
| Brand | CamCatur |
| Product name | CamCatur |
| Platforms | Windows, Android and web |
| Initial board scale | 50–100 simultaneous boards |
| Expansion target | 200–250+ boards |
| Recognition approach | Reuse existing open-source foundations only where commercially permissible |
| Main product | Multi-board tournament-wide broadcasting |
| Windows role | Camera and vision agent |
| Android role | Arbiter and operations companion |
| Web role | Organiser control centre and spectator site |
| Development environment | Google Antigravity |
| AI subscription | Google AI Pro |
| Documentation location | `www/caturcam/research/` |

## 2. Recommended technical direction (subject to implementation validation)

- Next.js, React and TypeScript.
- Tauri 2 + React Windows shell.
- Separate Python/OpenCV vision process.
- Kotlin and Jetpack Compose Android.
- FastAPI backend.
- PostgreSQL.
- NATS JetStream.
- Redis.
- OpenCV and a commercially approved model stack.
- ONNX Runtime for Windows inference evaluation.
- Stockfish subject to GPL compliance.
- Google Cloud.
- Docker and GitHub Actions.

## 3. Recommended build order

1. Organisation, identity and tenant security.
2. Tournament, section, round, player and pairing management.
3. Chess core and manual move entry.
4. Public manual live game broadcasting.
5. Event infrastructure and recovery.
6. Windows camera agent and calibration.
7. Recognition and manual correction.
8. Multi-board operations and scale.
9. Android operations app.
10. OBS, video and sponsor integrations.
11. Billing, entitlements and white-label.
12. Commercial beta and launch.

## 3A. v1.1 blocking decisions (resolve in week 1-2)

| # | Decision | Owner | Due | Linked doc |
|---|---|---|---|---|
| D1 | Primary customer segment and positioning statement | Founder | Week 3 | 01 s9.1, 13 |
| D2 | CV build vs license vs no-camera | Founder + legal | Week 2 | 04 s11.3 |
| D3 | DGT support: evaluate or exclude | Founder | Week 4 | 04 s11.5 |
| D4 | First camera model(s) and mount for the spike | Founder | Week 1 | 04 s11.1 |
| D5 | Pairing source: which software to import from | Founder | Week 2 | 13 |
| D6 | Design partner event and date | Founder | Week 4 | 14 |

## 3B. v1.1 decision log entries

```text
Decision ID: ADR-001   Date: 2026-11-10   Status: Proposed
Topic: Phase 1 stack simplification
Decision: Postgres-only eventing behind an EventBus interface; defer NATS and Redis.
Reason: Estimated 2-3 events/s at 100 boards; operational burden of JetStream not justified yet.
Revisit trigger: consumer lag, replay needs or load-test failure.

Decision ID: ADR-002   Date: 2026-11-10   Status: Proposed
Topic: Arbiter app platform
Decision: PWA first; native Android deferred.
Revisit trigger: pilot users require native-only features (background scanning, reliable push).

Decision ID: ADR-003   Date: 2026-11-10   Status: Proposed
Topic: Vision sequencing
Decision: 4-week feasibility spike precedes Phase 2 commitments; Go/No-go gate.

Decision ID: ADR-004   Date: 2026-11-10   Status: Proposed
Topic: MoveSource abstraction
Decision: All input (manual, PGN, DGT, camera) enters through one authoritative ingestion path.
```

## 4. Open decisions to resolve before implementation

### Brand
- What is the final product name?
- Which domain(s) are available?
- Has trademark and company-name clearance been completed?

### Tournament rules
- Which tournament formats must be supported at launch?
- Will the platform generate pairings or import them from existing systems?
- Which federation/rating identifiers are required?
- Which result approval workflow is needed?

### Camera hardware
- Which exact camera models will be tested first?
- USB only initially, or RTSP from the first beta?
- What mounting height and board dimensions are expected?
- Will the operator manage multiple cameras from one Windows PC?
- What is the expected lighting and room setup?

### Recognition
- Which existing code components are legally reusable?
- Will recognition run entirely on the Windows PC?
- Which hardware acceleration targets matter?
- What is the labelled-data acquisition and permission process?
- What accuracy threshold is required before public move publication?

### Broadcasting
- Is a broadcast page public by default or opt-in?
- What delay is required by tournament policy?
- Is engine analysis public, organiser-only or disabled during live play?
- Which external platforms are priorities?
- Is video streaming part of the first commercial release or a later add-on?

### Commercial
- Monthly subscription, per-event fee or both?
- What is included in each plan?
- Which Malaysian payment provider is suitable?
- What support hours and service commitments can be offered?
- What are the target gross margins per board-hour and viewer-hour?

### Infrastructure
- Which Google Cloud region is appropriate for initial customers?
- What availability and recovery targets are required?
- Which managed or hosted NATS option will be used?
- What are the expected video retention and egress costs?

## 5. Decision log template

For every significant change, record:

```text
Decision ID:
Date:
Topic:
Context:
Options considered:
Decision:
Reason:
Impact:
Security/privacy impact:
Licensing impact:
Migration required:
Owner:
Status:
```

## 6. Key risks to review continuously

1. Open-source licence incompatibility with proprietary distribution.
2. Recognition errors in real tournament conditions.
3. Camera and lighting variability.
4. Lost, duplicated or reordered events.
5. Offline/cloud state conflicts.
6. Spectator and video bandwidth cost.
7. Privacy and safeguarding of junior players.
8. Tournament rules and fair-play implications.
9. Scope expansion before the core workflow is stable.
10. Over-reliance on AI-generated code without tests and review.

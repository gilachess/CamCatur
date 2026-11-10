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
| Documentation location | `www/catur.org/cam/caturcam_research/` |
| Chess/video separation | Confirmed game-event distribution remains independent from video ingest, recording and programme delivery |
| Studio authority | Studio consumes confirmed game state; it cannot authorise or alter chess moves |
| Initial production validation | Evaluate OBS browser overlays and selected camera inputs before considering a native compositor |
| Video scope | Not part of Phase 1; requires production discovery and a separate pilot gate |

## 2. Recommended technical direction (subject to implementation validation)

- Next.js, React and TypeScript.
- (Superseded by A-ADR-002) No Windows desktop shell.
- (Revised by A-ADR-003) On-device recognition on the phone; Python/OpenCV for tooling and training.
- (Revised by R-ADR-002) Arbiter app as a PWA; Vision app framework chosen by spike (A-ADR-008).
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

After Phase 1 is stable:
- Production workflow discovery with organisers and operators.
- OBS Browser Source overlay prototype.
- Selected camera input and one-programme-output pilot.
- Video consent, retention and unit-economics validation.
- Decide whether native CamCatur Studio is justified.

## 3A. v1.1 blocking decisions (resolve in weeks 1-4)

| # | Decision | Owner | Due | Linked doc |
|---|---|---|---|---|
| D1 | Primary customer segment and positioning statement | Founder | Week 3 | 01 s9.1, 13 |
| D2 | CV build vs license vs no-camera | Founder + legal | Week 2 | 04 s11.3 |
| D3 | DGT support: evaluate or exclude | Founder | Week 4 | 04 s11.5 |
| D4 | Test phones (low/mid/high Android; an iPhone if available), mounts and power for the spike | Founder | Week 1 | 04 s11.1, s12 |
| D5 | Pairing source: which software to import from | Founder | Week 2 | 13 |
| D6 | Design partner event and date | Founder | Week 4 | 14 |
| D7 | Phone supply model: rental fleet, customer-owned or participant phones | Founder | Week 4 | 11 s8A.1 |
| D8 | Accept, amend or reject the phone-first reconciliation (RESEARCH_REVIEW.md) | Founder | Week 1 | `blueprint_addendum/RESEARCH_REVIEW.md` |

## 3B. v1.1 and v1.2 decision log entries

```text
Decision ID: R-ADR-001   Date: 2026-11-10   Status: Proposed
Topic: Phase 1 stack simplification
Decision: Postgres-only eventing behind an EventBus interface; defer NATS and Redis.
Reason: Estimated 2-3 events/s at 100 boards; operational burden of JetStream not justified yet.
Revisit trigger: consumer lag, replay needs or load-test failure.

Decision ID: R-ADR-002   Date: 2026-11-10   Status: Proposed
Topic: Arbiter app platform
Decision: PWA first; native Android deferred.
Revisit trigger: pilot users require native-only features (background scanning, reliable push).

Decision ID: R-ADR-003   Date: 2026-11-10   Status: Proposed
Topic: Vision sequencing
Decision: 4-week feasibility spike precedes Phase 2 commitments; Go/No-go gate.

Decision ID: R-ADR-004   Date: 2026-11-10   Status: Proposed
Topic: MoveSource abstraction
Decision: All input (manual, PGN, DGT, camera) enters through one authoritative ingestion path.

Decision ID: R-ADR-005   Date: 2026-11-10   Status: Proposed
Topic: Separate chess broadcast and video media architecture
Decision: Keep confirmed chess-event distribution independent from video
          ingest, media storage, programme composition and external streaming.
Reason: Different data, scaling, reliability, privacy and cost requirements.
Impact: Studio consumes confirmed game state through a presentation interface;
        it does not become authoritative for moves.
Revisit trigger: Production discovery and a real event pilot.

Decision ID: R-ADR-006   Date: 2026-11-10   Status: Proposed
Topic: Initial video production integration
Decision: Evaluate OBS Studio with CamCatur browser overlays before building
          a native compositor or encoder.
Reason: Validate operator workflows and willingness to pay before assuming
        the engineering and support burden of production software.
Impact: OBS remains an optional external production tool during the pilot.
Revisit trigger: Customer evidence shows limitations that justify native Studio.

Decision ID: R-ADR-007   Date: 2026-11-10   Status: Proposed
Topic: Adopt phone-first capture as the vision baseline
Decision: CamCatur Vision on smartphones is the baseline; Windows agent, Tauri shell and
          USB-webcam capture are superseded (see A-ADR-001, A-ADR-002, A-ADR-003).
Reason: Lower hardware barrier, no desktop dependency, OS-agnostic management.
Impact: Docs 01, 02, 04, 05, 06, 07, 09, 11, 14, 15 revised; spike rewritten for phones.
Revisit trigger: Gate G1/G2 results (thermal, battery, accuracy, lifecycle).

Decision ID: R-ADR-008   Date: 2026-11-10   Status: Proposed
Topic: Two-track plan
Decision: Track A (manual-mode platform, validation, design-partner event) runs in parallel
          with Track B (phone feasibility, addendum Stages 1-2); merge at gates G1-G3.
Reason: Avoid delaying customer contact and revenue while the highest-risk work is proven.
Impact: doc 14 section 8; addendum roadmap stages renamed.
Revisit trigger: Gate G1 failure or founder capacity limits.

Decision ID: R-ADR-009   Date: 2026-11-10   Status: Proposed
Topic: Server authority with provisional phone state
Decision: Phones emit candidate events and keep provisional local state; the server
          re-validates and is authoritative for published state.
Reason: Preserves auditability, delay enforcement and correction workflows (doc 05 s5).
Impact: Event acknowledgements, correction flow, offline reconciliation rules.
Revisit trigger: Local-venue/hybrid deployment design (Stage 5).
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

### Video and production (resolve during discovery, not Phase 1)

- Is the primary need one tournament-wide programme, featured-board coverage,
  individual board streams, or a combination?
- How many cameras are available and who owns/operates them?
- Is OBS acceptable to target production operators?
- Is browser-overlay-only integration sufficient initially?
- Is remote scene control required?
- Are commentary and audio mixing required?
- Which destinations are priorities (YouTube Live, Twitch or others)?
- Are recordings required, and who owns/retains them?
- What are acceptable video quality, latency and synchronisation targets?
- Who obtains consent and handles deletion/takedown requests?
- What pricing covers media ingest, output, storage, egress and support?

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

## 7. v1.3 note: decision ID prefixes

Research decisions use `R-ADR-nnn` (this file and doc 16). Addendum decisions use `A-ADR-nnn` in `blueprint_addendum/ARCHITECTURE_DECISIONS.md`. Do not use bare `ADR-nnn`.

# 08 — Development Roadmap

> **v1.1 amendment:** The 12-month plan below is **superseded for planning purposes** by `14_MVP_SCOPE_AND_90_DAY_PLAN.md`. It is retained as the long-term feature sequence. Key changes: computer-vision feasibility starts in week 1, a design-partner event runs in manual mode by Month 3, and native Android, billing automation, white-label and video move after the first paid pilot.

## 1. Planning assumptions

This is a proposed 12-month programme for a solo founder using AI coding agents, with external specialist/legal help as needed. It is not a guaranteed delivery estimate. Computer vision accuracy, camera hardware compatibility, mobile work and production operations are major schedule risks.

## 2. Twelve-month programme

### Month 1 — Foundation
- Private repository and branch strategy.
- Antigravity workspace and rules.
- Product and architecture documentation.
- Docker development environment.
- Database migrations.
- Authentication and organisation membership.
- Initial design system.

Exit: organiser can register, create an organisation and log in.

### Month 2 — Tournament management
- Tournament creation.
- Sections and rounds.
- Player registration.
- CSV import/export.
- Pairings and board numbers.
- Results and basic standings.

Exit: a tournament can be managed without cameras.

### Month 3 — Chess core and camera foundation
- Chess rules library evaluation.
- FEN and PGN support.
- Manual move entry.
- Windows camera capture.
- Agent registration.
- Camera preview and calibration.

Exit: one camera can be connected and calibrated; manual games remain usable.

### Month 4 — Computer vision
- Board and square detection.
- Piece recognition.
- Position reconstruction.
- Legal move validation.
- Confidence states.
- Manual correction queue.
- Annotated dataset and benchmark harness.

Exit: test footage can be processed and reviewed; accuracy is measured rather than assumed.

### Month 5 — Live games
- Cloud ingestion.
- Event contracts.
- NATS integration.
- Game persistence.
- WebSocket delivery.
- Interactive board.
- PGN export and replay.

Exit: one manual or camera-proposed game can be validated and viewed online.

### Month 6 — Multi-board broadcast
- Tournament broadcast pages.
- Multi-board list/grid.
- Board assignment.
- Featured game.
- Live standings.
- Public game URLs.
- Server-side broadcast delay.

Exit: 10–20 simultaneous boards work in a controlled environment.

### Month 7 — Windows and Android operations
- Windows installer and updates.
- Offline queue and recovery.
- Agent diagnostics.
- Android arbiter app.
- QR scanning.
- Manual correction and result submission.
- Push notifications.

Exit: operators can manage and monitor the event from supported devices.

### Month 8 — Professional broadcast
- OBS browser overlays.
- Video source configuration.
- Sponsor graphics.
- Featured game scenes.
- Recording metadata and storage.
- Optional external streaming integration.

Exit: a tournament can produce a professional livestream.

### Month 9 — Commercial infrastructure
- Plans and entitlements.
- Payment integration.
- Usage metering.
- White-label branding.
- Organisation teams and permissions.
- Customer support workflow.

Exit: a customer can subscribe and manage an organisation.

### Month 10 — Real tournament beta
- Pilot with 20–50 boards.
- Hardware compatibility testing.
- Operator training.
- Recognition correction review.
- Network failure simulations.
- Feedback and prioritised fixes.

Exit: one complete real event can be operated with documented fallback procedures.

### Month 11 — Scale and advanced features
- 100-board load test.
- 200–250+ board expansion test.
- Spectator load tests.
- AI tournament reports.
- Player analytics.
- Sponsor reporting.
- Video and storage optimisation.

Exit: performance and reliability targets demonstrated under defined profiles.

### Month 12 — Commercial release
- Security and privacy review.
- Licensing clearance.
- Production deployment.
- Backup/restore validation.
- Billing validation.
- Documentation and onboarding.
- Support and incident procedures.
- Controlled launch.

Exit: first commercial release available to approved customers.

## 3. First five sprints

| Sprint | Deliverable | Acceptance test |
|---|---|---|
| 1 | Project foundation | Local environment starts reproducibly |
| 2 | Authentication | Users can securely sign in and sign out |
| 3 | Organisations | Tenant isolation tests pass |
| 4 | Tournament management | Create tournament, section, player and round |
| 5 | Manual chess broadcast | Enter moves, persist game and view live public game |

## 4. Vertical-slice strategy

Do not build the entire backend before the frontend or vice versa.

First complete this end-to-end workflow:
1. Organiser registers.
2. Creates organisation.
3. Creates tournament.
4. Adds two players.
5. Creates a board/pairing.
6. Starts a manual game.
7. Enters and validates moves.
8. Publishes the game.
9. Opens the public game URL.
10. Exports PGN.

Then add camera recognition to the existing game ingestion path.

This proves the commercial platform independently of computer vision.

## 5. Features to postpone

- iOS app.
- Custom electronic board hardware.
- Full video editing suite.
- AI voice commentary.
- Social network features.
- Advanced player prediction.
- International federation integrations beyond essential exports.
- Custom pairing engine if a reliable supported workflow/import is sufficient initially.

## 6. Critical path and risks

The highest-risk workstreams are:
- Computer vision performance across real camera/board/lighting combinations.
- Commercial licensing of reused recognition code and model assets.
- Offline event reconciliation.
- Large-scale spectator delivery.
- Video bandwidth and cost.
- Tournament workflow correctness.
- Privacy and safeguarding for junior players.

Maintain a risk register and review it at every milestone.

## 7. v1.1 roadmap changes

| Item in v1.0 | v1.1 change |
|---|---|
| Month 4: computer vision begins | CV feasibility spike in weeks 1-4 (doc 04 section 11); Go/No-go decides Phase 2 scope |
| Month 7: Android arbiter app | Phase 1 PWA; native Android only on pilot evidence |
| Month 9: billing integration | Manual invoicing until at least 10 paying customers or a clear billing pain |
| Month 10: first real event | First real event (manual mode, design partner) by Month 3 |
| Month 8: OBS/video/sponsors | After first paid pilot |
| Month 11: 250-board expansion tests | Only after 100-board test passes and demand justifies |

### Added risks
- No validated differentiator (mitigation: doc 13).
- Solo-founder overload (mitigation: strict Phase 1 scope, weekly scope review).
- Hardware logistics and on-site support cost (mitigation: doc 11 section 8A).
- Reliance on AI-generated code without review (mitigation: tests-first on chess, event and tenant code).

### Governance
- Weekly 30-minute review: progress vs plan, top 3 risks, scope changes. Record scope additions in the decision log.
- **Scope rule:** nothing enters Phase 1 unless something of equal size leaves it.
- Each phase ends with a demo to the design partner and a written go/no-go.

### v1.3 note
The Windows ChessVision Agent work in the long-term sequence above is replaced by the phone-first Vision track. See `14_MVP_SCOPE_AND_90_DAY_PLAN.md` section 8 and `blueprint_addendum/RESEARCH_REVIEW.md` section 4.

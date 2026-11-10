# CamCatur Development Roadmap

**Status:** Proposed sequence — do not treat as a commitment to dates  
**Date:** 10 November 2026

> **Naming:** Addendum engineering stages are called **Stages 0-6** to avoid confusion with product phases (Phase 1-4) in `caturcam_research/`. **Sequencing note:** `RESEARCH_REVIEW.md` proposes running Stages 1-2 as a parallel feasibility track while the manual-mode platform and a design-partner event proceed (Track A).

## Guiding rule

Prove the riskiest technical assumptions before building the full product. Do not begin by generating a large application scaffold and hoping that computer vision will work later.

## Stage 0 — Research reconciliation

**Goal:** Understand the existing CamCatur research and identify which parts remain valid.

Tasks:
- Read every original research `.md` file.
- Inventory all Windows, webcam, desktop and server assumptions.
- Mark each item as retain, revise, replace, investigate or discard.
- Compare relevant capabilities and workflows of idChess and ChessZcam using available evidence.
- Record open questions and source links.

Deliverables:
- `RESEARCH_REVIEW.md` (first draft provided on 10 November 2026; pending founder review)
- Updated `README_BLUEPRINT_ADDENDUM.md`
- Prioritised risk register.

Exit criteria:
- No unresolved legacy platform assumption is silently carried into implementation.
- Product scope and initial target devices are agreed.

## Stage 1 — Mobile camera feasibility spike

**Goal:** Prove reliable camera capture and calibration on real phones.

Tasks:
- Test camera preview and sustained capture on representative Android phones.
- Evaluate resolution, frame rate, focus, exposure and thermal behaviour.
- Detect board corners and map the board to 64 squares.
- Support orientation and manual calibration correction.
- Record sample frames across lighting, board and piece variations.

Deliverables:
- Small disposable prototype.
- Device compatibility/performance report.
- Calibration test dataset.

Exit criteria:
- A phone can consistently locate and track a stationary board in the agreed test conditions.
- Known limitations are documented.

## Stage 2 — Recognition and chess-state feasibility

**Goal:** Convert board observations into a reliable move sequence.

Tasks:
- Evaluate piece/square recognition approaches.
- Build labelled test data with representative boards and pieces.
- Infer candidate moves from before/after board observations.
- Validate moves using a chess rules library.
- Test captures, castling, en passant, promotion, hand occlusion and illegal/ambiguous positions.
- Add confidence thresholds and human review.

Deliverables:
- Recognition prototype.
- Accuracy and failure analysis.
- Defined observation and candidate-move contracts.

Exit criteria:
- A representative test set reaches an explicitly agreed accuracy threshold.
- Uncertain positions are flagged rather than silently committed.
- A short game can be reconstructed into a valid PGN with correction support.

## Stage 3 — Offline-first mobile game recorder

**Goal:** Make one phone record a game robustly without depending on continuous connectivity.

Tasks:
- Implement local game/session persistence.
- Implement event IDs, sequence numbers and durable queue.
- Add app restart recovery.
- Add sync acknowledgements and retry handling.
- Test duplicate, missing and out-of-order event delivery.

Deliverables:
- Single-device recording prototype.
- Versioned event schema.
- Offline/recovery test report.

Exit criteria:
- A game survives app restart and temporary network loss.
- Reconnection does not duplicate or corrupt moves.

## Stage 4 — Backend and browser management MVP

**Goal:** Manage a phone and game from a browser.

Tasks:
- Implement authentication and secure device pairing.
- Implement tournament, round, player, board and game-session records.
- Implement device assignment and status.
- Implement game event ingestion and persistence.
- Build live game view, exception queue and manual correction.
- Add PGN export.

Deliverables:
- Backend API.
- Database migrations.
- Browser management application.
- End-to-end one-board workflow.

Exit criteria:
- A phone can be paired, assigned to a game, record a game and synchronise it to the web platform.
- Management works in supported browsers on Windows, macOS and Linux.

## Stage 5 — Multi-board pilot

**Goal:** Prove a small tournament deployment.

Suggested progression:
- 4 boards.
- 16 boards.
- 50 boards.
- Larger pilot only after reviewing measurements.

Tasks:
- Device provisioning and reassignment.
- Multi-board status dashboard.
- Network load and recovery tests.
- Operator correction workflow.
- Monitoring, logging and backups.
- Venue setup checklist.

Deliverables:
- Pilot deployment package.
- Operational runbook.
- Measured performance report.

Exit criteria:
- Pilot board count meets agreed recognition, sync and operator-workload targets.
- Failure recovery is documented and tested.

## Stage 6 — Broadcasting and production hardening

Tasks:
- Add selected broadcast integrations.
- Improve tournament workflows and role permissions.
- Add deployment automation, backups and observability.
- Complete privacy, security and retention policies.
- Validate Android device fleet and iOS support.
- Conduct larger load and field tests.

Deliverables:
- Production release candidate.
- Deployment and support documentation.
- Compatibility matrix.

## Mandatory engineering gates

At the end of each stage:
- Demonstrate working software on real hardware.
- Run repeatable tests.
- Record measured results and known limitations.
- Update architecture decisions when evidence contradicts assumptions.
- Do not mark a milestone complete based solely on generated code or a successful build.

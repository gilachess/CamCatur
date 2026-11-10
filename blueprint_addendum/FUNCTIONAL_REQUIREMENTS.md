# CamCatur Initial Functional Requirements

**Status:** Draft baseline for discovery and prototyping  
**Date:** 10 November 2026

Priority labels:
- **P0:** Required to prove the core product.
- **P1:** Required for a useful pilot.
- **P2:** Later enhancement.

## 1. Mobile vision requirements

| ID | Priority | Requirement |
|---|---|---|
| MOB-001 | P0 | The phone can capture video from its supported rear camera. |
| MOB-002 | P0 | The app can detect or guide calibration of a physical 8×8 chessboard. |
| MOB-003 | P0 | The app can map the board to 64 logical squares and determine orientation. |
| MOB-004 | P0 | The app can produce a board-state observation with per-square confidence/unknown status. |
| MOB-005 | P0 | The app can infer candidate moves from successive observations. |
| MOB-006 | P0 | Candidate moves are validated against legal chess positions before being committed. |
| MOB-007 | P0 | The app exposes uncertain/ambiguous observations for correction. |
| MOB-008 | P0 | The app can maintain a game record and generate FEN/PGN for accepted moves. |
| MOB-009 | P0 | The app persists the active game and pending events locally. |
| MOB-010 | P0 | The app can synchronise queued events after connectivity returns. |
| MOB-011 | P1 | The app reports camera, calibration, battery, storage and network health. |
| MOB-012 | P1 | The app supports secure device pairing with the management platform. |
| MOB-013 | P1 | The app detects board movement or major camera changes and prompts recalibration. |
| MOB-014 | P2 | The app supports configurable capture/recognition profiles for different phone models. |

## 2. Management requirements

| ID | Priority | Requirement |
|---|---|---|
| WEB-001 | P0 | The management UI runs in a modern browser without a Windows-only desktop client. |
| WEB-002 | P0 | An operator can register/pair a phone. |
| WEB-003 | P0 | An operator can create a tournament, round and board/game session. |
| WEB-004 | P0 | An operator can assign a phone to a game session. |
| WEB-005 | P0 | An operator can view device connection and sync status. |
| WEB-006 | P0 | An operator can view a game's current position and move history. |
| WEB-007 | P0 | An operator can correct or reject a recognition result. |
| WEB-008 | P0 | An operator can export a completed game as PGN. |
| WEB-009 | P1 | An operator can manage players, pairings and board assignments. |
| WEB-010 | P1 | An operator can monitor multiple boards in one dashboard. |
| WEB-011 | P1 | The interface is usable on Windows, macOS and Linux browsers. |
| WEB-012 | P2 | Role-based access supports multiple operators and arbiters. |
| WEB-013 | P2 | Broadcast integrations can publish selected games. |

## 3. Non-functional requirements

- **Portability:** Management must not depend on a particular desktop OS.
- **Mobile performance:** Recognition performance must be benchmarked on named device classes.
- **Reliability:** App restart and temporary network loss must not silently lose accepted local events.
- **Correctness:** The system must never silently convert an uncertain observation into a confirmed legal move.
- **Security:** Device pairing, user authentication and transport encryption are required before public deployment.
- **Observability:** Operators can identify disconnected, stale, unsynchronised and recognition-error states.
- **Maintainability:** Vision, chess-domain, sync and management modules have explicit interfaces.
- **Accessibility/usability:** Calibration and error correction must be understandable to non-technical tournament staff.

## 4. Initial acceptance scenarios

1. **Single-board capture:** A supported phone is calibrated above a physical board and records a short legal game into a valid PGN.
2. **Special moves:** The prototype correctly handles castling, en passant, captures and promotion, or clearly flags unsupported/ambiguous cases for human review.
3. **Hand occlusion:** A hand passing over the board does not automatically create a false move.
4. **Manual correction:** An operator can correct a misrecognised move and the resulting game history remains consistent and auditable.
5. **Network interruption:** Disconnecting the phone from the server does not discard locally queued events; reconnecting synchronises them without duplicates.
6. **Cross-OS management:** The same management workflow works in supported browsers on Windows, macOS and Linux.
7. **Device reassignment:** A device can be safely unassigned from one game and assigned to another without mixing game events.
8. **Multi-board pilot:** Several phones can report to one management instance and be monitored simultaneously.

## 5. Explicitly unproven assumptions

The following require experiments rather than optimistic claims:

- Reliable recognition across different board designs, piece sets and lighting.
- Recognition accuracy at tournament time controls and rapid piece movement.
- Sustained mobile camera operation without overheating or excessive battery drain.
- Reliable background operation on Android and iOS.
- Feasibility of a single cross-platform mobile framework for all target devices.
- Venue Wi-Fi capacity for the expected number of phones.
- Practical operator workload when several boards require manual correction.

## 6. Requirements added from research reconciliation (proposed)

| ID | Priority | Requirement |
|---|---|---|
| MS-001 | P0 | Manual game entry is a first-class MoveSource, always available as a fallback when vision is unavailable or uncertain. |
| MS-002 | P1 | PGN/file import is supported as a MoveSource; other sources (e.g. electronic boards) attach through the same ingestion path. |
| PUB-001 | P1 | A public live tournament page shows boards, results and standings. |
| PUB-002 | P1 | A spectator can find a game by player name or board number ("find my board"). |
| PUB-003 | P1 | Public move display honours a server-side delay that cannot be bypassed from public endpoints. |
| PRV-001 | P1 | Player display supports full name, first name/initials, or hidden modes per tournament, with safe defaults for junior events. |
| AUTH-001 | P0 | The server re-validates every candidate move and is authoritative for published state; phone state is provisional until acknowledged (R-ADR-009). |
| MET-001 | P1 | The system records interventions per 100 confirmed moves, false-confirmation count and setup time per board. |
| DEV-001 | P1 | Phone provisioning, reassignment and wipe between events is documented and testable. |
| DEV-002 | P1 | Operators can see per-device battery, temperature (where available), charging and storage status. |

# 01 — Product Vision and Requirements

> **v1.1 amendment:** See Section 9 at the end for the positioning requirement, MoveSource abstraction, success metrics and MVP/Later tiering. The five-application structure above is the long-term vision; Phase 1 builds only what `14_MVP_SCOPE_AND_90_DAY_PLAN.md` lists.

## 1. Product vision

Build a commercial, multi-tenant chess tournament operating and broadcasting platform for clubs, schools, academies, event organisers and chess federations.

The product should make it possible to operate a tournament, connect cameras to physical boards, recognise moves, publish many games simultaneously, and present a professional branded spectator experience.

The product should be designed as five connected applications:

| Product | Platform | Primary users | Main purpose |
|---|---|---|---|
| Tournament Control Centre | Web | Organisers, administrators | Manage organisations, events, players, rounds, pairings and results |
| ChessVision Agent | Windows | Camera operators | Connect cameras, calibrate boards, recognise moves and buffer events offline |
| CamCatur Mobile | Android | Arbiters, organisers | Monitor boards, correct games, submit results and receive alerts |
| CamCatur Live | Web/mobile browser | Players, families, spectators | Interactive 2D boards, move lists, player/game information, standings and tournament pages |
| CamCatur Studio | Web/Windows integrations (later phase) | Commentators, production operators | Combine selected physical camera feeds, confirmed 2D chess graphics, tournament information, commentary and sponsor graphics into a programme output |

The official product brand and platform name is **CamCatur**.

**Capability boundary:** CamCatur Live distributes structured chess data and interactive 2D game views. CamCatur Video handles physical camera media. CamCatur Studio combines selected media and confirmed game presentation data for a produced programme. These are separate capabilities and workloads. See `16_BROADCAST_STUDIO_AND_VIDEO_ARCHITECTURE.md`.

Studio and Video are not part of the Phase 1 MVP.

## 2. Target customers

- Chess clubs.
- Schools and educational organisations.
- Chess academies.
- Tournament organisers.
- Regional and national chess associations.
- Event production teams.
- Federations requiring white-label broadcasting.

## 3. Platform decisions

### Windows
Primary camera and computer-vision station. Supports USB webcams and IP/RTSP cameras, board calibration, board assignment, local recognition, diagnostics, offline event buffering and recovery.

### Android
Native operations companion. Supports tournament and board monitoring, manual corrections, results, player lookup, QR scanning, notifications and selected administrative actions. It is not initially a replacement for the Windows vision station.

### Web
Primary organiser product and public broadcast destination. Supports account and organisation management, tournaments, rounds, pairings, player registration, board control, live broadcasts, public pages, billing and analytics.

## 4. Scale goals

Initial commercial target:
- 50–100 simultaneously active physical boards.
- Multiple tournaments and sections.
- Multiple camera agents and operators.
- Public spectator access.

Expansion test targets:
- 200–250+ active boards.
- 5,000 simultaneous spectators as a performance-test objective.
- 2,000 concurrent game subscriptions as an initial test objective.

These are engineering targets, not proven capacity or service-level guarantees. Board event traffic, spectator traffic and live video traffic must be load-tested independently.

## 5. Core functional requirements

### Organisation and identity
- User registration and secure login.
- Organisation creation and membership.
- Invitations and role-based access.
- A user may belong to multiple organisations.
- MFA for privileged accounts.
- Audit trail for sensitive actions.

### Tournament operations
- Create, edit, publish and archive tournaments.
- Multiple sections within one tournament.
- Rounds and pairings.
- Player registration and CSV import/export.
- Board numbering and assignments.
- Result submission and approval.
- Standings and tie-break data.
- Round status and schedules.
- Game and tournament archives.

### Chess game management
- Manual move entry as the initial baseline.
- FEN and PGN import/export.
- UCI and SAN move representation.
- Legal move validation.
- Game snapshots and move history.
- Corrections with audit history.
- Game status: scheduled, active, paused, completed, disputed, archived.

### Camera and recognition
- USB and RTSP camera support.
- Camera discovery and assignment.
- Board calibration and orientation.
- Frame capture and preprocessing.
- Board and square detection.
- Piece classification.
- Candidate position reconstruction.
- Legal move inference.
- Confidence states and manual correction.
- Local event queue and cloud reconciliation.
- Camera health and diagnostics.

### Broadcasting
- Public tournament page.
- Multi-board list/grid.
- Featured game.
- Interactive board and move list.
- Live standings and completed games.
- Configurable publication delay.
- Shareable game URLs.
- PGN download and replay.
- Optional commentary and analysis.
- Broadcast analytics.
- Public/private/unlisted visibility modes.

### Production features
- OBS browser overlays.
- Sponsor logos and placements.
- Featured-board layouts.
- Video source configuration.
- Recording metadata and storage.
- Optional external streaming platform integration.
- White-label tournament domains and themes.

### Future video and production requirements (not Phase 1)
- Register and monitor authorised physical camera sources.
- Associate sources with a venue, board or production role.
- Render a browser-based overlay for a selected confirmed game.
- Combine selected camera footage, 2D board graphics, tournament information and sponsor assets in a production workflow.
- Evaluate an OBS-first integration before native composition.
- Support external streaming destinations through adapters.
- Treat recording as a separately authorised and scoped capability.
- Apply source access controls, consent, retention and deletion policies.
See document 16.

### Commercial features
- Plans and entitlements.
- Monthly subscriptions and per-event fees.
- Usage metering.
- Invoices and payment events.
- Team seats and board limits.
- Feature flags.
- Customer support and organisation settings.

## 6. Non-functional requirements

- Secure multi-tenancy.
- High data integrity for moves and results.
- Reconnect and replay after temporary outages.
- Offline-capable Windows agent.
- Observable services and structured logs.
- Backups and restore tests.
- Accessibility and responsive web layouts.
- Versioned API and event contracts.
- Privacy-conscious handling of minors' data and tournament footage.
- Graceful degradation when camera recognition is unavailable.

## 7. Product principles

1. Reliability before feature count.
2. Manual workflows must remain possible if recognition fails.
3. Cloud game state is authoritative.
4. Recognition uncertainty must be visible.
5. Camera hardware should be replaceable.
6. Spectator delivery must not depend on the organiser dashboard staying open.
7. Organisations own their tournament data, subject to contract and applicable law.
8. Design for 200+ boards, but operate only the infrastructure needed for current customers.

## 9. v1.1 amendments

### 9.1 Positioning (must be completed before Month 2)
The vision above is a feature list. Complete this statement using evidence from `13_MARKET_AND_USER_VALIDATION.md`:

> For **[primary segment]** who **[core pain]**, CamCatur is a **[category]** that **[key benefit]**. Unlike **[named alternative]**, we **[specific differentiator proven in interviews]**.

Candidate wedges to test (choose one, do not pursue all):
1. School and club tournaments in Malaysia/Southeast Asia: low cost, parent-friendly spectator experience, simple arbiter workflow.
2. Organisers who already own DGT electronic boards and need better broadcast and management.
3. Regional federations needing reliable multi-board broadcast with local payment and support.

### 9.2 MoveSource abstraction
Game input is a pluggable **MoveSource**, not a camera feature. All sources submit candidate events to the same authoritative ingestion path (doc 05):

| MoveSource | Phase | Notes |
|---|---|---|
| Manual entry (web/PWA) | 1 | Baseline; always available as fallback |
| PGN/file import | 1 | Pairing/result import and game archives |
| DGT electronic board | 2 (evaluate) | Existing hardware, lowest recognition risk; verify SDK/protocol terms |
| Camera (ChessVision Agent) | 2-3, gated by feasibility spike | See doc 04 |
| Third-party feeds | Later | Adapters only |

### 9.3 Product success metrics
Define and instrument from the first pilot:
- **Interventions per 100 confirmed moves** (north star for vision-assisted boards).
- **False-confirmation rate** (target zero; any occurrence is a Sev-1 review).
- **Setup time per board** (camera placed, calibrated, assigned) and per 50 boards.
- **Time from move made to public display** (P50/P95).
- **Arbiter tasks completed without leaving the exception queue.**
- **Spectator: time to find a specific player's game.**
- **Commercial:** events run, paid events, repeat-organiser rate, support hours per event.

### 9.4 Requirement tiering
- **MVP (Phase 1):** organisation/auth, tournament/sections/rounds/players, pairing import, manual game entry, public live page with find-my-board search, PGN export, server-side delay, arbiter PWA, audit trail.
- **Phase 2:** camera feasibility result, ChessVision Agent beta, offline queue, exception triage, DGT evaluation, Lichess broadcast adapter.
- **Later:** native Android, billing automation, white-label domains, advanced OBS/Studio controls, sponsor suite, multi-source video production, video recording/streaming, AI reports and analytics.
- **Production discovery (after Phase 1 live workflow is stable):** validate OBS browser overlays and selected camera inputs with real organisers and operators. This is discovery, not a commitment to build a native compositor.

### 9.5 Hardware and venue assumptions to document
Camera model(s), mount type and height, board size, lighting, network at venue, who supplies hardware, and who provides on-site support. See doc 11 section 8.

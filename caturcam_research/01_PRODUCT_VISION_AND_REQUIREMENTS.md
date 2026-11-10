# 01 — Product Vision and Requirements

## 1. Product vision

Build a commercial, multi-tenant chess tournament operating and broadcasting platform for clubs, schools, academies, event organisers and chess federations.

The product should make it possible to operate a tournament, connect cameras to physical boards, recognise moves, publish many games simultaneously, and present a professional branded spectator experience.

The product should be designed as five connected applications:

| Product | Platform | Primary users | Main purpose |
|---|---|---|---|
| Tournament Control Centre | Web | Organisers, administrators | Manage organisations, events, players, rounds, pairings and results |
| ChessVision Agent | Windows | Camera operators | Connect cameras, calibrate boards, recognise moves and buffer events offline |
| ChessArena Mobile | Android | Arbiters, organisers | Monitor boards, correct games, submit results and receive alerts |
| ChessArena Live | Web/mobile browser | Players, families, spectators | Follow live games, standings and tournament information |
| Broadcast Studio | Web/Windows integrations | Commentators, production operators | Featured games, overlays, sponsor graphics and video distribution |

The final product brand must be newly created and checked for domain, trademark and company-name conflicts. “ChessArena” is a temporary codename only.

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

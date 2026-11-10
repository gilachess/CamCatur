# 06 — UI/UX Specification

> **v1.1 amendment:** This document lists screens and components. Venue workflows, exception handling, spectator needs and theming corrections are in Section 11 and in `15_USER_FLOWS_AND_VENUE_UX.md`. Section 11 wins on conflict.

## 1. Design direction

Create a distinctive, professional chess-event operations product. A dark navy interface with a restrained cyan/teal accent is a starting direction, not a final brand identity.

Suggested initial tokens:
- Background: #07131E
- Secondary background: #0C1E2C
- Surface: #102638
- Accent: #22D3EE
- Success: #22C55E
- Warning: #F59E0B
- Error: #EF4444
- Primary text: #F1F5F9
- Secondary text: #94A3B8

Use strong typography, compact tables, high contrast, consistent spacing and accessible focus states. Do not make every screen look like an esports game; organisers need operational clarity.

## 2. Shared design principles

- One design system across web and desktop.
- Native Android interaction conventions on mobile.
- Responsive layouts.
- Keyboard accessibility.
- Clear empty, loading, offline, error and permission states.
- Confirmation for destructive actions.
- Visible audit history for sensitive changes.
- Avoid dense data tables on small screens.
- Use virtual scrolling or pagination for large board lists.
- Do not render 100 live video previews simultaneously by default.

## 3. Web navigation

```text
Dashboard
  Overview
  Tournaments
    All tournaments
    Create tournament
    Players
    Sections
    Rounds
    Pairings
    Results
  Broadcasting
    Live tournaments
    Featured games
    Public pages
    Broadcast settings
  Boards & Cameras
    Board monitoring
    Camera management
    Agent management
    Recognition errors
  Video Studio
    Stream configuration
    OBS overlays
    Sponsor graphics
    Recordings
  Analytics
  Team & Permissions
  Billing
  Settings
```

## 4. Organiser dashboard

Show:
- Tournament status.
- Current round.
- Registered players.
- Active games.
- Connected cameras.
- Broadcast viewers.
- Recognition warnings.
- Results awaiting approval.
- Upcoming round schedule.
- Quick actions.

Warnings should link directly to the relevant board, game or action.

## 5. Board control room

Each board card should display:
- Board number.
- White and Black player names.
- Camera and agent status.
- Current move number.
- Game status.
- Last successful update.
- Recognition confidence/state.
- Pending correction indicator.
- Operator actions.

Provide filters by:
- Board range.
- Camera status.
- Game status.
- Recognition state.
- Section.
- Round.

Use pagination or virtual scrolling. Load a camera preview only when requested or visible.

## 6. Public broadcast page

Recommended layout:
1. Tournament identity and sponsor area.
2. Current round and status.
3. Featured board.
4. Interactive chessboard.
5. Player information.
6. Move list.
7. Other live games.
8. Standings.
9. Completed games/archive.
10. Optional commentary.
11. Optional engine analysis.
12. Optional video player.

Spectators should be able to open a game, replay moves, copy/download PGN and share a direct URL.

## 7. Windows ChessVision Agent screens (v1.0; superseded by CamCatur Vision phone screens, see section 11 item 10)

- First-run setup and device registration.
- Camera discovery.
- Camera preview.
- Board calibration.
- Board orientation.
- Tournament and board assignment.
- Recognition monitor.
- Correction queue.
- Connection and upload status.
- Agent and camera diagnostics.
- Support log export.

The agent should keep capture and queue processing separate from the UI so minimising or restarting the UI does not automatically destroy the camera session.

## 8. Android screens

Primary navigation:
- Home.
- Tournaments.
- Live boards.
- Alerts.
- Profile.

Key screens:
- Login and organisation selection.
- Tournament list and overview.
- Board list and board details.
- Game details.
- Manual move correction.
- Result submission.
- Player/pairing lookup.
- QR scanner.
- Notifications.
- Agent/camera health.

Use native notifications for board disconnection, uncertain recognition, completed games, pending results and round completion.

## 9. Design system components

Create reusable components:
- App shell and navigation.
- Tournament status badge.
- Board status card.
- Player identity row.
- Chessboard and move list.
- Recognition confidence indicator.
- Camera health indicator.
- Alert panel.
- Data table with filters.
- Empty state.
- Loading skeleton.
- Confirmation dialog.
- Permission-denied state.
- Offline banner.
- Sponsor placement.
- Broadcast player.

## 10. UX acceptance criteria

- An organiser can identify a disconnected board quickly.
- An operator can correct a game without losing its previous move history.
- A spectator can locate a featured game quickly.
- A public page never exposes private player data.
- The mobile app remains usable on a typical phone screen.
- Keyboard and screen-reader users can operate core web flows.
- Offline state is obvious and cannot be mistaken for successful cloud publication.

## 11. v1.1 amendments

1. **Theming:** the dark navy/cyan palette in section 1 applies to the **operations** surfaces (organiser dashboard, arbiter app, agent). The **public spectator site** must support light and dark, default to the user's system setting, and be validated for outdoor/bright-screen legibility. Verify contrast ratios (WCAG AA minimum) for all token pairs.
2. **Exception-first control room:** the default board view is a triage list of boards needing attention, sorted by urgency, with the full grid as a secondary view. See doc 15 flow F3.
3. **Setup at scale:** add batch workflows (QR-coded board labels, bulk camera-to-board assignment, calibration status wall, re-calibration queue). See doc 15 flow F2.
4. **Find my board:** the public site's primary entry point for parents and players is search by player name or board number. Add to the public layout in section 6 as item 0, above the featured board.
5. **Minors and privacy:** public player display must support initials/first-name-only/hidden modes per tournament, with organiser-controlled defaults appropriate for junior events.
6. **Mobile-first public pages:** design public pages at 360px width first; the featured board and a single live game must be usable one-handed.
7. **Degraded-state UX:** every operational screen needs explicit states for offline, delayed, stale (time since last update), and uncertain data, never relying on colour alone.
8. **Usability testing:** each flow in doc 15 must be tested with at least 3 real arbiters/organisers before it is declared done, with task-completion time recorded.
9. **Phase 1 arbiter app is a PWA** (see doc 02). Native Android screens in section 8 are deferred.

10. **CamCatur Vision (phone) screens replace section 7:** pairing with a short-lived code; camera placement guide; board detection and manual corner adjustment; calibration confirmation; recognition monitor with confidence; exception review (confirm, reject, correct); battery, temperature, charging, storage and sync status; pending/synced/failed event counts. Design for one-handed use and a screen that may be locked or interrupted.
11. **Two mobile surfaces, kept distinct:** CamCatur Vision (capture device) and CamCatur Arbiter (PWA first). Do not merge their navigation.

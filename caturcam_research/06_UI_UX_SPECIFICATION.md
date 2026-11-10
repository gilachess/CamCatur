# 06 — UI/UX Specification

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

## 7. Windows ChessVision Agent screens

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

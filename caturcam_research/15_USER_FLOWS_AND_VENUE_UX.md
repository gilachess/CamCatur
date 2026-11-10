# 15 — User Flows and Venue UX

**Status:** Proposed v1.1. Each flow requires usability testing with at least 3 real users before it is considered done. Time targets are hypotheses to be measured.

## 1. Personas

| Persona | Context | Needs |
|---|---|---|
| Chief arbiter | Standing, on phone/tablet, time-pressured, noisy room, unreliable Wi-Fi | Know what needs attention now; fix quickly; never lose data |
| Organiser | Before/after rounds, laptop | Set up fast, publish confidently, report to sponsors/federation |
| Vision device operator (Phase 2) | Management app on laptop/tablet; phones on tables; many boards | Fast calibration, clear status, minimal babysitting |
| Parent/spectator | Phone, often outside the hall | Find child's game in seconds; follow moves; know the result |
| Player | Phone/laptop afterwards | Replay, PGN, standing |

## 2. Venue constraints (design for these)
- Poor or intermittent connectivity.
- Bright and dim lighting; screens used one-handed.
- Interruptions every few seconds.
- Rounds begin at fixed times; setup windows are short.
- Many non-technical volunteers.
- Minors present.

## 3. Flows

### F1. Organiser: create a tournament and publish it
1. Create tournament and section(s).
2. Import players and pairings (or enter).
3. Preview public page; choose visibility, delay and player-name privacy mode.
4. Publish; copy public link and QR code.
**Success:** a new organiser reaches a publishable page without help. Measure time and errors.

### F2. Venue setup at scale (Phase 1 manual; Phase 2 phones)
1. Print board labels with QR codes and board numbers.
2. Arbiter devices scan a board to attach to a pairing; mismatches are flagged.
3. (Phase 2) Operator pairs phones, batch-assigns them to boards, runs guided calibration, checks battery/charging/temperature, and sees a status wall: not set up / calibrating / ready / needs recalibration.
4. Pre-round readiness check: all boards assigned, all devices online, delay set.
**Success:** time to ready 50 boards, tracked per event.

### F3. Arbiter: run a round from the exception queue
1. Home shows boards needing attention, sorted by urgency: disconnected, uncertain/invalid, stale (no update beyond threshold), result pending, dispute.
2. Tap a board to see game, last moves, status, last update time.
3. Correct a move or enter a result; the previous state stays visible in history.
4. Confirmation shows exactly what changed and who changed it.
5. Offline: entries queue locally with a visible "not yet published" state.
**Success:** a disconnected or uncertain board is found and resolved without scrolling a board grid.

### F4. Result approval
1. Arbiter submits result; organiser/chief arbiter approves.
2. Public standings update only after approval.
3. Disputed results are flagged, not auto-published.

### F5. Parent: find my child's game
1. Open tournament link or scan QR.
2. Search by player name (or board number). Results respect the privacy mode.
3. Open game: current position, move list, opponent, round, result when finished.
4. Optional: follow a player for notifications (Phase 2).
**Success:** under 15 seconds from opening the link to seeing the right board on a phone. Measure it.

### F6. Spectator: follow multiple games and the featured board
1. Tournament page: round status, find-my-board, featured game, live list.
2. Replay moves, copy/share link, download PGN.

### F7. Round-end and archive
1. All results approved; standings final.
2. Publish PGN and crosstable; archive tournament.

## 4. Status vocabulary (shared across all apps)
Use consistent labels and icons (never colour alone): **Live, Delayed, Stale (time since update), Offline, Uncertain, Needs attention, Paused, Completed, Disputed.**

## 5. Microcopy and error principles
- Say what happened, what is safe, and what to do next ("Moves are saved on this device and will publish when you reconnect").
- Never imply publication happened when it has not.
- Avoid jargon in public pages; avoid blame in operational errors.

## 6. Accessibility and device checks
- WCAG AA contrast and keyboard operation for organiser flows.
- Screen-reader labels for the board, move list and status badges; provide a text move list alternative.
- Minimum touch target sizes on arbiter and public pages.
- Test on one low-end Android phone and one older iPhone as part of the pilot.

## 7. Usability test script (per flow)
Task statement, success criteria, time to complete, errors, assists needed, verbatim quotes, severity-rated issues. Store results without personal data in the repository.

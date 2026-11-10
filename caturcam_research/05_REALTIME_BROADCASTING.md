# 05 — Realtime Broadcasting and Event Contracts

> **v1.1 amendment:** Section 8 (added) sets Phase 1 transport and fan-out guidance consistent with doc 02 section 9. The event contract in this document is unchanged.

## 1. Objective

Deliver live games from many boards through one tournament broadcast experience, with reliable recovery and optional publication delay.

Game events, spectator connections and video streams are different workloads. Measure and scale them independently.

- Keep 2D chess-event broadcasting separate from physical video delivery. Chess fans and arbiters need low-latency, low-bandwidth moves and clocks. Video is a high-bandwidth, separately authored workload for a subset of viewers. Do not combine them into one protocol. See document 16.

## 2. Event flow

```text
WINDOWS AGENT OR MANUAL GAME INPUT
              |
              v
       AUTHENTICATED INGESTION
              |
              v
       SCHEMA AND DEVICE CHECK
              |
              v
       GAME STATE VALIDATION
              |
              v
       PERSIST EVENT / SNAPSHOT
              |
              v
        NATS JETSTREAM
              |
     +--------+---------+----------+
     |        |         |          |
    PGN    Broadcast  Analytics  Archive
  Worker   Gateway     Worker     Worker
              |
              v
       SPECTATOR GATEWAY
              |
              v
      WEB / MOBILE / EMBEDS
```

## 3. Example event envelope

```json
{
  "event_id": "uuid",
  "event_type": "game.move.confirmed",
  "schema_version": 1,
  "organisation_id": "uuid",
  "tournament_id": "uuid",
  "game_id": "uuid",
  "board_id": "uuid",
  "agent_id": "uuid",
  "sequence": 24,
  "occurred_at": "2026-11-09T07:00:00Z",
  "payload": {
    "uci": "e2e4",
    "san": "e4",
    "fen_after": "FEN_STRING",
    "confidence": 0.98
  }
}
```

This is illustrative. Define strict JSON Schema or equivalent typed contracts, validation, required fields and compatibility rules before production.

## 4. Event reliability requirements

- Unique event IDs.
- Per-game monotonically increasing sequence numbers.
- Idempotent consumers.
- Duplicate suppression.
- Acknowledgements.
- Retry with bounded backoff.
- Dead-letter or quarantine handling.
- Replay from a known sequence.
- Periodic snapshots.
- Schema versioning.
- Consumer lag monitoring.
- Reconciliation after reconnect.
- Audit history for manual corrections.

Do not assume the message broker provides exactly-once business effects. Consumers must tolerate duplicate delivery.

## 5. Game state authority

The cloud game service is authoritative.

A camera agent submits an observation or candidate move. The server:
1. Authenticates the agent and checks its board assignment.
2. Validates the event schema and tenant scope.
3. Checks game sequence and expected previous state.
4. Validates chess legality.
5. Checks for duplicate event IDs.
6. Persists the event and updated state transactionally where practical.
7. Publishes the accepted event.
8. Acknowledges the agent.

A failed or uncertain event must not be exposed as a confirmed move.

## 6. Broadcast delay and privacy

Support configurable public release delay, for example:
- Immediate.
- 15 seconds.
- 30 seconds.
- 60 seconds.

The organiser/arbiter control view may receive immediate operational information. The public spectator gateway must enforce the configured release time server-side. Do not rely on JavaScript timers or a client-side setting to enforce delay.

Support public, unlisted, private and organisation-only visibility. Consider tournament rules and fair-play requirements before enabling live engine analysis or immediate move publication.

## 7. Spectator experience

- Tournament landing page.
- Current round and status.
- Featured game.
- Multi-board game list.
- Interactive board.
- Move list.
- Player details with privacy controls.
- Standings.
- Completed games.
- PGN export.
- Shareable URLs.
- Optional commentary.
- Optional video.
- Configurable analysis visibility.

## 8. Distribution integrations

Build the platform's own public broadcast as the primary destination.

Later integrations may include:
- Lichess broadcast publishing.
- Embeddable game widgets.
- Custom domains.
- Public PGN feeds.
- OBS browser overlays.
- YouTube or other video platforms.

Treat external integrations as adapters. Do not make the core game service depend on one external broadcast provider.

## 9. Scale test profiles

Initial test profiles:
- 100 active boards.
- 250 active boards.
- 1,000 concurrent spectators.
- 5,000 concurrent spectators.
- 2,000 concurrent game subscriptions.

Measure:
- Move propagation latency (P50/P95/P99).
- WebSocket connection success and reconnect rates.
- Event throughput and consumer lag.
- Database latency and connection saturation.
- CPU, memory and network traffic.
- Snapshot/replay duration.
- Video bandwidth separately from chess event bandwidth.
- If video is piloted, separately measure camera-hours, source bitrate, programme outputs, recording storage, viewer-hours, egress and production workstation load. Do not infer video capacity from chess-event load tests.

Initial proposed target: confirmed move propagation P95 under one second under the agreed test profile. This is a target, not a guarantee.

## 10. v1.1 Phase 1 transport and spectator fan-out

- **Ingestion** is low volume (see doc 02 section 9.1); use the Postgres events table with a per-game unique `(game_id, sequence)` constraint to enforce ordering and idempotency.
- **Fan-out** is the scaling risk. Serve public game state as cacheable snapshots plus an incremental feed keyed by last-seen sequence. Prefer SSE or a managed pub/sub in front of a CDN over per-viewer database reads.
- **Production overlays** use the same confirmed game state API or websocket/SSE subscription as spectator views. Overlays must not bypass the authoritative delay or access unconfirmed move states. See `16_BROADCAST_STUDIO_AND_VIDEO_ARCHITECTURE.md`.
- **Delay enforcement:** compute a `public_release_at` timestamp server-side per event; public endpoints return only events whose release time has passed. Add a test that proves clients cannot obtain undelayed events from public endpoints.
- **Find-my-board:** the public API must support lookup by player name (with privacy controls for minors, see doc 15) and return board number, opponent, round, result and a deep link.
- **Load test the spectator path first** (1,000 then 5,000 concurrent viewers on a tournament page), because it is likely to fail before ingestion does.
- **Lichess Broadcast adapter** is a Phase 2 distribution option that can provide spectator reach before the platform's own page is at scale; keep it behind the adapter boundary described in section 8 of v1.0.

# 02 — System Architecture

> **v1.3 amendment:** The Windows vision path (section 4), Tauri shell and desktop inference rows below are **superseded** by the phone-first direction (`blueprint_addendum/`). Section 10 summarises the deltas. Where this document conflicts with the addendum on capture device, recognition location or management client, the addendum wins.

> **v1.1 amendment:** Section 9 defines a **Phase 1 simplified stack**. NATS JetStream, Redis, Terraform-managed multi-service deployment and the native Android app are deferred until measured load or a decision-log entry justifies them. Sections 1-8 remain the long-term target.

## 1. Architecture principles

- Use a modular monolith for the initial business API, with clear internal domain boundaries.
- Separate workloads that need independent scaling: vision, realtime delivery, event workers and video processing.
- Use PostgreSQL as the authoritative persistent store.
- Use NATS JetStream for durable event distribution and replay.
- Use Redis for cache and ephemeral coordination, not as the source of truth.
- Keep camera inference local to Windows where practical.
- Use versioned, documented contracts between clients and services.
- Do not let desktop agents write directly to the production database.
- Keep structured chess-event delivery separate from video/media delivery.
- Keep the confirmed game service authoritative; production tools consume confirmed game state and must not write authoritative moves.
- Design future media ingest, composition, recording and external streaming as independently scalable capabilities.

## 2. Recommended technology stack

| Layer | Recommended technology | Notes |
|---|---|---|
| Web | Next.js, React, TypeScript | Organiser and public web applications |
| UI | Tailwind CSS, shadcn/ui or equivalent | Shared design system |
| Chess UI/core | chessops or another reviewed rules library | Verify licence and API suitability |
| ~~Windows shell~~ | ~~Tauri 2 + React~~ | Superseded: no mandatory desktop client (A-ADR-002) |
| Vision tooling | Python, OpenCV | Training, labelling and offline tooling; production inference is on-device (A-ADR-003); server-assisted fallback only behind an interface |
| Model training | PyTorch | Review code, model and dataset licences |
| Model inference | TFLite / ONNX Runtime Mobile (candidates) | On-device; choose by measured spike (A-ADR-008); verify licences |
| Mobile | Vision app: framework by spike (A-ADR-007/008); Arbiter app: PWA first | Replaces native Kotlin ops app for Phase 1 |
| Backend | Python, FastAPI, Pydantic | Modular API |
| Database | PostgreSQL | Authoritative application data |
| Event streaming | NATS JetStream | Durable events, replay, consumers |
| Cache | Redis | Cache and ephemeral coordination |
| Object storage | Google Cloud Storage | Recordings, images, exports |
| Containers | Docker | Reproducible deployment |
| Cloud | Google Cloud | Initial deployment target |
| CI/CD | GitHub Actions | Build, test and deployment |
| Observability | OpenTelemetry, Cloud Monitoring, Sentry or equivalent | Logs, metrics, traces and errors |
| Load testing | k6 | API and realtime load tests |

These are recommendations, not irreversible decisions. Pin versions and review current vendor support before implementation.

## 3. Logical architecture

```text
CLIENTS
  Vision App (phone)  Arbiter PWA        Web Applications
       |                  |                    |
       +------------------+--------------------+
                          |
                    API / AUTH LAYER
                          |
       +------------------+--------------------+
       |                  |                    |
  Identity/Org      Tournament Domain       Game API
       |                  |                    |
       +------------------+--------------------+
                          |
                  DURABLE EVENT BUS
                    NATS JetStream
                          |
        +-----------------+-------------------+
        |                 |                   |
  Game Processor    Broadcast Gateway    Background Workers
        |                 |                   |
        +-----------------+-------------------+
                          |
        +-----------------+-------------------+
        |                 |                   |
   PostgreSQL           Redis           Object Storage
                          |
                 PUBLIC SPECTATOR DELIVERY
                          |
              Web / Mobile / Embeds / OBS
```

### Future media and production boundary

```text
Confirmed game state --> Game presentation API --> 2D board/overlay
                                                       |
Physical camera --> Media source/ingest --------------+
                                                       |
                                             CamCatur Studio
                                                       |
                                             Programme encoder
                                                       |
                                           External streaming platform
```

Do not route video through the game-event pipeline. Do not make the game service depend on OBS, a media encoder or a streaming destination. See `16_BROADCAST_STUDIO_AND_VIDEO_ARCHITECTURE.md`.

## 4. Windows vision path (v1.0 design, superseded by phone-first; retained for reference)

```text
USB / RTSP CAMERA
       |
  Camera Manager
       |
  Frame Capture
       |
  Preprocessing
       |
  Board Detection
       |
  Perspective Calibration
       |
  64-Square Mapping
       |
  Piece Classification
       |
  Candidate Position Reconstruction
       |
  Legal Move / Temporal Validation
       |
  Confidence and Correction State
       |
  Local Durable Event Queue
       |
  Authenticated Cloud Ingestion
```

The vision process must be separate from the Tauri UI process. A UI restart should not automatically destroy the local event queue or ongoing capture session.

## 5. Deployable units for the first release

Avoid premature microservices. Start with:

1. Next.js web deployment.
2. Modular FastAPI application.
3. Windows desktop shell and local Python vision service.
4. Android application.
5. Realtime WebSocket gateway.
6. NATS JetStream deployment.
7. One or two background worker deployments.
8. Managed PostgreSQL.
9. Redis.
10. Cloud Storage.

Split the API into independently deployed services only when workload, security, ownership or operational needs justify it.

## 6. Service boundaries

### Identity and organisation module
Authentication, memberships, invitations, roles, permissions and audit logs.

### Tournament module
Tournaments, sections, players, rounds, pairings, results and standings.

### Game module
Game state, move validation, FEN, PGN, snapshots, corrections and event history.

### Device module
Agents, cameras, sessions, calibration, heartbeat, assignments and device revocation.

### Vision module
Frame processing, recognition jobs, observations, candidate states and diagnostics.

### Broadcast module
Broadcast sessions, game subscriptions, publication delay, visibility, public pages and viewer metrics.

### Media module
Video sources, recordings, assets, sponsor logos and storage metadata.

### CamCatur Video (future)
Media ingest and distribution: Authenticate, ingest and serve authorised physical camera streams; manage recording storage.

### CamCatur Studio (future)
Production workflow: Combine selected media, confirmed 2D graphics, commentary and branding into a programme output.

### Billing module
Plans, subscriptions, entitlements, usage, invoices and payment webhooks.

## 7. Realtime and cloud constraints

Cloud Run supports WebSockets, but WebSocket connections are subject to request timeouts and instances are stateless. Clients must reconnect, authenticate again as required, and resynchronise from a durable event sequence or snapshot. Review current Cloud Run WebSocket documentation before deployment.

Do not assume a stateless serverless container is an appropriate host for a persistent NATS JetStream cluster. Use an appropriate persistent deployment or managed service and test storage, failover and recovery.

## 8. Security boundaries

- Public spectator endpoints expose only explicitly public data.
- Organiser APIs enforce organisation membership and permissions.
- Device APIs authenticate registered agents independently of human users.
- Camera agents receive scoped credentials and can be revoked.
- Event consumers validate schema versions and tenant ownership.
- Secrets are never embedded in desktop or Android binaries.
- Use short-lived credentials and secure refresh/revocation patterns.

## 9. v1.1 Phase 1 simplified stack

### 9.1 Rationale
Estimated chess event load at 100 boards, one move per ~45 seconds per board, is roughly 2-3 events/second (budget 10x for bursts at time controls and round starts). This does not need a distributed log. Spectator fan-out, not game ingestion, is the scaling concern.

### 9.2 Phase 1 components
| Concern | Phase 1 choice | Revisit when |
|---|---|---|
| Web + public pages | Next.js, TypeScript | n/a |
| API | FastAPI modular monolith | Independent scaling/ownership needed |
| Database | PostgreSQL only (events table with per-game sequence; outbox pattern) | Load tests show contention |
| Event distribution to workers | Postgres outbox + LISTEN/NOTIFY or a simple worker poll | Consumer lag or replay needs exceed Postgres |
| Spectator realtime | SSE or managed pub/sub behind CDN, with snapshot + last-seen-sequence resync | WebSocket features needed; test connection limits |
| Cache | In-process / HTTP cache + CDN; add Redis only on measured need | Measured DB read pressure |
| Arbiter app | Installable PWA (offline-tolerant manual entry, push where supported) | Native-only features required by pilot users |
| CamCatur Vision (phone) | Phase 2, gated by phone spike (doc 04 sections 11-12) | n/a |
| Infra as code | Docker Compose + minimal scripted deploy | Second environment or team grows |

### 9.3 Design rules that survive the simplification
- Keep the event envelope, idempotency, sequence numbers and snapshot/replay contract from doc 05 unchanged, so NATS can be introduced later without changing clients.
- Put event publication behind an interface (`EventBus`) with a Postgres implementation now and a NATS implementation later.
- Public spectator pages must be cacheable and must not hit the primary database per viewer.
- Record each deferral and its trigger metric in the decision log (doc 12).

### 9.4 Runtime and framework count for the Vision app (Phase 2)
A native app plus a separate inference runtime plus a model conversion pipeline is a large surface for one developer. During the phone spike, compare native (Kotlin/Swift) against a cross-platform framework on measured camera latency, thermal behaviour and inference speed, and choose on evidence (A-ADR-008), not familiarity.

### 9.5 Video and production exclusions
Video, OBS integration, recording and native composition are excluded from Phase 1. CamCatur Live delivers structured chess events to interactive 2D browser views only. See `16_BROADCAST_STUDIO_AND_VIDEO_ARCHITECTURE.md`.

## 10. v1.3 Phone-first deltas

- **Authority:** the phone emits candidate events and keeps **provisional** local state; the server re-validates and is authoritative for published state (R-ADR-009). Phone state becomes official only after server acknowledgement.
- **Device registry and pairing:** devices pair with short-lived codes and receive per-device credentials; add `devices`, `device_pairings`, `device_assignments` and `sync_batches` to the schema (doc 03 s-note).
- **Event envelope:** align doc 05 fields with the addendum envelope (`event_id`, `device_id`, `game_session_id`, `sequence`, `captured_at`, `schema_version`).
- **Phase 1 stays hosted** (section 9). Local-venue and hybrid deployment modes from the addendum are deferred to Stage 5 and need explicit authority and conflict rules first.
- **Management** remains the browser app (Next.js); no desktop client.
- **Video:** phones are not video sources by default (A-ADR-003; see doc 16 section 5).

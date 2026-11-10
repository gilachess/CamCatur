# 02 — System Architecture

## 1. Architecture principles

- Use a modular monolith for the initial business API, with clear internal domain boundaries.
- Separate workloads that need independent scaling: vision, realtime delivery, event workers and video processing.
- Use PostgreSQL as the authoritative persistent store.
- Use NATS JetStream for durable event distribution and replay.
- Use Redis for cache and ephemeral coordination, not as the source of truth.
- Keep camera inference local to Windows where practical.
- Use versioned, documented contracts between clients and services.
- Do not let desktop agents write directly to the production database.

## 2. Recommended technology stack

| Layer | Recommended technology | Notes |
|---|---|---|
| Web | Next.js, React, TypeScript | Organiser and public web applications |
| UI | Tailwind CSS, shadcn/ui or equivalent | Shared design system |
| Chess UI/core | chessops or another reviewed rules library | Verify licence and API suitability |
| Windows shell | Tauri 2 + React | Native desktop shell |
| Vision service | Python, OpenCV | Separate process/service from UI |
| Model training | PyTorch | Review code, model and dataset licences |
| Model inference | ONNX Runtime | Windows deployment option; verify package licence |
| Android | Kotlin, Jetpack Compose | Native operations app |
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
  Windows Agent       Android App       Web Applications
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

## 4. Windows vision path

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

# 10 — Cloud Deployment and Operations

## 1. Environment separation

### Development
Local Docker Compose, local PostgreSQL, Redis, NATS, synthetic tournaments and test videos.

### Staging
Cloud environment with synthetic customer data, test subscriptions, simulated cameras, load tests and release-candidate verification.

### Production
Separate project/environment, managed database, durable event storage, monitored services, backups, restricted access and approved deployment process.

Never use real customer or junior-player data in development unless explicitly authorised and protected.

## 2. Google Cloud service mapping

| Requirement | Candidate service |
|---|---|
| Web/API containers | Cloud Run |
| PostgreSQL | Cloud SQL |
| Object storage | Cloud Storage |
| Container registry | Artifact Registry |
| Secrets | Secret Manager |
| Metrics/logs | Cloud Monitoring and Cloud Logging |
| CDN | Cloud CDN |
| DNS | Cloud DNS |
| Build/deploy | GitHub Actions with secure workload identity |
| Background workers | Cloud Run jobs/services or suitable persistent compute |
| NATS | Managed or appropriately hosted persistent NATS deployment |

Validate current service capabilities, regional availability, pricing and quotas before finalising.

## 3. NATS persistence

NATS JetStream needs durable storage and an availability design appropriate to its role. Do not assume an ordinary stateless Cloud Run container is suitable for hosting the durable cluster. Evaluate managed NATS or a persistent compute deployment, with tested backups, failover and recovery.

## 4. WebSocket operations

Cloud Run supports WebSockets, but connections are subject to request timeout and stateless-instance constraints. Implement:
- Heartbeats.
- Reconnection with exponential backoff and jitter.
- Authentication refresh.
- Last-seen sequence tracking.
- Snapshot/resynchronisation.
- Backpressure handling.
- Connection metrics.

Review current Cloud Run documentation and set appropriate timeout/instance configuration.

## 5. CI/CD pipeline

```text
Push / Pull Request
        |
        v
Formatting and lint
        |
        v
Type checking
        |
        v
Unit tests
        |
        v
Integration tests
        |
        v
Dependency and security scans
        |
        v
Build containers and installers
        |
        v
Deploy to staging
        |
        v
Smoke / E2E tests
        |
        v
Human production approval
        |
        v
Production deployment
        |
        v
Post-deployment health verification
```

Use signed/reproducible release artefacts where practical. Keep Windows signing certificates and Android signing keys out of source control.

## 6. Database operations

- Automated backups.
- Point-in-time recovery where supported.
- Regular restore drills.
- Migration review.
- Connection pooling.
- Query monitoring.
- Least-privilege database users.
- Separate migration credentials from runtime credentials.
- Retention and archival policy.

A backup that has never been restored is not a verified recovery plan.

## 7. Observability

Capture:
- API request rate and latency.
- Error rate.
- WebSocket connection count and reconnects.
- Event throughput and consumer lag.
- Per-board last update.
- Camera heartbeat.
- Recognition confidence and correction rate.
- Database saturation.
- Storage and video usage.
- Broadcast viewers.
- Billing webhook failures.

Use correlation IDs across API, event and worker logs. Avoid logging secrets, access tokens, unnecessary player personal data or raw video frames.

## 8. Cost management

Do not set a fixed infrastructure budget before measuring representative workloads.

Major cost drivers:
1. Database capacity.
2. Realtime connections.
3. Video recording.
4. Video egress and CDN delivery.
5. Vision inference.
6. Object storage.
7. Logs and monitoring.
8. Support and backups.

Meter usage from the beginning:
- Active board-hours.
- Tournament count.
- Recorded video-hours.
- Viewer-hours.
- Storage GB-months.
- Analysis jobs.
- Premium feature usage.

Use these figures to set sustainable plan limits and pricing.

## v1.3 note: deployment modes

Phase 1 deploys in hosted mode only. The addendum's local-venue and hybrid modes (LAN server, queued external broadcast, later synchronisation) are deferred to Stage 5 and require explicit authority and conflict-resolution rules before production use.

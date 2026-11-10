# 07 — Google Antigravity Implementation Plan

> **v1.3 amendment for agents:** Do **not** scaffold a Windows desktop shell, Tauri app, USB-webcam agent or native Kotlin operations app. Follow `blueprint_addendum/` (phone-first, browser Management, on-device recognition) and `blueprint_addendum/RESEARCH_REVIEW.md`. Where the stack list below disagrees, the addendum wins.

## 1. Development approach

Use Antigravity as an AI-assisted engineering environment, not as a substitute for architecture, testing or code review.

The documentation in this folder is the initial project source of truth. Keep it in version control and update it when a decision is approved.

Do not ask an agent to generate the entire platform in one prompt. Work in bounded modules and vertical slices.

## 2. Suggested repository structure

```text
camcatur/
  apps/
    web/
    windows-agent/
    android/
  services/
    api/
    realtime/
    vision/
    game-processing/
    broadcast/
    analysis/
    workers/
  packages/
    shared-types/
    chess-core/
    ui/
    api-client/
    event-contracts/
  infrastructure/
    docker/
    terraform/
    monitoring/
  docs/
    research/
      README.md
      01_PRODUCT_VISION_AND_REQUIREMENTS.md
      ...
  .agent/
    rules/
    workflows/
  docker-compose.yml
  README.md
```

The requested research folder is `www/catur.org/cam/caturcam_research/`. The application repository can be structured separately; do not force source code into a web server's public document root. Keep private source and configuration outside any publicly served directory.

## 3. Persistent project rules

Create a project-level rule file in the Antigravity-supported rules location. Example content:

### CHESS PLATFORM — GLOBAL RULES

You are developing a commercial, multi-tenant chess tournament broadcasting SaaS platform.

Architecture:
- Read the approved docs before implementation.
- Follow the documented module boundaries.
- Do not change architecture or API contracts silently.
- Keep web, desktop, Android, vision and backend responsibilities separate.
- Use versioned shared contracts.

Multi-tenancy:
- Verify organisation membership on the server.
- Never trust organisation IDs supplied by a client.
- Protect all tenant-owned records and storage.
- Test cross-tenant access denial.

Chess correctness:
- Never publish uncertain recognition as a confirmed move.
- Validate every accepted move against the legal position.
- Preserve sequence, move history, FEN and PGN consistency.
- Handle castling, en passant and promotion.
- Never overwrite newer game state with stale events.

Reliability:
- Event consumers must be idempotent.
- Support retries, replay and reconciliation.
- Preserve local camera events until acknowledged.
- Use structured logs and diagnostics.
- Handle network and service interruptions.

Code quality:
- TypeScript strict mode.
- Python type annotations and Pydantic validation.
- Database migrations for schema changes.
- Automated unit, integration and end-to-end tests.
- No secrets in source code.
- Avoid placeholder functionality in production paths.

Agent behaviour:
- Read relevant documentation first.
- Produce a plan before substantial changes.
- Work only in the assigned module.
- Do not rewrite unrelated files.
- Do not silently change dependency versions or licences.
- Report assumptions, risks and unresolved issues.
- Run tests before claiming completion.
- Report changed files and exact verification performed.
- Ask approval before destructive operations, production deployment, database resets or licensing-sensitive changes.

Definition of done:
1. Implementation complete.
2. Validation and error handling present.
3. Relevant tests pass.
4. Security implications reviewed.
5. Documentation updated.
6. No unrelated regression.
7. Feature verified in its intended environment.

## 4. Master project prompt

Use this as the initial Antigravity project instruction:

> You are the principal software architect and engineering coordinator for a new commercial chess tournament management and broadcasting platform.
>
> Target platforms are web (management and public pages) and smartphones (Android first, iOS later) for vision capture. Initial launch target is 50–100 simultaneous boards, with architecture and load testing for 200–250+ boards. Spectator scale is a separate workload.
>
> Core product capabilities include multi-tenant organisations, tournament management, players, sections, rounds, pairings, results, standings, physical chessboard camera recognition, manual correction, live multi-board broadcasting, public tournament pages, PGN/FEN, OBS integration, sponsor graphics, white-label pages, analytics and SaaS billing.
>
> Preferred stack:
> - Next.js, React and TypeScript.
> - (Superseded) No Windows desktop shell.
> - Vision app framework to be chosen by the phone spike; Arbiter app as a PWA.
> - Python + FastAPI.
> - PostgreSQL.
> - NATS JetStream.
> - Redis.
> - OpenCV.
> - Commercially approved PyTorch/ONNX model stack.
> - Stockfish subject to GPL compliance.
> - Google Cloud, Docker and GitHub Actions.
>
> The product is proprietary commercial SaaS. Review all third-party code, dependencies, models, weights and datasets for commercial licensing compatibility. Do not incorporate AGPL, GPL or other copyleft components into proprietary deliverables without an approved legal and architectural decision.
>
> First inspect the repository and the project documentation. Do not generate the whole application. Produce or update the product specification, architecture, ERD, API contracts, event contracts, security architecture, licensing inventory, UI specification, roadmap and test strategy. Then propose the first five bounded implementation milestones with dependencies, affected modules, acceptance criteria, tests and risks. Wait for approval before substantial implementation.
>
> Build tournament management and manual live broadcasting first. Integrate camera recognition only after the manual workflow is functional.

## 5. Suggested specialised agent roles

1. Principal Architect — architecture, contracts, integration and design review.
2. Frontend Engineer — Next.js dashboard, spectator UI and design system.
3. Backend Engineer — FastAPI, PostgreSQL, identity and tournament domains.
4. Computer Vision Engineer — capture, calibration, model evaluation and recognition.
5. Realtime Engineer — NATS, WebSockets, replay and scaling.
6. Mobile Engineer — CamCatur Vision app, camera lifecycle, on-device inference and device management.
7. QA/Security Engineer — test automation, load, privacy and security review.

These are logical roles. They do not need to be seven continuously running agents.

## 6. Parallel work rules

- Assign each agent a bounded module and explicit file ownership.
- Use separate branches or worktrees for parallel implementation.
- Define API and event contracts before parallel frontend/backend work.
- Avoid concurrent edits to shared schema, configuration and package files.
- Require the principal architect to review integration.
- Merge small changes frequently.
- Run the full relevant test suite after integration.

## 7. First Antigravity tasks

1. Inspect the workspace and report existing files.
2. Create/validate the documentation set.
3. Create repository skeleton and development tooling.
4. Create Docker Compose for PostgreSQL, Redis and NATS.
5. Scaffold FastAPI and Next.js.
6. Implement organisation, user and membership foundations.
7. Build a minimal tournament create/list workflow.
8. Add manual game creation and move entry.
9. Publish a manual game to a public spectator page.
10. Only then begin camera integration.

## 8. Prompt template for each implementation task

> Read the relevant architecture and product documents first.
>
> Task: [one bounded feature]
>
> Scope: [specific module and files]
>
> Out of scope: [explicit exclusions]
>
> Contracts: [API/event/schema references]
>
> Acceptance criteria: [observable behaviour]
>
> Tests required: [unit/integration/e2e]
>
> Security and privacy considerations: [requirements]
>
> Do not change unrelated modules or architecture without approval. Implement the smallest complete vertical slice, run the relevant tests, review the diff and report changed files, test results, assumptions and remaining risks.

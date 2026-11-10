# CaturCam Research — Commercial Chess Tournament Platform Blueprint

**Document set:** Product and technical blueprint v1.0  
**Date:** 9 November 2026  
**Working codename:** ChessArena (temporary; not a cleared brand)  
**Target platforms:** Windows, Android, Web  
**Initial broadcast scale:** 50–100 boards  
**Expansion design target:** 200–250+ boards, with spectator scale tested separately

## Purpose

This folder contains the product blueprint and Antigravity implementation guidance for building a new commercial chess tournament management and broadcasting platform inspired by the problem space addressed by ChessZCam. It is a new product and brand, not a re-skin or assumed derivative of ChessZCam.

The platform combines:
- Tournament administration and player management.
- Camera-based chessboard recognition.
- Multi-board live game broadcasting.
- Windows camera/vision agent.
- Android arbiter and operations app.
- Web organiser dashboard and public spectator pages.
- Broadcast production tools, OBS overlays and optional video streaming.
- White-label pages, sponsor placements, analytics and SaaS billing.

## Read these documents in order

1. `01_PRODUCT_VISION_AND_REQUIREMENTS.md`
2. `02_SYSTEM_ARCHITECTURE.md`
3. `03_DATABASE_SCHEMA.md`
4. `04_CHESS_VISION_ENGINE.md`
5. `05_REALTIME_BROADCASTING.md`
6. `06_UI_UX_SPECIFICATION.md`
7. `07_ANTIGRAVITY_IMPLEMENTATION.md`
8. `08_DEVELOPMENT_ROADMAP.md`
9. `09_TESTING_AND_ACCEPTANCE.md`
10. `10_CLOUD_DEPLOYMENT_AND_OPERATIONS.md`
11. `11_COMMERCIAL_SECURITY_AND_COMPLIANCE.md`
12. `12_DECISIONS_AND_OPEN_QUESTIONS.md`

## Important project rules

- Treat this documentation as the initial source of truth. Update it when an approved architectural decision changes.
- Build a functioning tournament-management and **manual-move live broadcast** workflow before integrating computer vision.
- The cloud game service is authoritative. A camera agent submits observations/candidate moves; the server validates before publishing confirmed game state.
- Never silently publish an uncertain recognition as a confirmed move.
- All tenant-owned records and operations must be protected by server-side organisation membership checks.
- Make event handlers idempotent and support reconnection, replay and reconciliation.
- Do not incorporate third-party source code, models, model weights or datasets until their exact licences and commercial-use conditions have been reviewed.
- Do not assume Google AI Pro includes production Google Cloud infrastructure or usage credits.
- ChessArena is only a working codename. Choose and clear the final brand separately.

## Intended repository placement

Copy this folder's Markdown files into:

`www/caturcam/research/`

The ZIP archive can be extracted directly into that directory.

## Status

This is a planning blueprint, not a claim that any feature, performance target, licence review or deployment has already been completed. All scale and accuracy figures are proposed targets requiring verification.

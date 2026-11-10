# CamCatur Research — Commercial Chess Tournament Platform Blueprint

**Document set:** Product and technical blueprint v1.1 (supersedes v1.0 where noted)  
**Date:** 10 November 2026  
**Official brand:** CamCatur  
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
13. `13_MARKET_AND_USER_VALIDATION.md` (v1.1)
14. `14_MVP_SCOPE_AND_90_DAY_PLAN.md` (v1.1)
15. `15_USER_FLOWS_AND_VENUE_UX.md` (v1.1)

## v1.1 changes (10 November 2026)

A product and project-management review found that v1.0 was technically strong but (a) had no market validation or defined differentiator, (b) scoped five applications for a solo founder, (c) scheduled the highest-risk work (computer vision) late, and (d) described screens rather than venue workflows. v1.1 adds documents 13-15 and amends 01, 02, 04, 05, 06, 08, 11 and 12. **Where an amendment conflicts with earlier text, the amendment wins.** Phase 1 scope is defined in `14_MVP_SCOPE_AND_90_DAY_PLAN.md`; everything outside it is deferred, not deleted.

## Naming rule

The official brand and product name is **CamCatur**. Competitor names appear only in market-research and teardown documents. Do not copy competitor UI, assets, copy or trade dress.

## Important project rules

- Treat this documentation as the initial source of truth. Update it when an approved architectural decision changes.
- Build a functioning tournament-management and **manual-move live broadcast** workflow before integrating computer vision.
- The cloud game service is authoritative. A camera agent submits observations/candidate moves; the server validates before publishing confirmed game state.
- Never silently publish an uncertain recognition as a confirmed move.
- All tenant-owned records and operations must be protected by server-side organisation membership checks.
- Make event handlers idempotent and support reconnection, replay and reconciliation.
- Do not incorporate third-party source code, models, model weights or datasets until their exact licences and commercial-use conditions have been reviewed.
- Do not assume Google AI Pro includes production Google Cloud infrastructure or usage credits.
- **CamCatur** is the official product and brand name across all applications, documents, and interfaces.

## Intended repository placement

Copy this folder's Markdown files into:

`www/catur.org/cam/caturcam_research/`

The ZIP archive can be extracted directly into that directory.

## Status

This is a planning blueprint, not a claim that any feature, performance target, licence review or deployment has already been completed. All scale and accuracy figures are proposed targets requiring verification.

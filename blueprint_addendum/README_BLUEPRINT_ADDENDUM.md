# CamCatur Blueprint Addendum

**Status:** Proposed architectural direction  
**Date:** 10 November 2026  
**Purpose:** Establish the phone-first, OS-agnostic product direction before implementation.

## How to use this addendum

Place this directory at the root of the CamCatur research/project repository. Read these documents before using any earlier research as an implementation specification.

These documents are an architectural addendum, not a claim that the original research has been fully reviewed. When the original `.md` files are available, reconcile them against this addendum and update or supersede conflicting sections.

## Document order

1. `PRODUCT_VISION.md` — product definition, users and scope.
2. `ARCHITECTURE_DECISIONS.md` — binding decisions and constraints.
3. `SYSTEM_ARCHITECTURE.md` — components, boundaries and data flow.
4. `FUNCTIONAL_REQUIREMENTS.md` — initial functional and non-functional requirements.
5. `DEVELOPMENT_ROADMAP.md` — staged validation and implementation plan.

## Non-negotiable direction

- The phone is the primary vision device.
- The phone camera captures the physical chessboard.
- Board and move recognition should run on the phone wherever technically feasible.
- The management platform is browser-based and OS-agnostic.
- Windows-specific desktop software and external webcams are not the primary product architecture.
- The system must support temporary connectivity loss and later synchronisation.
- Do not begin full implementation until the feasibility spikes and acceptance criteria in the roadmap have been reviewed.

## Terminology

- **Vision device:** A supported smartphone running CamCatur Vision.
- **Management platform:** The browser-based CamCatur tournament and device management application.
- **Game session:** A particular game associated with a tournament, round and board.
- **Recognition event:** A proposed or confirmed board-state/move update produced by the vision device.
- **Device assignment:** The association between a vision device and a game session or board.

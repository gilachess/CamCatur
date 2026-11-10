# CamCatur Architecture Decisions

**Status:** Proposed baseline — decisions to be validated by technical spikes  
**Date:** 10 November 2026

This document takes precedence over conflicting legacy assumptions about the capture device, recognition location and management client. Decision IDs use the prefix `A-ADR-`; the research log in `caturcam_research/12_DECISIONS_AND_OPEN_QUESTIONS.md` uses `R-ADR-`. The reconciliation with the research (scope, sequencing, authority, naming) is in `RESEARCH_REVIEW.md`.

## A-ADR-001: Smartphone is the primary vision device

**Decision:** The primary camera and vision platform is a smartphone running CamCatur Vision.

**Consequences:**
- Camera access and image processing are designed around mobile hardware.
- Phone placement, heat, battery, focus, exposure, permissions and background execution are first-class concerns.
- External webcams and Windows camera capture are not the baseline architecture.
- Any future alternative capture device must integrate through a defined device interface rather than dictate the core architecture.

## A-ADR-002: Management is a browser-based application

**Decision:** CamCatur Management is a web application accessible from supported browsers on Windows, macOS and Linux.

**Consequences:**
- Do not require a Windows desktop executable for ordinary tournament management.
- Use web standards and responsive layouts.
- Keep operating-system-specific functionality out of the management domain.
- Document browser support and test on representative browsers and operating systems.

## A-ADR-003: Recognition is local-first

**Decision:** Perform camera capture and the primary recognition pipeline on the phone wherever feasible.

**Consequences:**
- Do not continuously upload video as the default architecture.
- Send structured events, selected diagnostic frames and optional operator-requested media to the server.
- Benchmark mobile inference speed, memory, thermal behaviour and battery consumption.
- If a recognition task proves unsuitable for a phone, isolate it behind an interface so a server-assisted implementation can be evaluated without redesigning the whole product.

## A-ADR-004: Offline-tolerant capture and synchronisation

**Decision:** The phone must persist game events locally and synchronise them when connectivity is restored.

**Consequences:**
- Events need stable IDs, sequence numbers and timestamps.
- The server must safely handle retries and duplicate delivery (idempotency).
- The phone must expose pending, synced and failed event counts.
- Conflict and recovery rules must be explicit; do not silently overwrite a corrected game state.

## A-ADR-005: Recognition output is provisional until validated

**Decision:** A vision result is a candidate observation, not automatically a trusted chess move.

**Consequences:**
- Separate observed board state, inferred move and confirmed game state.
- Validate candidate moves against the legal position using a chess rules library.
- Track confidence and reasons for uncertainty.
- Provide manual correction, move rejection and resynchronisation workflows.
- Handle captures, castling, en passant, promotion, illegal positions, hand occlusion and pieces being replaced.

## A-ADR-006: Separate the vision, chess-domain and management layers

**Decision:** Use explicit component boundaries and versioned contracts.

**Consequences:**
- Camera frames do not become the management platform's domain model.
- Vision emits structured observations/events.
- Chess-domain logic interprets observations and validates moves.
- Management owns tournament entities, assignments, permissions, review and broadcast orchestration.
- Components can be replaced or improved independently.

## A-ADR-007: Android-first, iOS-next — subject to validation

**Decision:** Plan Android as the first mobile target, then iOS. Do not treat this as proof that one framework will work equally well on both.

**Consequences:**
- Build a camera and inference feasibility prototype on representative Android devices first.
- Evaluate iOS camera, lifecycle and inference constraints before finalising the cross-platform framework.
- Keep platform-specific camera/inference adapters behind interfaces.
- Revisit this decision if device availability, performance or deployment requirements favour another sequence.

## A-ADR-008: No premature commitment to a mobile framework or ML runtime

**Decision:** React Native, native Kotlin/Swift, OpenCV, TensorFlow Lite and ONNX Runtime Mobile are candidates, not final mandates.

**Consequences:**
- Compare native and cross-platform camera performance.
- Verify current platform support, model conversion constraints and licensing.
- Choose based on measured prototype results, not familiarity alone.
- Keep the recognition model and inference runtime replaceable.

## A-ADR-009: Scale claims require evidence

**Decision:** Design data structures and services to support growth, but make no production-scale claim until measured.

**Consequences:**
- Start with one phone and one board.
- Progress to 4, 16, 50 and larger board-count tests.
- Measure event throughput, latency, reconnection behaviour, server resource use and operator usability.
- Treat 100–300 simultaneous boards as a scale objective, not an MVP acceptance claim.

## A-ADR-010: Network transport is abstracted

**Decision:** Support local network and internet deployments through a documented communication layer.

**Consequences:**
- Use authenticated HTTPS APIs for commands and durable data exchange.
- Use WebSocket or another suitable real-time channel for live status/events where appropriate.
- Do not assume that all tournament venues have reliable internet.
- Test LAN-only operation and interrupted/reconnected internet scenarios.
- Avoid exposing unauthenticated device control endpoints.

## Decision review

Revisit these decisions after:
- A working camera/calibration prototype exists.
- On-device recognition has been benchmarked.
- Android/iOS framework feasibility has been assessed.
- Offline synchronisation has been tested.
- The original CamCatur research has been reconciled.

# 16 — Broadcast Studio and Video Architecture

> **Status:** Proposed future architecture. This document does not expand Phase 1.

## 1. Purpose

Define how CamCatur may combine confirmed chess game state, 2D board graphics, physical camera footage, tournament graphics, commentary and sponsor assets into a produced programme for destinations such as YouTube Live or Twitch.

This is a future production capability, not a prerequisite for the first commercial MVP.

## 2. Product capability boundaries

| Capability | Responsibility | Audience |
|---|---|---|
| **CamCatur Live** | Interactive 2D boards, moves, player/game information, standings and tournament pages | Players, families, spectators |
| **CamCatur Video** | Physical camera footage, media inputs and authorised recordings | Organisers, production operators, authorised viewers |
| **CamCatur Studio** | Combine selected camera feeds, confirmed 2D chess graphics, tournament information, commentary and sponsor graphics into a programme output | Commentators and production operators |

CamCatur Live distributes structured chess data. CamCatur Video handles media. CamCatur Studio produces a composed programme. These are related but separate capabilities.

## 3. Core architecture principle

**Keep chess-event distribution separate from video distribution.**

Chess moves are small, structured and validated events. Video has different bandwidth, latency, reliability, storage, privacy and cost characteristics.

Do not send video through the game-event pipeline. Do not make the authoritative game service depend on a video encoder, OBS session or streaming provider.

The confirmed game state remains authoritative in the CamCatur backend. Studio consumes confirmed state and presentation data; it must not independently declare a chess move official.

## 4. Conceptual signal flow

```text
                    CAMCATUR GAME PLATFORM
             Manual / PGN / DGT / Camera MoveSource
                              |
                    Authoritative validation
                              |
                     Confirmed game events
                         /           \
                        v             v
                 CamCatur Live   Studio data adapter
                 2D boards       names / clocks / moves /
                 moves/pages     featured game / metadata
                                      |
PHYSICAL CAMERAS --> Media input --> CAMCATUR STUDIO
                                      |
                           Scene composition and layout
                                      |
                               Programme output
                                      |
                               Streaming encoder
                                      |
                         YouTube Live / Twitch / other
```

This is conceptual. Composition and encoding may eventually be local, cloud-based or hybrid; that decision is deferred.

## 5. Video source model

Potential sources:
- USB camera connected to a production computer.
- IP camera or RTSP source.
- Capture-device input.
- Venue or commentary camera.
- Previously recorded, authorised media asset.

Each source should have a stable internal ID and metadata for organisation/tournament ownership, source type, assigned venue/board/role, connection health, preview availability, audio availability, resolution/frame rate where known, permission status and recording status.

Protect stream credentials. Never expose them in public pages, browser URLs, logs or client-side configuration. Use an appropriate server-side secret store or protected local credential store.

## 6. CamCatur Studio responsibilities

Future capabilities may include:
- Selectable scenes and layouts.
- Camera source selection and preview.
- A 2D board overlay bound to a selected confirmed game.
- Player names, board number, round and clock display.
- Tournament title, logos and sponsor placements.
- Commentary and audio-source selection.
- Featured-game switching.
- Programme preview and output status.
- Destination configuration and stream health.
- Optional recording controls and recording metadata.

Studio consumes versioned presentation contracts; it must not query internal database tables directly.

Candidate scenes to validate:
1. Featured board: physical camera plus 2D board and player/game information.
2. Analysis layout: large 2D board with a smaller physical camera window.
3. Venue overview: venue camera with tournament title and round.
4. Multi-board overview: selected board tiles or curated grid.
5. Commentary layout: commentator camera/audio with a featured 2D board.

Studio permissions must be distinct from tournament permissions. A production operator may switch scenes or featured games without automatically gaining authority to change pairings, results or confirmed game state.

## 7. Integration strategy: OBS first, native production later

For an early production pilot, evaluate **OBS Studio** before building a native compositor and encoder.

Possible workflow:
1. CamCatur serves a browser-based overlay for the selected game.
2. OBS adds the overlay as a Browser Source.
3. OBS captures one or more camera inputs.
4. The operator composes scenes in OBS.
5. OBS encodes and streams to the configured destination.

This validates real operator workflows without requiring CamCatur to build a complete video mixer, audio mixer and encoder.

CamCatur owns chess data, tournament identity, overlay content and the integration contract. OBS remains an external production tool during this evaluation.

Do not assume OBS integration automatically provides remote multi-operator control, cloud production, recording management or a managed streaming service. Validate these separately.

A native CamCatur Studio compositor/encoder is a later option only if customer research demonstrates a clear advantage in usability, reliability, integration or commercial economics.

## 8. Synchronisation and correctness

Physical video and the digital board may update at different times.

Future requirements:
- Confirmed chess events include server timestamps and per-game sequence numbers.
- Video sources expose timestamps/timing metadata where available.
- Studio records which game and event sequence its overlay represents.
- Operators can identify stale or disconnected overlays.
- Never silently present stale game data as current.
- Define acceptable audio/video and move/overlay synchronisation targets through production testing.
- Provide a safe fallback scene if synchronisation cannot be maintained.

A camera image of a physical board is not proof that a move has been validated by the game service.

## 9. Streaming destinations

Potential destinations include YouTube Live and Twitch, subject to customer demand and current platform requirements.

Use a destination-adapter boundary. The game service and Studio scene model must not depend on one provider's API or ingest protocol.

Account for credential protection, destination authorisation, start/stop state, connection health, reconnect behaviour, destination-specific output requirements, platform policies and archive behaviour.

For an initial pilot, manual destination configuration in OBS may be sufficient. Automated platform account linking and stream lifecycle management are separate scope items.

## 10. Recording and media storage

Live streaming and recording are separate decisions. Before implementing recording, define:
- Which sources are recorded: raw camera, composed programme, or both.
- Continuous, event-based or operator-triggered recording.
- Format and segmentation.
- Storage location and access controls.
- Retention and deletion workflow.
- Download/export permissions.
- Consent and notice requirements.
- Storage and egress cost allocation.
- Recovery from interrupted uploads.

Do not retain raw footage by default without a documented business and privacy purpose.

## 11. Capacity and cost model

Measure video independently from chess-event traffic:
- Active camera sources.
- Resolution, frame rate and average/peak bitrate.
- Total camera-hours.
- Number of simultaneous programme outputs.
- Recording hours and retained storage.
- Viewer-hours and delivery/egress.
- Production workstation CPU/GPU/network usage.
- Operator setup and support time.

Do not assume a 100-board event needs 100 simultaneous public video streams. A practical first production model may use a small number of selected cameras and one or a few composed programme outputs.

One composed tournament programme is materially different from a separate stream for every board. Treat per-board streaming as a separate product and cost decision.

Pricing must account for media ingest, processing, storage, delivery/egress and support. Chess-event traffic and video viewer-hours require separate usage measures.

## 12. Privacy, consent and safeguarding

Video may capture children, spectators, arbiters, staff, name badges and other personal information.

Before public video or recording:
- Provide clear venue camera notices and event-registration information.
- Define who obtains required permissions and consent.
- Provide a junior-player/guardian permission process where applicable.
- Provide camera framing options to avoid unnecessary capture of faces and bystanders.
- Make visibility explicit: private, organisation-only, unlisted or public.
- Restrict raw footage and recording access.
- Define retention, deletion and incident response.
- Avoid facial recognition and identity inference.
- Do not make public video the default for junior events.
- Obtain jurisdiction-specific legal review before pilots and commercial release.

Chessboard footage can still reveal identities or other personal information; do not treat it as automatically anonymous.

## 13. Phased delivery

| Stage | Scope | Exit condition |
|---|---|---|
| Phase 1 — CamCatur Live MVP | Tournament operations, manual moves, 2D public broadcast, delay, PGN, arbiter PWA | A real organiser can run and publish a tournament reliably |
| Phase 2 — Production discovery | Interview operators; test OBS Browser Source overlay and one or two camera inputs; document workflow and cost | Customer need and a repeatable pilot are evidenced |
| Phase 3 — Studio beta | Selected camera sources, featured-game overlay, scene presets, operator controls and one programme output through OBS or validated integration | Successful event pilot with measured reliability, consent and unit economics |
| Phase 4 — Native Studio evaluation | Decide whether native composition, encoding, recording and destination management are justified | Explicit decision supported by demand and cost/maintenance comparison |

Do not start Phase 3 until Phase 1 is stable and Phase 2 validates the production need.

## 14. Interfaces to preserve

These are future design boundaries, not Phase 1 implementation requirements:
- Game presentation API: authorised, confirmed game state and presentation metadata.
- Overlay endpoint: renders a selected game for browser-based production.
- Media source registry: source metadata and access policy.
- Media ingest interface: supported camera/media inputs.
- Studio scene model: source placement, overlays and graphics.
- Destination adapter: output configuration and status.
- Recording metadata service: authorised recording lifecycle.

Keep the initial game and tournament domain independent of these media components.

## 15. Validation questions

1. Do organisers want one tournament-wide programme, selected featured-board coverage, individual board streams, or a combination?
2. How many cameras are available at 20-, 50- and 100-board events?
3. Who operates the production workstation?
4. Is OBS familiar to target operators?
5. Is a browser overlay sufficient, or is remote scene control required?
6. Are commentary and audio mixing required?
7. Are recordings required, and who owns and retains them?
8. What video quality and latency are acceptable?
9. How many simultaneous programme outputs are expected?
10. Who obtains consent and handles deletion/takedown requests?
11. What would organisers pay for production capability?
12. Does the feature improve event conversion or repeat bookings?

## 16. Proposed decision records

```text
Decision ID: ADR-005
Topic: Separate chess broadcast and video media architecture
Status: Proposed
Decision: Keep confirmed chess-event distribution independent from video
          ingest, media storage, programme composition and external streaming.
Reason: Different data, scaling, reliability, privacy and cost requirements.
Impact: Studio consumes confirmed game state through a presentation interface;
        it does not become authoritative for moves.
Revisit trigger: Production discovery and a real event pilot.
```

```text
Decision ID: ADR-006
Topic: Initial video production integration
Status: Proposed
Decision: Evaluate OBS Studio with CamCatur browser overlays before building
          a native compositor or encoder.
Reason: Validate operator workflows and willingness to pay before assuming
        the engineering and support burden of production software.
Impact: OBS remains an optional external production tool during the pilot.
Revisit trigger: Customer evidence shows limitations that justify native Studio.
```

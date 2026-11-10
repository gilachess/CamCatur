# CamCatur Research Review and Reconciliation

**Status:** Draft for founder review (proposed, not yet accepted)  
**Date:** 10 November 2026  
**Fulfils:** Phase 0 / Stage 0 deliverable `RESEARCH_REVIEW.md` in `DEVELOPMENT_ROADMAP.md`  
**Reviews:** `caturcam_research/` documents 01-16 (v1.1/v1.2) against this addendum (A-ADR-001 to A-ADR-010)

## 1. Reconciliation rule

Two bodies of work exist and each is right about different things.

| Topic | Governing source |
|---|---|
| Capture device, recognition location, management client (phone-first, browser-based, local-first) | **Addendum** |
| Product scope, market validation, MVP sequencing, public broadcast, commercial model, safeguarding, video/Studio boundary | **Research v1.1/v1.2** |
| Event reliability, idempotency, delay, authority | Both (compatible); research gives more detail |

Where a research document still describes a Windows agent, desktop shell or USB webcam as the baseline, the addendum wins and the item is dispositioned below.

## 2. Inventory of legacy assumptions

Dispositions: **Retain**, **Revise**, **Replace**, **Investigate**, **Discard**.

| # | Item | Where | Disposition | Action |
|---|---|---|---|---|
| 1 | Windows ChessVision Agent as primary camera/CV station | 01, 02 s4, 04 s9, 06 s7, 09 s7, 11 | **Replace** | Superseded by CamCatur Vision (phone). Windows camera capture is not baseline (A-ADR-001). |
| 2 | Tauri 2 desktop shell | 02, 04 s9, 07, 12 | **Discard** | No mandatory desktop client (A-ADR-002). |
| 3 | USB webcam / RTSP as initial cameras | 01, 04 s6, 04 s11.1 | **Revise** | Not baseline. May return later as an alternative device type behind the device interface. |
| 4 | ONNX Runtime on Windows | 02 | **Replace** | Mobile runtime candidates (TFLite, ONNX Runtime Mobile) chosen by spike (A-ADR-008). |
| 5 | Python/OpenCV vision process on a PC | 02, 04 | **Revise** | Python remains for training, labelling and offline tooling. Production inference is on-device (A-ADR-003). Server-assisted inference stays behind an interface as a fallback. |
| 6 | Native Kotlin Android operations app | 02, 06 s8, 12 | **Revise** | Arbiter app is a PWA in Phase 1 (R-ADR-002). Vision app framework undecided (A-ADR-007/008). |
| 7 | Windows installer and support bundle | 11 s8, 04 s9 | **Discard** | Replace with device provisioning and app distribution guide. |
| 8 | Feasibility spike hardware (webcams) | 04 s11.1 | **Replace** | Phone spike (doc 04 s11.1 revised, s12). |
| 9 | Event envelope, idempotency, sequence numbers, replay | 05 | **Retain** | Align field names with addendum s6.2 during implementation (`device_id`, `event_id`, `game_session_id`, `sequence`, `schema_version`). |
| 10 | Confidence states (Verified/Uncertain/Invalid/Disconnected) | 04 s4 | **Retain** | Map to A-ADR-005: observed -> inferred -> confirmed. |
| 11 | Server-authoritative state and delay | 05 s5-6 | **Retain** | Resolve phone-vs-server authority (conflict C1 below). |
| 12 | Manual-first platform slice, PGN, public live page, find-my-board, privacy modes | 14, 15 | **Retain** | Addendum has no spectator-facing P0; add (see FUNCTIONAL_REQUIREMENTS s6). |
| 13 | Market validation, wedge, interviews | 13 | **Retain** | Not covered by addendum. |
| 14 | Phase 1 stack (Postgres-only, EventBus, defer NATS/Redis) | 02 s9 | **Retain** | Compatible with addendum (modular monolith). |
| 15 | Video/Studio separation, OBS-first | 16 | **Retain** | Parked; see C6 on phones as video sources. |
| 16 | Hardware unit economics | 11 s8A | **Revise** | Cost phones, mounts, power, spares, provisioning. |
| 17 | Management in a browser (Next.js) | 02 | **Retain** | Consistent with A-ADR-002. |
| 18 | Hosted deployment only | 02, 10 | **Investigate** | Addendum adds local-venue and hybrid modes (Stage 5). |

## 3. Conflicts and proposed resolutions

| # | Topic | Research | Addendum | Proposed resolution |
|---|---|---|---|---|
| C1 | Who is authoritative for game state? | Server validates and is authoritative (doc 05 s5) | Phone infers, validates and maintains FEN/PGN locally (SYSTEM_ARCHITECTURE s3.4) | Phone produces **candidate** events and local provisional state; **server re-validates and is authoritative for published state**; phone state is provisional until server acknowledgement. Consistent with A-ADR-005. Proposed as R-ADR-009. |
| C2 | Roadmap order | Platform first in manual mode, CV spike in parallel (doc 14) | Three feasibility stages, then backend (Stage 4) | **Two parallel tracks** (section 4). Customer contact and a manual-mode event do not wait for recognition. |
| C3 | Public spectator page | Phase 1 MVP | P2 (WEB-013) | Add as P1 requirement in addendum; it is the lowest-risk way to earn trust and revenue. |
| C4 | Mobile surfaces | "CamCatur Mobile" (arbiter), ChessVision Agent | CamCatur Vision | Two distinct surfaces: **Vision** (capture, native/cross-platform, framework by spike) and **Arbiter** (PWA first). Glossary in section 5. |
| C5 | "Phase" naming | Doc 14: Phase 1/2; doc 16: Phases 1-4 | Phases 0-6 | Product phases stay **Phase 1-4** (docs 14/16). Addendum engineering stages renamed **Stage 0-6**. Doc 16 video stages renamed **V1-V4**. |
| C6 | Phones and video | Doc 16: cameras as Studio media sources | A-ADR-003: no continuous video by default | A Vision phone is **not** a video source by default. Using one as a Studio source is a separate explicit opt-in with its own consent, bandwidth and cost. |
| C7 | ADR numbering | ADR-001 to ADR-006 | ADR-001 to ADR-010 | Prefixes: **R-ADR-** (research, doc 12) and **A-ADR-** (addendum). |
| C8 | Transport | SSE for spectators | WebSocket or similar | Compatible: SSE/pub-sub for public fan-out; authenticated WebSocket or HTTPS for device and operator channels. |
| C9 | Hardware economics | Webcam or camera per board | Phone per board | Cost the phone fleet explicitly before pricing (doc 11 s8A). |
| C10 | Success metrics | Interventions per 100 moves, false-confirm rate | Qualitative exit criteria | Adopt the research metrics as the numeric exit criteria for Stage 2. |

## 4. Merged plan (proposed)

Estimates are planning assumptions, not commitments.

**Track A - Platform and validation (doc 14):** auth/orgs, tournaments, pairing import, manual entry, public live page, find-my-board, arbiter PWA, design-partner event, interviews.

**Track B - Phone vision feasibility (addendum Stages 1-2, metrics from doc 04 s11-12):** camera/calibration spike, recognition spike, legal move reconstruction.

| Weeks | Track A | Track B | Gate |
|---|---|---|---|
| 1-4 | Foundations, interviews, positioning, vertical slice start | Stage 1: phone capture, thermal/battery, board detection and calibration on 3+ Android phones | **G1 (week 4):** calibration reliable in agreed conditions? Licensing decision D2 closed by week 2 |
| 5-8 | Vertical slice (rounds, manual entry, events table, public game page) | Stage 2 begins: recognition dataset, candidate move inference, chess validation | |
| 9-12 | Public page, find-my-board, arbiter PWA, design-partner event | Stage 2 continues: accuracy, false-confirm rate, interventions/100 moves | **G2 (week 12):** Go / Go with constraints / No-go for Vision pilot |
| 13+ | Pilot feedback, commercial validation (Gate C) | Stage 3: offline-first recorder merged with the Track A events table | **G3:** one-board end-to-end (phone -> server -> public page) |
| Later | Stage 4-5: pairing/auth/device registry; 4, 16, 50 board pilots | | Scale claims only after measurement (A-ADR-009) |

If G1 or G2 fails, Track A still ships a sellable manual and DGT/PGN-based product, and the vision approach is revisited.

## 5. Glossary

| Name | Meaning | Surface |
|---|---|---|
| CamCatur | Brand and platform | All |
| CamCatur Management | Browser-based organiser, arbiter and device control (includes the former "Tournament Control Centre") | Web |
| CamCatur Vision | Phone app that captures, calibrates, recognises and syncs | Android first, iOS later |
| CamCatur Arbiter (formerly "CamCatur Mobile") | Arbiter/exception handling | PWA first |
| CamCatur Live | Public 2D boards, pages, standings | Web |
| CamCatur Video | Physical camera media (future) | Doc 16 |
| CamCatur Studio | Produced programme (future) | Doc 16 |

Retire the name "ChessVision Agent".

## 6. Conventions

- Decision IDs: `R-ADR-nnn` (research log, doc 12) and `A-ADR-nnn` (addendum). Never use bare `ADR-nnn`.
- Product phases (docs 14/16 usage): Phase 1-4. Engineering stages (addendum): Stage 0-6. Video stages: V1-V4.
- Requirement IDs follow the addendum prefixes (`MOB-`, `WEB-`) plus those added in `FUNCTIONAL_REQUIREMENTS.md` s6.

## 7. New open questions

1. Phone supply: organiser-supplied rental fleet, customer-owned, or participant phones? (Participant phones raise consent and reliability issues.)
2. Which three Android test phones (low/mid/high tier) and is an iPhone available for lifecycle checks?
3. Mounting and power: stands, clamps, chargers, cable management; how long must a phone run per round?
4. Is a browser-based capture prototype (camera plus on-device inference in the browser) worth a quick spike, given iOS camera and background limits?
5. How are phones provisioned, wiped and reassigned between events?
6. Does the Arbiter PWA remain separate from Management, or become a responsive view of it?
7. Phone cost versus webcam cost per board at 20, 50 and 100 boards.

## 8. Changes made in the v1.3 patch

- Added this document.
- Prefixed ADR identifiers (`R-ADR-`, `A-ADR-`) and added R-ADR-007 to R-ADR-009.
- Renamed addendum "Phase" to "Stage" and doc 16 phases to V1-V4.
- Replaced Windows agent/Tauri/USB-webcam baseline statements in docs 01, 02, 04, 05, 06, 07, 09, 11, 12, 14, 15 with phone-first equivalents or supersession notes.
- Rewrote the doc 04 spike scope for phones and added doc 04 s12.
- Added doc 14 s8 (two-track plan), doc 09 s10 (additional tests), and requirements in the addendum.
- Fixed numbering errors (doc 11 duplicate section 8, doc 05 banner, doc 01 section 8, doc 12 week range).

# 04 — ChessVision Engine Specification

> **v1.1 amendment:** Section 11 adds a mandatory feasibility spike that **precedes** engine build-out, clarifies the accuracy metrics, and adds a build-vs-license decision gate. The vision work no longer waits until Month 4.

## 1. Objective

Build a replaceable computer-vision pipeline that converts physical chessboard observations into candidate chess positions and validated moves.

The engine must be independent of tournament administration and public broadcasting. It communicates with the platform through versioned events and authenticated APIs.

## 2. Licensing-first strategy

Investigate existing open-source foundations, but do not copy or bundle a repository, model or dataset until its exact commercial-use terms have been reviewed.

Candidate technologies to evaluate:
- OpenCV for image processing and calibration.
- PyTorch for model training.
- ONNX Runtime for Windows inference.
- CameraChessWeb / ChessCam as a research reference and possible component source, subject to its AGPL-3.0 licensing implications.
- Stockfish for chess analysis, subject to GPL-3.0 compliance.
- A chess rules library for move legality, FEN and PGN.

Do not assume:
- A permissive library licence covers separately downloaded model weights.
- A model licence covers its training dataset.
- A separate process automatically eliminates copyleft obligations.
- A cloud-hosted use case is exempt from AGPL obligations.

Maintain a software/model/data bill of materials and obtain legal review before distribution.

## 3. Recognition pipeline

| Stage | Responsibility | Output |
|---|---|---|
| 1. Capture | USB or RTSP frame acquisition | Frames and timestamps |
| 2. Preprocess | Crop, denoise, resize, exposure handling | Normalised frames |
| 3. Board detection | Locate board boundary | Board quadrilateral |
| 4. Calibration | Perspective correction and orientation | Homography/calibration |
| 5. Square mapping | Divide board into 64 square regions | Square crops/coordinates |
| 6. Piece recognition | Classify empty square and piece type/colour | Per-square probabilities |
| 7. Position reconstruction | Combine 64 predictions and previous state | Candidate FEN positions |
| 8. Legal validation | Check candidate transitions using chess rules | Legal/invalid candidates |
| 9. Move inference | Determine candidate UCI/SAN move | Candidate move |
| 10. Reconciliation | Compare temporal observations and cloud state | Proposed event |
| 11. Event generation | Produce versioned event | Cloud ingestion payload |

Do not publish a board position merely because 64 independent square classifiers produced labels. Use previous verified state, temporal stability and legal chess transitions.

## 4. Confidence states

### Verified
A stable observation matches a legal transition from the last confirmed state. Eligible for cloud validation and publication.

### Uncertain
Several candidate positions remain plausible, a hand obscures the board, or confidence is insufficient. Hold for more frames or human review.

### Invalid
No acceptable legal transition can be reconciled. Require operator intervention.

### Disconnected
Camera feed unavailable. Preserve the last confirmed game state and raise an alert.

Confidence thresholds must be calibrated empirically. Do not hard-code an arbitrary threshold as a claim of accuracy.

## 5. Move handling

- Handle castling.
- Handle en passant.
- Handle promotions and promotion-piece choice.
- Handle captures.
- Handle temporary hand occlusion.
- Handle pieces being returned to squares or adjusted.
- Detect board rotation/orientation.
- Avoid duplicate move proposals.
- Reconcile with the last confirmed FEN and sequence.
- Preserve a correction trail.

The vision agent proposes; the cloud game service validates and commits.

## 6. Camera support roadmap

Initial:
- USB webcams.
- RTSP IP cameras.
- 1080p inputs.
- Configurable frame rate.
- Manual camera selection.
- Rotation and board orientation.
- Local video-file replay for development.
- Calibration UI and diagnostics.

Later:
- Multiple camera angles per board.
- 4K cameras.
- Network camera discovery.
- Hardware acceleration.
- Camera compatibility profiles.
- Remote diagnostics.

## 7. Dataset programme

Create an annotated dataset from authorised footage.

Suggested structure:

```text
vision-dataset/
  boards/
    wooden/
    plastic/
    electronic/
  pieces/
    staunton/
    other-supported-sets/
  lighting/
    bright/
    low-light/
    mixed/
  obstructions/
  camera-angles/
  annotated-games/
```

For each sample, record:
- Source and consent/permission reference.
- Camera model and lens where known.
- Resolution and frame rate.
- Board and piece set.
- Lighting and camera angle.
- Board orientation.
- Ground-truth FEN.
- Ground-truth PGN.
- Move timestamps.
- Occlusion and difficulty labels.
- Recognition output and confidence.

Do not use footage containing children or other identifiable people for model training without the necessary permissions and privacy review. Prefer cropped board imagery that excludes faces and unrelated personal information.

## 8. Evaluation metrics

Measure:
- Board detection success.
- Calibration success.
- Per-square classification accuracy.
- Full-position exact-match accuracy.
- Confirmed move precision.
- Move recall.
- False-confirmation rate.
- Human correction rate.
- Time to detect a move.
- Performance by camera, board set, lighting and obstruction category.

Evaluate complete games, not just isolated frames. Small per-move errors compound across a game.

Proposed initial targets for supported test conditions:
- Board detection: 99%+.
- Full position reconstruction: 98%+.
- Confirmed move accuracy: 99.5%+.
- Silent incorrect move publication: zero tolerated.

These are targets for testing, not existing performance claims. Report confidence intervals and dataset composition.

## 9. Windows process architecture

- Tauri UI: configuration, previews, calibration and operator controls.
- Python vision process: camera capture, inference and candidate generation.
- Local durable queue: unacknowledged events and sequence state.
- Device credential store: protected OS-backed storage where practical.
- Cloud client: authenticated ingestion and acknowledgements.
- Diagnostics: structured local logs and exportable support bundle.

The local queue must survive UI restarts and, where feasible, machine restarts. Protect local files and credentials.

## 10. Offline and reconciliation behaviour

When offline:
1. Continue local capture and recognition where safe.
2. Store candidate/confirmed-local observations with sequence numbers.
3. Clearly show offline status.
4. Do not claim cloud publication.
5. Reconnect with backoff.
6. Send events in order.
7. Await cloud acknowledgement.
8. Reconcile against current cloud sequence and FEN.
9. Hold conflicts for operator review.
10. Never overwrite a newer cloud state with an older local event.

## 11. v1.1 Feasibility spike and decision gates

### 11.1 Spike (weeks 1-4, parallel to platform work)
Purpose: learn whether camera recognition can reach acceptable intervention rates on real hardware before committing roadmap and customers to it.

Scope:
- 2 camera types (one budget USB webcam, one better USB or RTSP camera).
- 2 board/piece sets (e.g., one tournament-style, one club/school set).
- 3 lighting conditions (bright, mixed, low).
- At least 20 complete recorded games with ground-truth PGN, including castling, en passant and promotion.
- Fixed overhead mount and a realistic angled mount.
- Boards photographed without faces where possible; follow dataset privacy rules in section 7.

Evaluate at least: (a) an existing open-source approach used strictly in a throwaway, licence-isolated experiment, and (b) a minimal in-house OpenCV + classifier baseline. The spike produces knowledge and a report, not shippable code.

### 11.2 Spike exit report
- Per-square and full-position accuracy with sample sizes and confidence intervals.
- Complete-game results: moves confirmed, moves held as uncertain, **false confirmations**, interventions per 100 moves.
- Failure analysis by lighting, angle, occlusion and piece set.
- Calibration time per board by a non-expert.
- Hardware cost per board at the tested quality level.
- Recommendation: **Go / Go with constraints / No-go for Phase 2.**

### 11.3 Decision gate: build vs license
Before any recognition code enters the product repository, record a decision-log entry (doc 12) choosing one of: original implementation; permissive-licensed components after review; AGPL components with a deliberate compliance plan and legal advice; or no camera in the product (DGT/manual only). This gate must close by end of week 2.

### 11.4 Corrected interpretation of accuracy targets
The v1.0 targets in section 8 are retained but must be read as follows:
- Per-move accuracy compounds: at 99.5% per move, an 80-move game contains zero errors only about 67% of the time (0.995^80). Therefore the safety property is **detection**, not perfection.
- **Primary safety metric:** false-confirmation rate (a wrong move shown as confirmed). Target zero in test; any production occurrence triggers incident review.
- **Primary efficiency metric:** interventions per 100 moves (moves held as uncertain or invalid that needed an arbiter). Set a Phase 2 pilot target only after the spike baseline exists.
- **Coverage:** percentage of moves auto-confirmed without intervention, reported separately from precision.
- Report per-hardware profile; do not average across profiles.

### 11.5 Alternative input: DGT electronic boards
Evaluate DGT board integration as a MoveSource (doc 01 section 9.2). Record SDK/protocol availability, licensing, supported models, connectivity (USB/Bluetooth/serial), and customer ownership rates from interviews before committing.

# 04 — ChessVision Engine Specification

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

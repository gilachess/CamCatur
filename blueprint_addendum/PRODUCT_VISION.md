# CamCatur Product Vision

**Status:** Proposed  
**Date:** 10 November 2026

## 1. Product definition

CamCatur is a **phone-first chess game recording system with a browser-based, operating-system-agnostic management platform**.

It is intended to provide capabilities in the same product category as idChess and ChessZcam, while developing its own implementation, workflows and technical architecture.

CamCatur is not primarily a Windows webcam application. A smartphone is the primary camera and vision device. A separate web application manages devices, tournaments, games, review, storage and broadcasting.

## 2. Product surfaces

### 2.1 CamCatur Vision (mobile)

A mobile application, initially targeting Android and subsequently iOS, that:

- Uses the phone's rear camera to observe a physical chessboard.
- Guides the operator through camera placement and board calibration.
- Detects and tracks the chessboard and its 64 squares.
- Recognises piece occupancy and board positions.
- Detects candidate moves by comparing successive positions.
- Validates candidate moves against chess rules.
- Allows uncertain recognition to be reviewed or corrected.
- Creates and maintains FEN/PGN game data.
- Sends game events and device status to CamCatur Management.
- Queues data locally during temporary network interruptions and synchronises later.
- Exposes clear camera, recognition, battery, storage and connectivity status.

The mobile app must not assume that a desktop computer or external webcam is present.

### 2.2 CamCatur Management (web)

A browser-based application that:

- Runs on current supported browsers on Windows, macOS and Linux.
- Manages tournaments, rounds, sections, players, boards and game sessions.
- Registers, monitors and assigns mobile vision devices.
- Displays live game status and recognition confidence/health indicators.
- Supports manual correction and recovery workflows.
- Collects and stores game records, FEN snapshots and PGN files.
- Provides export, review and broadcasting integrations.
- Supports multiple operators and role-based access as the product matures.

The management platform must not require a Windows-only desktop client.

## 3. Target users

- Tournament organisers.
- Chief arbiters and arbiters.
- Broadcast operators.
- Chess clubs and academies.
- Individual players or coaches recording games.
- Technical operators responsible for camera deployment and maintenance.

## 4. Product principles

1. **Phone-first vision:** The phone performs capture and, where feasible, recognition locally.
2. **Browser-first management:** Management is available through a standards-based web browser.
3. **Modular recognition:** Camera capture, board calibration, piece recognition, move inference and chess validation are separate modules.
4. **Human correction is normal:** The system must expose uncertainty and provide efficient correction tools rather than silently accepting dubious moves.
5. **Offline-tolerant:** Temporary network loss must not automatically destroy a game or its captured data.
6. **Tournament-scale design:** Data models and device workflows should support growth from one board to hundreds, without claiming that scale is already proven.
7. **Hardware flexibility:** Support a tested range of phones and camera capabilities rather than assuming every phone behaves identically.
8. **Observable operation:** Operators must be able to see device health, recognition status, sync status and errors.

## 5. Product boundaries

### In scope

- Smartphone camera capture and board calibration.
- On-device board/piece/move recognition.
- Game state and PGN generation.
- Browser-based tournament and device management.
- Local buffering and server synchronisation.
- Manual correction and export.
- Live display and future broadcast integrations.

### Not assumed for the first release

- Dedicated external webcams as the primary capture method.
- A Windows-only desktop application.
- Fully autonomous recognition in every lighting, board and camera condition.
- Guaranteed support for every phone model.
- Cloud video streaming as the default recognition method.
- A claim of production support for 100–300 boards before load testing.

## 6. Success criteria

The product should ultimately demonstrate:

- A supported phone can be positioned and calibrated without specialist computer-vision knowledge.
- A complete game can be captured and reconstructed into a legal move sequence.
- Uncertain moves are clearly surfaced and can be corrected.
- A game remains recoverable through temporary connectivity loss.
- An operator can manage multiple devices and games from a browser on different desktop operating systems.
- The system can scale through measured load tests, not assumptions.

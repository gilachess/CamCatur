# 03 — Database Schema and Data Model

## 1. General conventions

- PostgreSQL is the authoritative database.
- Use UUID primary keys for externally referenced entities.
- Store timestamps in UTC; display in the tournament's configured timezone.
- Use explicit foreign keys and database constraints.
- Use migrations for every schema change.
- Apply organisation ownership checks to every tenant-owned record.
- Use Row-Level Security as defence in depth where appropriate.
- Keep flexible JSONB metadata limited to genuinely variable data.
- Do not store raw camera frames in PostgreSQL.
- Store object references and metadata in PostgreSQL; store media bytes in object storage.

## 2. Domain groups and proposed tables

### A. Identity and multi-tenancy

| Table | Important columns |
|---|---|
| organisations | id, name, slug, status, created_at |
| users | id, email, password_hash or identity_provider_id, display_name, status |
| memberships | id, organisation_id, user_id, status |
| roles | id, organisation_id, name |
| permissions | id, code, description |
| role_permissions | role_id, permission_id |
| membership_roles | membership_id, role_id |
| invitations | id, organisation_id, email, token_hash, expires_at |
| audit_logs | id, organisation_id, actor_id, action, resource_type, resource_id, metadata, created_at |

A user can belong to multiple organisations. Do not put a single organisation_id directly on the user as the only membership relationship.

### B. Tournament operations

| Table | Important columns |
|---|---|
| tournaments | id, organisation_id, name, slug, status, timezone, start_at, end_at, visibility |
| tournament_sections | id, tournament_id, name, format, time_control |
| rounds | id, section_id, round_number, status, scheduled_at |
| tournament_players | id, tournament_id, player_id, registration_status, seed |
| pairings | id, round_id, board_number, white_player_id, black_player_id, status |
| results | id, pairing_id, result_code, submitted_by, approved_by, created_at |
| standings | id, section_id, player_id, points, rank, tie_break_data, calculated_at |

A tournament can contain multiple sections. Pairings belong to a round/section, not directly to the tournament alone.

### C. Player profiles

| Table | Important columns |
|---|---|
| players | id, display_name, federation_id, country_code |
| player_ratings | id, player_id, rating_type, rating, effective_date, source |
| player_profiles | player_id, biography, public_visibility |
| player_guardians | id, player_id, guardian_user_id, relationship |
| player_documents | id, player_id, storage_key, document_type, access_policy |

Minimise collection and public exposure of personal data, especially for minors. Do not expose guardian contact details or identity documents on public pages.

### D. Boards, cameras and agents

| Table | Important columns |
|---|---|
| boards | id, tournament_id, board_number, status, location_label |
| cameras | id, organisation_id, device_name, camera_type, status |
| camera_sessions | id, camera_id, agent_id, connected_at, disconnected_at |
| camera_calibrations | id, camera_id, calibration_data, version, created_at |
| agents | id, organisation_id, device_id, name, version, status, revoked_at |
| agent_heartbeats | id, agent_id, observed_at, status, diagnostics |
| board_assignments | id, board_id, pairing_id, camera_id, valid_from, valid_to |

A camera must be reusable across rounds. Avoid permanently coupling one physical camera to one game.

### E. Games and chess data

| Table | Important columns |
|---|---|
| games | id, pairing_id, board_id, status, current_fen, current_sequence, started_at, ended_at |
| moves | id, game_id, ply, uci, san, fen_after, source, confirmed_at |
| game_events | id, game_id, sequence, event_id, event_type, schema_version, payload, occurred_at, received_at |
| game_snapshots | id, game_id, sequence, fen, pgn_offset or archive reference, created_at |
| game_analysis | id, game_id, engine_name, engine_version, depth, evaluation, principal_variation |
| game_comments | id, game_id, author_id, comment, visibility |
| game_archives | id, game_id, pgn_storage_key, checksum, created_at |
| correction_events | id, game_id, operator_id, old_state, new_state, reason, created_at |

Recommended constraints:
- UNIQUE(game_id, ply) on moves.
- UNIQUE(game_id, sequence) on game_events.
- UNIQUE(game_id, event_id) where appropriate.
- UNIQUE(round_id, board_number) on pairings.
- Validate move legality in application/domain logic and maintain database consistency.

### F. Broadcasting

| Table | Important columns |
|---|---|
| broadcasts | id, tournament_id, name, status, visibility |
| broadcast_games | id, broadcast_id, game_id, featured, sort_order |
| broadcast_settings | id, broadcast_id, delay_seconds, theme, settings |
| broadcast_sessions | id, broadcast_id, started_at, ended_at |
| stream_sources | id, broadcast_id, protocol, source_reference, status |
| stream_recordings | id, broadcast_id, storage_key, duration, checksum |
| broadcast_analytics | id, broadcast_id, sampled_at, viewers, metrics |

A tournament can have several broadcast sessions. A broadcast can include many games.

### G. Media and sponsorship

| Table | Important columns |
|---|---|
| media_assets | id, organisation_id, storage_key, media_type, size_bytes, checksum |
| sponsors | id, organisation_id, name, logo_asset_id |
| sponsorships | id, tournament_id, sponsor_id, placement, start_at, end_at |
| announcements | id, tournament_id, message, status, published_at |

### H. Billing and entitlements

| Table | Important columns |
|---|---|
| plans | id, name, price_minor_units, currency, billing_period |
| subscriptions | id, organisation_id, plan_id, provider, provider_subscription_id, status |
| entitlements | id, plan_id, feature_code, limit_value |
| usage_records | id, organisation_id, metric, quantity, period_start, period_end |
| invoices | id, subscription_id, provider_invoice_id, amount_minor_units, status |
| payment_events | id, provider_event_id, event_type, payload, processed_at |

Use a unique constraint on provider event IDs to prevent duplicate webhook processing.

### I. AI and operations

| Table | Important columns |
|---|---|
| model_versions | id, model_name, version, checksum, licence_reference |
| recognition_jobs | id, camera_session_id, status, started_at, finished_at |
| recognition_observations | id, job_id, board_position, confidence, observation_data |
| system_alerts | id, organisation_id, severity, message, status, created_at |
| feature_flags | id, organisation_id, feature_code, enabled, configuration |

## 3. Indexing strategy

Start with indexes for:
- All foreign keys used in joins.
- tournaments(organisation_id, status).
- rounds(section_id, round_number).
- pairings(round_id, board_number).
- games(board_id, status).
- moves(game_id, ply).
- game_events(game_id, sequence).
- game_events(event_id).
- agents(organisation_id, status).
- camera_sessions(camera_id, connected_at).
- broadcasts(tournament_id, status).

Review actual query plans before adding extensive indexes.

## 4. Event retention and partitioning

Game events may become a high-volume table. Begin with a simple indexed table. Introduce time- or hash-based partitioning only when measured volume and retention requirements justify it.

Keep a durable archive/export path for completed tournaments. Do not delete event history merely to reduce live query load.

## 5. Migration requirements

- Use Alembic or an equivalent migration tool.
- Never rely on an agent's local database state as the schema source of truth.
- Review migrations before production.
- Prefer expand/migrate/contract changes for zero- or low-downtime deployment.
- Back up before destructive changes.
- Test migrations against representative staging data.

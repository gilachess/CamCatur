# 11 — Commercial, Security and Compliance Plan

## 1. Commercial positioning

Sell to organisations rather than relying only on individual players:
- Clubs.
- Schools.
- Academies.
- Tournament organisers.
- Federations.
- Event production teams.

Potential differentiators:
- One control room for the whole tournament.
- Reliable multi-board broadcasting.
- Camera recognition with human correction.
- White-label public tournament pages.
- Sponsor and OBS integration.
- Tournament archives and analytics.

## 2. Indicative pricing hypotheses

These are initial market-testing hypotheses in Malaysian ringgit, not validated prices:

| Tier | Indicative price | Initial positioning |
|---|---:|---|
| Club | RM99/month | Basic tournament management |
| Academy | RM249/month | Multiple tournaments and player management |
| Tournament | RM499/event | Live multi-board broadcast |
| Professional | RM999/event | Advanced broadcast and production |
| Federation | Custom | Larger-scale and white-label deployment |

Validate willingness to pay, support cost, camera hardware cost, cloud usage and local competitor pricing before publishing prices.

## 3. Entitlements

Represent plan capabilities in backend-controlled entitlements, for example:
- Active boards.
- Active tournaments.
- Team members.
- Recording storage.
- Broadcast delay options.
- White-label availability.
- OBS integration.
- Advanced analytics.

Never enforce limits only in the frontend. Enforce them in trusted backend services and meter actual usage.

## 4. Payment architecture

For a Malaysian launch:
- Evaluate Stripe availability and terms for the business entity.
- Evaluate suitable Malaysian payment providers.
- Support invoice-based corporate customers.
- Support recurring subscriptions and one-time event fees.
- Verify webhook signatures.
- Deduplicate provider events.
- Track failed payments and subscription status.
- Keep a provider abstraction to reduce lock-in.

Confirm current provider availability, settlement, tax and business eligibility before choosing.

## 5. Licensing compliance

Create and maintain a software, model and dataset bill of materials.

For every dependency/model/dataset record:
- Exact name and version.
- Source URL.
- Licence identifier and full text.
- Copyright notices.
- Modifications.
- Distribution method.
- Whether included in Windows/Android packages.
- Whether used only server-side.
- Model-weight licence.
- Training-data rights.
- Attribution and notice obligations.
- Legal approval status.

Special review:
- CameraChessWeb / ChessCam repository and AGPL-3.0 implications.
- Stockfish and GPL-3.0 obligations.
- Ultralytics and its AGPL/Enterprise licensing model, if considered.
- Model weights and training datasets.
- UI assets, fonts, icons and templates.

Do not assume SaaS use, separate processes or dynamic linking automatically remove all obligations. Obtain qualified legal advice before shipping a proprietary product with copyleft components.

## 6. Privacy and child safeguarding

The platform may process junior-player names, ratings, photographs, video and event participation information.

Implement:
- Data minimisation.
- Private-by-default personal fields.
- Clear tournament camera/video notices.
- Consent and permission workflow appropriate to the event and jurisdiction.
- Retention and deletion policy.
- Access controls for recordings.
- Guardian-related data protection.
- Data export and correction processes.
- Incident response.
- No public guardian contact details.
- No unnecessary facial recognition or identity inference.

Review applicable Malaysian privacy law and any other jurisdictions where customers or players are located. Obtain legal advice on obligations, cross-border processing and retention.

### Additional controls for live video and recordings

- Treat live footage and recordings as personal data where people can be identified directly or indirectly.
- Define who is responsible for venue notices and required permissions or consent before capture and publication.
- Provide camera placement/framing guidance to reduce unnecessary capture of children, spectators and bystanders.
- Make visibility explicit: private, organisation-only, unlisted or public.
- Restrict raw source footage and recording access separately from public chess game access.
- Define retention periods, deletion workflow and incident response before recording is enabled.
- Do not make public video or recording the default for junior events.
- Do not introduce facial recognition or identity inference in production.
- Obtain jurisdiction-specific legal review before pilots and release.

## 7. Security baseline

- MFA for privileged users.
- Role-based access control.
- Tenant isolation.
- Short-lived access tokens.
- Device registration and revocation.
- Secure local credential storage.
- TLS in transit.
- Encryption at rest.
- Secrets manager.
- Rate limiting.
- Audit logs.
- Backups and restore tests.
- Dependency and container scanning.
- Secure file uploads.
- Payment webhook verification.
- Incident response procedure.
- Least-privilege service accounts.

## 8. Operational readiness

Before commercial release:
- Terms of service.
- Privacy policy.
- Data processing agreement where needed.
- Support contact and escalation.
- Status/incident communication.
- Backup and disaster recovery plan.
- Customer onboarding documentation.
- Windows agent installation guide.
- Camera compatibility guide.
- Tournament operator guide.
- Security contact and vulnerability handling.
- Release and rollback process.

## 8. v1.1 Hardware, services and unit economics

The pricing in section 2 covers software only. Per-event economics must include:
- **Hardware:** cameras, mounts/stands, cables, PC or mini-PC per operator station, spare units, labels.
- **Hardware model options to test:** customer-owned; rented kit per event; bundled kit with the event fee.
- **On-site support:** setup time, operator training, staffing during rounds, travel.
- **Cloud and bandwidth:** per board-hour and per viewer-hour; video separately.
- **Payment and tax costs** for the chosen Malaysian provider.

Create a per-event unit-economics sheet (revenue, hardware amortisation, support hours, cloud cost, margin) for a 20, 50 and 100 board event. Do not publish prices until it shows a margin at each size.

### Video-specific cost accounting (future phase)

Track camera-hours and source bitrate; media ingest and processing cost; simultaneous programme outputs; recording hours and retained storage; viewer-hours and delivery/egress; production workstation hardware/network; and production operator/support hours.

Do not price video solely by board count. One composed tournament programme using selected cameras has a different cost profile from one independent video stream per board. Recording and public streaming may need separate entitlements or event charges.

## 9. v1.1 Safeguarding and privacy (junior events)

- Default public display of minors to initials or first name; organiser must opt in to full names.
- No public camera or video of players without a documented consent process.
- Retention limits for footage, frames and images, with deletion on request where applicable.
- Document the legal basis and notices under Malaysian data protection requirements and any applicable federation or school rules; obtain local legal advice before the pilot.

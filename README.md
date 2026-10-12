# LifeLink — Blood Availability & Coordination Platform

A mobile-friendly, compliance-first starter for coordinating hospitals, licensed blood centres, and voluntary donors. It is **not** a medical device, blood bank, transfusion decision system, or live blood inventory network. Seed data is fictional.

## Critical legal and safety boundaries

- The default app is a coordination and inventory-visibility prototype. It does not sell blood, charge a commission on blood, determine donor eligibility, match transfusions, or guarantee stock.
- In India, blood centres require appropriate licensing and are subject to national/state rules. Review current requirements with the relevant State Licensing Authority, State Blood Transfusion Council, and qualified counsel before launch. Official starting points: [CDSCO blood bank FAQ](https://cdsco.mohfw.gov.in/opencms/opencms/en/FAQ/) and [e-RaktKosh](https://eraktkosh.mohfw.gov.in/eraktkoshPortal/).
- Blood components may have regulated processing charges. Do not implement a transaction commission on blood or components unless counsel confirms a lawful model. If permitted, use a separately contracted, transparent software/service fee that does not delay emergency care.
- This starter does not upload donor health reports. Health data is sensitive; a production system needs explicit consent, purpose limitation, strict role-based access, encryption/key management, audit logs, retention/deletion rules, breach response, and legal review.
- Inventory is demo-only until verified centres update it. Hospitals must confirm availability directly and follow clinical compatibility/crossmatch protocols.

## Included

- Responsive premium public website and inventory search
- Account registration/login with bcrypt password hashing
- Roles: `hospital`, `blood_centre`, `donor`, `admin`
- Server-side authorization checks and admin verification endpoints
- Blood-centre inventory updates and expiry dates
- Hospital requests and centre-side status transitions
- Voluntary donor profile with opt-in contact permission (no health-report storage)
- SQLite persistence, Helmet headers, rate limiting, secure session cookie settings
- Seed demo users and inventory; automated validation/authorization tests

## Requirements and local setup

- Node.js 20+
- npm

```bash
cp .env.example .env
# Windows PowerShell: Copy-Item .env.example .env
npm install
npm test
npm start
```

Open http://localhost:3000.

### Demo accounts

Created only when `SEED_DEMO=true` and database is empty. Change/remove these immediately; never use them in production.

- Admin: `admin@lifelink.local` / `DemoAdmin!2026`
- Hospital: `hospital@lifelink.local` / `DemoHospital!2026`
- Blood centre: `centre@lifelink.local` / `DemoCentre!2026`
- Donor: `donor@lifelink.local` / `DemoDonor!2026`

## Environment

- `SESSION_SECRET`: use a long random secret (32+ characters minimum)
- `DATABASE_PATH`: database file path; prefer managed PostgreSQL for production
- `NODE_ENV=production`: enables secure cookies; requires HTTPS/reverse proxy
- `SEED_DEMO=false`: recommended in production

## API overview

- `GET /api/health`
- `GET /api/inventory?bloodGroup=O%2B&component=Red%20Blood%20Cells&district=...`
- `POST /api/auth/register` (self-registration is limited to hospital/donor; blood centres require a controlled onboarding process)
- `POST /api/auth/login`, `POST /api/auth/logout`, `GET /api/me`
- `POST /api/donor-profile` (donor only)
- `POST /api/inventory` (verified centre only)
- `POST /api/requests`, `GET /api/requests`
- `PATCH /api/requests/:id/status` (centre/admin)
- `GET /api/admin/pending-users`, `PATCH /api/admin/users/:id/verify`

## Important implementation notes

This is a useful starter, not a finished commercial platform. It has no payment gateway, no commission engine, no email/SMS notifications, no health report upload, and no real-time integration with e-RaktKosh or hospital systems. The UI currently focuses on public inventory discovery and account entry; request/inventory management APIs are available for integration into role-specific dashboards. Before production, add CSRF protection appropriate to the session design, MFA for privileged accounts, verified licence-document review with renewal alerts, email/phone verification, password reset, session revocation, detailed audit access, privacy/consent workflows, stronger error monitoring, and independent penetration testing.

## Production launch checklist

1. Obtain written regulatory/legal guidance on centre onboarding, allowed charges, privacy, emergency workflows, and state-specific rules.
2. Verify each blood centre licence and renewal date before allowing stock updates.
3. Migrate SQLite to managed PostgreSQL with migrations and encrypted backups for multi-instance production.
4. Add MFA for admin/centre staff, email/phone verification, password reset, and login anomaly monitoring.
5. Implement CSRF protection, CSP review, encryption in transit/at rest, immutable audit logs, retention and deletion schedules, incident response, and least privilege.
6. Add backups/restore drills, uptime monitoring, error tracking, rate-limit tuning, and disaster recovery.
7. Keep transfusion decisions, donor eligibility, compatibility testing, and release decisions with qualified clinical staff and licensed centres.
8. Integrate official inventory sources only with written permission and a data contract; never scrape or imply official integration.
9. No payment gateway or blood commission is enabled. Add only a legally reviewed, separately contracted service-fee workflow if permitted.
10. Deploy over HTTPS, store secrets in your host's secret manager, disable demo seeding, and run tests/dependency scans in CI.

## Deployment

SQLite and local sessions require persistent storage, so this starter should only be deployed to a platform that supports durable disks. For commercial multi-instance use, migrate to PostgreSQL first. Set `NODE_ENV=production`, strong `SESSION_SECRET`, `SEED_DEMO=false`, and persistent `DATABASE_PATH`; configure HTTPS and verify secure cookies behind the reverse proxy. Run `npm test` during CI and deploy to staging before production.

## Disclaimer

Development starter only; not legal, clinical, security, or regulatory advice. Do not use it as the sole source of live blood availability or to make patient-care decisions.

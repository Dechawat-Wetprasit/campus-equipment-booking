# Campus Equipment Booking API

## Overview
A REST API for reserving shared campus resources (cameras, projectors, meeting rooms). The system prevents the same equipment from being booked for overlapping times.

## Tech Stack
- **Framework:** Hono (TypeScript)
- **Runtime & Deployment:** Cloudflare Workers
- **Database:** Cloudflare D1 (Serverless SQL Database)

## Setup & Run Locally

```bash
# 1. Install dependencies
npm install

# 2. Login to Cloudflare (if not already logged in)
npx wrangler login

# 3. Create the database (if not already created)
# npx wrangler d1 create booking_db
# (Update wrangler.toml with the generated database_id)

# 4. Initialize schema and seed data locally
npx wrangler d1 execute booking_db --local --file=./schema.sql

# 5. Run in development mode
npm run dev
```

The local server will start at `http://127.0.0.1:8787`.  
The API base URL is `http://127.0.0.1:8787/api`.

## Production Deployment

```bash
# 1. Apply schema to production D1 database
npx wrangler d1 execute booking_db --remote --file=./schema.sql

# 2. Deploy the worker
npm run deploy
```

**Live Deployed URL:** `https://campus-equipment-booking.plant-watering-tracker.workers.dev/api`

## Project Structure

```
campus-equipment-booking/
├── src/
│   ├── index.ts          # Entry point, Cloudflare bindings, server setup
│   └── routes/
│       ├── equipment.ts  # Equipment endpoints (D1 queries)
│       └── bookings.ts   # Bookings CRUD endpoints (D1 queries)
├── scripts/
│   └── run_instructor_tests.js # Automated test script for instructor's curl commands
├── schema.sql            # SQLite schema and seed data
├── wrangler.toml         # Cloudflare Workers configuration & D1 binding
├── API_CONTRACT.md       # API contract documentation
├── AI_LOG.md             # AI usage log
├── QUALITY_GATE_REVIEW.md# Quality gate checks and improvements
├── TEST_EVIDENCE.md      # Evidence of test execution
├── package.json
└── tsconfig.json
```

## Database Schema / ERD

```
┌──────────────────────┐       ┌───────────────────────────────┐
│      equipment       │       │          bookings             │
├──────────────────────┤       ├───────────────────────────────┤
│ id TEXT (PK)         │◄──FK──│ id TEXT (PK)                  │
│ name TEXT NOT NULL    │       │ equipment_id TEXT NOT NULL     │
│ location TEXT NOT NULL│       │ borrower_name TEXT NOT NULL    │
│ created_at TEXT       │       │ start_at TEXT NOT NULL         │
└──────────────────────┘       │ end_at TEXT NOT NULL           │
                               │ purpose TEXT NOT NULL          │
                               │ created_at TEXT                │
                               │ updated_at TEXT                │
                               └───────────────────────────────┘
```

**Relationship:** One equipment → Many bookings (1:N)

### Business Rules
1. `equipmentId` must reference an existing equipment record
2. `startAt` must be before `endAt`
3. Bookings for the same equipment **must not overlap** (checked on both create and update)
4. All SQL queries use **parameter binding** (`stmt.bind()`) to prevent SQL injection

## Seed Data
The following equipment records are auto-created when executing `schema.sql`:

| ID    | Name                | Location    |
|-------|---------------------|-------------|
| eq-1  | Projector A         | Building 1  |
| eq-2  | Camera Canon EOS R5 | Building 2  |
| eq-3  | Meeting Room 301    | Building 3  |

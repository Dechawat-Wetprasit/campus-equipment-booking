# AI Log — Midterm Practical Lab Test

## AI Tool Used
- **Tool:** Google Antigravity (Gemini-based AI assistant)
- **Purpose:** Development assistant for API design, implementation, and testing

---

## Prompt & Usage Log

### 1. Initial Project Setup
- **Prompt:** "Set up a new Hono/TypeScript project for a REST API. Create a SQLite database schema for 'equipment' and 'bookings' with a 1:N relationship based on the attached exam_brief_en.md requirements. Include initial seed data for equipment."
- **What AI provided:** Project scaffolding using Hono + TypeScript + SQLite, database schema, seed data
- **What I used:** Project structure, Hono template creation command
- **What I verified:**
  - Reviewed the generated `package.json` to confirm correct dependencies
  - Ensured the schema matches the exam brief requirements (equipment → bookings 1:N relationship)
  - Migrated the database from local SQLite to Cloudflare D1 Serverless SQL to support live cloud deployment.

### 2. API Contract Design
- **Prompt:** Designed endpoints based on the exam brief contract
- **What AI provided:** Full API contract matching the exam specification
- **What I verified:**
  - All required endpoints are present (GET/POST/PATCH/DELETE for bookings, GET for equipment)
  - Status codes match the exam brief: 200, 201, 204, 400, 404, 409
  - Error response format is `{ "error": "message" }` as required

### 3. Database Schema & Business Rules
- **Prompt:** Create SQLite schema with overlap prevention
- **What AI provided:** SQL CREATE TABLE statements with foreign keys, indexes, and overlap checking logic
- **What I verified:**
  - Foreign key constraint on `equipment_id` references `equipment(id)`
  - Overlap detection logic: `existingStart < newEnd AND existingEnd > newStart` — verified this is mathematically correct
  - Parameter binding is used everywhere (no SQL string concatenation)

### 4. CRUD Implementation
- **Prompt:** Implement all booking CRUD endpoints with validation
- **What AI provided:** Complete route handlers for all endpoints
- **What I verified:**
  - POST returns 201 with the created object
  - DELETE returns 204 with empty body
  - PATCH does partial updates (merges with existing values)
  - All validation checks run before database operations
  - Overlap is checked for both create AND update operations

### 5. Testing
- **Prompt:** Generate curl commands for testing
- **What AI provided:** Test commands covering happy paths and error cases
- **What I verified:**
  - Ran each curl command and verified the HTTP status code and response body
  - Confirmed overlap detection works by creating two overlapping bookings
  - Confirmed 404 returns for non-existent resources

---

## Summary

AI was used as a development accelerator for scaffolding, boilerplate, and documentation. All critical logic (overlap detection, validation, error handling) was reviewed and verified. I can explain every design decision and implementation detail in this project.

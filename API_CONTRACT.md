# API Contract — Campus Equipment Booking API

**Base URL:** `http://localhost:8787/api`

## Assumptions
1. Equipment records are pre-seeded and read-only (no CRUD for equipment creation/deletion via API in this scope).
2. All datetimes are in ISO 8601 format (e.g., `2026-10-20T09:00:00.000Z`).
3. Booking IDs are auto-generated with the format `bk-{uuid-8-chars}`.
4. Overlap detection uses the logic: two bookings overlap if `existingStart < newEnd AND existingEnd > newStart`.
5. PATCH allows partial updates — only provided fields are changed; omitted fields keep their current values.
6. DELETE returns `204 No Content` with an empty body on success.

---

## Equipment Endpoints

### `GET /api/equipment`
List all available equipment.

**Response:** `200 OK`
```json
[
  { "id": "eq-1", "name": "Projector A", "location": "Building 1" },
  { "id": "eq-2", "name": "Camera Canon EOS R5", "location": "Building 2" },
  { "id": "eq-3", "name": "Meeting Room 301", "location": "Building 3" }
]
```

### `GET /api/equipment/:id`
Get a single equipment by ID.

**Response:** `200 OK`
```json
{ "id": "eq-1", "name": "Projector A", "location": "Building 1" }
```

**Error:** `404 Not Found`
```json
{ "error": "Equipment with id 'eq-99' not found" }
```

---

## Bookings Endpoints

### `GET /api/bookings`
List all bookings. Supports optional query parameter `?equipmentId=eq-1` to filter.

**Response:** `200 OK`
```json
[
  {
    "id": "bk-abc12345",
    "equipmentId": "eq-1",
    "borrowerName": "Somchai Jaidee",
    "startAt": "2026-10-20T09:00:00.000Z",
    "endAt": "2026-10-20T11:00:00.000Z",
    "purpose": "Class presentation",
    "createdAt": "2026-10-06T06:30:00.000Z",
    "updatedAt": "2026-10-06T06:30:00.000Z"
  }
]
```

### `GET /api/bookings/:id`
Get a single booking by ID.

**Response:** `200 OK`
```json
{
  "id": "bk-abc12345",
  "equipmentId": "eq-1",
  "borrowerName": "Somchai Jaidee",
  "startAt": "2026-10-20T09:00:00.000Z",
  "endAt": "2026-10-20T11:00:00.000Z",
  "purpose": "Class presentation",
  "createdAt": "2026-10-06T06:30:00.000Z",
  "updatedAt": "2026-10-06T06:30:00.000Z"
}
```

**Error:** `404 Not Found`
```json
{ "error": "Booking with id 'bk-notexist' not found" }
```

### `POST /api/bookings`
Create a new booking.

**Request Body:**
```json
{
  "equipmentId": "eq-1",
  "borrowerName": "Somchai Jaidee",
  "startAt": "2026-10-20T09:00:00.000Z",
  "endAt": "2026-10-20T11:00:00.000Z",
  "purpose": "Class presentation"
}
```

**Response:** `201 Created` — returns the created booking object

**Errors:**
| Status | Condition |
|--------|-----------|
| `400`  | Missing or invalid fields (e.g., empty `borrowerName`, `startAt >= endAt`, invalid ISO date) |
| `404`  | `equipmentId` does not exist |
| `409`  | Booking time conflicts with an existing booking for the same equipment |

### `PATCH /api/bookings/:id`
Update an existing booking (partial update).

**Request Body** (all fields optional):
```json
{
  "borrowerName": "Updated Name",
  "startAt": "2026-10-20T10:00:00.000Z",
  "endAt": "2026-10-20T12:00:00.000Z"
}
```

**Response:** `200 OK` — returns the updated booking object

**Errors:**
| Status | Condition |
|--------|-----------|
| `400`  | Invalid dates or `startAt >= endAt` |
| `404`  | Booking or new `equipmentId` not found |
| `409`  | Updated time conflicts with another existing booking |

### `DELETE /api/bookings/:id`
Delete a booking.

**Response:** `204 No Content` (empty body)

**Error:** `404 Not Found`
```json
{ "error": "Booking with id 'bk-notexist' not found" }
```

---

## Error Format

All error responses follow this format:
```json
{ "error": "A message understandable to a user or developer" }
```

## HTTP Status Code Summary

| Code | Meaning |
|------|---------|
| `200` | Success |
| `201` | Created successfully |
| `204` | Deleted successfully (no content) |
| `400` | Bad request — missing or invalid data |
| `404` | Resource not found |
| `409` | Conflict — booking time overlaps |
| `500` | Internal server error |

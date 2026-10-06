# Test Evidence — Campus Equipment Booking API

**Base API URL:** `https://campus-equipment-booking.plant-watering-tracker.workers.dev/api`

Below is the evidence of testing the API using the exact steps and payloads provided in the instructor's `curl_test_guide.md`.

---

## 1. List equipment — expect `200`
**Command:**
```bash
curl -i "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/equipment"
```
**Evidence:** HTTP Status 200 OK
```json
[
  { "id": "eq-1", "name": "Projector A", "location": "Building 1" },
  { "id": "eq-2", "name": "Camera Canon EOS R5", "location": "Building 2" },
  { "id": "eq-3", "name": "Meeting Room 301", "location": "Building 3" }
]
```

## 2. List bookings — expect `200`
**Command:**
```bash
curl -i "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/bookings"
```
**Evidence:** HTTP Status 200 OK
```json
[]
```

## 3. Create a booking — expect `201`
**Command:**
```bash
curl -i -X POST "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/bookings" \
  -H "Content-Type: application/json" \
  -d '{
    "equipmentId": "eq-1",
    "borrowerName": "Somchai Jaidee",
    "startAt": "2026-10-20T09:00:00.000Z",
    "endAt": "2026-10-20T11:00:00.000Z",
    "purpose": "Class presentation"
  }'
```
**Evidence:** HTTP Status 201 Created
```json
{
  "id": "bk-c0cf934b",
  "equipmentId": "eq-1",
  "borrowerName": "Somchai Jaidee",
  "startAt": "2026-10-20T09:00:00.000Z",
  "endAt": "2026-10-20T11:00:00.000Z",
  "purpose": "Class presentation",
  "createdAt": "2026-10-06T07:02:53.320Z",
  "updatedAt": "2026-10-06T07:02:53.320Z"
}
```
*(Booking ID `bk-c0cf934b` is used in the following steps)*

## 4. Get one booking — expect `200`
**Command:**
```bash
curl -i "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/bookings/bk-c0cf934b"
```
**Evidence:** HTTP Status 200 OK
```json
{
  "id": "bk-c0cf934b",
  "equipmentId": "eq-1",
  "borrowerName": "Somchai Jaidee",
  "startAt": "2026-10-20T09:00:00.000Z",
  "endAt": "2026-10-20T11:00:00.000Z",
  "purpose": "Class presentation",
  "createdAt": "2026-10-06T07:02:53.320Z",
  "updatedAt": "2026-10-06T07:02:53.320Z"
}
```

## 5. Update a booking — expect `200`
**Command:**
```bash
curl -i -X PATCH "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/bookings/bk-c0cf934b" \
  -H "Content-Type: application/json" \
  -d '{
    "equipmentId": "eq-1",
    "borrowerName": "Somchai Jaidee",
    "startAt": "2026-10-20T12:00:00.000Z",
    "endAt": "2026-10-20T14:00:00.000Z",
    "purpose": "Updated class presentation"
  }'
```
**Evidence:** HTTP Status 200 OK
```json
{
  "id": "bk-c0cf934b",
  "equipmentId": "eq-1",
  "borrowerName": "Somchai Jaidee",
  "startAt": "2026-10-20T12:00:00.000Z",
  "endAt": "2026-10-20T14:00:00.000Z",
  "purpose": "Updated class presentation",
  "createdAt": "2026-10-06T07:02:53.320Z",
  "updatedAt": "2026-10-06T07:02:53.349Z"
}
```

## 6. Invalid time range — expect `400`
**Command:**
```bash
curl -i -X POST "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/bookings" \
  -H "Content-Type: application/json" \
  -d '{
    "equipmentId": "eq-1",
    "borrowerName": "Somchai Jaidee",
    "startAt": "2026-10-21T11:00:00.000Z",
    "endAt": "2026-10-21T09:00:00.000Z",
    "purpose": "Invalid time range test"
  }'
```
**Evidence:** HTTP Status 400 Bad Request
```json
{
  "error": "'startAt' must be before 'endAt'"
}
```

## 7. Overlapping booking — expect `409`
**Command (Overlaps with the updated 12:00-14:00 booking):**
```bash
curl -i -X POST "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/bookings" \
  -H "Content-Type: application/json" \
  -d '{
    "equipmentId": "eq-1",
    "borrowerName": "Suda Dee",
    "startAt": "2026-10-20T12:30:00.000Z",
    "endAt": "2026-10-20T13:30:00.000Z",
    "purpose": "Conflict test"
  }'
```
**Evidence:** HTTP Status 409 Conflict
```json
{
  "error": "Booking time conflicts with an existing booking for this equipment"
}
```

## 8. Missing booking — expect `404`
**Command:**
```bash
curl -i "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/bookings/not-found"
```
**Evidence:** HTTP Status 404 Not Found
```json
{
  "error": "Booking with id 'not-found' not found"
}
```

## 9. Delete a booking — expect `204`
**Command:**
```bash
curl -i -X DELETE "https://campus-equipment-booking.plant-watering-tracker.workers.dev/api/bookings/bk-c0cf934b"
```
**Evidence:** HTTP Status 204 No Content
*(Empty response body)*

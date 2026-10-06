# Quality Gate Review — Midterm Practical Lab Test

## Review Record

| Quality Gate area | Finding | Action taken | Evidence |
|---|---|---|---|
| **Reliability/Accuracy** | SQL Injection vulnerability could exist if request data is concatenated into SQL strings. | Reviewed all SQL operations and ensured consistent use of parameter binding (`.bind()`) everywhere. | Code search confirmed zero occurrences of string concatenation in `bookings.ts` and `equipment.ts`. All Cloudflare D1 queries use `?` placeholders. |
| **Reasoning/You Own It** | The overlap detection logic needs to be mathematically sound for both create and update operations. | Verified the `start_at < ? AND end_at > ?` logic and added `AND id != ?` to exclude the current booking during updates. | `curl` tests in `TEST_EVIDENCE.md` return `409 Conflict` for overlapping times and `200 OK` for valid partial updates. |
| **Reliability** | The server could crash with an unhandled exception if the POST/PATCH request body is not valid JSON. | Leveraged Hono's global `app.onError` to catch `SyntaxError` from invalid JSON payloads. | Sending malformed text via `curl` returns a `400 Bad Request` with `{ "error": "Request body must be valid JSON" }` instead of crashing. |
| **Accuracy** | The initial input validation allowed empty spaces (e.g., `"borrowerName": "   "`). | Added `.trim() === ""` checks to ensure required strings like `borrowerName` and `purpose` are not empty. | `curl` tests with empty strings return `400 Bad Request` appropriately. |
| **Reliability** | The initial local SQLite setup (sql.js) was not suitable for a production cloud deployment and lost data on restarts. | Migrated the entire database architecture to use Cloudflare D1 Serverless SQL for robust, persistent cloud storage. | Data persists correctly on the deployed Cloudflare API, passing all 9 `curl` tests from the instructor's guide. |

---

## Submission Decision

- **READY:** All required work is complete, tests have been checked, and I can explain the submission.

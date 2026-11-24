\# Restful Booker API Tests



REST API test suite for the \[Restful Booker](https://restful-booker.herokuapp.com/apidoc/index.html) demo API, built with \*\*Postman\*\* and run in CI with \*\*Newman\*\* + \*\*GitHub Actions\*\*.



\## What it covers



A full CRUD flow with request chaining:



| # | Request | What is checked |

|---|---------|-----------------|

| 1 | `POST /auth` | Returns a non-empty token (saved as `{{token}}`) |

| 2 | `POST /booking` | Status, booking ID returned (saved as `{{bookingId}}`), saved data matches the request |

| 3 | `GET /booking/{{bookingId}}` | Status, field presence and data types |

| 4 | `PUT /booking/{{bookingId}}` | Token auth via cookie, full response matches the update (deep equality) |

| 5 | `DELETE /booking/{{bookingId}}` | Booking is deleted |



\## Design choices



\- \*\*Tests create their own data.\*\* The API is a shared public sandbox that resets often, so each run creates the booking it reads, updates and deletes instead of relying on existing IDs.

\- \*\*Types over values for shared data.\*\* Exact values are only asserted on data the test created itself.



\## Findings (API behaviour that deviates from REST conventions)



| Case | Expected | Actual |

|------|----------|--------|

| `POST /booking` with empty body | `400 Bad Request` | `500 Internal Server Error` |

| `POST /booking` (resource created) | `201 Created` | `200 OK` |

| `DELETE` on a non-existent booking | `404 Not Found` | `405 Method Not Allowed` |

| `DELETE` success | `200 OK` / `204 No Content` | `201 Created` |



\## Run locally



```bash

npm install -g newman

newman run "Restful Booker API Tests.postman\_collection.json"

```



\## CI



`.github/workflows/api-tests.yml` runs the collection on every push and pull request to `main`, and uploads JUnit results as an artifact.



> Credentials in the collection are the public demo credentials published by Restful Booker. Real credentials should be stored in CI secrets, never in the repo.


# REST API Testing — Results

> **Status: Not executed from this document.** Run the included Postman collection and replace this template with observed results. JSONPlaceholder is a public fake API; write operations are simulated and do not persist like production writes.

## 1. Environment
- Tool/version: Record Postman version
- Base URL: `https://jsonplaceholder.typicode.com`
- Execution date:
- OS:
- Collection version:
- Network/service limitations:

## 2. Planned tests
| ID | Request | Purpose | Expected behavior | Actual status | Result |
|---|---|---|---|---|---|
| API-001 | GET `/posts` | Retrieve list | HTTP 200; JSON array with expected fields | Record after run | Not Run |
| API-002 | GET `/posts/1` | Retrieve one post | HTTP 200; returned ID matches request | Record after run | Not Run |
| API-003 | POST `/posts` | Simulate create | Typically HTTP 201; response includes submitted fields and ID | Record after run | Not Run |
| API-004 | PUT `/posts/1` | Simulate update | Typically HTTP 200; response reflects submitted update | Record after run | Not Run |
| API-005 | DELETE `/posts/1` | Simulate delete | Typically HTTP 200 for this practice service | Record after run | Not Run |
| API-006 | GET `/posts/999999` | Missing resource | HTTP 404 | Record after run | Not Run |

## 3. Execution summary
| Metric | Actual value |
|---|---:|
| Planned tests | 6 |
| Passed | To be counted |
| Failed | To be counted |
| Blocked | To be counted |
| Not run | To be counted |

These are planned requests, not claims that the tests passed.

## 4. Findings
For failures, record request/environment, expected vs actual status/body, reproduction steps, sanitized evidence, and whether the issue appears to be service, network, expectation, or application related. Do not log a defect solely because a public service is temporarily unavailable.

## 5. Limitations
- Public services can change or become unavailable.
- JSONPlaceholder simulates write operations; data is not persisted like a real backend.
- This is a learning demonstration, not security, performance, or production certification.
- Verify current behavior at execution time.

**Conclusion: Pending execution.**

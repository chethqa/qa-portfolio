# Vehicle Management & Booking System: Defect-Based Test Cases

A set of **27 manual test cases** for a vehicle rental / booking system, written around the kinds of defects that are hard to find: race conditions, boundary times, timezone and DST handling, payment edge cases, access control and data integrity.

Each case starts from a defect pattern, then defines preconditions, test data, reproducible steps and an expected result, so a tester can run it and a developer can see exactly what should have happened.

> **Note:** These are sample test cases that represent common bug patterns in booking systems. They are not taken from a real company's bug list. Business rules used in the expected results (24h free cancellation, 15 min grace period, 12% tax, and so on) are stated assumptions that would be confirmed against real requirements.

## Summary

| Severity | Count |
|---|---|
| Critical | 9 |
| High | 12 |
| Medium | 6 |
| **Total** | **27** |

**Modules covered:** Booking, Date/Time, Availability, Cancellation, Return, Driver Eligibility, Pricing, Payment, Access Control, Reassignment, Concurrency, Data Integrity, Maintenance, Notifications, Import, Session, Reports

## Test case index

| ID | Module | Defect scenario | Severity | Priority |
|---|---|---|---|---|
| [TC-001](test-cases/TEST_CASES.md#tc-001--critical--p1) | Booking | Double booking: two users reserve the same vehicle and slot at the same moment, and both get a confirmation | Critical | P1 |
| [TC-002](test-cases/TEST_CASES.md#tc-002--critical--p1) | Booking | Partial overlap not detected: a new booking that starts inside an existing booking is accepted | Critical | P1 |
| [TC-003](test-cases/TEST_CASES.md#tc-003--high--p1) | Booking | Back-to-back bookings wrongly rejected (or wrongly allowed past the buffer) at the exact boundary time | High | P1 |
| [TC-004](test-cases/TEST_CASES.md#tc-004--high--p1) | Date/Time | Booking times shift by hours when the user's timezone differs from the server timezone | High | P1 |
| [TC-005](test-cases/TEST_CASES.md#tc-005--high--p2) | Date/Time | Bookings that cross midnight or span multiple days show wrong duration, day count or price | High | P2 |
| [TC-006](test-cases/TEST_CASES.md#tc-006--medium--p3) | Date/Time | Booking across a DST change or month/year end calculates the wrong time or availability | Medium | P3 |
| [TC-007](test-cases/TEST_CASES.md#tc-007--critical--p1) | Availability | A vehicle marked 'Under Maintenance' can still be booked through the API or a stale search result | Critical | P1 |
| [TC-008](test-cases/TEST_CASES.md#tc-008--high--p2) | Availability | Search and filter results stay stale after a booking is made (cached availability) | High | P2 |
| [TC-009](test-cases/TEST_CASES.md#tc-009--critical--p1) | Booking | Extending a booking succeeds even though another booking starts right after it | Critical | P1 |
| [TC-010](test-cases/TEST_CASES.md#tc-010--high--p1) | Cancellation | Cancelled booking does not release the vehicle slot, or the cancellation fee is wrong at the 24-hour boundary | High | P1 |
| [TC-011](test-cases/TEST_CASES.md#tc-011--high--p2) | Return | Late return fee wrongly charged inside the grace period or calculated with the wrong rounding | High | P2 |
| [TC-012](test-cases/TEST_CASES.md#tc-012--medium--p2) | Return | Odometer or fuel level accepts impossible values at return (lower than pickup reading, negative, non-numeric) | Medium | P2 |
| [TC-013](test-cases/TEST_CASES.md#tc-013--critical--p1) | Driver Eligibility | A driver whose license expires between the booking date and the pickup date is still allowed to pick up the vehicle | Critical | P1 |
| [TC-014](test-cases/TEST_CASES.md#tc-014--high--p2) | Driver Eligibility | Driver can book a vehicle class that is not allowed by the license type or age rule | High | P2 |
| [TC-015](test-cases/TEST_CASES.md#tc-015--high--p1) | Pricing | Total price is wrong when a promo code, seasonal rate and tax are combined, or when rounding is applied | High | P1 |
| [TC-016](test-cases/TEST_CASES.md#tc-016--critical--p1) | Payment | Payment is charged but the booking is not confirmed (gateway timeout or missed webhook), leaving an orphan payment | Critical | P1 |
| [TC-017](test-cases/TEST_CASES.md#tc-017--critical--p1) | Payment | Double-clicking 'Pay', refreshing or using the Back button creates duplicate bookings or duplicate charges | Critical | P1 |
| [TC-018](test-cases/TEST_CASES.md#tc-018--critical--p1) | Access Control | A user can view or edit another user's booking by changing the booking ID in the URL or API call (IDOR) | Critical | P1 |
| [TC-019](test-cases/TEST_CASES.md#tc-019--high--p2) | Access Control | An employee approves or edits their own booking request, or a role keeps old permissions after being changed | High | P2 |
| [TC-020](test-cases/TEST_CASES.md#tc-020--critical--p1) | Reassignment | When a booked vehicle becomes unavailable (breakdown, accident), the booking is left pointing to it with no alert or replacement | Critical | P1 |
| [TC-021](test-cases/TEST_CASES.md#tc-021--high--p2) | Concurrency | Two admins edit the same booking at the same time and the last save silently overwrites the other change | High | P2 |
| [TC-022](test-cases/TEST_CASES.md#tc-022--high--p2) | Data Integrity | Deleting a vehicle or user removes or breaks their past and future bookings, reports and invoices | High | P2 |
| [TC-023](test-cases/TEST_CASES.md#tc-023--high--p2) | Maintenance | A vehicle due for service by mileage or date can still be booked, or a maintenance window overlaps existing bookings without warning | High | P2 |
| [TC-024](test-cases/TEST_CASES.md#tc-024--medium--p2) | Notifications | Reminder or confirmation emails are duplicated, sent at the wrong time (timezone) or not sent after a reschedule | Medium | P2 |
| [TC-025](test-cases/TEST_CASES.md#tc-025--medium--p3) | Import | Bulk vehicle CSV import accepts duplicate plate numbers, bad dates or partially fails without a report | Medium | P3 |
| [TC-026](test-cases/TEST_CASES.md#tc-026--medium--p3) | Session | Session timeout during checkout loses the selection, or the user can finish the booking with an expired session | Medium | P3 |
| [TC-027](test-cases/TEST_CASES.md#tc-027--medium--p2) | Reports | Utilization and revenue reports do not match the booking data (cancelled, no-show or extended bookings counted wrongly) | Medium | P2 |

## Techniques demonstrated

- Boundary value analysis (buffer times, 24h cancellation window, grace period, license expiry day)
- Concurrency and race conditions (double booking, double-click payments, simultaneous admin edits)
- Timezone, DST and cross-midnight date handling
- API and UI parity checks, including direct API calls that bypass the UI
- Security and access control (IDOR, role changes, self-approval)
- Payment and idempotency failure modes (lost webhooks, orphan charges)
- Data integrity, caching, reporting accuracy and CSV import validation

## Repository layout

```
├── README.md
├── test-cases/
│   ├── TEST_CASES.md     # full detail for all cases, grouped by module
│   └── test-cases.csv    # same data, importable into Jira / Xray / TestRail
└── source/
    └── Vehicle_Booking_Defect_Test_Cases.xlsx   # original spreadsheet
```

## Test case template

Each case has: ID, module, defect scenario, severity, priority, preconditions, test data, steps, expected result, linked bug ID, retest result and status. Status values: Not Run / Pass / Fail / Blocked / Skipped.

## Author

**Chereth Cucharo**, Senior QA Engineer / QA Lead. 9+ years in manual, functional, regression, compatibility and API testing.

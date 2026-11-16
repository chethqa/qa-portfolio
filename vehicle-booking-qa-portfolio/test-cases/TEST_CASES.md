# Test Cases

Full detail for every test case, grouped by module.

## Contents

- [Booking](#booking) (4)
- [Date/Time](#datetime) (3)
- [Availability](#availability) (2)
- [Cancellation](#cancellation) (1)
- [Return](#return) (2)
- [Driver Eligibility](#driver-eligibility) (2)
- [Pricing](#pricing) (1)
- [Payment](#payment) (2)
- [Access Control](#access-control) (2)
- [Reassignment](#reassignment) (1)
- [Concurrency](#concurrency) (1)
- [Data Integrity](#data-integrity) (1)
- [Maintenance](#maintenance) (1)
- [Notifications](#notifications) (1)
- [Import](#import) (1)
- [Session](#session) (1)
- [Reports](#reports) (1)


## Booking

### TC-001 · Critical / P1

**Defect scenario:** Double booking: two users reserve the same vehicle and slot at the same moment, and both get a confirmation

**Preconditions:** Vehicle V-101 is Available. Two users (User A, User B) are logged in on separate browsers.

**Test data:** Slot: 2026-12-01 09:00 to 2026-12-01 17:00

**Steps:**

1. Both users open V-101 and select the same slot
2. Both click 'Confirm Booking' at the same time (use two tabs or a script firing parallel requests)
3. Check both responses and the bookings list

**Expected result:** Exactly one booking is confirmed. The other user gets a clear 'vehicle no longer available' message and nothing is saved or charged. Only one record exists for V-101 in that slot.

**Status:** Not Run

---

### TC-002 · Critical / P1

**Defect scenario:** Partial overlap not detected: a new booking that starts inside an existing booking is accepted

**Preconditions:** Existing booking on V-101: 09:00 to 12:00.

**Test data:** New slots: 11:00-14:00, 08:00-10:00, 09:30-11:30 (fully inside), 08:00-13:00 (fully covers)

**Steps:**

1. Try to book each new slot for V-101
2. Observe the result for each

**Expected result:** All four overlapping attempts are rejected with an availability error. No overlapping record is created.

**Status:** Not Run

---

### TC-003 · High / P1

**Defect scenario:** Back-to-back bookings wrongly rejected (or wrongly allowed past the buffer) at the exact boundary time

**Preconditions:** Existing booking on V-101 ends 12:00. Buffer setting = 0 min (then repeat with 30 min).

**Test data:** Start times: 12:00, 12:01, 11:59, and 12:30 with the 30-min buffer

**Steps:**

1. Set buffer to 0 min; book 12:00-15:00
2. Book 11:59-15:00
3. Set buffer to 30 min; book 12:00-15:00 and 12:30-15:00

**Expected result:** Buffer 0: 12:00 is accepted, 11:59 is rejected. Buffer 30: 12:00 is rejected, 12:30 is accepted. Behavior is the same in UI, API and calendar.

**Status:** Not Run

---

### TC-009 · Critical / P1

**Defect scenario:** Extending a booking succeeds even though another booking starts right after it

**Preconditions:** V-101 has Booking A (09:00-12:00) and Booking B (13:00-17:00).

**Test data:** Extend Booking A to 14:00, then to 12:45

**Steps:**

1. Open Booking A and choose Extend
2. Enter the new end time 14:00 and submit
3. Repeat with 12:45

**Expected result:** 14:00 is rejected because it overlaps Booking B. 12:45 is accepted only if the buffer rule allows it. Booking B is never affected.

**Status:** Not Run

---


## Date/Time

### TC-004 · High / P1

**Defect scenario:** Booking times shift by hours when the user's timezone differs from the server timezone

**Preconditions:** Server stored in UTC. User profile timezone is Asia/Manila (UTC+8). A second user is in a different timezone.

**Test data:** Booking: 2026-12-01 09:00 Asia/Manila

**Steps:**

1. Create the booking as the Manila user
2. View it in the booking details, calendar, confirmation email and admin report
3. Log in as the other-timezone user and view the same booking

**Expected result:** Every view shows 09:00 Manila time (or the correct converted time for the other user). Availability conflicts are computed on the absolute time, not the displayed text.

**Status:** Not Run

---

### TC-005 · High / P2

**Defect scenario:** Bookings that cross midnight or span multiple days show wrong duration, day count or price

**Preconditions:** Daily rate is set to 1,500. Hourly rate is 250.

**Test data:** Slots: 22:00 to 02:00 next day; 2026-12-01 08:00 to 2026-12-03 08:00; 08:00 to 08:01 on the 4th day

**Steps:**

1. Create each booking
2. Compare the duration, day count and total price shown at checkout and on the receipt

**Expected result:** Duration and price follow the documented rule (for example, each started 24h block is one day). The totals match across checkout, receipt and admin report.

**Status:** Not Run

---

### TC-006 · Medium / P3

**Defect scenario:** Booking across a DST change or month/year end calculates the wrong time or availability

**Preconditions:** Test tenant timezone set to a DST region (e.g., America/New_York). Another case: year-end.

**Test data:** Booking: 2027-03-14 01:00 to 05:00 (DST starts); 2026-12-31 20:00 to 2027-01-01 04:00

**Steps:**

1. Create both bookings
2. Check the stored UTC value, displayed duration and calendar placement

**Expected result:** Durations reflect real elapsed time (e.g., 3h across the DST jump). No gaps, duplicates or off-by-one-day errors in the calendar.

**Status:** Not Run

---


## Availability

### TC-007 · Critical / P1

**Defect scenario:** A vehicle marked 'Under Maintenance' can still be booked through the API or a stale search result

**Preconditions:** V-102 is Available and listed in search results. An admin then sets V-102 to 'Under Maintenance'.

**Test data:** POST /api/bookings with vehicle_id = V-102

**Steps:**

1. User opens the search results page and keeps it open
2. Admin sets V-102 to 'Under Maintenance'
3. User clicks 'Book' from the stale page
4. Repeat by sending the booking request directly to the API

**Expected result:** Both attempts are rejected with a clear message. Server-side validation checks the current status, not the cached listing.

**Status:** Not Run

---

### TC-008 · High / P2

**Defect scenario:** Search and filter results stay stale after a booking is made (cached availability)

**Preconditions:** Availability caching is enabled. V-103 is free on 2026-12-05.

**Test data:** Search: date 2026-12-05, type = SUV

**Steps:**

1. User A searches and sees V-103
2. User B books V-103 for that date
3. User A refreshes the search and also opens the vehicle page
4. Check the calendar view

**Expected result:** V-103 is no longer shown as available for that date in search, vehicle page or calendar, within the documented cache time (or immediately).

**Status:** Not Run

---


## Cancellation

### TC-010 · High / P1

**Defect scenario:** Cancelled booking does not release the vehicle slot, or the cancellation fee is wrong at the 24-hour boundary

**Preconditions:** Policy: free cancellation until 24h before pickup, 20% fee after. Pickup is 2026-12-10 09:00.

**Test data:** Cancel at 24h 01m before, exactly 24h, and 23h 59m before

**Steps:**

1. Cancel three separate bookings at the three times listed
2. Check fee, refund amount and status
3. Immediately try to book the freed slot with another user

**Expected result:** Fee follows the policy at each boundary. Status becomes Cancelled, the slot is released at once, and the refund is correct and logged.

**Status:** Not Run

---


## Return

### TC-011 · High / P2

**Defect scenario:** Late return fee wrongly charged inside the grace period or calculated with the wrong rounding

**Preconditions:** Grace period = 15 min. Late fee = 300 per started hour.

**Test data:** Returns at +10 min, +15 min, +16 min, +61 min

**Steps:**

1. Check out a vehicle and complete the return at each time
2. Review the late fee in the return summary and invoice

**Expected result:** +10 and +15 min: no fee. +16 min: one hour fee. +61 min: two hours fee. The invoice matches the return summary.

**Status:** Not Run

---

### TC-012 · Medium / P2

**Defect scenario:** Odometer or fuel level accepts impossible values at return (lower than pickup reading, negative, non-numeric)

**Preconditions:** Pickup odometer = 45,200 km. Fuel at pickup = Full.

**Test data:** Return odometer: 45,100; -5; 45,200; abc; 999999999. Fuel: 120%

**Steps:**

1. Start the return process
2. Enter each value and submit

**Expected result:** Values lower than pickup, negative, non-numeric or out of range are rejected with validation messages. Equal value is accepted only per business rule.

**Status:** Not Run

---


## Driver Eligibility

### TC-013 · Critical / P1

**Defect scenario:** A driver whose license expires between the booking date and the pickup date is still allowed to pick up the vehicle

**Preconditions:** Driver license expiry = 2026-12-05. Booking pickup = 2026-12-10, created on 2026-11-13.

**Test data:** License expiry dates: 2026-12-05, same day as pickup, day after pickup

**Steps:**

1. Create the booking before the license expires
2. Move the system date to the pickup day (or use test data) and run check-out
3. Repeat with expiry on the pickup day and the day after

**Expected result:** Booking creation warns or blocks when the license is expired by the pickup/return date. Check-out is blocked for an expired license. Same-day expiry follows the documented rule.

**Status:** Not Run

---

### TC-014 · High / P2

**Defect scenario:** Driver can book a vehicle class that is not allowed by the license type or age rule

**Preconditions:** V-201 is a truck (requires a professional license). Driver holds a standard license, age 20. Rule: minimum age 21 for premium vehicles.

**Test data:** Vehicle types: truck, premium sedan, compact car

**Steps:**

1. Log in as the driver
2. Try to book each vehicle type
3. Try again through the API

**Expected result:** Truck and premium sedan are blocked in UI and API with a clear reason. Compact car is allowed.

**Status:** Not Run

---


## Pricing

### TC-015 · High / P1

**Defect scenario:** Total price is wrong when a promo code, seasonal rate and tax are combined, or when rounding is applied

**Preconditions:** Peak-season surcharge +20%. Promo SAVE10 = 10% off. Tax 12%. Rule order is documented.

**Test data:** Rate 1,333.33 per day, 3 days; promo SAVE10; also an expired promo and an already used promo

**Steps:**

1. Create a booking with the peak dates and apply SAVE10
2. Compare checkout, receipt, payment amount and report
3. Try an expired promo and a reused promo
4. Try stacking two promo codes

**Expected result:** Calculation follows the documented order and rounds to 2 decimals consistently everywhere. Expired and reused promos are rejected. Stacking follows policy.

**Status:** Not Run

---


## Payment

### TC-016 · Critical / P1

**Defect scenario:** Payment is charged but the booking is not confirmed (gateway timeout or missed webhook), leaving an orphan payment

**Preconditions:** Payment gateway sandbox. Ability to delay or drop the webhook.

**Test data:** Card: sandbox success card; webhook delayed by 60 s, then dropped

**Steps:**

1. Start a booking and pay
2. Simulate a gateway timeout after the charge succeeds
3. Check the booking status and the payment record
4. Resend the webhook later

**Expected result:** The booking is held as Pending, then confirmed on a later webhook or reconciliation job. If it cannot be confirmed, the payment is refunded automatically and the user is notified. No orphan charge remains.

**Status:** Not Run

---

### TC-017 · Critical / P1

**Defect scenario:** Double-clicking 'Pay', refreshing or using the Back button creates duplicate bookings or duplicate charges

**Preconditions:** Checkout page is open with a valid card.

**Test data:** One order, double-click and a rapid second submit

**Steps:**

1. Click 'Pay' twice quickly
2. On another run, submit, then press Back and submit again
3. Check bookings and payments

**Expected result:** Only one booking and one charge are created (idempotent request). The second attempt shows the existing booking or a friendly message.

**Status:** Not Run

---


## Access Control

### TC-018 · Critical / P1

**Defect scenario:** A user can view or edit another user's booking by changing the booking ID in the URL or API call (IDOR)

**Preconditions:** User A has booking #1001. User B is logged in and has no access to it.

**Test data:** GET/PUT/DELETE /api/bookings/1001 as User B; URL /bookings/1001/edit

**Steps:**

1. Log in as User B
2. Open the URL for booking #1001 directly
3. Send GET, PUT and DELETE requests to the API with User B's token

**Expected result:** All attempts return 403/404 and no booking data is exposed or changed.

**Status:** Not Run

---

### TC-019 · High / P2

**Defect scenario:** An employee approves or edits their own booking request, or a role keeps old permissions after being changed

**Preconditions:** Approval workflow is enabled. User has the Employee role and then gets promoted to Manager.

**Test data:** Role changes: Employee to Manager to Employee

**Steps:**

1. Employee submits a booking that needs approval
2. Try to approve it as the same user (with Manager role)
3. Demote the user and reuse the existing session

**Expected result:** Self-approval is blocked. Permission changes take effect on the next request without needing re-login.

**Status:** Not Run

---


## Reassignment

### TC-020 · Critical / P1

**Defect scenario:** When a booked vehicle becomes unavailable (breakdown, accident), the booking is left pointing to it with no alert or replacement

**Preconditions:** Booking for V-104 tomorrow. A similar vehicle V-105 is free.

**Test data:** Status change: V-104 to Out of Service

**Steps:**

1. Admin marks V-104 Out of Service
2. Check the affected booking, notifications and dashboard
3. Reassign the booking to V-105
4. Try reassigning to a vehicle that is booked

**Expected result:** Affected bookings are flagged and the user and admin are notified. Reassignment to a free vehicle works and keeps the price rules. Reassignment to a busy vehicle is blocked.

**Status:** Not Run

---


## Concurrency

### TC-021 · High / P2

**Defect scenario:** Two admins edit the same booking at the same time and the last save silently overwrites the other change

**Preconditions:** Booking #2002 is open in two admin sessions.

**Test data:** Admin 1 changes the vehicle. Admin 2 changes the end time.

**Steps:**

1. Both admins open the booking
2. Admin 1 saves first
3. Admin 2 saves second

**Expected result:** Admin 2 sees a conflict message (version check) and must refresh. No silent overwrite and no invalid combination (e.g., new vehicle with old times that overlap).

**Status:** Not Run

---


## Data Integrity

### TC-022 · High / P2

**Defect scenario:** Deleting a vehicle or user removes or breaks their past and future bookings, reports and invoices

**Preconditions:** Vehicle V-106 has past, active and future bookings.

**Test data:** Delete V-106; delete a driver with bookings

**Steps:**

1. Try to delete V-106 as admin
2. If allowed, open the past bookings, reports and invoices
3. Repeat for the driver

**Expected result:** Deletion is blocked while future or active bookings exist, or the record is archived (soft delete). History, reports and invoices still display the vehicle and driver details.

**Status:** Not Run

---


## Maintenance

### TC-023 · High / P2

**Defect scenario:** A vehicle due for service by mileage or date can still be booked, or a maintenance window overlaps existing bookings without warning

**Preconditions:** Service interval: every 10,000 km. V-107 is at 9,950 km. A booking exists tomorrow.

**Test data:** Create maintenance window overlapping the existing booking

**Steps:**

1. Schedule maintenance that overlaps the booking
2. Check the warning and the booking status
3. Try to book V-107 for a trip that will pass 10,000 km

**Expected result:** The overlap is flagged with a warning and a conflict list. Vehicles past the service limit are blocked or flagged per policy.

**Status:** Not Run

---


## Notifications

### TC-024 · Medium / P2

**Defect scenario:** Reminder or confirmation emails are duplicated, sent at the wrong time (timezone) or not sent after a reschedule

**Preconditions:** Email reminders: 24h and 1h before pickup. Test mailbox available.

**Test data:** Reschedule a booking after the 24h reminder has been sent

**Steps:**

1. Create a booking and wait for (or trigger) the 24h reminder
2. Reschedule the booking to another time
3. Check the emails received

**Expected result:** One confirmation and one reminder per schedule. After reschedule, old reminders are cancelled and new ones are sent at the correct local times.

**Status:** Not Run

---


## Import

### TC-025 · Medium / P3

**Defect scenario:** Bulk vehicle CSV import accepts duplicate plate numbers, bad dates or partially fails without a report

**Preconditions:** Import feature is available. Plate number must be unique.

**Test data:** CSV with duplicate plates, blank required fields, invalid dates, 5,000 rows

**Steps:**

1. Import the CSV
2. Review the result message and imported records
3. Import the same file again

**Expected result:** Invalid rows are rejected with a downloadable error report (row and reason). Valid rows are imported once. Re-import does not create duplicates.

**Status:** Not Run

---


## Session

### TC-026 · Medium / P3

**Defect scenario:** Session timeout during checkout loses the selection, or the user can finish the booking with an expired session

**Preconditions:** Session timeout = 15 min.

**Test data:** Idle 16 minutes on the payment page

**Steps:**

1. Start a booking and wait for the session to expire
2. Click 'Pay'
3. Log in again

**Expected result:** User is redirected to login, no charge is made without a valid session, and after login the booking selection is restored or a clear message is shown. The held slot is released after the hold time.

**Status:** Not Run

---


## Reports

### TC-027 · Medium / P2

**Defect scenario:** Utilization and revenue reports do not match the booking data (cancelled, no-show or extended bookings counted wrongly)

**Preconditions:** A month of mixed data: completed, cancelled, no-show, extended bookings.

**Test data:** Report period: 2026-11-01 to 2026-11-30

**Steps:**

1. Generate the utilization and revenue reports
2. Calculate the expected totals manually from the booking list
3. Export to CSV/PDF and compare

**Expected result:** On-screen, exported and manual totals agree. Cancelled and no-show bookings are treated per the documented rule.

**Status:** Not Run

---

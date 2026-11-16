# Test Cases

Full detail for every test case, grouped by module.

## Contents

- [Data Ingestion](#data-ingestion) (3)
- [Date/Time](#datetime) (2)
- [Alerts](#alerts) (4)
- [Calibration](#calibration) (2)
- [Unit Handling](#unit-handling) (1)
- [Reports](#reports) (2)
- [Dashboard](#dashboard) (1)
- [Map / Location](#map--location) (1)
- [Access Control](#access-control) (2)
- [Audit Log](#audit-log) (1)
- [Concurrency](#concurrency) (1)
- [Data Retention](#data-retention) (1)
- [Sensor Management](#sensor-management) (1)
- [Manual Entry](#manual-entry) (1)
- [API / Integration](#api--integration) (1)
- [Performance](#performance) (1)

## Data Ingestion

### TC-001 · Critical / P1 · Integration

**Defect scenario:** Duplicate sensor readings are saved when a device resends a batch after a network timeout

**Preconditions:** Sensor SN-AQ-01 is registered and sending readings to the API.

**Test data:** Batch of 50 readings with the same device timestamps, sent twice

**Steps:**

1. Send the batch and simulate a timeout before the device receives the response
2. Device resends the same batch
3. Check the readings table, charts and averages

**Expected result:** Each reading (sensor + timestamp) is stored once. The resend returns success without creating duplicates. Averages and counts are not inflated.

**Status:** Not Run

---

### TC-002 · High / P1 · Functional

**Defect scenario:** Out-of-order or late readings overwrite newer values or are dropped silently

**Preconditions:** Sensor is online. Latest stored reading is 10:05.

**Test data:** Readings stamped 10:00 (late), 10:10, 10:03 (late), received in the order 10:10, 10:00, 10:03

**Steps:**

1. Send the readings in the order listed
2. Open the live view and the history chart
3. Check rolling averages for 10:00-10:10

**Expected result:** All readings are stored by their device timestamp. 'Latest value' stays 10:10. History and averages include the late readings in the correct order.

**Status:** Not Run

---

### TC-003 · High / P1 · Negative

**Defect scenario:** Invalid sensor values (null, negative, NaN, sentinel -999, impossible range) are accepted and distort charts and alerts

**Preconditions:** Temperature sensor valid range is -40 to 85 C. Humidity range is 0 to 100%.

**Test data:** Values: null, "", -999, 9999, "abc", NaN, humidity 101, temperature 85.01

**Steps:**

1. Send each value through the device API
2. Check responses, stored data, charts and alert logic

**Expected result:** Invalid values are rejected or flagged as bad quality and excluded from averages and alerts. A clear error is returned. Boundary values (85.00) are accepted.

**Status:** Not Run

---


## Date/Time

### TC-004 · High / P1 · Functional

**Defect scenario:** Readings show the wrong time when the device clock, server clock and user timezone differ

**Preconditions:** Device clock is 3 minutes ahead. Server is UTC. User timezone is Asia/Manila (UTC+8).

**Test data:** Device timestamp 2026-12-01T10:03:00 (device local, UTC+0)

**Steps:**

1. Send readings from the device
2. Compare the stored time, the chart axis and the exported CSV for a Manila user
3. Switch the user timezone and compare again

**Expected result:** Time is stored in UTC with the correct offset. Every screen and export shows consistent local time. Future-dated device timestamps are flagged or corrected per rule.

**Status:** Not Run

---

### TC-005 · Medium / P2 · Boundary

**Defect scenario:** Daily or monthly averages are wrong on DST change days (23-hour or 25-hour days) and at month/year end

**Preconditions:** Site timezone is Europe/Berlin. Hourly readings exist for 2027-03-28 and 2026-10-25.

**Test data:** Dates: DST start, DST end, 2026-12-31 to 2027-01-01

**Steps:**

1. Generate the daily average report for each date
2. Count the readings used per day
3. Compare to a manual calculation

**Expected result:** Daily averages use the real number of hours in the site day. No readings are counted twice or lost, and year-end values land in the right day.

**Status:** Not Run

---


## Alerts

### TC-006 · High / P1 · Boundary

**Defect scenario:** Alert threshold is not triggered when the value equals the threshold exactly or sits right above or below it

**Preconditions:** Rule: PM2.5 alert when value >= 35.0 ug/m3 (documented).

**Test data:** Values: 34.99, 35.00, 35.01

**Steps:**

1. Send each value from a test sensor
2. Check alert creation, severity and notification

**Expected result:** 34.99 creates no alert. 35.00 and 35.01 create an alert, following the documented operator (>=). Same behavior in UI, API and notifications.

**Status:** Not Run

---

### TC-007 · High / P1 · Functional

**Defect scenario:** Alert flapping: a value that moves around the threshold creates a flood of duplicate alerts and emails

**Preconditions:** Alert with 5 minutes of cooldown/hysteresis is configured.

**Test data:** Readings alternate 34.5, 35.5, 34.5, 35.5 every minute for 10 minutes

**Steps:**

1. Send the alternating readings
2. Count the alerts created and notifications sent
3. Let the value stay below the threshold and check auto-resolve

**Expected result:** One open alert (not one per crossing) within the cooldown. A single notification is sent. The alert resolves after the value stays normal for the set time.

**Status:** Not Run

---

### TC-008 · Critical / P1 · Functional

**Defect scenario:** Sensor going offline is not detected or alerted, and old data is shown as current

**Preconditions:** Sensor reports every 5 minutes. Offline rule: no data for 15 minutes.

**Test data:** Stop the sensor; wait 16 minutes; restore it

**Steps:**

1. Stop the data stream
2. Check the status on the dashboard and map after 16 minutes
3. Check the alert and notification
4. Restore data and check status and alert resolution

**Expected result:** Sensor shows Offline with a 'last seen' time (not the old value as live). One offline alert is sent. Status returns to Online and the alert resolves when data resumes.

**Status:** Not Run

---

### TC-009 · Medium / P2 · Functional

**Defect scenario:** Notifications are sent to users who were removed from the site, or not sent to users added later; quiet hours ignored

**Preconditions:** Alert recipients: User A (removed from site), User B (added today). Quiet hours 22:00-06:00 for User C.

**Test data:** Trigger an alert at 23:00

**Steps:**

1. Remove User A from the site and add User B
2. Trigger the alert
3. Check emails/SMS/push for A, B and C

**Expected result:** Only current site members are notified. User B receives it. User C gets it according to the quiet hours rule (queued or critical-only).

**Status:** Not Run

---


## Calibration

### TC-010 · Critical / P1 · Functional

**Defect scenario:** Calibration offset is applied twice, or is applied to old historical readings

**Preconditions:** Sensor has a +1.5 offset added today at 12:00. Existing readings before 12:00 are stored.

**Test data:** Raw 20.0 C before 12:00 and 20.0 C after 12:00

**Steps:**

1. Add the calibration offset with effective time 12:00
2. View the readings before and after 12:00 in table, chart and export
3. Edit the offset to +2.0 and check again

**Expected result:** Offset applies only from the effective time, once. Raw and calibrated values are both traceable. History is not silently rewritten. Edits are audit-logged.

**Status:** Not Run

---

### TC-011 · High / P2 · Functional

**Defect scenario:** Sensor overdue for calibration is still treated as valid data in compliance reports

**Preconditions:** Calibration interval 6 months. Sensor last calibrated 7 months ago.

**Test data:** Compliance report for the last quarter

**Steps:**

1. Generate the compliance report
2. Check how readings of the overdue sensor are shown
3. Add a new calibration and regenerate

**Expected result:** The overdue sensor is flagged, and data from the overdue period is marked or excluded per rule. New calibration clears the flag going forward.

**Status:** Not Run

---


## Unit Handling

### TC-012 · Critical / P1 · Functional

**Defect scenario:** Unit conversion errors between units (C/F, ppm/ppb, ug/m3/mg/m3) change values or alerts

**Preconditions:** Site A uses Celsius and ug/m3. Site B uses Fahrenheit and mg/m3. Alert rules are stored in base units.

**Test data:** 25 C, 77 F, 0.035 mg/m3, 35 ug/m3, 1 ppm = 1000 ppb

**Steps:**

1. Send the same measurement from both sites in their own units
2. Compare stored base value, displayed value, charts, alerts and exports
3. Change the user's display unit setting

**Expected result:** Conversions are correct and rounded consistently. Same real measurement gives the same alert result. Switching display units never changes the stored base value.

**Status:** Not Run

---


## Reports

### TC-013 · Critical / P1 · Regression

**Defect scenario:** Compliance report totals, averages and max values do not match the raw data (missing hours, rounding, bad data included)

**Preconditions:** One month of data with gaps, bad-quality values and one offline period.

**Test data:** Report period: 2026-11-01 to 2026-11-30

**Steps:**

1. Generate the monthly report
2. Export raw data and calculate average, max, min and data coverage % manually
3. Compare the report, the export and the dashboard

**Expected result:** Report, export and dashboard agree. Data coverage % is shown, bad data is excluded, and the rounding rule is applied once at the end.

**Status:** Not Run

---

### TC-014 · Medium / P2 · Performance

**Defect scenario:** Large exports (many sensors, many months) time out, cut off data silently, or break CSV formatting (commas, quotes, special characters)

**Preconditions:** 50 sensors, 12 months of 1-minute data. Site names with commas, quotes and accents (e.g., 'Río, "North" Plant').

**Test data:** Export range: 12 months, all sensors

**Steps:**

1. Request a CSV export for the full range
2. Wait for the result or the download email
3. Open in Excel and compare row counts with the database

**Expected result:** Export finishes (or runs in the background with notice). Row count matches. Special characters are escaped correctly and encoding is UTF-8.

**Status:** Not Run

---


## Dashboard

### TC-015 · High / P2 · Functional

**Defect scenario:** Dashboard shows stale or cached values, or mixes sites after switching the site selector quickly

**Preconditions:** User has access to Site A and Site B with different readings.

**Test data:** Switch A to B to A rapidly while data is refreshing

**Steps:**

1. Open the dashboard on Site A
2. Switch quickly between sites several times
3. Compare tiles, charts and map markers with each site's data

**Expected result:** All widgets always show data from the selected site only. Slow earlier responses do not overwrite newer selections.

**Status:** Not Run

---


## Map / Location

### TC-016 · Medium / P2 · Negative

**Defect scenario:** Sensor location is wrong when coordinates are swapped, outside valid range, or the site uses a different coordinate format

**Preconditions:** Sensor edit form with latitude and longitude fields.

**Test data:** Lat/Long: 14.5995, 120.9842; 120.9842, 14.5995 (swapped); 91, 181; blank; 14,5995 (comma)

**Steps:**

1. Enter each coordinate pair
2. Save and open the map
3. Import sensors by CSV with the same values

**Expected result:** Invalid, swapped-out-of-range and blank values are rejected with clear messages. Decimal comma is handled or rejected consistently. Valid markers show at the right place.

**Status:** Not Run

---


## Access Control

### TC-017 · Critical / P1 · Security

**Defect scenario:** A user can view another organization's sites, sensors or readings by changing the ID in the URL or API (IDOR)

**Preconditions:** Org 1 user and Org 2 user. Site #501 belongs to Org 1.

**Test data:** GET /api/sites/501, /api/sensors/9001/readings as an Org 2 user

**Steps:**

1. Log in as the Org 2 user
2. Open /sites/501 directly
3. Call the site, sensor, reading and export APIs for Org 1 IDs

**Expected result:** All calls return 403/404 with no data leaked, including in export and report endpoints and chart data.

**Status:** Not Run

---

### TC-018 · High / P2 · Security

**Defect scenario:** Role changes (Admin to Viewer) do not take effect until re-login, and Viewer can still edit thresholds via the API

**Preconditions:** User has an active session as Admin. Role is then changed to Viewer.

**Test data:** PUT /api/alert-rules/12 with the old session token

**Steps:**

1. Admin user signs in
2. Another admin changes the role to Viewer
3. The user sends the update through the UI and the API

**Expected result:** Permissions are checked on each request. The update is blocked in UI and API (403) immediately after the role change.

**Status:** Not Run

---


## Audit Log

### TC-019 · High / P2 · Functional

**Defect scenario:** Changes to thresholds, calibration or sensor assignment are not logged, or logs show the wrong user/time

**Preconditions:** Audit log is enabled.

**Test data:** Edit threshold 35 to 50; change calibration offset; reassign sensor to another site; delete an alert rule

**Steps:**

1. Perform each change as different users
2. Open the audit log and filter by user, date and action
3. Try to edit or delete a log entry

**Expected result:** Each change has one log entry with who, when, old value and new value. Time is correct for the viewer's timezone. Log entries cannot be edited or deleted.

**Status:** Not Run

---


## Concurrency

### TC-020 · High / P2 · Concurrency

**Defect scenario:** Two users edit the same threshold or sensor settings at the same time and the last save silently overwrites the first

**Preconditions:** Alert rule #12 is open in two sessions.

**Test data:** User 1 sets threshold 40. User 2 sets cooldown 10 min and saves later.

**Steps:**

1. Both users open the rule
2. User 1 saves
3. User 2 saves

**Expected result:** User 2 gets a conflict message and must refresh, or only changed fields are merged. No silent overwrite of User 1's threshold.

**Status:** Not Run

---


## Data Retention

### TC-021 · High / P2 · Functional

**Defect scenario:** Data older than the retention period is deleted while still needed by reports, or not deleted at all (and data not archived)

**Preconditions:** Retention: raw data 12 months, hourly aggregates 5 years.

**Test data:** Run the retention job on data 12 months + 1 day old

**Steps:**

1. Run the retention job
2. Check the raw table and the aggregate table
3. Open a 3-year report

**Expected result:** Raw data older than 12 months is archived/removed. Aggregates remain, and the 3-year report still works. The job is logged and can be re-run safely.

**Status:** Not Run

---


## Sensor Management

### TC-022 · High / P2 · Functional

**Defect scenario:** Replacing a faulty sensor with a new one breaks history, creates duplicate sensor entries, or reuses the old calibration

**Preconditions:** Sensor SN-AQ-01 has 6 months of history. New device SN-AQ-09 replaces it.

**Test data:** Replace SN-AQ-01 with SN-AQ-09 at 2026-11-20 10:00

**Steps:**

1. Use the replace sensor function
2. Check history before and after 10:00 in one chart
3. Check calibration and alert settings of the new sensor
4. Try registering SN-AQ-09 a second time

**Expected result:** History is continuous for the location, with the change point marked. The new sensor needs its own calibration. Duplicate serial numbers are rejected.

**Status:** Not Run

---


## Manual Entry

### TC-023 · Medium / P3 · Negative

**Defect scenario:** Manually entered readings or incident notes allow future dates, duplicate entries, or bypass validation rules used for device data

**Preconditions:** Manual entry form (e.g., water sample results) is available.

**Test data:** Date: tomorrow; same sample twice; value outside range; unit missing

**Steps:**

1. Submit each entry
2. Check validation messages and saved data
3. Check how the manual data appears in reports

**Expected result:** Future dates, duplicates, out-of-range values and missing units are rejected. Valid manual entries are labeled as manual in reports.

**Status:** Not Run

---


## API / Integration

### TC-024 · Medium / P2 · Integration

**Defect scenario:** Third-party/webhook integration fails silently, retries forever, or sends alerts with wrong payload fields

**Preconditions:** Webhook configured to an external endpoint that can return 200, 500 or time out.

**Test data:** Endpoint responses: 200, 500, timeout, 410

**Steps:**

1. Trigger an alert for each endpoint behavior
2. Check retry attempts, failure status and logs
3. Fix the endpoint and check recovery

**Expected result:** Retries use backoff and stop after the limit. Failures are visible to admins. Payload fields, units and timestamps are correct. Alerts are not lost after recovery.

**Status:** Not Run

---


## Performance

### TC-025 · Medium / P2 · Performance

**Defect scenario:** Dashboard or chart becomes very slow or crashes with many sensors or long date ranges (e.g., 1-minute data for 1 year)

**Preconditions:** Test data: 200 sensors, 1 year of 1-minute readings.

**Test data:** Chart range: 1 year, 20 sensors selected

**Steps:**

1. Open the chart with the full range
2. Zoom, change the range and add sensors
3. Record the load time and browser memory use

**Expected result:** Data is downsampled/aggregated for large ranges. The page stays responsive within the agreed time and shows no errors, while zooming in gives fine detail.

**Status:** Not Run

---

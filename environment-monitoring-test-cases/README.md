# Environmental Monitoring Platform: Defect-Based Test Cases

**25 manual test cases** for an environmental sensor tracking platform (air quality, temperature, humidity). They cover the data pipeline from device to dashboard, alerting, calibration, compliance reporting and access control.

Each case starts from a realistic defect pattern, then defines preconditions, test data, reproducible steps and an expected result.

> **Note:** These are sample test cases that represent common bug patterns in IoT and monitoring systems. They are not taken from a real company's bug list. Thresholds, ranges and retention periods in the expected results are stated assumptions that would be confirmed against real requirements.

## Summary

| Severity | Count |
|---|---|
| Critical | 6 |
| High | 12 |
| Medium | 7 |
| **Total** | **25** |

**Test types:** Functional (12), Negative (3), Integration (2), Boundary (2), Performance (2), Security (2), Regression (1), Concurrency (1)

**Modules covered:** Data Ingestion, Date/Time, Alerts, Calibration, Unit Handling, Reports, Dashboard, Map / Location, Access Control, Audit Log, Concurrency, Data Retention, Sensor Management, Manual Entry, API / Integration, Performance

## Test case index

| ID | Module | Defect scenario | Type | Severity | Priority |
|---|---|---|---|---|---|
| [TC-001](test-cases/TEST_CASES.md#tc-001--critical--p1--integration) | Data Ingestion | Duplicate sensor readings are saved when a device resends a batch after a network timeout | Integration | Critical | P1 |
| [TC-002](test-cases/TEST_CASES.md#tc-002--high--p1--functional) | Data Ingestion | Out-of-order or late readings overwrite newer values or are dropped silently | Functional | High | P1 |
| [TC-003](test-cases/TEST_CASES.md#tc-003--high--p1--negative) | Data Ingestion | Invalid sensor values (null, negative, NaN, sentinel -999, impossible range) are accepted and distort charts and alerts | Negative | High | P1 |
| [TC-004](test-cases/TEST_CASES.md#tc-004--high--p1--functional) | Date/Time | Readings show the wrong time when the device clock, server clock and user timezone differ | Functional | High | P1 |
| [TC-005](test-cases/TEST_CASES.md#tc-005--medium--p2--boundary) | Date/Time | Daily or monthly averages are wrong on DST change days (23-hour or 25-hour days) and at month/year end | Boundary | Medium | P2 |
| [TC-006](test-cases/TEST_CASES.md#tc-006--high--p1--boundary) | Alerts | Alert threshold is not triggered when the value equals the threshold exactly or sits right above or below it | Boundary | High | P1 |
| [TC-007](test-cases/TEST_CASES.md#tc-007--high--p1--functional) | Alerts | Alert flapping: a value that moves around the threshold creates a flood of duplicate alerts and emails | Functional | High | P1 |
| [TC-008](test-cases/TEST_CASES.md#tc-008--critical--p1--functional) | Alerts | Sensor going offline is not detected or alerted, and old data is shown as current | Functional | Critical | P1 |
| [TC-009](test-cases/TEST_CASES.md#tc-009--medium--p2--functional) | Alerts | Notifications are sent to users who were removed from the site, or not sent to users added later; quiet hours ignored | Functional | Medium | P2 |
| [TC-010](test-cases/TEST_CASES.md#tc-010--critical--p1--functional) | Calibration | Calibration offset is applied twice, or is applied to old historical readings | Functional | Critical | P1 |
| [TC-011](test-cases/TEST_CASES.md#tc-011--high--p2--functional) | Calibration | Sensor overdue for calibration is still treated as valid data in compliance reports | Functional | High | P2 |
| [TC-012](test-cases/TEST_CASES.md#tc-012--critical--p1--functional) | Unit Handling | Unit conversion errors between units (C/F, ppm/ppb, ug/m3/mg/m3) change values or alerts | Functional | Critical | P1 |
| [TC-013](test-cases/TEST_CASES.md#tc-013--critical--p1--regression) | Reports | Compliance report totals, averages and max values do not match the raw data (missing hours, rounding, bad data included) | Regression | Critical | P1 |
| [TC-014](test-cases/TEST_CASES.md#tc-014--medium--p2--performance) | Reports | Large exports (many sensors, many months) time out, cut off data silently, or break CSV formatting (commas, quotes, special characters) | Performance | Medium | P2 |
| [TC-015](test-cases/TEST_CASES.md#tc-015--high--p2--functional) | Dashboard | Dashboard shows stale or cached values, or mixes sites after switching the site selector quickly | Functional | High | P2 |
| [TC-016](test-cases/TEST_CASES.md#tc-016--medium--p2--negative) | Map / Location | Sensor location is wrong when coordinates are swapped, outside valid range, or the site uses a different coordinate format | Negative | Medium | P2 |
| [TC-017](test-cases/TEST_CASES.md#tc-017--critical--p1--security) | Access Control | A user can view another organization's sites, sensors or readings by changing the ID in the URL or API (IDOR) | Security | Critical | P1 |
| [TC-018](test-cases/TEST_CASES.md#tc-018--high--p2--security) | Access Control | Role changes (Admin to Viewer) do not take effect until re-login, and Viewer can still edit thresholds via the API | Security | High | P2 |
| [TC-019](test-cases/TEST_CASES.md#tc-019--high--p2--functional) | Audit Log | Changes to thresholds, calibration or sensor assignment are not logged, or logs show the wrong user/time | Functional | High | P2 |
| [TC-020](test-cases/TEST_CASES.md#tc-020--high--p2--concurrency) | Concurrency | Two users edit the same threshold or sensor settings at the same time and the last save silently overwrites the first | Concurrency | High | P2 |
| [TC-021](test-cases/TEST_CASES.md#tc-021--high--p2--functional) | Data Retention | Data older than the retention period is deleted while still needed by reports, or not deleted at all (and data not archived) | Functional | High | P2 |
| [TC-022](test-cases/TEST_CASES.md#tc-022--high--p2--functional) | Sensor Management | Replacing a faulty sensor with a new one breaks history, creates duplicate sensor entries, or reuses the old calibration | Functional | High | P2 |
| [TC-023](test-cases/TEST_CASES.md#tc-023--medium--p3--negative) | Manual Entry | Manually entered readings or incident notes allow future dates, duplicate entries, or bypass validation rules used for device data | Negative | Medium | P3 |
| [TC-024](test-cases/TEST_CASES.md#tc-024--medium--p2--integration) | API / Integration | Third-party/webhook integration fails silently, retries forever, or sends alerts with wrong payload fields | Integration | Medium | P2 |
| [TC-025](test-cases/TEST_CASES.md#tc-025--medium--p2--performance) | Performance | Dashboard or chart becomes very slow or crashes with many sensors or long date ranges (e.g., 1-minute data for 1 year) | Performance | Medium | P2 |

## Techniques demonstrated

- Data quality testing: duplicates, late and out-of-order readings, invalid and sentinel values
- Boundary value analysis (alert thresholds, validity ranges, offline timeout, retention cutoff)
- Timezone, clock drift and DST handling in time-series data
- Alert behavior: flapping, cooldown/hysteresis, offline detection, notification recipients
- Calibration and unit-conversion correctness, with raw versus calibrated traceability
- Report accuracy: manual recalculation against reports, exports and dashboards
- Security and access control (IDOR across organizations, role changes, audit logging)
- Concurrency, performance and integration (webhook retries, large exports, big charts)

## Repository layout

```
├── README.md
├── test-cases/
│   ├── TEST_CASES.md     # full detail for all cases, grouped by module
│   └── test-cases.csv    # same data, importable into Jira / Xray / TestRail
└── source/
    └── Environment_Tracking_Test_Cases.xlsx   # original spreadsheet
```

## Author

**Chereth Cucharo**, Senior QA Engineer / QA Lead. 9+ years in manual, functional, regression, compatibility and API testing.

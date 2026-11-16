# Medical Imaging Software: Defect-Based Test Cases

**25 manual test cases** for a DICOM/PACS-style medical imaging system (image viewer, worklist, reporting and integrations). They focus on patient safety, measurement accuracy, DICOM handling, privacy and workflow reliability.

Each case starts from a realistic defect pattern, then defines preconditions, test data, reproducible steps and an expected result.

> **Note:** These are sample test cases that represent common bug patterns in imaging software. They are not taken from a real company's bug list. All data described is synthetic or fully anonymized; no real patient data is used. Merge policies, tolerances and time rules in the expected results are stated assumptions that would be confirmed against real requirements.

## Summary

| Severity | Count |
|---|---|
| Critical | 8 |
| High | 13 |
| Medium | 4 |
| **Total** | **25** |

**Test types:** Functional (13), Boundary (2), Negative (2), Integration (2), Security (2), Regression (1), Concurrency (1), Performance (1), Compatibility (1)

**Modules covered:** Patient Safety, Display, Measurements, DICOM Import, Series Handling, Multi-planar, Comparison, Annotations, Reporting, Worklist, Anonymization, Access Control, Audit Log, Performance, Compression, Date/Time, Printing / Export, Integration, Session / Data Loss, Compatibility

## Test case index

| ID | Module | Defect scenario | Type | Severity | Priority |
|---|---|---|---|---|---|
| [TC-001](test-cases/TEST_CASES.md#tc-001--critical--p1--functional) | Patient Safety | Image displayed under the wrong patient when two patients have the same name or similar IDs | Functional | Critical | P1 |
| [TC-002](test-cases/TEST_CASES.md#tc-002--critical--p1--functional) | Display | Left/right orientation markers or image flip are wrong after rotate, flip or opening a study with unusual orientation | Functional | Critical | P1 |
| [TC-003](test-cases/TEST_CASES.md#tc-003--high--p1--functional) | Display | Window/level (brightness/contrast) presets show wrong values or reset when scrolling through a series | Functional | High | P1 |
| [TC-004](test-cases/TEST_CASES.md#tc-004--critical--p1--functional) | Measurements | Distance or area measurement is wrong when pixel spacing is missing, anisotropic or the image is zoomed/resampled | Functional | Critical | P1 |
| [TC-005](test-cases/TEST_CASES.md#tc-005--critical--p1--boundary) | Measurements | Hounsfield unit (HU) value is wrong when rescale slope/intercept are missing or differ between slices | Boundary | Critical | P1 |
| [TC-006](test-cases/TEST_CASES.md#tc-006--high--p1--negative) | DICOM Import | Import fails or silently skips files when the DICOM has missing optional tags, private tags, or very large size | Negative | High | P1 |
| [TC-007](test-cases/TEST_CASES.md#tc-007--high--p1--integration) | DICOM Import | Duplicate studies or series created when the same study arrives twice or UIDs repeat | Integration | High | P1 |
| [TC-008](test-cases/TEST_CASES.md#tc-008--high--p2--functional) | DICOM Import | Patient demographics are not updated (or are overwritten wrongly) when a later study arrives with a corrected name or DOB | Functional | High | P2 |
| [TC-009](test-cases/TEST_CASES.md#tc-009--high--p1--functional) | Series Handling | Slices appear in the wrong order or a slice is missing when instance numbers and slice positions disagree | Functional | High | P1 |
| [TC-010](test-cases/TEST_CASES.md#tc-010--high--p2--functional) | Multi-planar | MPR (axial/coronal/sagittal) crosshair or reformatted images are misaligned or show wrong anatomy side | Functional | High | P2 |
| [TC-011](test-cases/TEST_CASES.md#tc-011--high--p2--functional) | Comparison | Comparing current and prior studies shows mismatched slices, wrong dates, or syncs scrolling to the wrong location | Functional | High | P2 |
| [TC-012](test-cases/TEST_CASES.md#tc-012--critical--p1--regression) | Annotations | Annotations, arrows and measurements disappear, move, or attach to the wrong image after save, reload or study update | Regression | Critical | P1 |
| [TC-013](test-cases/TEST_CASES.md#tc-013--critical--p1--concurrency) | Reporting | Report is attached to the wrong study, or a signed/final report can still be edited or lost on concurrent edit | Concurrency | Critical | P1 |
| [TC-014](test-cases/TEST_CASES.md#tc-014--high--p2--functional) | Reporting | Key images or measurements inserted in the report do not match what was shown in the viewer (wrong slice, stale value) | Functional | High | P2 |
| [TC-015](test-cases/TEST_CASES.md#tc-015--high--p1--functional) | Worklist | Worklist filters, sorting or status are wrong (study shown as read when unread, urgent studies not on top, studies disappear) | Functional | High | P1 |
| [TC-016](test-cases/TEST_CASES.md#tc-016--critical--p1--security) | Anonymization | De-identified (anonymized) export still contains patient name, ID, dates or private tags, or burned-in text in pixel data | Security | Critical | P1 |
| [TC-017](test-cases/TEST_CASES.md#tc-017--critical--p1--security) | Access Control | User sees studies from another site or department, or a technologist can sign reports (role escalation) | Security | Critical | P1 |
| [TC-018](test-cases/TEST_CASES.md#tc-018--high--p2--functional) | Audit Log | Access to patient images and reports is not logged, or the log shows the wrong user or time | Functional | High | P2 |
| [TC-019](test-cases/TEST_CASES.md#tc-019--high--p2--performance) | Performance | Viewer freezes, crashes or shows blank images when scrolling very large studies (1000+ slices, multi-frame, 4K) | Performance | High | P2 |
| [TC-020](test-cases/TEST_CASES.md#tc-020--medium--p2--functional) | Compression | Lossy compressed images are shown or exported as if they were lossless, or quality drops after repeated re-save | Functional | Medium | P2 |
| [TC-021](test-cases/TEST_CASES.md#tc-021--medium--p2--boundary) | Date/Time | Study and acquisition dates/times shift or sort wrongly across timezones, DST, or when time tags are missing | Boundary | Medium | P2 |
| [TC-022](test-cases/TEST_CASES.md#tc-022--medium--p2--functional) | Printing / Export | Printed film, PDF or screenshot export has the wrong scale, missing overlays, or cropped patient information | Functional | Medium | P2 |
| [TC-023](test-cases/TEST_CASES.md#tc-023--high--p1--integration) | Integration | HL7/worklist messages (order, update, cancel, merge) are lost, duplicated or applied to the wrong patient | Integration | High | P1 |
| [TC-024](test-cases/TEST_CASES.md#tc-024--high--p2--negative) | Session / Data Loss | Unsaved annotations or report text are lost on timeout, network drop or browser refresh | Negative | High | P2 |
| [TC-025](test-cases/TEST_CASES.md#tc-025--medium--p2--compatibility) | Compatibility | Viewer behaves differently across browsers, monitors and zoom levels (colors, grayscale, high-DPI, multi-monitor) | Compatibility | Medium | P2 |

## Techniques demonstrated

- Patient safety testing: wrong-patient display, orientation markers, signed-report locking
- Measurement accuracy: pixel spacing, anisotropic images, Hounsfield units with per-slice rescale values
- DICOM import and integration: missing or private tags, duplicate UIDs, demographic updates, HL7 order and merge messages
- Privacy and security: anonymized exports, cross-site access control, audit logging
- Workflow and data loss: annotations, worklist filters, report drafts, session timeout
- Series handling: slice ordering, MPR sync, prior/current comparison
- Performance, compression and cross-browser/monitor compatibility

## Repository layout

```
├── README.md
├── test-cases/
│   ├── TEST_CASES.md     # full detail for all cases, grouped by module
│   └── test-cases.csv    # same data, importable into Jira / Xray / TestRail
└── source/
    └── Medical_Imaging_Test_Cases.xlsx   # original spreadsheet
```

## Author

**Chereth Cucharo**, Senior QA Engineer / QA Lead. 9+ years in manual, functional, regression, compatibility and API testing.

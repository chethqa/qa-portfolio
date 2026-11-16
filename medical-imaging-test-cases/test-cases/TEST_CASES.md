# Test Cases

Full detail for every test case, grouped by module.

## Contents

- [Patient Safety](#patient-safety) (1)
- [Display](#display) (2)
- [Measurements](#measurements) (2)
- [DICOM Import](#dicom-import) (3)
- [Series Handling](#series-handling) (1)
- [Multi-planar](#multi-planar) (1)
- [Comparison](#comparison) (1)
- [Annotations](#annotations) (1)
- [Reporting](#reporting) (2)
- [Worklist](#worklist) (1)
- [Anonymization](#anonymization) (1)
- [Access Control](#access-control) (1)
- [Audit Log](#audit-log) (1)
- [Performance](#performance) (1)
- [Compression](#compression) (1)
- [Date/Time](#datetime) (1)
- [Printing / Export](#printing--export) (1)
- [Integration](#integration) (1)
- [Session / Data Loss](#session--data-loss) (1)
- [Compatibility](#compatibility) (1)

## Patient Safety

### TC-001 · Critical / P1 · Functional

**Defect scenario:** Image displayed under the wrong patient when two patients have the same name or similar IDs

**Preconditions:** Two synthetic patients exist: 'DOE^JOHN' ID 1001 and 'DOE^JOHN' ID 1010 (different DOB).

**Test data:** Patient IDs 1001 and 1010; same name, different DOB and studies

**Steps:**

1. Search the worklist by name 'DOE'
2. Open the study for ID 1001, then switch to ID 1010 without closing the viewer
3. Check the patient banner, DOB, study date and images on every switch

**Expected result:** Banner (name, ID, DOB) always matches the displayed images. Similar names are clearly distinguished in the list. No image from one patient appears under the other.

**Status:** Not Run

---


## Display

### TC-002 · Critical / P1 · Functional

**Defect scenario:** Left/right orientation markers or image flip are wrong after rotate, flip or opening a study with unusual orientation

**Preconditions:** Study with L/R markers and a known asymmetric feature. Study with non-standard patient orientation.

**Test data:** Actions: rotate 90, flip horizontal, flip vertical, reset

**Steps:**

1. Open the study and note the L/R markers
2. Apply each rotate/flip action and combine them
3. Reset the view and reload the study

**Expected result:** Orientation markers follow the image after every transform. Reset restores the original. The saved view state reloads correctly.

**Status:** Not Run

---

### TC-003 · High / P1 · Functional

**Defect scenario:** Window/level (brightness/contrast) presets show wrong values or reset when scrolling through a series

**Preconditions:** CT series with 200 slices. Presets: Lung, Bone, Brain.

**Test data:** Preset values per the preset table; custom W/L of 400/40

**Steps:**

1. Apply the Lung preset and scroll to slice 150
2. Set custom W/L and scroll back and forth
3. Switch to another series and return

**Expected result:** Chosen W/L stays across slices of the same series. Presets apply the documented values. The values shown on screen match what is rendered.

**Status:** Not Run

---


## Measurements

### TC-004 · Critical / P1 · Functional

**Defect scenario:** Distance or area measurement is wrong when pixel spacing is missing, anisotropic or the image is zoomed/resampled

**Preconditions:** Synthetic images: spacing 0.5 x 0.5 mm, 0.5 x 1.0 mm, and one with no pixel spacing tag.

**Test data:** Known reference object of 100 mm length (phantom)

**Steps:**

1. Draw a line on the 100 mm reference
2. Repeat at zoom 50%, 100% and 400%
3. Repeat on the anisotropic and no-spacing images

**Expected result:** Measurement is 100 mm (within tolerance) at every zoom. Anisotropic spacing is handled per axis. Missing spacing shows pixels or a clear 'uncalibrated' label, never a false mm value.

**Status:** Not Run

---

### TC-005 · Critical / P1 · Boundary

**Defect scenario:** Hounsfield unit (HU) value is wrong when rescale slope/intercept are missing or differ between slices

**Preconditions:** CT series where slope/intercept are 1 / -1024 on most slices and 1 / 0 on one slice.

**Test data:** Region of interest on water (expected about 0 HU) and air (about -1000 HU)

**Steps:**

1. Draw an ROI on each slice type
2. Compare mean, min and max HU with expected values
3. Try an image without rescale tags

**Expected result:** HU values are correct per slice using that slice's own rescale values. Missing tags are handled with a visible warning.

**Status:** Not Run

---


## DICOM Import

### TC-006 · High / P1 · Negative

**Defect scenario:** Import fails or silently skips files when the DICOM has missing optional tags, private tags, or very large size

**Preconditions:** Synthetic DICOM sets: missing StudyDescription, private vendor tags, 2 GB multi-frame, corrupted file.

**Test data:** Four files listed above plus one valid control file

**Steps:**

1. Import each file (or send by C-STORE from a test modality)
2. Check the import log, study list and viewer
3. Import the same set again

**Expected result:** Valid files import, with missing optional fields shown as blank. Corrupted files are rejected with a clear message and logged. No silent skips. Re-import does not create duplicates.

**Status:** Not Run

---

### TC-007 · High / P1 · Integration

**Defect scenario:** Duplicate studies or series created when the same study arrives twice or UIDs repeat

**Preconditions:** Test modality or DICOM sender available.

**Test data:** Same StudyInstanceUID sent twice; same SeriesInstanceUID with different SOP; identical SOPInstanceUID

**Steps:**

1. Send the study
2. Send it again after 1 minute
3. Send a modified copy with the same SOP UID

**Expected result:** Duplicates are merged or rejected per policy. The study shows each image once, with no doubled slices. Conflicting copies are flagged, not overwritten silently.

**Status:** Not Run

---

### TC-008 · High / P2 · Functional

**Defect scenario:** Patient demographics are not updated (or are overwritten wrongly) when a later study arrives with a corrected name or DOB

**Preconditions:** Patient 1001 exists with a misspelled name.

**Test data:** New study for 1001 with corrected name and DOB; another with a conflicting DOB

**Steps:**

1. Send the study with the corrected name
2. Send a study with a conflicting DOB
3. Check the patient record, worklist and old studies

**Expected result:** Corrections follow the defined merge rules and are audit-logged. Conflicts are flagged for review, not auto-merged into the wrong patient.

**Status:** Not Run

---


## Series Handling

### TC-009 · High / P1 · Functional

**Defect scenario:** Slices appear in the wrong order or a slice is missing when instance numbers and slice positions disagree

**Preconditions:** CT series where InstanceNumber is shuffled but ImagePositionPatient is correct.

**Test data:** Series of 120 slices, one slice with a duplicate instance number

**Steps:**

1. Open the series and scroll from first to last
2. Check slice position values
3. Use the scroll bar and cine mode

**Expected result:** Slices are ordered by true anatomical position. All 120 slices are shown. Slice number and location shown on screen are correct.

**Status:** Not Run

---


## Multi-planar

### TC-010 · High / P2 · Functional

**Defect scenario:** MPR (axial/coronal/sagittal) crosshair or reformatted images are misaligned or show wrong anatomy side

**Preconditions:** Volumetric CT/MR series with consistent geometry.

**Test data:** Click a landmark in the axial view; move the crosshair

**Steps:**

1. Open the MPR view
2. Click a landmark in the axial view
3. Check the same landmark in the coronal and sagittal views
4. Scroll and rotate the volume

**Expected result:** All views stay synchronized at the same 3D point. The reformats are correctly oriented. No stretching on non-square voxels.

**Status:** Not Run

---


## Comparison

### TC-011 · High / P2 · Functional

**Defect scenario:** Comparing current and prior studies shows mismatched slices, wrong dates, or syncs scrolling to the wrong location

**Preconditions:** Patient has current and prior CT studies with different slice thickness.

**Test data:** Two studies 1 year apart, 1 mm and 5 mm slices

**Steps:**

1. Open both studies side by side
2. Turn on synchronized scrolling
3. Scroll and compare location values

**Expected result:** Sync uses anatomical position, not slice index. Study dates and descriptions are shown on the right sides. Different thickness is handled correctly.

**Status:** Not Run

---


## Annotations

### TC-012 · Critical / P1 · Regression

**Defect scenario:** Annotations, arrows and measurements disappear, move, or attach to the wrong image after save, reload or study update

**Preconditions:** A study with 3 annotated slices in two series.

**Test data:** Text note, arrow, ellipse, line measurement on slices 12, 40 and 78

**Steps:**

1. Add the annotations and save
2. Reload the study and open it from another workstation
3. Add a new series to the study and reopen

**Expected result:** Every annotation reappears on the exact image and position. Annotations are never moved to another slice or patient. Author and time are recorded.

**Status:** Not Run

---


## Reporting

### TC-013 · Critical / P1 · Concurrency

**Defect scenario:** Report is attached to the wrong study, or a signed/final report can still be edited or lost on concurrent edit

**Preconditions:** Two radiologists open the same study. Report status Draft.

**Test data:** Radiologist A signs; Radiologist B edits at the same time

**Steps:**

1. Both open the report
2. A edits and signs the report
3. B edits and saves afterwards
4. Check the report history

**Expected result:** Signed report is locked. B gets a conflict message and cannot overwrite. Version history shows both actions with user and time. The report stays linked to the correct study.

**Status:** Not Run

---

### TC-014 · High / P2 · Functional

**Defect scenario:** Key images or measurements inserted in the report do not match what was shown in the viewer (wrong slice, stale value)

**Preconditions:** Study with measurements on slices 12 and 40.

**Test data:** Insert key images and measurement values into the report

**Steps:**

1. Add key images from slices 12 and 40
2. Change a measurement after inserting it
3. Open the final report PDF

**Expected result:** Report images and values match the viewer. Edited measurements update or warn that the report is out of date. The PDF matches the on-screen report.

**Status:** Not Run

---


## Worklist

### TC-015 · High / P1 · Functional

**Defect scenario:** Worklist filters, sorting or status are wrong (study shown as read when unread, urgent studies not on top, studies disappear)

**Preconditions:** 20 synthetic studies with mixed priority (STAT, routine), status and dates.

**Test data:** Filters: modality CT, status Unread, date range, priority STAT

**Steps:**

1. Apply each filter alone and in combination
2. Sort by priority and date
3. Open a study, close it without signing, and check its status

**Expected result:** Counts and results match the data. STAT studies appear first. Status changes only on the defined actions. Refresh does not lose studies.

**Status:** Not Run

---


## Anonymization

### TC-016 · Critical / P1 · Security

**Defect scenario:** De-identified (anonymized) export still contains patient name, ID, dates or private tags, or burned-in text in pixel data

**Preconditions:** Study with patient data in header tags, private tags and burned-in text on an ultrasound image.

**Test data:** Export using the anonymize option

**Steps:**

1. Export the study anonymized
2. Inspect the DICOM tags with a DICOM tag viewer
3. Open the images and look for burned-in text
4. Check the export file names and folders

**Expected result:** No direct identifiers in tags, file names or folder names. Burned-in text is flagged or removed. Study links (UIDs) stay consistent in the set. The export is audit-logged.

**Status:** Not Run

---


## Access Control

### TC-017 · Critical / P1 · Security

**Defect scenario:** User sees studies from another site or department, or a technologist can sign reports (role escalation)

**Preconditions:** Users: Radiologist (Site A), Technologist (Site A), Radiologist (Site B). Studies at both sites.

**Test data:** Direct URL to a Site B study as a Site A user; API calls for the same

**Steps:**

1. Log in as the Site A radiologist
2. Open the Site B study URL and API endpoints directly
3. As the technologist, try to sign or amend a report

**Expected result:** Cross-site access is denied (403/404) in UI and API. The technologist cannot sign. All denied attempts are logged.

**Status:** Not Run

---


## Audit Log

### TC-018 · High / P2 · Functional

**Defect scenario:** Access to patient images and reports is not logged, or the log shows the wrong user or time

**Preconditions:** Audit log is enabled.

**Test data:** Actions: open study, print, export, share, change report, delete

**Steps:**

1. Perform each action as different users
2. Review the audit log by user and patient
3. Try to edit or delete a log entry

**Expected result:** Each action has one entry (who, what, patient/study, when). Time is correct for the viewer's timezone. Entries cannot be edited or deleted.

**Status:** Not Run

---


## Performance

### TC-019 · High / P2 · Performance

**Defect scenario:** Viewer freezes, crashes or shows blank images when scrolling very large studies (1000+ slices, multi-frame, 4K)

**Preconditions:** Workstation with minimum supported specification.

**Test data:** CT with 1500 slices; 500-frame cine; 4K display

**Steps:**

1. Open the large study
2. Scroll quickly with the mouse wheel and the scroll bar
3. Open three large studies at once
4. Monitor memory and time to first image

**Expected result:** First image shows within the agreed time. Scrolling is smooth with no blank slices. Memory is released when studies close. No crash.

**Status:** Not Run

---


## Compression

### TC-020 · Medium / P2 · Functional

**Defect scenario:** Lossy compressed images are shown or exported as if they were lossless, or quality drops after repeated re-save

**Preconditions:** Study stored as JPEG lossy and a lossless copy.

**Test data:** Same image in lossless, JPEG 90 and JPEG 50

**Steps:**

1. Open each version and compare the images
2. Export and re-import the lossy version several times
3. Check the image information overlay

**Expected result:** Lossy compression is labeled. Original data is never lossy-recompressed without notice. Quality does not degrade after re-save or export.

**Status:** Not Run

---


## Date/Time

### TC-021 · Medium / P2 · Boundary

**Defect scenario:** Study and acquisition dates/times shift or sort wrongly across timezones, DST, or when time tags are missing

**Preconditions:** Studies from sites in different timezones. One study with no StudyTime.

**Test data:** Study at 23:55 on 2026-12-31; DST change day; missing time

**Steps:**

1. Import the studies
2. Check the date/time in the worklist, banner and report
3. Sort the worklist by date
4. View as a user in another timezone

**Expected result:** Dates stay on the correct day. Sorting is consistent. Missing time is handled with a blank or flag. The timezone rule is the same everywhere.

**Status:** Not Run

---


## Printing / Export

### TC-022 · Medium / P2 · Functional

**Defect scenario:** Printed film, PDF or screenshot export has the wrong scale, missing overlays, or cropped patient information

**Preconditions:** Study with measurements and overlays.

**Test data:** Print layouts: 1x1, 2x2, 4x4; export JPEG and PDF

**Steps:**

1. Print or export with each layout
2. Check the scale bar, measurements, banner and orientation markers
3. Compare with the on-screen view

**Expected result:** Output matches the screen. Patient banner, markers and measurements are present and not cropped. Scale is correct.

**Status:** Not Run

---


## Integration

### TC-023 · High / P1 · Integration

**Defect scenario:** HL7/worklist messages (order, update, cancel, merge) are lost, duplicated or applied to the wrong patient

**Preconditions:** Test HIS/RIS sending HL7 messages.

**Test data:** ORM (new order), ORM (cancel), ADT A08 (update), ADT A40 (merge)

**Steps:**

1. Send each message type
2. Check the worklist and patient records
3. Resend the same message
4. Send messages out of order

**Expected result:** Each message is applied once to the correct patient. Resends are ignored safely. Out-of-order messages are handled and logged. Errors are visible to admins.

**Status:** Not Run

---


## Session / Data Loss

### TC-024 · High / P2 · Negative

**Defect scenario:** Unsaved annotations or report text are lost on timeout, network drop or browser refresh

**Preconditions:** Session timeout set to 15 minutes. Unsaved report draft with annotations.

**Test data:** Idle for 16 minutes; disconnect network for 2 minutes; press refresh

**Steps:**

1. Start a report and annotate without saving
2. Trigger each condition
3. Log in again and open the study

**Expected result:** Draft is autosaved or a warning is shown before leaving. After login the work is restored. No data is silently lost.

**Status:** Not Run

---


## Compatibility

### TC-025 · Medium / P2 · Compatibility

**Defect scenario:** Viewer behaves differently across browsers, monitors and zoom levels (colors, grayscale, high-DPI, multi-monitor)

**Preconditions:** Chrome, Edge, Firefox, Safari. Dual monitors with different resolutions.

**Test data:** Browser zoom 100%, 125%, 150%; move the window between monitors

**Steps:**

1. Open the same study in each browser
2. Change browser zoom and move between monitors
3. Compare grayscale, W/L and measurements

**Expected result:** Images and measurements are the same in all supported browsers. The layout holds at different zooms and on both monitors. Unsupported browsers show a clear message.

**Status:** Not Run

---

# Bug Reports — Examples and Template

> Examples below are illustrative templates, not verified defects. Report a defect as observed only after reproducing it in an authorized test environment.

## Defect report template
| Field | Details |
|---|---|
| Defect ID | BUG-XXX |
| Title | [Area] Concise description of observed problem |
| Environment | App/version, OS, browser/device |
| Preconditions | Required account state, settings, or data |
| Steps to reproduce | Exact numbered steps |
| Expected result | Requirement-based expected behavior |
| Actual result | What happened during execution |
| Severity | Critical / High / Medium / Low, with rationale |
| Priority | Agreed with product team; distinct from severity |
| Reproducibility | Always / Intermittent / Once / Unable to reproduce |
| Evidence | Sanitized screenshot/video/logs |
| Related test case | TC-XXX |
| Status | New / Triaged / In Progress / Fixed / Retest / Closed / Reopened |

## Illustrative example — Form validation
**Title:** Registration form accepts malformed email address

- **Environment:** Record during execution.
- **Preconditions:** Registration form is available.
- **Steps:** 1. Open the form. 2. Enter `chereth@`. 3. Complete other required fields with safe synthetic data. 4. Submit.
- **Expected:** The form rejects malformed email and explains how to correct it.
- **Actual:** Not yet executed — record the actual result.
- **Severity/priority:** Assess after confirming behavior and impact.
- **Evidence:** Attach a screenshot only if reproduced.

## Reporting practices
- Use a concise, specific title.
- Include environment and build.
- Keep steps minimal and repeatable.
- Separate expected and actual results.
- Attach relevant evidence and link the test case.
- Remove credentials and personal/customer data.
- Retest fixes and run targeted regression.

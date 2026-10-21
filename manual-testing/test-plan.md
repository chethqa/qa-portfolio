# Manual Testing Test Plan

## Document information
| Field | Value |
|---|---|
| Project | Demo Web Application — Manual QA Case Study |
| Author | Chereth Cucharo |
| Status | Starter template — customize before execution |
| Version | 1.0 |
| Test types | Functional, negative, boundary, usability, regression |

## 1. Objective
Evaluate whether selected workflows behave according to documented requirements, provide useful validation and error handling, and avoid regressions in the tested scope.

## 2. Scope
**In scope:** page loading; required-field and format validation; valid and invalid submissions; boundary inputs; success/error messages; navigation; targeted regression; supported-browser checks.

**Out of scope unless added:** penetration testing, security certification, load testing, full accessibility conformance, production data, and third-party systems outside the agreed boundary.

## 3. Approach
1. Review requirements and expected behavior.
2. Identify critical journeys and risks.
3. Prepare synthetic test data and test cases.
4. Execute positive, negative, boundary, and exploratory tests.
5. Record actual results and evidence.
6. Log reproducible defects.
7. Retest fixes and run targeted regression.
8. Summarize coverage, unresolved risks, and recommendation.

## 4. Entry criteria
- Environment is reachable and authorized.
- Scope and expected behavior are understood.
- Safe test data and test accounts are available.
- Required browsers/devices are available.
- Known limitations are documented.

## 5. Exit criteria
- In-scope cases have an execution status.
- Failed cases have linked defect records or explanations.
- Critical workflows have been tested.
- Blocking issues and residual risks are communicated.
- The release recommendation reflects actual results.

## 6. Test types
| Type | Purpose | Example |
|---|---|---|
| Positive | Confirm valid use works | Submit valid form data |
| Negative | Confirm invalid input is handled | Submit malformed email |
| Boundary | Check edge values | Minimum/maximum field length |
| Regression | Check existing behavior after change | Repeat key flow after a fix |
| Compatibility | Check supported browsers/devices | Repeat flow in Chrome and Firefox |
| Exploratory | Investigate uncovered risks | Try unusual navigation sequences |

## 7. Record the actual environment
- Application/version/build:
- URL/environment:
- Date/time:
- OS/device:
- Browser/version:
- User role:
- Test data:
- Relevant configuration:
- Limitations:

Use synthetic data only. Never include passwords, tokens, personal information, or confidential customer data in this public portfolio.

## 8. Risks and mitigations
| Risk | Mitigation |
|---|---|
| Unclear requirements | Confirm expected behavior before assigning status |
| Unstable environment | Record interruptions; mark affected tests Blocked |
| Shared test data | Use isolated, authorized demo data |
| Browser differences | Run agreed critical flows in supported browsers |
| Stale results | Record execution date and version/build |

## 9. Deliverables
Completed test workbook, defect reports/evidence, execution summary, retest/regression results, open risks, and release recommendation.

**Current recommendation: Pending execution.**

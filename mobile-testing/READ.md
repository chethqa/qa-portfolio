# Mobile Testing

This folder highlights my mobile QA strategy and practical mobile test case design for Android, iOS, and mobile web workflows.

## Contents

- **`Mobile-Testing-Approach.pdf`** — End-to-end mobile QA approach covering the 5-phase testing workflow (*Understand, Plan, Execute, Report, Improve*), Android vs. iOS platform-specific checks, device and OS coverage tiers, a sample 2-week release cycle, and actionable bug reporting standards.
- **`Forminator_Mobile_QA_Test_Cases.xlsx`** — Mobile QA test suite for the **Forminator WordPress Plugin** covering **Critical** and **Major** severity test cases across narrow mobile viewports (`320px–430px`), AJAX form submissions, Stripe/PayPal/mobile wallet & 3DS payments, native camera/file uploads, multi-step pagination, touch signatures, virtual keyboards, date/time pickers, searchable dropdowns, popups/sticky headers, CAPTCHAs, and WP Admin mobile UI. Includes a dynamic execution summary dashboard and severity chart.

## Highlights

- **Platform-Specific Coverage:** Tailored checks for Android fragmentation, gestures, permissions, and OEM skins alongside iOS safe areas/Dynamic Island, Apple Pay, Focus modes, and Safari behaviors.
- **Real-World Mobile Conditions:** Tests throttled networks, touch gestures, keyboard occlusion, viewport auto-zoom, and payment/modal edge cases.
- **Actionable Deliverables:** Structured test cases with preconditions, steps, expected results, status tracking, and severity alignment.
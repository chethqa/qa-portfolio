# Ecommerce Test Plan

A generic test plan for an online store, covering the areas that most affect revenue and customer trust. It can be adapted to any platform (Shopify, WooCommerce, Magento or a custom build).

**Author:** Chereth Cucharo, Senior QA Engineer
**Document type:** Test plan summary with sample critical test cases

---

## 1. Objectives

- Verify that customers can browse, add to cart, check out and pay without errors.
- Confirm that orders, refunds, inventory and emails behave correctly after purchase.
- Protect customer data and payments, and confirm the store works across browsers and devices.
- Find critical and high-severity defects before launch or release.

## 2. Scope

| In scope | Out of scope |
|---|---|
| Storefront, cart, checkout and payments | Load testing at extreme scale (separate plan) |
| Customer accounts and order management | Penetration testing by a specialist team |
| Shipping, tax and inventory rules | Third-party service internals (payment gateway, carrier) |
| Installed apps, plugins and integrations | |
| Compatibility, performance and accessibility | |

## 3. Test Environment

- **Store setup:** staging store with the payment gateway in sandbox/test mode
- **Browsers:** Chrome, Safari, Firefox, Edge (latest two versions)
- **Devices:** desktop, tablet, iOS and Android phones
- **Test data:** products with and without variants, discount codes, test customer accounts, test cards and sandbox wallet accounts

## 4. Test Areas

| Area | What is tested |
|---|---|
| **Functional** | Search, filters, product pages, variants, cart, customer accounts, forms |
| **Checkout & Payments** | Guest and account checkout, cards, wallets, declines, 3D Secure, taxes, shipping rates |
| **Orders & Fulfillment** | Confirmation page and email, admin orders, refunds, cancellations, inventory updates |
| **Integrations** | Analytics, email/SMS, CRM, ERP, carrier APIs, webhooks |
| **Compatibility** | Browsers, devices, responsive layouts |
| **Performance** | Core Web Vitals, page weight, third-party scripts, load handling |
| **Security & Compliance** | HTTPS, PCI-DSS responsibilities, access control, input validation, privacy laws |
| **Accessibility & SEO** | WCAG 2.1 AA, keyboard use, screen readers, metadata, redirects, structured data |

## 5. Approach

- **Priority:** critical paths first (browse, cart, checkout, payment), then secondary features.
- **Types of testing:** manual functional, regression, compatibility, API (Postman), exploratory.
- **Regression:** re-run the critical-path suite after every theme, plugin or app update.
- **Defect tracking:** each defect is logged with steps, expected and actual results, severity, environment and evidence.

### Severity guide

| Severity | Meaning | Example |
|---|---|---|
| Critical | Blocks purchase or risks money or data | Customer charged twice |
| High | Major feature broken, workaround exists | Discount code not applied |
| Medium | Minor feature or edge case fails | Wrong sort order |
| Low | Cosmetic or wording issue | Misaligned icon |

## 6. Tools

Jira (defect tracking), Postman (API testing), Chrome DevTools, BrowserStack or real devices, Lighthouse and PageSpeed Insights, axe DevTools.

## 7. Entry and Exit Criteria

**Entry:** build deployed to staging, test data and sandbox payments ready, smoke test passed.

**Exit:**

- No open critical or high-severity defects
- All critical-path test cases passing
- Performance and accessibility targets met
- Regression suite passed on the release candidate

## 8. Sample Critical Test Cases

A selection from the full set of 25 cases in [`Ecommerce_Sample_Test_Cases.xlsx`](Ecommerce_Sample_Test_Cases.xlsx).

| ID | Area | Test case | Expected result |
|---|---|---|---|
| TC-CHK-001 | Checkout | Guest checkout with a valid card | Order placed, totals correct, confirmation email received, order paid in admin |
| TC-PAY-001 | Payments | Declined card | Clear error message, cart preserved, no order and no charge created |
| TC-PAY-002 | Payments | Double-click on Pay | Only one order and one charge created |
| TC-PAY-003 | Payments | Browser closed right after payment | Order still created via webhook; no payment without an order |
| TC-PAY-006 | Payments | Charged amount matches order total | Checkout, confirmation, admin order and gateway charge are identical |
| TC-CRT-003 | Cart & Pricing | Last unit in stock bought by two users | Second user told item is unavailable; stock never goes negative |
| TC-ORD-002 | Orders | Full refund | Gateway refunded, order status updated, customer notified, stock restocked |
| TC-SEC-001 | Security | Viewing another customer's order | Access denied; no data of other customer exposed |
| TC-SEC-002 | Security | Tampering with price in the request | Server ignores client price and rejects the request |
| TC-MOB-001 | Compatibility | Checkout on iOS and Android | Works end to end on both platforms |

## 9. Risks and Assumptions

- Payment, tax and shipping behavior is verified in sandbox mode; a final low-value live transaction is recommended at launch, then refunded.
- Third-party apps can change behavior after updates, so integrations are re-tested on each release.
- Test cases are samples and should be adjusted to the store's products, markets and payment methods.

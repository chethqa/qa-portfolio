# WordPress Plugin Regression Checklist

Mark each item Pass, Fail, Blocked, N/A, or Not Run. Attach evidence where useful.

## Environment
- [ ] Record WordPress, PHP, plugin, theme, browser, and relevant database versions.
- [ ] Record single-site or multisite configuration.
- [ ] Confirm test site is isolated and has a recoverable backup.
- [ ] Review change scope, ticket, and risk areas.

## Installation and lifecycle
- [ ] Fresh installation works.
- [ ] Activation works without fatal errors.
- [ ] Deactivation works as documented.
- [ ] Upgrade from supported previous version works.
- [ ] Settings/data are preserved or migrated as documented.
- [ ] Uninstall behavior matches documentation on disposable data.

## Core functionality
- [ ] Main user workflow works.
- [ ] Settings load, save, and persist.
- [ ] Required fields and invalid input are handled.
- [ ] Success/error messages are accurate.
- [ ] Empty, boundary, and unusual input values are handled.
- [ ] Previously fixed defects remain fixed.

## WordPress integration
- [ ] Admin pages load for correct user roles.
- [ ] Permission checks behave as designed.
- [ ] Front-end pages render correctly.
- [ ] Relevant hooks, shortcodes, blocks, widgets, and integrations work if applicable.
- [ ] No unexpected PHP warnings, notices, or fatal errors in relevant logs.
- [ ] No unexpected JavaScript console errors in tested flows.

## Compatibility
- [ ] Supported browsers checked.
- [ ] Relevant responsive/mobile views checked.
- [ ] Supported WordPress versions checked as required.
- [ ] Supported PHP versions checked as required.
- [ ] Theme/plugin conflict checks performed if in scope.
- [ ] Multisite behavior checked if supported.

## Data handling smoke checks
- [ ] Roles cannot access unauthorized settings/actions.
- [ ] Input/output behavior matches expected validation and escaping.
- [ ] Sensitive data is not exposed in error messages or logs.
- [ ] Only synthetic data and authorized environments used.

## Completion
- [ ] Failed tests have defect IDs or documented explanations.
- [ ] Fixes retested.
- [ ] Targeted regression completed.
- [ ] Open risks and limitations recorded.
- [ ] Test summary and release recommendation updated.

## Run details
- Date:
- Tester:
- Plugin/version:
- WordPress/PHP:
- Environment:
- Change/ticket:
- Overall status:
- Open defects:
- Recommendation:

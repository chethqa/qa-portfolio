# WordPress Plugin QA — Test Cases

> Adapt these planned scenarios to a plugin and environment you are authorized to test. They are not claims of completed execution.

## Environment to record
WordPress version; PHP version; plugin/version; theme; environment; browser; user role; single-site/multisite; date/build.

| ID | Area | Scenario | Expected result | Priority | Status |
|---|---|---|---|---|---|
| WP-001 | Installation | Install from an authorized source | Installation completes or gives a clear error | High | Not Run |
| WP-002 | Activation | Activate plugin | Activates without fatal error; expected UI is available | High | Not Run |
| WP-003 | Deactivation | Deactivate plugin | Disabled cleanly; cleanup matches documentation | Medium | Not Run |
| WP-004 | Settings | Open settings as authorized admin | Settings page loads without unexpected errors | High | Not Run |
| WP-005 | Permissions | Open settings as lower-privilege user | Access is restricted as designed | High | Not Run |
| WP-006 | Validation | Save invalid/missing settings | Invalid values rejected with useful feedback | High | Not Run |
| WP-007 | Persistence | Save settings, navigate away, return | Settings persist correctly | High | Not Run |
| WP-008 | Front end | Visit affected page | Feature behaves as documented | High | Not Run |
| WP-009 | Theme | Repeat flow with supported theme | Layout and behavior remain usable | Medium | Not Run |
| WP-010 | Browser | Repeat critical flow in supported browsers | No unexpected supported-browser differences | Medium | Not Run |
| WP-011 | Upgrade | Upgrade from supported prior version on test site | Upgrade completes; data/settings preserved as documented | High | Not Run |
| WP-012 | Regression | Recheck core flows after update | Existing functionality still works | High | Not Run |
| WP-013 | Conflict | Test controlled compatible plugin set | No unexpected conflict; confirmed conflicts documented | Medium | Not Run |
| WP-014 | Error handling | Trigger supported failure condition | Error handled gracefully without sensitive data exposure | Medium | Not Run |
| WP-015 | Uninstall | Follow uninstall procedure on disposable data | Cleanup matches documentation | Medium | Not Run |
| WP-016 | Multisite | If supported, test network activation and site behavior | Behavior matches multisite documentation | Medium | Not Run |
| WP-017 | Responsive UI | Check relevant narrow viewport | Controls remain visible and usable | Low | Not Run |
| WP-018 | Accessibility smoke test | Keyboard-navigate key controls | Controls reachable; focus visible; not a full audit | Medium | Not Run |

## Execution record
For each case, add actual result, Pass/Fail/Blocked/Not Run, evidence, defect ID, date, and tester.

## Notes
- Never test destructive upgrade/uninstall flows on production.
- Back up disposable environments before upgrades or cleanup.
- Confirm expected behavior before logging a defect.
- Never publish credentials, API keys, private URLs, or confidential information.

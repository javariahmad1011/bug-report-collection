# BUG-001 — User Search Ignores Status Filter When Username Is Provided

## Summary

When an administrator searches for system users using both the **Username** and **Status** filters, the selected Status value is not applied.



# BUG-001 — User Search Ignores Status Filter When Username Is Provided

## Summary

When an administrator searches for system users using both the **Username** and **Status** filters, the selected Status value is not applied. The search results include users with statuses other than the one selected.

---

## Bug Details

| Field                   | Details                                                     |
| ----------------------- | ----------------------------------------------------------- |
| **Bug ID**              | BUG-001                                                     |
| **Title**               | User Search Ignores Status Filter When Username Is Provided |
| **Module**              | Admin                                                       |
| **Feature**             | User Management — User Search / Filtering                   |
| **Category**            | Functional                                                  |
| **Environment**         | OrangeHRM Starter Demo                                      |
| **Browser**             | Google Chrome 150.x                                         |
| **Operating System**    | Windows 11 Pro 64-bit                                       |
| **Application Version** | OrangeHRM Starter — Hosted Demo                             |
| **Build Number**        | Not exposed in application UI                               |
| **Severity**            | Medium                                                      |
| **Priority**            | High                                                        |
| **Reporter**            | QA Engineer                                                 |
| **Assigned To**         | Unassigned                                                  |
| **Status**              | New                                                         |
| **Reproducibility**     | Reproducible                                                |
| **Frequency**           | 5/5 attempts                                                |

---

## Description

The **System Users** search allows an administrator to filter user records using multiple criteria.

When a Username value is entered together with **Status = Disabled**, the application returns matching usernames regardless of their account status.

The Username criterion is applied successfully, but the Status criterion appears to be ignored when both filters are submitted together.

This results in an inaccurate filtered result set and may lead administrators to incorrectly identify enabled accounts as disabled accounts.

---

## Preconditions

1. User is logged in with an account that has access to the **Admin** module.
2. User has permission to access **User Management**.
3. At least two system users exist with similar usernames.
4. One test user has **Enabled** status.
5. One test user has **Disabled** status.

---

## Test Data

| Username                 | User Role | Status   |
| ------------------------ | --------- | -------- |
| qa.automation.active     | ESS       | Enabled  |
| qa.automation.disabled   | ESS       | Disabled |
| qa.automation.regression | ESS       | Enabled  |

**Search criteria:**

| Field    | Value         |
| -------- | ------------- |
| Username | qa.automation |
| Status   | Disabled      |

---

## Steps to Reproduce

1. Launch the OrangeHRM application.
2. Log in using a valid administrator account.
3. Navigate to **Admin**.
4. Open **User Management > Users**.
5. Locate the System Users search/filter section.
6. Enter `qa.automation` in the **Username** field.
7. Select **Disabled** from the **Status** dropdown.
8. Leave all other filters unchanged.
9. Click **Search**.
10. Review the records displayed in the search results.

---

## Actual Result

The search results contain users matching the Username criterion with both **Enabled** and **Disabled** statuses.

Example:

| Username                 | Status   |
| ------------------------ | -------- |
| qa.automation.active     | Enabled  |
| qa.automation.disabled   | Disabled |
| qa.automation.regression | Enabled  |

The selected **Status = Disabled** filter is not reflected in the returned result set.

---

## Expected Result

The system should apply all populated search criteria together.

When:

* Username contains `qa.automation`
* Status equals `Disabled`

the results should contain only records matching both conditions.

Expected result:

| Username               | Status   |
| ---------------------- | -------- |
| qa.automation.disabled | Disabled |

No **Enabled** user accounts should be displayed.

---

## Business Impact

Administrators cannot reliably identify users based on account status when combining Username and Status filters.

This can cause:

* Incorrect user-account reviews.
* Additional manual verification effort.
* Incorrect administrative decisions when enabling or disabling accounts.
* Increased risk during user-access audits.
* Difficulty identifying inactive or disabled accounts in environments containing large numbers of users.

The issue has higher operational significance for organizations using User Management results during periodic access-control reviews.

---

## Root Cause Analysis

**Status:** Suspected — requires developer confirmation.

The search criteria implementation may not be combining the Username and Status parameters correctly.

Possible causes include:

* The Status parameter is omitted from the search request when Username is populated.
* The backend query applies only the Username condition.
* Multiple criteria are not being combined using the expected logical `AND` condition.
* The frontend Status value is not correctly mapped to the corresponding backend status identifier.

Network request and backend query inspection are required to confirm the root cause.

---

## Suggested Fix

Update the User Management filtering logic so that all populated search criteria are submitted and evaluated together.

For this scenario, the resulting query should logically behave as:

```text
Username contains "qa.automation"
AND
Status = Disabled
```

The fix should also be validated against the following combinations:

* Username only
* Status only
* Username + Status
* Username + User Role
* Username + Employee Name
* Username + User Role + Status
* All available filters populated
* Reset followed by a new search

Automated regression coverage should be added for multi-filter search combinations.

---

## Attachments

| Attachment                              | Description                                                                |
| --------------------------------------- | -------------------------------------------------------------------------- |
| `BUG-001_user_search_status_filter.png` | Screenshot showing Status = Disabled with Enabled users present in results |
| `BUG-001_user_search_results.png`       | Search result table containing mixed account statuses                      |
| `BUG-001_user_search_request.har`       | Network request captured while submitting the combined filters             |
| `BUG-001_user_search_response.json`     | API response returned for the affected search                              |

---

## Notes

* Issue reproduces consistently across repeated searches.
* Status-only filtering should be verified separately to isolate whether the defect affects the Status control itself or only combined filtering.
* The **Reset** action should also be tested to confirm that stale filter values are not retained between searches.
* Cross-browser verification should be completed in Firefox and Microsoft Edge after the defect is confirmed.
* API/network inspection is recommended before assigning the defect to determine whether the issue originates in the frontend request or backend filtering logic.

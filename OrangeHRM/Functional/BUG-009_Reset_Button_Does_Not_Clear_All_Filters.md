# BUG-009 — Reset Button Does Not Clear Applied User Search Filters

## Summary

When an administrator applies one or more filters on the **System Users** page and clicks the **Reset** button, the selected filter values are not completely cleared. Some fields retain their previous values, causing subsequent searches to use stale search criteria.

---

# Bug Details

| Field                   | Details                                                 |
| ----------------------- | ------------------------------------------------------- |
| **Bug ID**              | BUG-009                                                 |
| **Title**               | Reset Button Does Not Clear Applied User Search Filters |
| **Module**              | Admin                                                   |
| **Feature**             | User Management → System Users Search → Reset Filters   |
| **Category**            | Functional                                              |
| **Environment**         | OrangeHRM Demo                                          |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/              |
| **Browser**             | Google Chrome Version 150.x (64-bit)                    |
| **Operating System**    | Windows 11 Pro 64-bit                                   |
| **Application Version** | Hosted Demo                                             |
| **Build Number**        | N/A                                                     |
| **Severity**            | Medium                                                  |
| **Priority**            | Medium                                                  |
| **Reporter**            | Javaria Ahmad                                           |
| **Assigned To**         | TBD                                                     |
| **Status**              | New                                                     |
| **Reproducibility**     | Always                                                  |
| **Frequency**           | 5/5 Attempts                                            |

---

# Description

The **System Users** page provides multiple filters that allow administrators to search for users by Username, User Role, Employee Name, and Status.

The page also provides a **Reset** button that should clear all entered and selected search criteria and restore the search section to its default state.

When multiple filters are populated and the administrator clicks **Reset**, the Username field is cleared; however, previously selected dropdown values such as **User Role** and **Status** remain populated.

If the administrator performs another search without manually clearing these retained values, the application continues to apply the stale filters.

This results in unexpected search results and makes the Reset functionality unreliable.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **Admin → User Management → Users**.
3. Ensure multiple user records are available with different roles and statuses.
4. The **System Users** search panel must be visible.

---

# Test Data

### Initial Search Criteria

| Field         | Value      |
| ------------- | ---------- |
| Username      | john       |
| User Role     | ESS        |
| Employee Name | John Smith |
| Status        | Disabled   |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **Admin**.
4. Open **User Management → Users**.
5. Enter **john** in the Username field.
6. Select **ESS** from the User Role dropdown.
7. Enter or select **John Smith** in the Employee Name field.
8. Select **Disabled** from the Status dropdown.
9. Click **Search**.
10. Verify that filtered search results are displayed.
11. Click the **Reset** button.
12. Observe the values displayed in all search filter fields.
13. Without manually changing any retained field, click **Search** again.
14. Observe the returned search results.

---

# Actual Result

The **Reset** button does not clear all populated search criteria.

The Username field is cleared, but one or more dropdown fields retain their previously selected values.

### Observed Filter State After Reset

| Field         | Value Before Reset | Value After Reset |
| ------------- | ------------------ | ----------------- |
| Username      | john               | Cleared           |
| User Role     | ESS                | ESS               |
| Employee Name | John Smith         | Cleared           |
| Status        | Disabled           | Disabled          |

The retained **User Role** and **Status** values continue to affect subsequent searches.

As a result, clicking **Search** after Reset does not return the complete default user list.

---

# Expected Result

Clicking the **Reset** button should clear all search criteria and restore every filter to its default state.

### Expected Filter State After Reset

| Field         | Expected Value |
| ------------- | -------------- |
| Username      | Blank          |
| User Role     | Select         |
| Employee Name | Blank          |
| Status        | Select         |

After Reset:

1. No previous search criteria should remain selected.
2. Any validation or search state associated with the previous filters should be cleared.
3. The search form should return to its initial default state.
4. A subsequent search with no criteria should return the default System Users list.
5. Previous filter values should not be included in subsequent search requests.

---

# Business Impact

This issue causes administrators to perform searches using unintended filter criteria.

Potential impacts include:

* Valid user records may appear to be missing.
* Administrators may make decisions based on incomplete search results.
* Additional manual effort is required to clear individual filters.
* User account reviews may become inefficient.
* Support teams may incorrectly assume user records do not exist.
* Access-management activities may be delayed.
* Users may incorrectly interpret the Reset action as having cleared all search criteria.

Although the issue does not cause direct data loss, it reduces the reliability of a frequently used administrative search function.

---

# Possible Cause

The Reset action appears to clear text input values but does not correctly reset the state of dropdown components.

Possible technical causes include:

* Dropdown components are not included in the form reset handler.
* Controlled dropdown values are maintained in frontend state after reset.
* Form state and UI component state are not synchronized.
* The Reset button clears only text-based form controls.
* Selected dropdown values are cached separately from the main search form.
* Search parameters are retained in application state after Reset.
* The request payload is not regenerated after the filters are reset.

Developer investigation is required to confirm the root cause.

---

# Suggested Fix

Update the Reset functionality so that all search criteria are restored to their default values.

The reset operation should clear:

1. Username
2. User Role
3. Employee Name
4. Status
5. Any hidden search parameters
6. Validation messages
7. Search-specific state
8. Cached filter values

The application should also ensure that no previous filter values are included in the next search request after Reset.

Regression testing should verify:

* Reset with Username only
* Reset with User Role only
* Reset with Employee Name only
* Reset with Status only
* Reset with all filters populated
* Reset after a successful search
* Reset after a search returning no records
* Reset before executing Search
* Search immediately after Reset
* Multiple Reset actions
* Reset after changing filter values several times
* Browser refresh after Reset
* Pagination after Reset
* Browser Back/Forward navigation
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                       | Description                                                  |
| -------------------------------- | ------------------------------------------------------------ |
| BUG-009_Filters_Before_Reset.png | Screenshot showing populated search filters before Reset     |
| BUG-009_Filters_After_Reset.png  | Screenshot showing User Role and Status retained after Reset |
| BUG-009_Search_After_Reset.png   | Screenshot showing filtered results returned after Reset     |
| BUG-009_Reset_Filter_Flow.mp4    | Screen recording demonstrating the Reset issue               |
| BUG-009_Search_Request.har       | Network request showing retained parameters after Reset      |
| BUG-009_Search_Payload.json      | Request payload containing stale filter values               |

---

# Notes

* Issue reproduced consistently with multiple filter combinations.
* Text fields and dropdown fields should be tested independently to identify which controls are affected.
* Verify whether retained dropdown values are visual only or are also included in the backend request.
* Confirm that Reset clears both UI state and underlying search parameters.
* Verify behavior after browser refresh and navigation away from the page.
* Retest all available search filter combinations after the defect is resolved.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

# BUG-010 — Pagination Record Count Does Not Match Displayed User Results

## Summary

The **System Users** page displays an incorrect pagination record count when navigating between result pages. The total record count shown in the pagination summary does not match the number of user records available across all pages.

---

# Bug Details

| Field                   | Details                                                       |
| ----------------------- | ------------------------------------------------------------- |
| **Bug ID**              | BUG-010                                                       |
| **Title**               | Pagination Record Count Does Not Match Displayed User Results |
| **Module**              | Admin                                                         |
| **Feature**             | User Management → System Users Pagination                     |
| **Category**            | Functional                                                    |
| **Environment**         | OrangeHRM Demo                                                |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/                    |
| **Browser**             | Google Chrome Version 150.x (64-bit)                          |
| **Operating System**    | Windows 11 Pro 64-bit                                         |
| **Application Version** | Hosted Demo                                                   |
| **Build Number**        | N/A                                                           |
| **Severity**            | Medium                                                        |
| **Priority**            | Medium                                                        |
| **Reporter**            | Javaria Ahmad                                                 |
| **Assigned To**         | TBD                                                           |
| **Status**              | New                                                           |
| **Reproducibility**     | Always                                                        |
| **Frequency**           | 5/5 Attempts                                                  |

---

# Description

The **System Users** page uses pagination when the number of user records exceeds the maximum number of rows displayed on a single page.

When multiple pages of user records are available, the pagination summary displays a total record count that does not match the actual number of records available across the result set.

For example, the page displays a total of **27 records**, while manually counting the records across all available pages results in **30 users**.

The incorrect count persists while navigating between pages and after refreshing the browser.

This creates inconsistent information between the pagination component and the actual user data displayed in the table.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **Admin → User Management → Users**.
3. Ensure enough user records exist to generate multiple result pages.
4. No search filters should be applied.
5. The user list should contain at least three pages of records.

---

# Test Data

### User List Configuration

| Field                    | Value |
| ------------------------ | ----- |
| Search Filters           | None  |
| Expected Total Users     | 30    |
| Records Per Page         | 10    |
| Expected Number of Pages | 3     |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **Admin**.
4. Open **User Management → Users**.
5. Ensure all search filters are empty.
6. Click **Search** if required to load the complete user list.
7. Observe the total record count displayed above or below the results table.
8. Count the number of user records displayed on Page 1.
9. Navigate to Page 2.
10. Count the number of records displayed.
11. Navigate to Page 3.
12. Count the remaining records.
13. Calculate the total number of records across all pages.
14. Compare the calculated total with the pagination record count displayed by the application.
15. Refresh the page and repeat the verification.

---

# Actual Result

The application displays an incorrect total record count.

### Observed Pagination Data

| Page             | Records Displayed |
| ---------------- | ----------------- |
| Page 1           | 10                |
| Page 2           | 10                |
| Page 3           | 10                |
| **Actual Total** | **30**            |

However, the pagination summary displays:

**27 Records Found**

The displayed record count does not match the actual number of records available across the pages.

The incorrect value remains visible when navigating between result pages.

---

# Expected Result

The pagination summary should accurately reflect the total number of user records returned by the system.

### Expected Pagination Data

| Item             | Expected Value   |
| ---------------- | ---------------- |
| Total Records    | 30               |
| Records Per Page | 10               |
| Total Pages      | 3                |
| Displayed Count  | 30 Records Found |

The total count should remain consistent across all pages unless the dataset changes due to:

* Record creation
* Record deletion
* Search filters
* User status changes
* Data refresh from the backend

---

# Business Impact

This issue provides administrators with inaccurate information about the number of system users.

Potential impacts include:

* Incorrect user-account inventory.
* Inaccurate access-review counts.
* Difficulty reconciling user records during audits.
* Administrators may assume records are missing.
* Incorrect reporting of active system users.
* Additional manual effort to verify total records.
* Reduced confidence in pagination and user-management data.
* Potential discrepancies between frontend totals and backend data.

The issue may be particularly significant during access reviews or compliance activities where accurate user counts are required.

---

# Possible Cause

The pagination component may be using an incorrect total-count value returned by the backend or calculated by the frontend.

Possible technical causes include:

* API response contains an incorrect total record count.
* Frontend uses the current page count instead of the complete dataset count.
* Deleted or inactive records are included inconsistently.
* Pagination metadata is not refreshed after user changes.
* Cached record count is used after the dataset changes.
* Count query and data query apply different filtering conditions.
* Off-by-one or offset calculation error in pagination logic.
* Backend count query excludes valid records that are still returned by the data endpoint.

Developer investigation is required to determine whether the discrepancy originates from the API response, database query, or frontend pagination logic.

---

# Suggested Fix

Ensure the pagination component uses the correct total number of records returned by the backend.

The application should:

1. Calculate the total record count using the same filtering criteria as the data query.
2. Return accurate pagination metadata from the API.
3. Refresh the total count when records are created or deleted.
4. Recalculate pagination after filters are applied or reset.
5. Prevent cached count values from being reused after dataset changes.
6. Ensure frontend pagination calculations use the backend total consistently.

Regression testing should verify:

* User list with fewer than one full page
* Exactly one full page of users
* Multiple result pages
* Partially filled final page
* Next page navigation
* Previous page navigation
* First page navigation
* Last page navigation
* Record count after creating a user
* Record count after deleting a user
* Record count after applying filters
* Record count after Reset
* Pagination after browser refresh
* Pagination after sorting
* Pagination after changing user status
* API pagination metadata
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                       | Description                                                |
| -------------------------------- | ---------------------------------------------------------- |
| BUG-010_Pagination_Count.png     | Screenshot showing incorrect total record count            |
| BUG-010_Page_1.png               | Screenshot showing records displayed on Page 1             |
| BUG-010_Page_2.png               | Screenshot showing records displayed on Page 2             |
| BUG-010_Page_3.png               | Screenshot showing records displayed on Page 3             |
| BUG-010_Pagination_Flow.mp4      | Screen recording demonstrating pagination across all pages |
| BUG-010_User_List_Request.har    | Network request captured while loading paginated results   |
| BUG-010_Pagination_Response.json | API response containing pagination metadata                |

---

# Notes

* Issue reproduced consistently while navigating through multiple result pages.
* Verify whether the incorrect count originates from frontend calculation or backend pagination metadata.
* Compare the API `total`, `count`, `limit`, and `offset` values with the records returned.
* Validate pagination after user creation and deletion.
* Verify whether hidden, disabled, or deleted records affect the displayed count.
* Perform regression testing with filtered and unfiltered result sets.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

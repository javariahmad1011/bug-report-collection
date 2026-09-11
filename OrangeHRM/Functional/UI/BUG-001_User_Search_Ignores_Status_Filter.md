
# BUG-001 — User Search Does Not Apply Status Filter When Combined With Username Filter

## Summary

When an administrator searches for system users using both the **Username** and **Status** filters, the selected Status value is not applied correctly. The search results include users with statuses other than the one selected.

---

# Bug Details

| Field | Details |
|------|---------|
| **Bug ID** | BUG-001 |
| **Title** | User Search Does Not Apply Status Filter When Combined With Username Filter |
| **Module** | Admin |
| **Feature** | User Management → System Users Search |
| **Category** | Functional |
| **Environment** | OrangeHRM Demo |
| **Application URL** | https://opensource-demo.orangehrmlive.com/ |
| **Browser** | Google Chrome Version 150.x (64-bit) |
| **Operating System** | Windows 11 Pro 64-bit |
| **Application Version** | Hosted Demo |
| **Build Number** | N/A |
| **Severity** | Medium |
| **Priority** | High |
| **Reporter** | Javaria Ahmad |
| **Assigned To** | TBD |
| **Status** | New |
| **Reproducibility** | Always |
| **Frequency** | 5/5 Attempts |

---

# Description

The **System Users** page allows administrators to filter users using multiple search criteria.

When both **Username** and **Status** filters are applied together, the application ignores the selected **Status** value and returns users that match only the Username criteria.

As a result, users with both **Enabled** and **Disabled** statuses are displayed even though the Status filter is set to **Disabled**.

This produces inaccurate search results and makes it difficult for administrators to identify users based on account status.

---

# Preconditions

1. Login using an Administrator account.
2. Navigate to **Admin → User Management → Users**.
3. Ensure the following users exist:

| Username | User Role | Status |
|----------|-----------|---------|
| john.smith | ESS | Enabled |
| john.disabled | ESS | Disabled |
| john.testing | ESS | Enabled |

---

# Test Data

### Search Criteria

| Field | Value |
|------|-------|
| Username | john |
| Status | Disabled |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **Admin**.
4. Open **User Management → Users**.
5. Locate the search section.
6. Enter **john** into the Username field.
7. Select **Disabled** from the Status dropdown.
8. Leave all remaining filters unchanged.
9. Click **Search**.
10. Observe the returned search results.

---

# Actual Result

The search returns all users matching the Username value regardless of the selected Status.

### Returned Results

| Username | Status |
|----------|---------|
| john.smith | Enabled ❌ |
| john.disabled | Disabled ✅ |
| john.testing | Enabled ❌ |

The selected **Status = Disabled** filter is ignored.

---

# Expected Result

The system should apply both Username and Status filters together.

### Expected Results

| Username | Status |
|----------|---------|
| john.disabled | Disabled |

Only users matching **both** search criteria should be displayed.

---

# Business Impact

This issue prevents administrators from accurately identifying users based on account status.

Potential impacts include:

- Incorrect user account reviews.
- Increased manual verification effort.
- Incorrect administrative actions.
- Difficulties during user-access audits.
- Reduced confidence in search accuracy.

---

# Possible Cause

The application appears to ignore the selected **Status** parameter when both Username and Status filters are submitted together.

Developer investigation is required to determine whether the issue originates from the frontend request or backend filtering logic.

---

# Suggested Fix

Ensure all selected search criteria are submitted and evaluated together before returning search results.

Regression testing should verify:

- Username filter only
- Status filter only
- Username + Status
- Username + User Role
- Username + Employee Name
- Username + User Role + Status
- All available filters
- Reset followed by a new search

---

# Attachments

| Attachment | Description |
|------------|-------------|
| BUG-001_User_Search_Status_Filter.png | Screenshot showing Status filter selected with incorrect results |
| BUG-001_Search_Results.png | Search results containing Enabled and Disabled users |
| BUG-001_Request.har | Network request captured during search |
| BUG-001_Response.json | API response returned for the search request |

---

# Notes

- Issue reproduced consistently in multiple executions.
- Verify the fix across Chrome, Firefox, and Microsoft Edge.
- Perform regression testing for all search filter combinations after the issue is resolved.

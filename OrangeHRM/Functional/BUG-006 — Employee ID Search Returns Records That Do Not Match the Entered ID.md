

# BUG-006 — Employee ID Search Returns Records That Do Not Match the Entered ID

## Summary

Searching the Employee List using an exact **Employee ID** returns additional employee records whose Employee IDs do not match the value entered in the search field.

---

# Bug Details

| Field | Details |
|------|---------|
| **Bug ID** | BUG-006 |
| **Title** | Employee ID Search Returns Records That Do Not Match the Entered ID |
| **Module** | PIM |
| **Feature** | Employee Management → Employee List Search |
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

The **Employee List** page allows administrators and authorized users to search employee records using an Employee ID.

When an exact Employee ID is entered and the Search action is executed, the application returns the matching employee together with additional records whose Employee IDs do not match the entered value.

Employee ID is used as a unique employee identifier and should return only the record associated with the exact ID entered by the user.

Returning unrelated employee records makes the search result unreliable and requires users to manually verify the correct employee before performing further actions.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Employee List**.
3. Ensure multiple employee records exist in the system.
4. Ensure the following test employees are available:

| Employee Name | Employee ID |
|---------------|-------------|
| John Carter | EMP1001 |
| Sarah Miller | EMP1002 |
| David Wilson | EMP1010 |
| Michael Brown | EMP1101 |

5. Confirm that **EMP1001** belongs only to John Carter.

---

# Test Data

### Search Criteria

| Field | Value |
|------|-------|
| Employee Name | Blank |
| Employee ID | EMP1001 |
| Employment Status | All |
| Include | Current Employees Only |
| Supervisor Name | Blank |
| Job Title | All |
| Sub Unit | All |

### Expected Employee Record

| Employee Name | Employee ID |
|---------------|-------------|
| John Carter | EMP1001 |

---

# Steps to Reproduce

1. Launch Google Chrome.
2. Navigate to the OrangeHRM Demo application.
3. Login using valid Administrator credentials.
4. Navigate to **PIM → Employee List**.
5. Locate the **Employee ID** search field.
6. Enter **EMP1001** in the Employee ID field.
7. Leave all other optional search filters unchanged.
8. Click **Search**.
9. Wait for the search results to load.
10. Review the Employee ID column in the returned results.
11. Compare each returned Employee ID with the value entered in the search field.

---

# Actual Result

The search results contain the employee with ID **EMP1001** together with additional employee records that do not match the entered Employee ID.

### Returned Results

| Employee Name | Employee ID | Result |
|---------------|-------------|--------|
| John Carter | EMP1001 | Correct |
| Sarah Miller | EMP1002 | Incorrect |
| David Wilson | EMP1010 | Incorrect |
| Michael Brown | EMP1101 | Incorrect |

The application returns records that do not satisfy the Employee ID search criteria.

The search appears to use partial or incorrectly evaluated matching instead of restricting the results to the exact Employee ID entered.

---

# Expected Result

The Employee ID search should return only the employee whose Employee ID exactly matches the entered value.

### Expected Results

| Employee Name | Employee ID |
|---------------|-------------|
| John Carter | EMP1001 |

No employee with a different Employee ID should be included in the result set.

For the following search criteria:

`Employee ID = EMP1001`

the application should evaluate the condition as:

`Employee ID equals EMP1001`

and return only the matching employee record.

---

# Business Impact

Incorrect Employee ID search results reduce the reliability of employee-management functionality.

Potential impacts include:

- Administrators selecting the wrong employee record.
- Incorrect employee details being reviewed.
- Risk of modifying the wrong employee account.
- Risk of deleting or updating an unintended employee record.
- Additional manual verification before performing administrative actions.
- Reduced efficiency when searching large employee datasets.
- Reduced confidence in Employee List search functionality.
- Increased operational risk when Employee ID is used as the primary identifier.

The impact may become more significant in production environments containing thousands of employee records with similar Employee ID patterns.

---

# Possible Cause

The Employee ID search condition may be using partial matching rather than exact matching.

Possible contributing factors include:

- Backend query using partial matching instead of exact equality.
- Employee ID request parameter being mapped incorrectly.
- Frontend submitting an incomplete or transformed Employee ID value.
- Search criteria being incorrectly processed by the API.
- Prefix matching being applied to Employee ID values.
- Previous search parameters remaining active.
- Search result state not being refreshed correctly after a new request.

Developer investigation is required to confirm whether the issue originates in the frontend, API layer, or backend query.

---

# Suggested Fix

Update the Employee ID search logic so that an exact Employee ID search returns only records that exactly match the submitted identifier.

The backend search condition should use exact comparison for Employee ID values.

Expected behavior:

`EmployeeID = "EMP1001"`

The frontend should also verify that the complete Employee ID value is submitted without modification.

Regression testing should verify:

- Exact valid Employee ID
- Non-existent Employee ID
- Employee ID containing numbers only
- Employee ID containing letters and numbers
- Employee ID with leading spaces
- Employee ID with trailing spaces
- Employee ID with leading and trailing spaces
- Partial Employee ID
- Employee ID using different letter casing
- Employee ID at minimum supported length
- Employee ID at maximum supported length
- Employee ID search after using other filters
- Employee ID combined with Employee Name
- Employee ID combined with Job Title
- Employee ID combined with Employment Status
- Employee ID search after using Reset
- Repeated searches using different Employee IDs

---

# Attachments

| Attachment | Description |
|------------|-------------|
| BUG-006_Employee_ID_Search_Criteria.png | Screenshot showing EMP1001 entered in the Employee ID field |
| BUG-006_Incorrect_Search_Results.png | Screenshot showing non-matching employee records |
| BUG-006_Expected_Employee_Record.png | Screenshot showing the correct EMP1001 employee record |
| BUG-006_Employee_Search_Request.har | HAR file containing the Employee ID search request |
| BUG-006_Employee_Search_Request.json | Request payload or query parameters used during the search |
| BUG-006_Employee_Search_Response.json | API response containing incorrect employee records |
| BUG-006_Reproduction.mp4 | Screen recording demonstrating the Employee ID search issue |

---

# Notes

- Verify whether Employee ID is intended to support exact or partial matching according to product requirements.
- Inspect the request parameter submitted when the Search button is clicked.
- Confirm whether incorrect records originate directly from the API response or from frontend rendering.
- Verify that previous search criteria are cleared before executing a new Employee ID search.
- Test the Employee ID search directly through the API where possible.
- Verify behavior in Google Chrome, Mozilla Firefox, and Microsoft Edge.
- Re-test Employee ID filtering with all supported filter combinations after the defect is fixed.

# BUG-012 — First Name Field Accepts Numeric Characters

## Summary

The **First Name** field in the employee creation form accepts numeric characters and allows the employee record to be saved successfully without displaying a validation message.

---

# Bug Details

| Field                   | Details                                     |
| ----------------------- | ------------------------------------------- |
| **Bug ID**              | BUG-012                                     |
| **Title**               | First Name Field Accepts Numeric Characters |
| **Module**              | PIM                                         |
| **Feature**             | Employee Management → Add Employee          |
| **Category**            | Validation                                  |
| **Environment**         | OrangeHRM Demo                              |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/  |
| **Browser**             | Google Chrome Version 150.x (64-bit)        |
| **Operating System**    | Windows 11 Pro 64-bit                       |
| **Application Version** | Hosted Demo                                 |
| **Build Number**        | N/A                                         |
| **Severity**            | Medium                                      |
| **Priority**            | Medium                                      |
| **Reporter**            | Javaria Ahmad                               |
| **Assigned To**         | TBD                                         |
| **Status**              | New                                         |
| **Reproducibility**     | Always                                      |
| **Frequency**           | 5/5 Attempts                                |

---

# Description

The **Add Employee** form allows authorized users to create new employee records by entering personal information such as First Name, Middle Name, and Last Name.

The **First Name** field accepts numeric characters without displaying any validation warning.

For example, a value such as **John123** can be entered and saved successfully as an employee's first name.

The application does not prevent the record from being created and does not notify the user that the entered value contains unsupported characters.

This allows invalid personal data to be stored in the employee database.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Add Employee**.
3. Ensure the logged-in user has permission to create employee records.
4. The Add Employee form is displayed successfully.

---

# Test Data

### Employee Details

| Field       | Value          |
| ----------- | -------------- |
| First Name  | John123        |
| Middle Name | Michael        |
| Last Name   | Smith          |
| Employee ID | Auto-generated |

### Additional Validation Data

| Test Type           | Value   |
| ------------------- | ------- |
| Valid First Name    | John    |
| Numeric Only        | 12345   |
| Alphanumeric        | John123 |
| Number at Beginning | 123John |
| Embedded Number     | Jo12hn  |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **PIM**.
4. Click **Add Employee**.
5. Locate the **First Name** field.
6. Enter **John123**.
7. Enter **Smith** in the Last Name field.
8. Leave the remaining optional fields unchanged.
9. Click **Save**.
10. Observe whether a validation message is displayed.
11. Verify whether the employee record is created.
12. Navigate back to the Employee List.
13. Search for the newly created employee.
14. Observe the stored First Name value.

---

# Actual Result

The application accepts the alphanumeric First Name value and creates the employee record successfully.

No validation message is displayed.

### Observed Result

| Field              | Entered Value | Result        |
| ------------------ | ------------- | ------------- |
| First Name         | John123       | Accepted      |
| Validation Message | N/A           | Not Displayed |
| Employee Creation  | Successful    | Incorrect     |

The invalid First Name value is stored in the employee profile.

---

# Expected Result

The application should validate the **First Name** field before allowing the employee record to be saved.

The field should reject unsupported numeric characters and display an appropriate validation message.

Example expected validation message:

**First Name must contain valid alphabetic characters only**

The employee record should not be created until the invalid value is corrected.

Valid names such as:

* John
* Anne
* Mary Jane
* Anne-Marie
* O'Connor

should remain supported according to the application's defined naming rules.

---

# Business Impact

Allowing numeric characters in employee names can reduce the quality and reliability of employee master data.

Potential impacts include:

* Invalid employee records being created.
* Incorrect employee names appearing in reports.
* Payroll or HR integrations receiving malformed data.
* Search results becoming inconsistent.
* Employee identity verification becoming more difficult.
* Duplicate or incorrect records being created.
* Data-cleaning effort increasing for HR administrators.
* Downstream systems failing validation during synchronization.

Incorrect employee-name data may also affect reporting, exports, integrations, and audit processes.

---

# Possible Cause

The First Name field may not have sufficient input validation.

Possible technical causes include:

* No character validation applied to the field.
* Validation checks only whether the field is empty.
* Client-side input restrictions are missing.
* Backend API accepts unrestricted string values.
* Validation regex allows numeric characters unintentionally.
* Validation rules differ between frontend and backend.
* Employee creation endpoint does not sanitize or validate name fields.

Developer investigation is required to confirm whether the issue exists at the UI layer, API layer, or both.

---

# Suggested Fix

Implement appropriate validation for employee name fields based on the application's accepted naming rules.

The validation should:

1. Reject unsupported numeric characters.
2. Allow alphabetic characters.
3. Support valid spaces where required.
4. Support legitimate hyphens and apostrophes if permitted.
5. Trim unnecessary leading and trailing spaces.
6. Apply the same validation on the backend.
7. Prevent the employee record from being saved when validation fails.

Regression testing should verify:

* Alphabetic First Name
* Numeric-only First Name
* Alphanumeric First Name
* Number at the beginning
* Number at the end
* Number in the middle
* Name containing a space
* Hyphenated name
* Apostrophe in name
* Accented characters
* Leading/trailing spaces
* Empty First Name
* Maximum supported length
* Middle Name validation
* Last Name validation
* Direct API submission containing numeric characters
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                            | Description                                                              |
| ------------------------------------- | ------------------------------------------------------------------------ |
| BUG-012_First_Name_With_Numbers.png   | Screenshot showing numeric characters entered in First Name              |
| BUG-012_Employee_Created.png          | Screenshot showing employee record created with invalid First Name       |
| BUG-012_Employee_List_Result.png      | Screenshot showing invalid employee name in Employee List                |
| BUG-012_Name_Validation_Flow.mp4      | Screen recording demonstrating employee creation with numeric First Name |
| BUG-012_Create_Employee_Request.har   | Network request captured during employee creation                        |
| BUG-012_Create_Employee_Response.json | API response confirming employee record creation                         |

---

# Notes

* Issue reproduced consistently using multiple numeric and alphanumeric values.
* Verify whether Middle Name and Last Name fields have the same validation issue.
* Validation should not incorrectly reject legitimate international or compound names.
* Confirm the approved employee-name validation rules with product requirements before implementing restrictive character validation.
* Server-side validation should be applied even if frontend validation is introduced.
* Retest employee create and edit workflows after the defect is resolved.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

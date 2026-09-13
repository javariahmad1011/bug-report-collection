# BUG-013 — Maximum Length Is Not Enforced for Employee Name Field

## Summary

The **First Name** field on the **Add Employee** form allows input beyond the expected maximum character limit and permits the employee record to be saved successfully without displaying a validation message.

---

# Bug Details

| Field                   | Details                                                |
| ----------------------- | ------------------------------------------------------ |
| **Bug ID**              | BUG-013                                                |
| **Title**               | Maximum Length Is Not Enforced for Employee Name Field |
| **Module**              | PIM                                                    |
| **Feature**             | Employee Management → Add Employee                     |
| **Category**            | Validation                                             |
| **Environment**         | OrangeHRM Demo                                         |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/             |
| **Browser**             | Google Chrome Version 150.x (64-bit)                   |
| **Operating System**    | Windows 11 Pro 64-bit                                  |
| **Application Version** | Hosted Demo                                            |
| **Build Number**        | N/A                                                    |
| **Severity**            | Medium                                                 |
| **Priority**            | Medium                                                 |
| **Reporter**            | Javaria Ahmad                                          |
| **Assigned To**         | TBD                                                    |
| **Status**              | New                                                    |
| **Reproducibility**     | Always                                                 |
| **Frequency**           | 5/5 Attempts                                           |

---

# Description

The **Add Employee** form accepts employee personal information, including First Name, Middle Name, and Last Name.

The **First Name** field does not enforce the expected maximum input length.

A value significantly longer than the supported field limit can be entered and submitted without any validation warning. The application allows the employee record to be created with the oversized value.

This can result in inconsistent data storage and may cause display, reporting, export, or integration issues where downstream systems expect a defined maximum length.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Add Employee**.
3. Ensure the logged-in user has permission to create employee records.
4. The Add Employee form is displayed successfully.

---

# Test Data

### Employee Details

| Field       | Value                                            |
| ----------- | ------------------------------------------------ |
| First Name  | Alexandertestautomationemployeevalidationexample |
| Middle Name | Michael                                          |
| Last Name   | Smith                                            |
| Employee ID | Auto-generated                                   |

### Length Validation Data

| Test Type                   | Value                        |
| --------------------------- | ---------------------------- |
| Normal Length               | Alexander                    |
| Boundary Value              | Maximum supported characters |
| Above Maximum               | Maximum + 1 character        |
| Significantly Above Maximum | 40+ characters               |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **PIM**.
4. Click **Add Employee**.
5. Locate the **First Name** field.
6. Enter a First Name value exceeding the expected maximum character limit.
7. Enter a valid Last Name.
8. Leave the remaining optional fields unchanged.
9. Click **Save**.
10. Observe whether a validation message is displayed.
11. Verify whether the employee record is created.
12. Open the newly created employee profile.
13. Observe the stored First Name value.

---

# Actual Result

The application accepts the oversized First Name value and saves the employee record successfully.

No validation message is displayed.

### Observed Result

| Field              | Entered Value                                    | Result        |
| ------------------ | ------------------------------------------------ | ------------- |
| First Name         | Alexandertestautomationemployeevalidationexample | Accepted      |
| Validation Message | N/A                                              | Not Displayed |
| Employee Creation  | Successful                                       | Incorrect     |

The full oversized value is stored in the employee record.

---

# Expected Result

The application should enforce the defined maximum character length for the **First Name** field.

When the user reaches the maximum supported length, the system should either:

1. Prevent additional characters from being entered, or
2. Display a clear validation message when the field exceeds the supported limit.

Example expected validation message:

**Should not exceed the maximum allowed characters**

The employee record should not be saved while the field contains a value beyond the permitted length.

---

# Business Impact

Failure to enforce field-length restrictions may result in invalid or inconsistent employee data.

Potential impacts include:

* Employee names may overflow or truncate in the UI.
* Reports may display malformed values.
* Exported data may exceed downstream field limits.
* API or integration payloads may fail validation.
* Database constraints may be violated in stricter environments.
* Search and sorting behavior may become inconsistent.
* Data migration processes may require additional cleansing.
* HR administrators may need to manually correct invalid records.

The issue may also expose inconsistencies between frontend validation, backend validation, and database schema constraints.

---

# Possible Cause

The employee name field may not have a maximum-length validation rule implemented consistently across the application layers.

Possible technical causes include:

* Missing HTML `maxlength` attribute.
* Frontend validation schema does not define a maximum length.
* Backend endpoint accepts strings beyond the intended limit.
* UI validation and database constraints use different length values.
* Validation is only applied to required fields.
* Field component does not enforce configured metadata.
* Input sanitization or schema validation is missing.

Developer investigation is required to confirm the expected field limit and identify where validation is not being applied.

---

# Suggested Fix

Define and enforce a consistent maximum length for employee name fields across the frontend, API, and persistence layers.

The application should:

1. Apply the approved maximum length to the First Name field.
2. Prevent or reject input beyond that limit.
3. Display a clear validation message when the limit is exceeded.
4. Apply the same rule on the backend API.
5. Ensure the database column supports the same defined constraint.
6. Handle pasted values that exceed the supported length.
7. Apply equivalent validation to Middle Name and Last Name where appropriate.

Regression testing should verify:

* One character below the maximum
* Exactly at the maximum
* One character above the maximum
* Significantly above the maximum
* Copy/paste of oversized values
* Leading and trailing spaces
* Valid hyphenated names
* Valid apostrophes
* International characters
* Middle Name length
* Last Name length
* Employee edit workflow
* Direct API submission above the limit
* Import or bulk-create workflows
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                            | Description                                                          |
| ------------------------------------- | -------------------------------------------------------------------- |
| BUG-013_Exceeded_Max_Length.png       | Screenshot showing First Name exceeding the expected character limit |
| BUG-013_Record_Saved.png              | Screenshot showing employee record saved with oversized First Name   |
| BUG-013_No_Length_Validation.png      | Screenshot showing no validation message after exceeding the limit   |
| BUG-013_Max_Length_Flow.mp4           | Screen recording demonstrating the issue                             |
| BUG-013_Create_Employee_Request.har   | Network request captured during employee creation                    |
| BUG-013_Create_Employee_Response.json | API response confirming the oversized value was accepted             |

---

# Notes

* Issue reproduced consistently using multiple oversized values.
* Confirm the exact approved maximum character limit with product requirements before implementing the fix.
* Verify whether First Name, Middle Name, and Last Name use the same or different limits.
* Server-side validation should reject oversized values even if frontend validation is bypassed.
* Verify database schema constraints match application-level validation.
* Retest employee creation and edit workflows after the defect is resolved.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

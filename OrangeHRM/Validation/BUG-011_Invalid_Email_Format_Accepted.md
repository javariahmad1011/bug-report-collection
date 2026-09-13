# BUG-011 — Invalid Email Format Is Accepted in Employee Contact Details

## Summary

The application accepts an invalid email address in the employee contact details without displaying a validation message. The record can be saved successfully even when the email value does not follow a valid email format.

---

# Bug Details

| Field                   | Details                                                      |
| ----------------------- | ------------------------------------------------------------ |
| **Bug ID**              | BUG-011                                                      |
| **Title**               | Invalid Email Format Is Accepted in Employee Contact Details |
| **Module**              | PIM                                                          |
| **Feature**             | Employee Management → Contact Details                        |
| **Category**            | Validation                                                   |
| **Environment**         | OrangeHRM Demo                                               |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/                   |
| **Browser**             | Google Chrome Version 150.x (64-bit)                         |
| **Operating System**    | Windows 11 Pro 64-bit                                        |
| **Application Version** | Hosted Demo                                                  |
| **Build Number**        | N/A                                                          |
| **Severity**            | Medium                                                       |
| **Priority**            | High                                                         |
| **Reporter**            | Javaria Ahmad                                                |
| **Assigned To**         | TBD                                                          |
| **Status**              | New                                                          |
| **Reproducibility**     | Always                                                       |
| **Frequency**           | 5/5 Attempts                                                 |

---

# Description

The **Contact Details** section within the PIM module allows authorized users to enter and update employee email information.

When an invalid email value such as **john.smith@company** or **john.smith@** is entered into the email field, the application does not display a validation error.

The invalid value is accepted and the employee record is saved successfully.

This allows incorrectly formatted email addresses to be stored against employee profiles and may affect any business process that relies on valid employee contact information.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Employee List**.
3. Open an existing employee record.
4. Navigate to the employee's **Contact Details** section.
5. Ensure the logged-in user has permission to edit employee contact information.

---

# Test Data

### Employee

| Field         | Value      |
| ------------- | ---------- |
| Employee Name | John Smith |
| Employee ID   | 1025       |

### Email Validation Data

| Test Type       | Value                                                   |
| --------------- | ------------------------------------------------------- |
| Valid Email     | [john.smith@company.com](mailto:john.smith@company.com) |
| Invalid Email 1 | john.smith@company                                      |
| Invalid Email 2 | john.smith@                                             |
| Invalid Email 3 | john.smithcompany.com                                   |
| Invalid Email 4 | john [smith@company.com](mailto:smith@company.com)      |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **PIM**.
4. Open **Employee List**.
5. Search for **John Smith**.
6. Open the employee record.
7. Navigate to **Contact Details**.
8. Locate the employee email field.
9. Enter **john.smith@company**.
10. Click **Save**.
11. Observe whether a validation message is displayed.
12. Refresh the employee record.
13. Return to **Contact Details**.
14. Observe the saved email value.

---

# Actual Result

The application accepts the invalid email value and saves the employee record successfully.

No validation message is displayed.

### Observed Result

| Field              | Entered Value      | Result        |
| ------------------ | ------------------ | ------------- |
| Email              | john.smith@company | Accepted      |
| Validation Message | N/A                | Not Displayed |
| Save Operation     | Successful         | Incorrect     |

After refreshing the page, the invalid email address remains stored in the employee profile.

---

# Expected Result

The application should validate the email address before allowing the record to be saved.

For an invalid email such as:

**john.smith@company**

the system should:

1. Prevent the form from being submitted.
2. Highlight the email field.
3. Display a clear validation message.
4. Keep the user on the Contact Details page until the issue is corrected.

Example expected validation message:

**Enter a valid email address**

Only correctly formatted email addresses such as:

**[john.smith@company.com](mailto:john.smith@company.com)**

should be accepted.

---

# Business Impact

Allowing invalid email addresses to be stored creates unreliable employee contact data.

Potential impacts include:

* Employee notifications may fail.
* Password or account-related communications may not reach the intended user.
* HR communications may be sent to invalid addresses.
* Integrations relying on employee email values may fail.
* Reports may contain incorrect contact information.
* Administrators may need to manually identify and correct invalid records.
* Data-quality checks may fail during audits or migration activities.

If employee email addresses are used by downstream systems, invalid values may also cause integration or synchronization failures.

---

# Possible Cause

The email field may not have sufficient client-side or server-side validation.

Possible technical causes include:

* Missing email-format validation rule.
* Validation performed only for required/non-empty values.
* Frontend validation regex is incomplete.
* Backend API accepts email values without format validation.
* HTML email input type is not configured correctly.
* Validation is bypassed when the form is submitted programmatically.
* Client-side validation exists but is not triggered before saving.

Developer investigation is required to verify whether validation is missing at the frontend, backend, or both layers.

---

# Suggested Fix

Implement email-format validation on both the frontend and backend.

The application should reject values that do not meet the supported email format.

Validation should cover:

* Missing `@` symbol
* Missing domain
* Missing local part
* Spaces within the address
* Multiple `@` symbols
* Invalid domain structure
* Leading or trailing whitespace
* Invalid special-character combinations

Regression testing should verify:

* Valid corporate email
* Valid email with subdomain
* Valid email containing numbers
* Valid email containing permitted special characters
* Missing `@`
* Missing domain
* Missing local part
* Multiple `@` characters
* Spaces in email
* Leading/trailing spaces
* Empty optional email field
* Maximum supported email length
* Copy/paste into email field
* Save and reload behavior
* API validation when invalid email is submitted directly
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                        | Description                                                 |
| --------------------------------- | ----------------------------------------------------------- |
| BUG-011_Invalid_Email_Input.png   | Screenshot showing invalid email entered in Contact Details |
| BUG-011_Invalid_Email_Saved.png   | Screenshot showing invalid email stored after Save          |
| BUG-011_No_Validation_Message.png | Screenshot showing missing email validation                 |
| BUG-011_Email_Validation_Flow.mp4 | Screen recording demonstrating invalid email acceptance     |
| BUG-011_Save_Request.har          | Network request captured while saving the invalid value     |
| BUG-011_Save_Response.json        | API response showing successful save of invalid email       |

---

# Notes

* Issue reproduced consistently using multiple invalid email formats.
* Verify whether both Work Email and Other Email fields are affected.
* Server-side validation should be implemented even if frontend validation is added.
* Verify that existing valid email addresses remain unaffected after the fix.
* Check whether invalid email addresses can also be submitted directly through the API.
* Retest create and edit employee workflows after resolution.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

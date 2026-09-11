# BUG-002 — Username Field Accepts Leading and Trailing Spaces During User Creation

## Summary

The Username field on the **Add User** form accepts leading and trailing whitespace without displaying a validation message or normalizing the entered value before submission.

---

# Bug Details

| Field                   | Details                                                                 |
| ----------------------- | ----------------------------------------------------------------------- |
| **Bug ID**              | BUG-002                                                                 |
| **Title**               | Username Field Accepts Leading and Trailing Spaces During User Creation |
| **Module**              | Admin                                                                   |
| **Feature**             | User Management → Add User                                              |
| **Category**            | Validation                                                              |
| **Environment**         | OrangeHRM Demo                                                          |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/                              |
| **Browser**             | Google Chrome Version 150.x (64-bit)                                    |
| **Operating System**    | Windows 11 Pro 64-bit                                                   |
| **Application Version** | Hosted Demo                                                             |
| **Build Number**        | N/A                                                                     |
| **Severity**            | Medium                                                                  |
| **Priority**            | High                                                                    |
| **Reporter**            | Javaria Ahmad                                                           |
| **Assigned To**         | TBD                                                                     |
| **Status**              | New                                                                     |
| **Reproducibility**     | Always                                                                  |
| **Frequency**           | 5/5 Attempts                                                            |

---

# Description

The **Add User** form allows administrators to create system user accounts.

When a Username is entered with leading or trailing whitespace, the application accepts the value without displaying a validation error or removing the unnecessary spaces before processing the record.

For example, entering:

`  qa.user  `

is accepted as a valid Username instead of being normalized to:

`qa.user`

This may create visually identical or difficult-to-identify user accounts and may cause inconsistent behavior during login, search, duplicate validation, and user administration.

---

# Preconditions

1. Login using an Administrator account.
2. Navigate to **Admin → User Management → Users**.
3. A valid employee record must exist and be available for user-account creation.
4. The Username `qa.user` must not already exist unless duplicate-validation behavior is also being verified.

---

# Test Data

### User Details

| Field            | Value         |
| ---------------- | ------------- |
| User Role        | ESS           |
| Employee Name    | Test Employee |
| Status           | Enabled       |
| Username         | `  qa.user  ` |
| Password         | Test@12345    |
| Confirm Password | Test@12345    |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **Admin**.
4. Open **User Management → Users**.
5. Click **Add**.
6. Select **ESS** from the User Role dropdown.
7. Select a valid employee from the Employee Name field.
8. Select **Enabled** from the Status dropdown.
9. Enter `  qa.user  ` in the Username field, including spaces before and after the Username.
10. Enter a valid password.
11. Enter the same value in the Confirm Password field.
12. Click **Save**.
13. Observe the validation behavior and resulting user record.

---

# Actual Result

The application accepts the Username containing leading and trailing spaces.

No validation message is displayed indicating that whitespace is not permitted.

The Username is processed without clear normalization, allowing whitespace to affect the stored or submitted value.

Example submitted value:

```text
"  qa.user  "
```

This may result in a user record that appears visually similar to `qa.user` but behaves differently during search, login, or duplicate validation.

---

# Expected Result

The application should normalize the Username before validation and submission.

Leading and trailing whitespace should either:

1. Be automatically removed before processing the Username, or
2. Trigger a validation message preventing submission.

The normalized value should be:

```text
qa.user
```

The application should then perform duplicate Username validation against the normalized value.

---

# Business Impact

Improper Username normalization may result in inconsistent user-account data.

Potential impacts include:

* Creation of duplicate-looking user accounts.
* Difficulty locating users through search.
* Login failures caused by unexpected whitespace.
* Incorrect duplicate Username validation.
* Additional administrative effort when managing accounts.
* Data-quality issues within user-management records.
* Increased support requests from users unable to authenticate with apparently correct credentials.

This is particularly important in environments where administrators create and maintain large numbers of user accounts.

---

# Possible Cause

The Username validation appears to process the raw input value without trimming leading and trailing whitespace.

The application may be validating uniqueness before normalizing the Username.

Developer investigation should verify whether whitespace is being retained:

* In the frontend input value.
* In the API request payload.
* During backend validation.
* In the database record.

---

# Suggested Fix

Normalize the Username value before performing validation or persistence.

The application should trim leading and trailing whitespace before:

* Required-field validation.
* Length validation.
* Duplicate Username validation.
* API submission.
* Database persistence.

For example:

```text
Input:
"  qa.user  "

Normalized:
"qa.user"
```

Regression testing should verify:

* Username without spaces
* Leading spaces only
* Trailing spaces only
* Leading and trailing spaces
* Multiple internal spaces
* Username containing only whitespace
* Existing Username with added leading spaces
* Existing Username with added trailing spaces
* Username at minimum allowed length
* Username at maximum allowed length
* Username exceeding maximum allowed length

---

# Attachments

| Attachment                            | Description                                                          |
| ------------------------------------- | -------------------------------------------------------------------- |
| BUG-002_Username_Whitespace_Input.png | Screenshot showing Username entered with leading and trailing spaces |
| BUG-002_User_Created.png              | Screenshot showing resulting user record                             |
| BUG-002_Create_User_Request.har       | Network request captured during user creation                        |
| BUG-002_Create_User_Request.json      | Request payload containing the Username value                        |
| BUG-002_Create_User_Response.json     | API response returned after submission                               |

---

# Notes

* Verify whether spaces are retained in the backend or only accepted temporarily by the frontend.
* Verify Username behavior after logging out and attempting authentication with the newly created account.
* Verify whether `qa.user` and `  qa.user  ` are treated as separate usernames.
* Verify search behavior using both the trimmed and untrimmed Username values.
* Perform regression testing across Chrome, Firefox, and Microsoft Edge.
* API and database validation should be reviewed to ensure Username normalization is enforced server-side.

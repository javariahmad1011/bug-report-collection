# BUG-007 — Add User Button Does Not Open Create User Form

## Summary

When an administrator clicks the **Add** button on the **System Users** page, the application does not open the **Add User** form and remains on the existing user list page.

---

# Bug Details

| Field                   | Details                                        |
| ----------------------- | ---------------------------------------------- |
| **Bug ID**              | BUG-007                                        |
| **Title**               | Add User Button Does Not Open Create User Form |
| **Module**              | Admin                                          |
| **Feature**             | User Management → Add User                     |
| **Category**            | Functional                                     |
| **Environment**         | OrangeHRM Demo                                 |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/     |
| **Browser**             | Google Chrome Version 150.x (64-bit)           |
| **Operating System**    | Windows 11 Pro 64-bit                          |
| **Application Version** | Hosted Demo                                    |
| **Build Number**        | N/A                                            |
| **Severity**            | High                                           |
| **Priority**            | High                                           |
| **Reporter**            | Javaria Ahmad                                  |
| **Assigned To**         | TBD                                            |
| **Status**              | New                                            |
| **Reproducibility**     | Always                                         |
| **Frequency**           | 5/5 Attempts                                   |

---

# Description

The **System Users** page provides an **Add** button that allows administrators to create new application users.

When an administrator clicks the **Add** button, the application does not navigate to the **Add User** page. No form, loading indicator, error message, or confirmation message is displayed.

The administrator remains on the **System Users** page and cannot proceed with creating a new user account.

This issue blocks the standard user-provisioning workflow for administrators.

---

# Preconditions

1. A valid Administrator account is available.
2. The administrator is successfully authenticated.
3. The logged-in account has permission to manage system users.
4. The application is accessible without network connectivity issues.
5. The administrator is on **Admin → User Management → Users**.

---

# Test Data

### Administrator Account

| Field                      | Value     |
| -------------------------- | --------- |
| User Role                  | Admin     |
| Account Status             | Enabled   |
| User Management Permission | Available |

### Intended New User

| Field         | Value         |
| ------------- | ------------- |
| User Role     | ESS           |
| Employee Name | John Smith    |
| Status        | Enabled       |
| Username      | john.smith.qa |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **Admin** from the main navigation menu.
4. Open **User Management → Users**.
5. Verify that the **System Users** page is displayed.
6. Locate the **Add** button above the user records table.
7. Click the **Add** button once.
8. Observe the application behavior.
9. Click the **Add** button again.
10. Observe whether the user creation form is displayed.

---

# Actual Result

Clicking the **Add** button does not open the **Add User** page.

The application remains on:

**Admin → User Management → Users**

No visible response is provided to the administrator.

The following behaviors are observed:

* The Add User form is not displayed.
* The URL does not change.
* No loading indicator is displayed.
* No validation or error message appears.
* No modal or drawer is opened.
* Repeated clicks produce the same result.

The administrator cannot initiate user creation.

---

# Expected Result

Clicking the **Add** button should navigate the administrator to the **Add User** form.

The form should display the required user creation fields, including:

| Field            | Expected Behavior                        |
| ---------------- | ---------------------------------------- |
| User Role        | Dropdown should be available             |
| Employee Name    | Employee lookup should be available      |
| Status           | Status dropdown should be available      |
| Username         | Username input should be available       |
| Password         | Password input should be available       |
| Confirm Password | Confirmation field should be available   |
| Save             | Should create the user after valid input |
| Cancel           | Should return to the System Users page   |

The application URL should update to the appropriate user-creation route.

---

# Business Impact

This issue prevents administrators from creating new system user accounts through the standard user-management interface.

Potential impacts include:

* New employees cannot be provisioned with application access.
* User onboarding may be delayed.
* Administrators may need technical or database-level workarounds.
* Operational teams may be unable to assign application access on time.
* Access-management processes may fail during employee onboarding.
* Administrative workload may increase due to manual escalation.
* Business users may be unable to access the system when required.

Because the defect blocks a primary administrative function, it has a direct impact on user provisioning and access management.

---

# Possible Cause

The Add button event may not be correctly connected to the expected navigation action.

Possible technical causes include:

* Missing or broken click-event handler.
* Incorrect frontend route configuration.
* JavaScript exception preventing navigation.
* Disabled navigation action despite the button appearing enabled.
* Incorrect permission handling on the client side.
* UI component rendered without the required navigation callback.
* Route change blocked after a recent frontend deployment.

Developer investigation is required to confirm the root cause.

---

# Suggested Fix

Ensure the **Add** button correctly triggers navigation to the Add User page for authorized administrators.

The application should:

1. Validate that the logged-in user has user-management permission.
2. Trigger the Add User navigation action after the button is clicked.
3. Load the Add User form successfully.
4. Display a meaningful error message if navigation or form loading fails.
5. Prevent duplicate actions caused by rapid repeated clicks.

Regression testing should verify:

* Add button with an Admin account
* Add button with authorized custom roles
* Add User page navigation
* Browser refresh on Add User page
* Browser Back navigation
* Cancel button navigation
* Save user workflow
* Duplicate username validation
* Password validation
* Employee selection
* User Role selection
* Status selection
* Direct URL access to the Add User route
* Permission behavior for unauthorized users
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                              | Description                                                          |
| --------------------------------------- | -------------------------------------------------------------------- |
| BUG-007_Add_User_Button_Not_Working.png | Screenshot showing System Users page after clicking Add              |
| BUG-007_Click_Behavior.mp4              | Screen recording showing repeated Add button clicks with no response |
| BUG-007_Browser_Console.png             | Browser console output captured after clicking Add                   |
| BUG-007_Network_Log.har                 | Network activity captured while reproducing the issue                |
| BUG-007_Page_State.png                  | Screenshot confirming the page remains unchanged after the action    |

---

# Notes

* Issue reproduced consistently in multiple executions.
* No visible error message is displayed to the administrator.
* Verify whether a JavaScript error is generated when the Add button is clicked.
* Verify whether the issue is permission-related or affects all Administrator accounts.
* Verify the Add User route independently using direct navigation.
* Retest the complete user-creation workflow after the defect is resolved.
* Perform cross-browser regression testing in Chrome, Firefox, and Microsoft Edge.


# BUG-017 — Unauthorized User Can Access Admin Page Using Direct URL

## Summary

A user without Administrator permissions can access the **System Users** page by entering the Admin page URL directly in the browser. The application does not correctly enforce authorization when the protected route is accessed outside the normal navigation flow.

---

# Bug Details

| Field                   | Details                                                  |
| ----------------------- | -------------------------------------------------------- |
| **Bug ID**              | BUG-017                                                  |
| **Title**               | Unauthorized User Can Access Admin Page Using Direct URL |
| **Module**              | Admin                                                    |
| **Feature**             | Authorization → Protected Route Access                   |
| **Category**            | Security                                                 |
| **Environment**         | OrangeHRM Demo                                           |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/               |
| **Browser**             | Google Chrome Version 150.x (64-bit)                     |
| **Operating System**    | Windows 11 Pro 64-bit                                    |
| **Application Version** | Hosted Demo                                              |
| **Build Number**        | N/A                                                      |
| **Severity**            | Critical                                                 |
| **Priority**            | High                                                     |
| **Reporter**            | Javaria Ahmad                                            |
| **Assigned To**         | TBD                                                      |
| **Status**              | New                                                      |
| **Reproducibility**     | Always                                                   |
| **Frequency**           | 5/5 Attempts                                             |

---

# Description

The application restricts access to the **Admin** module for non-administrator users through the navigation menu.

When an ESS user logs in, the **Admin** menu is correctly hidden.

However, if the same user manually enters the direct URL for the **System Users** page in the browser address bar, the protected Admin page is loaded successfully.

The application therefore appears to enforce access restrictions only at the user interface level rather than validating authorization when the protected resource is requested.

This allows a user without the required role or permission to access administrative functionality that should be restricted.

---

# Preconditions

1. A valid ESS/non-Administrator user account exists.
2. The account is active and can login successfully.
3. The user does not have Admin or User Management permissions.
4. The direct URL for the protected System Users page is known.
5. The user is authenticated using the non-Administrator account.

---

# Test Data

### Test User

| Field                      | Value         |
| -------------------------- | ------------- |
| Username                   | ess.test.user |
| User Role                  | ESS           |
| Status                     | Enabled       |
| Admin Permission           | No            |
| User Management Permission | No            |

### Protected Resource

| Field           | Value        |
| --------------- | ------------ |
| Module          | Admin        |
| Page            | System Users |
| Expected Access | Denied       |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid **ESS** user account.
3. Verify that the **Admin** menu is not displayed in the main navigation.
4. Copy or enter the direct URL for the **System Users** page into the browser address bar.
5. Press **Enter**.
6. Wait for the page to load.
7. Observe whether the Admin page is displayed.
8. Attempt to view the user-management data available on the page.
9. Refresh the page.
10. Observe whether access remains available.

---

# Actual Result

The ESS user is able to open the protected **System Users** page using the direct URL.

The page loads successfully despite the user not having Administrator permissions.

### Observed Access

| Item                          | Result |
| ----------------------------- | ------ |
| Admin Menu Visible            | No     |
| Direct Admin URL Accessible   | Yes    |
| System Users Page Loaded      | Yes    |
| User Data Visible             | Yes    |
| Authorization Error Displayed | No     |
| Access Denied Response        | No     |

The application hides the Admin module from navigation but does not prevent direct access to the protected route.

---

# Expected Result

The application should enforce authorization on every request to a protected resource.

When an ESS or other unauthorized user attempts to access an Admin URL directly, the system should:

1. Validate the authenticated user's role and permissions.
2. Reject access to the protected resource.
3. Prevent administrative data from being returned.
4. Redirect the user to an authorized page or display an access-denied page.
5. Return an appropriate authorization response from the backend.

Example expected behavior:

**403 Forbidden**

or an application-level message such as:

**You do not have permission to access this resource.**

Removing the Admin option from the navigation menu should not be treated as an authorization control.

---

# Business Impact

This issue represents a significant authorization weakness because users may be able to access functionality outside their assigned permissions.

Potential impacts include:

* Unauthorized access to user-management information.
* Exposure of employee or account-related data.
* Violation of role-based access control requirements.
* Unauthorized administrative actions if protected operations are also accessible.
* Increased risk of privilege escalation.
* Failure of security and compliance controls.
* Exposure of sensitive information to users without a business need.
* Audit findings related to insufficient access control.

If create, edit, delete, or other administrative actions are also accessible through direct routes or API requests, the impact may extend beyond unauthorized viewing to modification of application data.

---

# Possible Cause

The application appears to rely on frontend navigation restrictions without consistently enforcing authorization at the route or backend level.

Possible technical causes include:

* Protected route missing a role/permission guard.
* Authorization implemented only in the navigation component.
* Backend endpoint does not verify user permissions.
* Role validation is not performed on page initialization.
* Session token is validated for authentication but not authorization.
* Permission metadata is trusted from the client.
* Route-level access-control configuration is incomplete.
* Admin APIs are accessible to any authenticated user.

Developer investigation is required to determine whether the issue affects only the frontend route or also the underlying backend APIs.

---

# Suggested Fix

Implement server-side and route-level authorization for all protected administrative resources.

The application should:

1. Validate the authenticated user's permissions before loading any Admin route.
2. Enforce authorization on the corresponding backend API endpoints.
3. Return **403 Forbidden** for authenticated users without sufficient permissions.
4. Prevent restricted data from being included in the response.
5. Maintain UI restrictions as an additional usability control, not as the primary security control.
6. Apply centralized role-based access control to all privileged modules.
7. Log unauthorized access attempts where appropriate.

Regression and security testing should verify:

* ESS user accessing Admin through navigation
* ESS user accessing Admin through direct URL
* ESS user refreshing a protected Admin route
* ESS user using browser Back/Forward navigation
* Direct access to Add User page
* Direct access to Edit User page
* Direct access to Delete User functionality
* Direct API requests to Admin endpoints
* Administrator access to protected routes
* Session expiry while viewing an Admin route
* Role change during an active session
* Different restricted user roles
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                         | Description                                                           |
| ---------------------------------- | --------------------------------------------------------------------- |
| BUG-017_ESS_User_No_Admin_Menu.png | Screenshot showing Admin option hidden for ESS user                   |
| BUG-017_Direct_URL_Access.png      | Screenshot showing protected Admin page accessible through direct URL |
| BUG-017_User_Data_Visible.png      | Screenshot showing System Users data visible to unauthorized user     |
| BUG-017_Authorization_Flow.mp4     | Screen recording demonstrating unauthorized direct URL access         |
| BUG-017_Admin_Request.har          | Network request captured while accessing the protected route          |
| BUG-017_Admin_Response.json        | Backend response returned to the unauthorized user                    |

---

# Notes

* Issue reproduced consistently using a non-Administrator account.
* Verify whether the issue affects read-only access or also create, edit, and delete operations.
* Backend authorization should be tested independently from frontend navigation restrictions.
* Review all protected modules for similar direct URL access weaknesses.
* Verify that unauthorized API requests return an appropriate authorization status.
* Perform role-based access-control regression testing after the issue is resolved.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

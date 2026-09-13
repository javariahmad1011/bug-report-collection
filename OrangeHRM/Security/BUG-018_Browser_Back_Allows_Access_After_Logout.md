# BUG-018 — Logged-Out User Can View Previously Authenticated Page Using Browser Back Button

## Summary

After a user logs out of the application, pressing the browser **Back** button displays the previously authenticated page instead of keeping the user on the login screen or preventing access to cached protected content.

---

# Bug Details

| Field                   | Details                                                                          |
| ----------------------- | -------------------------------------------------------------------------------- |
| **Bug ID**              | BUG-018                                                                          |
| **Title**               | Logged-Out User Can View Previously Authenticated Page Using Browser Back Button |
| **Module**              | Authentication                                                                   |
| **Feature**             | Logout → Session Termination / Browser Navigation                                |
| **Category**            | Security                                                                         |
| **Environment**         | OrangeHRM Demo                                                                   |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/                                       |
| **Browser**             | Google Chrome Version 150.x (64-bit)                                             |
| **Operating System**    | Windows 11 Pro 64-bit                                                            |
| **Application Version** | Hosted Demo                                                                      |
| **Build Number**        | N/A                                                                              |
| **Severity**            | High                                                                             |
| **Priority**            | High                                                                             |
| **Reporter**            | Javaria Ahmad                                                                    |
| **Assigned To**         | TBD                                                                              |
| **Status**              | New                                                                              |
| **Reproducibility**     | Always                                                                           |
| **Frequency**           | 5/5 Attempts                                                                     |

---

# Description

The application provides a **Logout** option that should terminate the authenticated session and prevent further access to protected pages.

After logging out successfully, the user is redirected to the login page as expected.

However, when the user presses the browser **Back** button, the previously authenticated page is displayed again.

The protected page content remains visible without requiring the user to authenticate again.

This behavior creates a security and privacy concern, particularly on shared or public devices, because another person may be able to view previously accessed application data after the original user has logged out.

---

# Preconditions

1. A valid OrangeHRM user account is available.
2. The user can login successfully.
3. The user has access to at least one authenticated page containing application data.
4. Browser history navigation is enabled.
5. The test is performed in a normal browser session.

---

# Test Data

### User Account

| Field    | Value           |
| -------- | --------------- |
| Username | admin.test.user |
| Role     | Admin           |
| Status   | Enabled         |

### Protected Page

| Field                   | Value         |
| ----------------------- | ------------- |
| Module                  | PIM           |
| Page                    | Employee List |
| Authentication Required | Yes           |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid user account.
3. Navigate to **PIM → Employee List**.
4. Verify that protected employee data is displayed.
5. Open the user profile menu.
6. Click **Logout**.
7. Verify that the application redirects to the login page.
8. Click the browser **Back** button once.
9. Observe the page displayed.
10. Attempt to interact with the previously authenticated page.
11. Refresh the page.
12. Observe whether authentication is required.

---

# Actual Result

After logout, pressing the browser **Back** button displays the previously authenticated **Employee List** page.

### Observed Behavior

| Action                             | Result                   |
| ---------------------------------- | ------------------------ |
| Logout                             | Successful               |
| Redirect to Login                  | Successful               |
| Browser Back                       | Protected page displayed |
| Previous Page Content Visible      | Yes                      |
| Login Prompt Immediately Displayed | No                       |

Previously loaded authenticated content remains visible after session termination.

Depending on the page state, some actions may fail only after a new server request is made, but protected information remains visible in the browser.

---

# Expected Result

After logout, the user should not be able to access or view protected application content using browser history.

When the browser **Back** button is pressed, the application should either:

1. Keep the user on the login page, or
2. Redirect immediately back to the login page after detecting that no valid authenticated session exists.

Protected pages should not expose sensitive information from browser cache after logout.

The system should ensure that:

* The authenticated session is terminated.
* Protected routes validate session state.
* Sensitive authenticated pages are not restored from browser cache.
* The user must authenticate again before viewing protected content.

---

# Business Impact

This issue may expose application data after a user has intentionally logged out.

Potential impacts include:

* Sensitive employee information may remain visible on shared devices.
* Unauthorized users may view previously accessed pages.
* Logout may give users a false sense that application data is no longer accessible.
* Personally identifiable or administrative information may remain exposed.
* Security and privacy requirements may not be met.
* Shared workstation environments become more vulnerable.
* Compliance or audit findings may result from ineffective logout controls.

The risk is higher for users accessing the application from shared, kiosk, or public computers.

---

# Possible Cause

The issue may be caused by protected pages being restored from browser cache without revalidating the authenticated session.

Possible technical causes include:

* Missing cache-control headers on authenticated pages.
* Browser Back/Forward Cache restoring protected page state.
* Frontend route guard not validating authentication after history navigation.
* Logout clears the server session but leaves client-side application state populated.
* Authentication tokens remain accessible in browser storage.
* Protected content is cached by the browser.
* Session validation only occurs during API requests rather than when routes are restored.

Developer investigation is required to verify both client-side session cleanup and server-side cache configuration.

---

# Suggested Fix

Strengthen logout handling and protected-page cache behavior.

The application should:

1. Completely invalidate the authenticated session during logout.
2. Remove authentication tokens and sensitive session data from client storage.
3. Validate authentication whenever a protected route is restored.
4. Redirect unauthenticated users to the login page.
5. Configure appropriate response headers for sensitive authenticated pages.
6. Prevent stale protected content from being exposed through browser history.
7. Ensure logout invalidates the server-side session or token where applicable.

Recommended cache controls for authenticated content should be reviewed according to the application's architecture, including appropriate use of:

* `Cache-Control`
* `no-store`
* `no-cache`
* `must-revalidate`

Regression and security testing should verify:

* Browser Back after logout
* Browser Forward after logout
* Multiple Back button presses
* Logout from Admin page
* Logout from Employee List
* Logout from employee profile
* Refresh after logout
* Direct protected URL after logout
* Opening a protected URL in a new tab after logout
* Session token reuse after logout
* Multiple browser tabs using the same session
* Browser Back/Forward Cache behavior
* Session expiry followed by browser Back
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                             | Description                                                                  |
| -------------------------------------- | ---------------------------------------------------------------------------- |
| BUG-018_Logout_Login_Page.png          | Screenshot showing successful redirect to login after logout                 |
| BUG-018_Back_Button_Protected_Page.png | Screenshot showing protected page visible after pressing browser Back        |
| BUG-018_Cached_Employee_Data.png       | Screenshot showing previously authenticated content still visible            |
| BUG-018_Logout_Back_Flow.mp4           | Screen recording demonstrating the complete logout and browser Back sequence |
| BUG-018_Logout_Request.har             | Network activity captured during logout and history navigation               |
| BUG-018_Response_Headers.txt           | Response headers from the affected protected page                            |

---

# Notes

* Issue reproduced consistently after successful logout.
* Verify whether the displayed page is purely cached or whether protected API calls are also still authorized.
* Test whether authentication tokens are removed from cookies, local storage, and session storage after logout.
* Review browser cache headers on all authenticated pages.
* Validate behavior with multiple open tabs.
* Perform session-management regression testing after the fix.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

# BUG-004 — Authenticated Session Remains Accessible After Logout

## Summary

After logging out of OrangeHRM, previously authenticated pages can still be accessed using the browser Back button without requiring the user to authenticate again.

---

# Bug Details

| Field | Details |
|------|---------|
| **Bug ID** | BUG-004 |
| **Title** | Authenticated Session Remains Accessible After Logout |
| **Module** | Authentication |
| **Feature** | User Session Management → Logout |
| **Category** | Security |
| **Environment** | OrangeHRM Demo |
| **Application URL** | https://opensource-demo.orangehrmlive.com/ |
| **Browser** | Google Chrome Version 150.x (64-bit) |
| **Operating System** | Windows 11 Pro 64-bit |
| **Application Version** | Hosted Demo |
| **Build Number** | N/A |
| **Severity** | High |
| **Priority** | High |
| **Reporter** | Javaria Ahmad |
| **Assigned To** | TBD |
| **Status** | New |
| **Reproducibility** | Always |
| **Frequency** | 5/5 Attempts |

---

# Description

The OrangeHRM application provides a logout option that should terminate the authenticated user session and prevent further access to protected application content.

After successfully logging out, using the browser **Back** button displays previously authenticated pages without immediately redirecting the user to the login page.

The previously viewed application content remains visible even though the user has already logged out.

Protected pages should not be available after session termination.

The application should invalidate the authenticated session and prevent sensitive application pages from being displayed from browser history or cache.

---

# Preconditions

1. A valid OrangeHRM user account must exist.
2. The user must be able to successfully login.
3. Browser cache and cookies should be enabled.
4. The user must navigate to at least one authenticated page before logging out.

---

# Test Data

### Authentication Details

| Field | Value |
|------|-------|
| User Role | Administrator |
| Session State | Authenticated |
| Browser | Google Chrome |
| Test Action | Logout followed by browser Back navigation |

---

# Steps to Reproduce

1. Launch Google Chrome.
2. Navigate to the OrangeHRM Demo application.
3. Login using valid Administrator credentials.
4. Navigate to **PIM → Employee List**.
5. Confirm that authenticated employee information is displayed.
6. Click the user profile menu.
7. Click **Logout**.
8. Verify that the application redirects to the login page.
9. Click the browser **Back** button.
10. Observe the previously authenticated page.

---

# Actual Result

The browser displays the previously authenticated OrangeHRM page after the user has logged out.

Previously viewed application content remains visible without requiring the user to login again.

The user can view cached information from the authenticated session.

Depending on the affected page, sensitive information may remain visible until the page is manually refreshed or another navigation request is made.

---

# Expected Result

After logout, the authenticated session should be completely terminated.

When the user clicks the browser Back button:

1. Protected application content should not be displayed.
2. The application should redirect the user to the login page.
3. Any expired authentication token should be rejected.
4. Sensitive authenticated pages should not be served from browser cache.

The application should require the user to login again before accessing any protected resource.

---

# Business Impact

This issue may expose previously authenticated information to another person using the same device or browser session.

Potential impacts include:

- Unauthorized viewing of sensitive employee information.
- Exposure of administrative application data.
- Privacy risks on shared or public computers.
- Increased risk of unauthorized access after a user logs out.
- Reduced confidence in session-security controls.
- Potential non-compliance with organizational security requirements.

The impact is higher for users accessing OrangeHRM from shared workstations or public devices.

---

# Possible Cause

The application may not be preventing authenticated pages from being stored or displayed from the browser cache after logout.

Possible contributing factors include:

- Missing or incorrect cache-control headers.
- Authentication token not fully invalidated during logout.
- Client-side session state remaining available.
- Protected routes not verifying authentication state when restored from browser history.
- Browser Back/Forward Cache restoring the previous authenticated page.

Developer investigation is required to determine whether the issue is caused by browser caching, client-side session management, or server-side session invalidation.

---

# Suggested Fix

Ensure that logout completely invalidates the authenticated session and prevents previously authenticated pages from being accessible.

Recommended implementation considerations include:

- Invalidate the active authentication token or server-side session during logout.
- Verify authentication before rendering every protected route.
- Apply appropriate cache-control headers to authenticated pages.
- Prevent sensitive pages from being restored from browser cache after logout.
- Redirect unauthenticated users to the login page when accessing protected routes.

Example security headers for protected responses should be reviewed:

```text
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Expires: 0

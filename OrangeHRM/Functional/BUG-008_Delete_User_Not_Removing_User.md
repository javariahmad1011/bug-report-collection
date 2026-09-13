# BUG-008 — Deleted User Record Remains Visible in System Users List

## Summary

When an administrator deletes an existing user from the **System Users** page, the application displays a successful deletion message, but the deleted user record remains visible in the user list.

---

# Bug Details

| Field                   | Details                                                  |
| ----------------------- | -------------------------------------------------------- |
| **Bug ID**              | BUG-008                                                  |
| **Title**               | Deleted User Record Remains Visible in System Users List |
| **Module**              | Admin                                                    |
| **Feature**             | User Management → Delete User                            |
| **Category**            | Functional                                               |
| **Environment**         | OrangeHRM Demo                                           |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/               |
| **Browser**             | Google Chrome Version 150.x (64-bit)                     |
| **Operating System**    | Windows 11 Pro 64-bit                                    |
| **Application Version** | Hosted Demo                                              |
| **Build Number**        | N/A                                                      |
| **Severity**            | High                                                     |
| **Priority**            | High                                                     |
| **Reporter**            | Javaria Ahmad                                            |
| **Assigned To**         | TBD                                                      |
| **Status**              | New                                                      |
| **Reproducibility**     | Always                                                   |
| **Frequency**           | 5/5 Attempts                                             |

---

# Description

The **System Users** page allows administrators to remove existing application users.

When an administrator selects a user, clicks the **Delete** action, and confirms the deletion, the application displays a success notification indicating that the record has been deleted.

However, the deleted user remains visible in the **System Users** table.

Refreshing the page and performing a new search for the same username still returns the record, indicating that the user was not actually removed from the system.

This creates a discrepancy between the success message displayed by the user interface and the actual state of the user record.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **Admin → User Management → Users**.
3. Ensure the logged-in administrator has permission to delete users.
4. Ensure a test user exists and is available for deletion.
5. The test user must not be the currently logged-in Administrator account.

---

# Test Data

### User Selected for Deletion

| Field         | Value        |
| ------------- | ------------ |
| Username      | qa.test.user |
| User Role     | ESS          |
| Employee Name | QA Test User |
| Status        | Enabled      |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **Admin**.
4. Open **User Management → Users**.
5. Enter **qa.test.user** in the Username search field.
6. Click **Search**.
7. Verify that the user appears in the search results.
8. Select the checkbox for **qa.test.user**.
9. Click the **Delete Selected** button.
10. Confirm the deletion in the confirmation dialog.
11. Observe the success notification.
12. Review the System Users table.
13. Refresh the browser.
14. Search again for **qa.test.user**.
15. Observe the returned results.

---

# Actual Result

The application displays a successful deletion notification, but the selected user record remains available in the system.

After refreshing the page and searching for the deleted username, the same user is returned.

### Observed Result

| Username     | User Role | Status  | Record State  |
| ------------ | --------- | ------- | ------------- |
| qa.test.user | ESS       | Enabled | Still Present |

The success message does not reflect the actual outcome of the deletion operation.

---

# Expected Result

After the administrator confirms the deletion:

1. The selected user should be permanently removed from the System Users list.
2. A success message should be displayed only after the deletion operation completes successfully.
3. The record should no longer appear after refreshing the page.
4. Searching for the deleted username should return no matching records.
5. The deleted user should no longer be able to authenticate using the removed account.

### Expected Search Result

| Username     | Expected Result  |
| ------------ | ---------------- |
| qa.test.user | No Records Found |

---

# Business Impact

This issue prevents administrators from reliably removing user access from the application.

Potential impacts include:

* Former or unauthorized users may retain application access.
* User offboarding processes may not complete successfully.
* Administrators may incorrectly assume access has been revoked.
* Security and compliance requirements may be violated.
* Access reviews may contain inaccurate user records.
* Manual database or technical intervention may be required.
* User management data may become inconsistent.
* Audit results may incorrectly show active accounts that should have been removed.

If deleted accounts remain active, the defect may create a significant access-control risk.

---

# Possible Cause

The user interface appears to display the success notification before confirming that the backend deletion operation has completed successfully.

Possible technical causes include:

* Delete API request is not sent.
* Incorrect User ID is submitted in the deletion request.
* Backend deletion transaction fails without returning an appropriate error.
* Frontend ignores an unsuccessful API response.
* Soft-delete flag is not updated correctly.
* Cached user data is displayed instead of refreshed server data.
* Success notification is triggered regardless of the API response status.
* Database transaction is rolled back after the frontend reports success.

Developer investigation is required to confirm the root cause.

---

# Suggested Fix

Ensure that the deletion workflow verifies the backend response before displaying a successful deletion notification.

The application should:

1. Submit the correct unique User ID to the delete endpoint.
2. Verify that the deletion operation completes successfully.
3. Return an appropriate success response only after the database operation is committed.
4. Remove the deleted record from the UI after successful deletion.
5. Refresh or invalidate the user list after the operation.
6. Display a meaningful error message if the deletion fails.
7. Ensure deleted user credentials can no longer be used for authentication.

Regression testing should verify:

* Delete a single user
* Delete multiple users
* Delete an Enabled user
* Delete a Disabled user
* Cancel deletion from the confirmation dialog
* Search for a deleted user
* Refresh after deletion
* Pagination after deletion
* User count after deletion
* Delete user from different result pages
* Attempt to delete the currently logged-in administrator
* Attempt deletion without sufficient permission
* Authentication attempt using deleted user credentials
* API response for successful deletion
* API response for failed deletion
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                              | Description                                                             |
| --------------------------------------- | ----------------------------------------------------------------------- |
| BUG-008_Delete_User_Success_Message.png | Screenshot showing successful deletion notification                     |
| BUG-008_User_Record_Still_Visible.png   | Screenshot showing deleted user still present in the System Users table |
| BUG-008_Search_After_Deletion.png       | Search result showing the deleted username still exists                 |
| BUG-008_Delete_Request.har              | Network request captured during the deletion operation                  |
| BUG-008_Delete_Response.json            | API response returned by the delete request                             |
| BUG-008_Delete_User_Flow.mp4            | Screen recording showing the complete delete workflow                   |

---

# Notes

* Issue reproduced consistently in multiple executions.
* The application displays a success notification even though the record is not removed.
* Verify whether the backend record remains active or only the frontend table is stale.
* Verify whether the deleted user can still login after the operation.
* Review the delete API response and database transaction behavior.
* Validate the user count before and after deletion.
* Perform regression testing for both single-user and bulk-user deletion.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

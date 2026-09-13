# BUG-015 — Long Employee Names Are Truncated Without Tooltip in Employee List

## Summary

Long employee names displayed in the **Employee List** are truncated when they exceed the available column width, and the application does not provide a tooltip or another mechanism to view the complete value.

---

# Bug Details

| Field                   | Details                                                            |
| ----------------------- | ------------------------------------------------------------------ |
| **Bug ID**              | BUG-015                                                            |
| **Title**               | Long Employee Names Are Truncated Without Tooltip in Employee List |
| **Module**              | PIM                                                                |
| **Feature**             | Employee Management → Employee List                                |
| **Category**            | UI                                                                 |
| **Environment**         | OrangeHRM Demo                                                     |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/                         |
| **Browser**             | Google Chrome Version 150.x (64-bit)                               |
| **Operating System**    | Windows 11 Pro 64-bit                                              |
| **Application Version** | Hosted Demo                                                        |
| **Build Number**        | N/A                                                                |
| **Screen Resolution**   | 1366 × 768                                                         |
| **Browser Zoom**        | 100%                                                               |
| **Severity**            | Low                                                                |
| **Priority**            | Medium                                                             |
| **Reporter**            | Javaria Ahmad                                                      |
| **Assigned To**         | TBD                                                                |
| **Status**              | New                                                                |
| **Reproducibility**     | Always                                                             |
| **Frequency**           | 5/5 Attempts                                                       |

---

# Description

The **Employee List** displays employee information in a tabular layout.

When an employee has a long first name, last name, or combined display name that exceeds the available column width, the text is truncated in the table.

The truncated value is displayed without a tooltip, expandable text, horizontal scrolling, or another method to view the complete employee name.

As a result, users cannot reliably identify employees with similar names directly from the list and may need to open individual records to determine the full value.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Employee List**.
3. Ensure at least one employee record contains a long name.
4. Use the browser at 100% zoom.
5. Use a desktop resolution of **1366 × 768**.

---

# Test Data

### Employee Record

| Field             | Value                 |
| ----------------- | --------------------- |
| Employee ID       | 1042                  |
| First Name        | ChristopherAlexander  |
| Middle Name       | Jonathan              |
| Last Name         | Montgomery-Wellington |
| Employment Status | Full-Time Permanent   |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **PIM**.
4. Open **Employee List**.
5. Search for employee ID **1042**.
6. Locate the employee record in the results table.
7. Observe the employee name displayed in the corresponding name column.
8. Move the mouse pointer over the truncated text.
9. Wait for a tooltip or full-value preview.
10. Observe the application behavior.

---

# Actual Result

The employee name is truncated because the text exceeds the available column width.

The full value is not available through hover, tooltip, expansion, or another UI mechanism.

### Observed Result

| Field      | Stored Value          | Displayed Value    |
| ---------- | --------------------- | ------------------ |
| First Name | ChristopherAlexander  | ChristopherAlex... |
| Last Name  | Montgomery-Wellington | Montgomery-Wel...  |

Hovering over the truncated text does not display the complete value.

The user must open the employee profile to confirm the full employee name.

---

# Expected Result

The Employee List should allow users to identify the complete value when text is truncated.

The application should use one of the approved UI patterns, such as:

1. Display the complete employee name where sufficient space is available.
2. Apply an ellipsis only when required.
3. Display the full value in a tooltip when the user hovers over truncated text.
4. Ensure the tooltip is keyboard accessible.
5. Preserve the complete underlying value without data loss.

For example:

**Displayed value:** `ChristopherAlex...`

**Tooltip:** `ChristopherAlexander`

Users should be able to access the complete employee name without opening the employee profile.

---

# Business Impact

This issue reduces the usability of the Employee List when records contain long or similar employee names.

Potential impacts include:

* Users may select the wrong employee record.
* Employees with similar names may be difficult to distinguish.
* Administrators must open individual profiles to verify full names.
* Employee search and review workflows take longer.
* HR users may incorrectly identify records during administrative tasks.
* The issue creates an inconsistent experience for data-heavy tables.
* Truncated values reduce accessibility when the hidden text cannot be exposed through keyboard navigation.

The issue is primarily visual and usability-related but can contribute to user-selection errors in large employee datasets.

---

# Possible Cause

The table appears to apply a fixed column width with CSS text-overflow behavior but does not expose the full text value.

Possible technical causes include:

* `text-overflow: ellipsis` is applied without a tooltip.
* Fixed-width table columns are too narrow for supported data.
* The component does not include a `title` or accessible description for truncated values.
* Tooltip functionality is not triggered for overflowing content.
* Responsive table rules reduce column width without alternative display behavior.
* Column widths are not adjusted for longer employee names.

Developer investigation is required to confirm how overflow handling is implemented in the shared table component.

---

# Suggested Fix

Update the Employee List table to provide access to the complete value whenever text is visually truncated.

Recommended implementation:

1. Retain ellipsis behavior where necessary to preserve table layout.
2. Detect when text overflows its container.
3. Display the full value in a tooltip on hover.
4. Make the full value accessible through keyboard focus.
5. Ensure assistive technologies can access the complete text.
6. Review the current column width to determine whether additional space can reasonably be allocated.
7. Apply the same behavior consistently to other table fields that may contain long values.

Regression testing should verify:

* Short employee names
* Long first names
* Long last names
* Long middle names
* Hyphenated names
* Names containing spaces
* International characters
* Multiple employees with similar long names
* Mouse hover
* Keyboard focus
* Browser zoom at 100%
* Browser zoom at 125%
* Browser zoom at 150%
* 1366 × 768 resolution
* 1920 × 1080 resolution
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                   | Description                                                            |
| ---------------------------- | ---------------------------------------------------------------------- |
| BUG-015_Text_Truncated.png   | Screenshot showing truncated employee name                             |
| BUG-015_No_Tooltip.png       | Screenshot showing no full-value tooltip on hover                      |
| BUG-015_Long_Name_Record.png | Screenshot of the employee profile containing the complete stored name |
| BUG-015_Truncation_Flow.mp4  | Screen recording demonstrating the truncation behavior                 |
| BUG-015_Responsive_View.png  | Screenshot showing the issue at 1366 × 768 resolution                  |

---

# Notes

* Issue reproduced consistently with long employee names.
* Verify whether Job Title, Sub Unit, Supervisor, and other table values have the same truncation behavior.
* Confirm that the complete value remains correctly stored in the employee record.
* Tooltip implementation should support both mouse and keyboard users.
* Review accessibility requirements when exposing truncated content.
* Perform regression testing on other pages that use the same table component.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

# BUG-014 — Employee List Table Headers Are Misaligned With Corresponding Data Columns

## Summary

The column headers in the **Employee List** table are not correctly aligned with their corresponding data columns. The misalignment causes employee information to appear under the wrong visual header, reducing table readability and making record interpretation difficult.

---

# Bug Details

| Field                   | Details                                                                    |
| ----------------------- | -------------------------------------------------------------------------- |
| **Bug ID**              | BUG-014                                                                    |
| **Title**               | Employee List Table Headers Are Misaligned With Corresponding Data Columns |
| **Module**              | PIM                                                                        |
| **Feature**             | Employee Management → Employee List                                        |
| **Category**            | UI                                                                         |
| **Environment**         | OrangeHRM Demo                                                             |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/                                 |
| **Browser**             | Google Chrome Version 150.x (64-bit)                                       |
| **Operating System**    | Windows 11 Pro 64-bit                                                      |
| **Application Version** | Hosted Demo                                                                |
| **Build Number**        | N/A                                                                        |
| **Screen Resolution**   | 1920 × 1080                                                                |
| **Browser Zoom**        | 100%                                                                       |
| **Severity**            | Low                                                                        |
| **Priority**            | Medium                                                                     |
| **Reporter**            | Javaria Ahmad                                                              |
| **Assigned To**         | TBD                                                                        |
| **Status**              | New                                                                        |
| **Reproducibility**     | Always                                                                     |
| **Frequency**           | 5/5 Attempts                                                               |

---

# Description

The **Employee List** page displays employee records in a tabular format with headers for information such as Employee ID, First Name, Last Name, Job Title, Employment Status, Sub Unit, Supervisor, and Actions.

The table headers are not horizontally aligned with their respective data columns.

The misalignment becomes particularly noticeable in the middle and right-side columns, where data values appear shifted relative to the corresponding header labels.

For example, values belonging to the **Employment Status** column appear visually closer to the **Job Title** header, while values under **Sub Unit** and **Supervisor** are also offset from their respective headings.

This creates ambiguity when reviewing employee information and reduces the usability of the employee list.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Employee List**.
3. Ensure multiple employee records are available.
4. Use the browser at 100% zoom.
5. Use a desktop viewport with a resolution of **1920 × 1080**.
6. Ensure no browser accessibility scaling or custom stylesheet is enabled.

---

# Test Data

### Employee Records

| Employee ID | First Name | Last Name | Job Title         | Employment Status   |
| ----------- | ---------- | --------- | ----------------- | ------------------- |
| 1025        | John       | Smith     | QA Engineer       | Full-Time Permanent |
| 1026        | Sarah      | Wilson    | HR Manager        | Full-Time Permanent |
| 1027        | Michael    | Brown     | Software Engineer | Contract            |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **PIM**.
4. Open **Employee List**.
5. Ensure multiple employee records are displayed.
6. Observe the table header row.
7. Compare the horizontal position of each header with the data displayed directly below it.
8. Review the **Job Title**, **Employment Status**, **Sub Unit**, and **Supervisor** columns.
9. Scroll vertically through the employee list if additional records are available.
10. Observe the alignment between the headers and corresponding table cells.

---

# Actual Result

Several table headers do not align correctly with the data displayed underneath them.

### Observed Alignment

| Column            | Observed Behavior                    |
| ----------------- | ------------------------------------ |
| Employee ID       | Correctly aligned                    |
| First Name        | Correctly aligned                    |
| Last Name         | Slightly offset                      |
| Job Title         | Data shifted from header             |
| Employment Status | Data not centered under header       |
| Sub Unit          | Header and values visibly misaligned |
| Supervisor        | Data shifted relative to header      |
| Actions           | Icons not centered under header      |

The issue makes it difficult to immediately determine which header belongs to specific employee data.

---

# Expected Result

Each table header should be horizontally aligned with its corresponding data column.

The table should maintain consistent:

* Column widths
* Header padding
* Cell padding
* Text alignment
* Vertical alignment
* Action icon positioning

For example:

| Header            | Corresponding Data  |
| ----------------- | ------------------- |
| Employee ID       | 1025                |
| First Name        | John                |
| Last Name         | Smith               |
| Job Title         | QA Engineer         |
| Employment Status | Full-Time Permanent |
| Sub Unit          | Quality Assurance   |
| Supervisor        | Michael Brown       |

The relationship between each header and its data should be visually clear without requiring additional interpretation.

---

# Business Impact

The issue does not prevent users from accessing employee data; however, it negatively affects readability and usability of the Employee List.

Potential impacts include:

* Administrators may associate data with the wrong column.
* Employee information may be misinterpreted during reviews.
* HR teams may require additional time to verify records.
* Large employee lists become more difficult to scan.
* The interface appears inconsistent and unpolished.
* Users may lose confidence in the accuracy of tabular information.
* Administrative tasks involving frequent record review become less efficient.

The impact increases when tables contain a large number of columns or records.

---

# Possible Cause

The issue appears to be related to inconsistent width, padding, or layout rules between table header and data-cell components.

Possible technical causes include:

* Header and body columns use different width calculations.
* Different padding values are applied to `<th>` and `<td>` elements.
* CSS flex properties are inconsistent between header and row components.
* Fixed column widths are applied only to the table header.
* Dynamic content changes body column widths without updating headers.
* Action column width is calculated differently from other columns.
* Responsive CSS rules affect table header and body independently.
* Header and row components use separate grid definitions.

Developer investigation is required to identify the exact CSS or layout rule responsible for the misalignment.

---

# Suggested Fix

Ensure the table header and data rows use the same column layout definition.

The implementation should:

1. Apply consistent widths to corresponding header and body cells.
2. Use identical horizontal padding for `<th>` and `<td>` elements.
3. Use a shared grid or table-column configuration.
4. Maintain consistent alignment for text and action icons.
5. Prevent long values from unexpectedly changing column widths.
6. Verify layout behavior at different supported viewport sizes.
7. Ensure responsive CSS does not apply inconsistent rules to header and body sections.

Regression testing should verify:

* Employee List with a single record
* Employee List with multiple records
* Long employee names
* Long job titles
* Long employment-status values
* Empty optional fields
* Action column alignment
* Pagination
* Sorting
* Search/filter results
* 100% browser zoom
* 125% browser zoom
* 150% browser zoom
* 1366 × 768 resolution
* 1920 × 1080 resolution
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                              | Description                                                |
| --------------------------------------- | ---------------------------------------------------------- |
| BUG-014_Table_Header_Misaligned.png     | Screenshot showing table headers offset from employee data |
| BUG-014_Header_Alignment_Comparison.png | Annotated screenshot highlighting affected columns         |
| BUG-014_Employee_List_Full_Page.png     | Full-page screenshot of the Employee List                  |
| BUG-014_Alignment_Issue.mp4             | Screen recording showing the table alignment issue         |
| BUG-014_Computed_Styles.txt             | Captured CSS properties for affected header and body cells |

---

# Notes

* Issue reproduced consistently at 100% browser zoom.
* Verify whether alignment changes when table data contains longer values.
* Confirm whether the issue affects other OrangeHRM tables using the same table component.
* Check **Admin → System Users** and other record-list pages for similar behavior.
* Verify alignment at supported desktop resolutions and browser zoom levels.
* No data corruption or functional failure was observed.
* Perform UI regression testing after modifying shared table styles.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

# BUG-016 — Action Buttons Are Not Properly Aligned on Employee Form

## Summary

The **Save** and **Cancel** action buttons on the employee form are not consistently aligned within the form footer. The buttons appear vertically offset and do not follow the same spacing and alignment pattern used across other OrangeHRM forms.

---

# Bug Details

| Field                   | Details                                                  |
| ----------------------- | -------------------------------------------------------- |
| **Bug ID**              | BUG-016                                                  |
| **Title**               | Action Buttons Are Not Properly Aligned on Employee Form |
| **Module**              | PIM                                                      |
| **Feature**             | Employee Management → Employee Details Form              |
| **Category**            | UI                                                       |
| **Environment**         | OrangeHRM Demo                                           |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/               |
| **Browser**             | Google Chrome Version 150.x (64-bit)                     |
| **Operating System**    | Windows 11 Pro 64-bit                                    |
| **Application Version** | Hosted Demo                                              |
| **Build Number**        | N/A                                                      |
| **Screen Resolution**   | 1366 × 768                                               |
| **Browser Zoom**        | 100%                                                     |
| **Severity**            | Low                                                      |
| **Priority**            | Low                                                      |
| **Reporter**            | Javaria Ahmad                                            |
| **Assigned To**         | TBD                                                      |
| **Status**              | New                                                      |
| **Reproducibility**     | Always                                                   |
| **Frequency**           | 5/5 Attempts                                             |

---

# Description

The employee details form contains primary and secondary action buttons used to save or cancel changes.

The **Save** and **Cancel** buttons are visually misaligned within the form action area. The buttons do not share the same vertical position and the spacing between them is inconsistent with other forms in the application.

The issue is particularly noticeable when comparing the employee form with other OrangeHRM pages that use the standard button layout.

Although the buttons remain functional, the inconsistent positioning reduces visual consistency and gives the interface an unfinished appearance.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Employee List**.
3. Open an existing employee record or navigate to an employee-edit form.
4. Use the browser at 100% zoom.
5. Use a desktop viewport with a resolution of **1366 × 768**.

---

# Test Data

### Employee Record

| Field           | Value      |
| --------------- | ---------- |
| Employee Name   | John Smith |
| Employee ID     | 1025       |
| Record Status   | Existing   |
| Edit Permission | Available  |

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **PIM**.
4. Open **Employee List**.
5. Select an existing employee record.
6. Open an editable employee details section.
7. Scroll to the bottom of the form.
8. Locate the **Cancel** and **Save** buttons.
9. Compare the vertical alignment of both buttons.
10. Observe the spacing between the buttons and the form container.
11. Compare the layout with action buttons on another OrangeHRM form.

---

# Actual Result

The **Save** and **Cancel** buttons are not consistently aligned within the form footer.

Observed issues include:

* One button appears slightly higher than the other.
* Horizontal spacing between buttons is inconsistent.
* Button placement does not match the alignment used on other forms.
* The action group appears visually offset from the expected form grid.

### Observed Layout

| Button         | Observed Behavior                   |
| -------------- | ----------------------------------- |
| Cancel         | Slightly offset from Save button    |
| Save           | Positioned lower than Cancel button |
| Spacing        | Inconsistent                        |
| Form Alignment | Does not match standard layout      |

The issue remains visible after refreshing the page.

---

# Expected Result

The **Save** and **Cancel** buttons should be aligned consistently within the form footer.

The button group should:

1. Use the same vertical alignment.
2. Apply consistent horizontal spacing.
3. Follow the standard OrangeHRM button layout.
4. Maintain consistent positioning across desktop resolutions.
5. Preserve alignment when browser zoom is changed within supported limits.
6. Remain visually aligned regardless of form content length.

The button group should appear visually balanced and consistent with other application forms.

---

# Business Impact

The issue does not prevent users from completing the employee-edit workflow; however, it reduces the visual quality and consistency of the application.

Potential impacts include:

* The interface appears unpolished.
* UI consistency across modules is reduced.
* Users may perceive the form as incorrectly rendered.
* Visual defects become more noticeable during demos or client reviews.
* Shared UI components may contain styling inconsistencies that affect additional pages.

The impact is primarily cosmetic, but correcting the issue improves overall usability and interface consistency.

---

# Possible Cause

The issue appears to be related to inconsistent CSS or layout rules applied to the form action buttons.

Possible technical causes include:

* Different margin values applied to each button.
* Inconsistent button heights.
* Different line-height or padding values.
* Action buttons rendered inside separate containers.
* Flexbox alignment is not configured consistently.
* Shared button component styles are overridden on the PIM form.
* Responsive CSS applies different positioning to primary and secondary actions.
* Browser-specific rendering differences.

Developer investigation is required to identify the exact style responsible for the misalignment.

---

# Suggested Fix

Update the form action container so both buttons use a consistent shared layout.

The implementation should:

1. Place **Cancel** and **Save** inside the same flex or grid container.
2. Use consistent button height and vertical padding.
3. Apply a standard gap between action buttons.
4. Align buttons using a shared vertical baseline.
5. Remove unnecessary margins or component-specific overrides.
6. Reuse the standard form-action styling used elsewhere in OrangeHRM.
7. Verify responsive behavior at supported viewport sizes.

Regression testing should verify:

* Add Employee form
* Edit Employee form
* Personal Details
* Contact Details
* Job Details
* Emergency Contacts
* Save/Cancel button alignment
* Forms with validation messages
* Forms with long content
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

| Attachment                             | Description                                                        |
| -------------------------------------- | ------------------------------------------------------------------ |
| BUG-016_Button_Alignment_Issue.png     | Screenshot showing misaligned Save and Cancel buttons              |
| BUG-016_Button_Alignment_Annotated.png | Annotated screenshot highlighting the vertical offset              |
| BUG-016_Comparison_Form.png            | Screenshot comparing correct alignment on another application form |
| BUG-016_Responsive_View.png            | Screenshot showing button positioning at 1366 × 768                |
| BUG-016_Computed_Styles.txt            | Captured CSS properties for the affected button components         |

---

# Notes

* Issue reproduced consistently at 100% browser zoom.
* Both buttons remain clickable and functional.
* No form submission issue was observed.
* Verify whether the same shared button component is used on other PIM forms.
* Check whether alignment changes when validation messages appear.
* Perform UI regression testing after modifying shared button or form-footer styles.
* Verify the fix across Chrome, Firefox, and Microsoft Edge.

# BUG-005 — Employee List Search Response Exceeds Acceptable Performance Threshold

## Summary

Searching the Employee List with broad search criteria takes significantly longer than the acceptable response-time threshold, causing the page to remain in a loading state before results are displayed.

---

# Bug Details

| Field | Details |
|------|---------|
| **Bug ID** | BUG-005 |
| **Title** | Employee List Search Response Exceeds Acceptable Performance Threshold |
| **Module** | PIM |
| **Feature** | Employee Management → Employee List Search |
| **Category** | Performance |
| **Environment** | OrangeHRM Demo |
| **Application URL** | https://opensource-demo.orangehrmlive.com/ |
| **Browser** | Google Chrome Version 150.x (64-bit) |
| **Operating System** | Windows 11 Pro 64-bit |
| **Application Version** | Hosted Demo |
| **Build Number** | N/A |
| **Severity** | Medium |
| **Priority** | Medium |
| **Reporter** | Javaria Ahmad |
| **Assigned To** | TBD |
| **Status** | New |
| **Reproducibility** | Intermittent |
| **Frequency** | 4/5 Attempts |

---

# Description

The **Employee List** page allows administrators and authorized users to search employee records using multiple search criteria.

When a broad employee search is executed, the application remains in a loading state for several seconds before the search results are displayed.

During testing, the response time consistently exceeded the expected performance threshold for an interactive search operation.

The delay is noticeable to the user and becomes more significant when searches are repeated within the same session.

The search operation should return results within an acceptable response time under normal application load.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Employee List**.
3. The Employee List must contain multiple employee records.
4. Chrome Developer Tools must be available for measuring network response time.
5. Browser cache should be cleared before the first execution.
6. Network throttling must be disabled.

---

# Test Data

### Search Configuration

| Field | Value |
|------|-------|
| Employee Name | Blank |
| Employee ID | Blank |
| Employment Status | All |
| Include | Current Employees Only |
| Supervisor Name | Blank |
| Job Title | All |
| Sub Unit | All |

### Performance Measurements

| Attempt | Search Response Time |
|---------|----------------------|
| 1 | 6.2 seconds |
| 2 | 7.1 seconds |
| 3 | 5.8 seconds |
| 4 | 6.7 seconds |
| 5 | 3.9 seconds |

### Expected Performance Threshold

```text
Employee List search response should complete within 3 seconds under normal load.

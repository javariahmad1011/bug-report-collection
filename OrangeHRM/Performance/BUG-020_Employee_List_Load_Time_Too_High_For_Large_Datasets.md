# BUG-020 — Employee List Load Time Degrades Significantly With Large Dataset

## Summary

The **Employee List** page takes significantly longer than the expected performance threshold to load when the employee dataset contains a large number of records. With approximately **5,000 employee records**, the page requires more than **15 seconds** to become fully usable.

---

# Bug Details

| Field                   | Details                                                           |
| ----------------------- | ----------------------------------------------------------------- |
| **Bug ID**              | BUG-020                                                           |
| **Title**               | Employee List Load Time Degrades Significantly With Large Dataset |
| **Module**              | PIM                                                               |
| **Feature**             | Employee Management → Employee List                               |
| **Category**            | Performance                                                       |
| **Environment**         | OrangeHRM Demo / Performance Test Dataset                         |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/                        |
| **Browser**             | Google Chrome Version 150.x (64-bit)                              |
| **Operating System**    | Windows 11 Pro 64-bit                                             |
| **Application Version** | Hosted Demo                                                       |
| **Build Number**        | N/A                                                               |
| **Network**             | Stable broadband connection                                       |
| **Test Dataset**        | Approximately 5,000 Employee Records                              |
| **Severity**            | High                                                              |
| **Priority**            | High                                                              |
| **Reporter**            | Javaria Ahmad                                                     |
| **Assigned To**         | TBD                                                               |
| **Status**              | New                                                               |
| **Reproducibility**     | Frequently                                                        |
| **Frequency**           | 4/5 Attempts                                                      |

---

# Description

The **Employee List** page is used by HR and administrative users to search, review, and manage employee records.

When the employee dataset contains a large number of records, the initial Employee List page load becomes significantly slower.

During testing with approximately **5,000 employee records**, the page required between **14 and 19 seconds** before the employee table was fully populated and the interface became responsive.

During this period, the loading indicator remains visible and users cannot effectively interact with the employee records.

The performance degradation becomes more noticeable as the number of employee records increases, indicating that the page or underlying API does not scale efficiently with larger datasets.

---

# Preconditions

1. Login using a valid Administrator account.
2. The test environment contains approximately 5,000 employee records.
3. Navigate to the **PIM** module.
4. Use a stable network connection.
5. Browser cache should be cleared before the first performance measurement.
6. No other resource-intensive browser activity should be running.
7. Browser Developer Tools should be available for request timing verification.

---

# Test Data

### Dataset Configuration

| Item                      | Value               |
| ------------------------- | ------------------- |
| Total Employee Records    | Approximately 5,000 |
| Default Search Filters    | None                |
| Expected Records Per Page | Paginated subset    |
| User Role                 | Administrator       |
| Network                   | Stable broadband    |

### Performance Measurements

| Attempt | Dataset Size   | Page Load Time |
| ------- | -------------- | -------------- |
| 1       | ~5,000 records | 15.8 seconds   |
| 2       | ~5,000 records | 17.4 seconds   |
| 3       | ~5,000 records | 14.6 seconds   |
| 4       | ~5,000 records | 18.9 seconds   |
| 5       | ~5,000 records | 8.2 seconds    |

### Observed Average

**14.98 seconds**

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Open the browser Developer Tools.
4. Navigate to the **Network** tab.
5. Clear all existing network requests.
6. Navigate to **PIM → Employee List**.
7. Start measuring the page load duration.
8. Observe the loading indicator.
9. Wait until the employee records are displayed.
10. Verify that pagination controls and table actions are available.
11. Record the API response time.
12. Record the time until the interface becomes responsive.
13. Refresh the page.
14. Repeat the test five times.
15. Compare the recorded durations.

---

# Actual Result

The Employee List page takes approximately **14–19 seconds** to load when the dataset contains approximately 5,000 employees.

During the delay:

* The loading indicator remains visible.
* Employee records are unavailable.
* Search and table interactions cannot be used effectively.
* The page appears unresponsive for several seconds.
* Performance varies considerably between repeated executions.

### Observed Performance

| Metric                | Result        |
| --------------------- | ------------- |
| Fastest Observed Load | 8.2 seconds   |
| Slowest Observed Load | 18.9 seconds  |
| Average Load Time     | 14.98 seconds |
| Expected Threshold    | ≤ 5 seconds   |
| Threshold Met         | No            |

The performance degradation becomes more noticeable as dataset size increases.

---

# Expected Result

The Employee List should load within the agreed application performance threshold even when the system contains a large employee dataset.

For approximately 5,000 employee records, the target should be:

**Initial Employee List load ≤ 5 seconds**

The application should:

1. Retrieve only the data required for the current page.
2. Apply pagination at the backend or database level.
3. Avoid loading the complete employee dataset into the browser.
4. Render the employee table without excessive delay.
5. Keep the user interface responsive during data retrieval.
6. Maintain predictable performance as the employee dataset grows.

The response time should remain within acceptable limits for production-representative data volumes.

---

# Business Impact

Slow Employee List performance directly affects HR and administrative workflows.

Potential impacts include:

* Increased time required to locate employee records.
* Delays during employee onboarding and offboarding.
* Reduced HR team productivity.
* Slower employee record maintenance.
* Poor user experience for organizations with large workforces.
* Increased likelihood of repeated clicks or duplicate requests.
* Increased server load if users repeatedly refresh the page.
* Difficulty scaling the application for larger organizations.
* Longer access-review and employee-audit activities.

The issue may become increasingly severe as the employee database continues to grow.

---

# Possible Cause

The performance issue may be caused by inefficient data retrieval, pagination, or rendering logic.

Possible technical causes include:

* Full employee dataset retrieved before pagination is applied.
* Pagination performed on the frontend instead of the database.
* Database queries performing full-table scans.
* Missing indexes on commonly retrieved employee fields.
* Excessive joins between employee-related tables.
* N+1 queries when loading related employee information.
* Separate API calls triggered for each table row.
* Large API response payload.
* Unnecessary employee attributes included in list responses.
* Total-record count query executing inefficiently.
* Frontend rendering thousands of records or objects before displaying the first page.
* Employee profile images or additional metadata being loaded unnecessarily.
* Insufficient backend resource allocation during high-volume requests.

Developer investigation and performance profiling are required to identify the primary bottleneck.

---

# Suggested Fix

Optimize the Employee List data-loading strategy for large datasets.

Recommended improvements include:

1. Implement server-side pagination.
2. Retrieve only the records required for the current page.
3. Apply `LIMIT` and `OFFSET` or equivalent database pagination.
4. Review database execution plans.
5. Add or optimize indexes for frequently queried fields.
6. Eliminate unnecessary database joins.
7. Investigate and remove N+1 query patterns.
8. Return only fields required by the Employee List table.
9. Avoid loading employee images or extended details during initial list retrieval.
10. Optimize the total-record count query.
11. Use caching for stable reference data where appropriate.
12. Review frontend rendering performance.
13. Introduce lazy loading where appropriate.
14. Test scalability using production-representative datasets.

Performance regression testing should verify:

* 100 employee records
* 500 employee records
* 1,000 employee records
* 5,000 employee records
* 10,000+ employee records
* First-page load
* Next-page navigation
* Previous-page navigation
* Search with large dataset
* Filtering with large dataset
* Sorting with large dataset
* Employee List after record creation
* Employee List after deletion
* Concurrent users accessing Employee List
* API response time
* Database execution time
* Frontend rendering time
* Browser memory usage
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                            | Description                                            |
| ------------------------------------- | ------------------------------------------------------ |
| BUG-020_Employee_List_Loading.png     | Screenshot showing prolonged loading indicator         |
| BUG-020_Network_Timing.png            | Browser Network tab showing slow Employee List request |
| BUG-020_Performance_Profile.json      | Browser performance profile captured during page load  |
| BUG-020_Load_Time_Results.xlsx        | Recorded load-time measurements across test runs       |
| BUG-020_Employee_List_Performance.mp4 | Screen recording demonstrating slow page loading       |
| BUG-020_Employee_List_Request.har     | HAR file containing Employee List request timings      |
| BUG-020_Employee_List_Response.json   | API response captured during performance testing       |

---

# Notes

* Issue reproduced in 4 out of 5 executions.
* Performance degradation becomes more noticeable as employee volume increases.
* Separate backend response time from frontend rendering time during investigation.
* Compare cold-cache and warm-cache results.
* Validate whether the API retrieves records outside the visible page.
* Review database execution plans using production-representative data volumes.
* Conduct load testing with multiple concurrent HR users.
* Retest using the same dataset after performance optimization.
* Verify performance across Chrome, Firefox, and Microsoft Edge.

# BUG-019 — System User Search Response Exceeds Acceptable Performance Threshold

## Summary

The **System Users** search takes significantly longer than the acceptable response-time threshold when filtering a moderate user dataset. Search results require approximately **8–12 seconds** to load, causing noticeable delays during routine user-management activities.

---

# Bug Details

| Field                   | Details                                                              |
| ----------------------- | -------------------------------------------------------------------- |
| **Bug ID**              | BUG-019                                                              |
| **Title**               | System User Search Response Exceeds Acceptable Performance Threshold |
| **Module**              | Admin                                                                |
| **Feature**             | User Management → System Users Search                                |
| **Category**            | Performance                                                          |
| **Environment**         | OrangeHRM Demo                                                       |
| **Application URL**     | https://opensource-demo.orangehrmlive.com/                           |
| **Browser**             | Google Chrome Version 150.x (64-bit)                                 |
| **Operating System**    | Windows 11 Pro 64-bit                                                |
| **Application Version** | Hosted Demo                                                          |
| **Build Number**        | N/A                                                                  |
| **Network**             | Stable broadband connection                                          |
| **Severity**            | Medium                                                               |
| **Priority**            | High                                                                 |
| **Reporter**            | Javaria Ahmad                                                        |
| **Assigned To**         | TBD                                                                  |
| **Status**              | New                                                                  |
| **Reproducibility**     | Frequently                                                           |
| **Frequency**           | 4/5 Attempts                                                         |

---

# Description

The **System Users** page allows administrators to search for user accounts using criteria such as Username, User Role, Employee Name, and Status.

When a search is submitted against a moderate user dataset, the application remains in a loading state for several seconds before displaying the matching results.

During testing, search completion consistently required approximately **8–12 seconds**, despite using a stable network connection and a relatively small result set.

The delay occurs with both single-filter and combined-filter searches and is significantly longer than expected for a standard administrative lookup.

This reduces the efficiency of user-management activities, particularly when administrators need to perform multiple searches during access reviews or account maintenance.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **Admin → User Management → Users**.
3. Ensure the System Users dataset contains at least 500 user records.
4. Use a stable network connection.
5. No other resource-intensive browser activity should be running.
6. Browser cache may be cleared before the initial execution to establish a consistent baseline.

---

# Test Data

### Search Scenario 1

| Field         | Value |
| ------------- | ----- |
| Username      | john  |
| User Role     | Any   |
| Employee Name | Blank |
| Status        | Any   |

### Search Scenario 2

| Field         | Value   |
| ------------- | ------- |
| Username      | Blank   |
| User Role     | ESS     |
| Employee Name | Blank   |
| Status        | Enabled |

### Performance Measurements

| Attempt | Search Criteria               | Response Time |
| ------- | ----------------------------- | ------------- |
| 1       | Username = john               | 9.2 seconds   |
| 2       | Role = ESS                    | 11.4 seconds  |
| 3       | Status = Enabled              | 8.7 seconds   |
| 4       | Role = ESS + Status = Enabled | 12.1 seconds  |
| 5       | Username = john               | 4.8 seconds   |

### Observed Average

**9.24 seconds**

---

# Steps to Reproduce

1. Launch the OrangeHRM application.
2. Login using a valid Administrator account.
3. Navigate to **Admin**.
4. Open **User Management → Users**.
5. Wait until the System Users page is fully loaded.
6. Enter **john** in the Username field.
7. Open the browser Developer Tools.
8. Navigate to the **Network** tab.
9. Clear existing network requests.
10. Click **Search**.
11. Measure the time between submitting the search and the results becoming available.
12. Record the API request duration.
13. Repeat the search five times.
14. Repeat the test using **User Role = ESS** and **Status = Enabled**.
15. Compare the recorded response times.

---

# Actual Result

The System Users search frequently requires approximately **8–12 seconds** before results are displayed.

During this period:

* The application remains in a loading state.
* The user cannot immediately review the requested results.
* Repeated searches introduce cumulative delays.
* Search performance varies noticeably between executions.

### Observed Performance

| Metric                    | Result       |
| ------------------------- | ------------ |
| Fastest Observed Response | 4.8 seconds  |
| Slowest Observed Response | 12.1 seconds |
| Average Response Time     | 9.24 seconds |
| Expected Threshold        | ≤ 3 seconds  |
| Threshold Met             | No           |

The delay is observable even when the number of returned records is small.

---

# Expected Result

The System Users search should complete within the agreed application performance threshold under normal operating conditions.

For a moderate dataset and standard search criteria, the expected target should be:

**Search response ≤ 3 seconds**

The application should:

1. Submit the search request immediately.
2. Process the selected criteria efficiently.
3. Return only the required paginated data.
4. Display the results without excessive delay.
5. Maintain consistent response times across repeated searches.

Search duration should not increase significantly when only a small number of matching records are returned.

---

# Business Impact

Slow search performance reduces administrator productivity and can negatively affect high-volume account-management workflows.

Potential impacts include:

* Increased time required for user administration.
* Delays during account provisioning and deprovisioning.
* Slower access reviews and audits.
* Reduced productivity for support and security teams.
* Repeated clicks if users assume the search action has failed.
* Increased risk of duplicate requests.
* Poor user experience during peak usage periods.
* Performance degradation becoming more severe as the user dataset grows.

The impact is greater for teams that perform frequent user searches throughout the working day.

---

# Possible Cause

The performance issue may originate from inefficient backend query execution or excessive frontend processing.

Possible technical causes include:

* Database search fields are not appropriately indexed.
* SQL query performs full-table scans.
* Multiple unnecessary database joins are executed.
* Total-count and data queries are executed inefficiently.
* Search endpoint retrieves more records than required before pagination is applied.
* Filtering is performed in application memory instead of at database level.
* Multiple dependent API calls are triggered for a single search.
* Employee or role information is retrieved using N+1 queries.
* API response contains unnecessary payload data.
* Frontend performs excessive processing before rendering results.
* Server resources are constrained during concurrent usage.

Developer investigation and backend profiling are required to identify the primary bottleneck.

---

# Suggested Fix

Profile the complete search flow and optimize the operations contributing most heavily to response time.

Recommended investigation should include:

1. Review database execution plans for user-search queries.
2. Add or optimize indexes for frequently searched fields.
3. Apply filtering before retrieving result data.
4. Apply pagination at database-query level.
5. Remove unnecessary joins and duplicate queries.
6. Review potential N+1 query behavior.
7. Return only fields required by the System Users table.
8. Cache stable reference data where appropriate.
9. Review API serialization and frontend rendering time.
10. Perform load testing with production-representative data volumes.

Performance regression testing should verify:

* Search by Username
* Search by User Role
* Search by Employee Name
* Search by Status
* Combined filters
* Search returning zero records
* Search returning one record
* Search returning many records
* First search after login
* Repeated searches
* Search after Reset
* Pagination after search
* 500 user records
* 1,000 user records
* 5,000+ user records
* Concurrent user searches
* API response time
* Database query execution time
* Chrome
* Firefox
* Microsoft Edge

---

# Attachments

| Attachment                         | Description                                                 |
| ---------------------------------- | ----------------------------------------------------------- |
| BUG-019_Slow_Search_Loading.png    | Screenshot showing prolonged loading state after Search     |
| BUG-019_Network_Timing.png         | Browser Network tab showing slow search request duration    |
| BUG-019_Performance_Profile.json   | Exported browser performance profile captured during search |
| BUG-019_Search_Response_Times.xlsx | Recorded response times across multiple test executions     |
| BUG-019_Search_Performance.mp4     | Screen recording demonstrating delayed search results       |
| BUG-019_Search_Request.har         | HAR file containing search request and timing information   |
| BUG-019_Search_Response.json       | Search API response captured during slow execution          |

---

# Notes

* Issue reproduced in 4 out of 5 executions.
* Response time should be measured separately for network, backend processing, and frontend rendering.
* Verify performance using a production-representative dataset before determining the final performance SLA.
* Test under both single-user and concurrent-user load.
* Compare filtered search performance against the initial unfiltered user-list request.
* Review database execution plans and API telemetry during reproduction.
* Retest after optimization using identical datasets and search criteria.
* Verify performance across Chrome, Firefox, and Microsoft Edge.

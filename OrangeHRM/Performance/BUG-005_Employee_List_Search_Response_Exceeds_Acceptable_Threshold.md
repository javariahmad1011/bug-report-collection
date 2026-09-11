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

During testing, the response time repeatedly exceeded the expected performance threshold for an interactive search operation.

The delay is noticeable to the user and may become more significant when searches are repeated within the same session.

The search operation should return results within an acceptable response time under normal application load.

---

# Preconditions

1. Login using a valid Administrator account.
2. Navigate to **PIM → Employee List**.
3. Ensure multiple employee records exist in the Employee List.
4. Open Chrome Developer Tools.
5. Clear the browser cache before the first execution.
6. Ensure network throttling is disabled.
7. Use a stable internet connection during testing.

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

Employee List search response should complete within **3 seconds** under normal load.

---

# Steps to Reproduce

1. Launch Google Chrome.
2. Navigate to the OrangeHRM Demo application.
3. Login using valid Administrator credentials.
4. Navigate to **PIM → Employee List**.
5. Open Chrome Developer Tools.
6. Select the **Network** tab.
7. Clear all existing network requests.
8. Confirm that network throttling is set to **No throttling**.
9. Leave all optional Employee List search filters empty.
10. Click **Search**.
11. Observe the loading indicator displayed on the Employee List page.
12. Measure the time from clicking Search until the employee records are fully displayed.
13. Identify the Employee List API request in the Network tab.
14. Record the API response duration.
15. Repeat the search five times.
16. Compare the recorded response times with the expected 3-second performance threshold.

---

# Actual Result

The Employee List search takes approximately **3.9 to 7.1 seconds** to complete.

Four out of five test executions exceeded the defined **3-second response-time threshold**.

The following behavior is observed while the request is being processed:

- The loading indicator remains visible for several seconds.
- Employee search results are unavailable during the delay.
- The user cannot immediately interact with the returned employee records.
- Repeated searches continue to produce noticeable response delays.
- The user may assume that the Search action was not successfully triggered.

The highest recorded response time during testing was **7.1 seconds**.

---

# Expected Result

The Employee List search should complete within the defined performance threshold under normal application load.

The expected response time should be:

**3 seconds or less**

The application should:

- Process search requests efficiently.
- Return employee records without noticeable delay.
- Maintain consistent response times across repeated searches.
- Avoid unnecessary or duplicate network requests.
- Display appropriate loading feedback while processing.
- Allow the user to continue working without excessive waiting time.

---

# Business Impact

Slow Employee List search performance directly affects administrators and HR users who regularly search and manage employee records.

Potential impacts include:

- Increased time required to locate employee information.
- Reduced productivity for HR and administrative users.
- Poor user experience during frequent employee searches.
- Increased likelihood of users clicking the Search button multiple times.
- Additional duplicate requests being generated due to perceived application inactivity.
- Reduced confidence in application responsiveness.
- Increased support complaints related to application performance.
- Greater performance degradation in environments containing significantly larger employee datasets.

In production environments with larger datasets and concurrent users, the response delay may become more significant.

---

# Possible Cause

The delay may be related to inefficient processing of broad Employee List search requests.

Possible contributing factors include:

- Database queries scanning unnecessary employee records.
- Missing or inefficient database indexes.
- Excessive response payload size.
- Backend filtering being applied after retrieving a large dataset.
- Multiple dependent API requests being executed sequentially.
- Expensive joins between employee-related database tables.
- Inefficient server-side pagination.
- Repeated retrieval of static reference data.
- Unnecessary frontend re-rendering after receiving the response.
- Duplicate network requests triggered during the search operation.

Developer investigation and backend performance profiling are required to determine the exact root cause.

---

# Suggested Fix

Review and optimize the Employee List search workflow to ensure acceptable response times.

Recommended investigation areas include:

- Review database execution plans for Employee List queries.
- Add or optimize indexes for frequently searched fields.
- Apply filtering at the database level before retrieving unnecessary records.
- Verify server-side pagination is implemented efficiently.
- Reduce unnecessary response payload data.
- Remove duplicate or redundant API requests.
- Cache static reference data where appropriate.
- Review backend service processing time.
- Review frontend rendering time after API response.
- Implement query optimization for broad searches.

Performance regression testing should verify the following scenarios:

- Search with no filters.
- Search by Employee Name.
- Search by Employee ID.
- Search by Employment Status.
- Search by Job Title.
- Search by Sub Unit.
- Search using multiple filters.
- First search after login.
- Repeated searches within the same session.
- Pagination after search.
- Sorting search results.
- Large result sets.
- Small result sets.
- Search after clearing browser cache.
- Search after application refresh.

The Employee List search should consistently complete within the agreed performance threshold.

---

# Attachments

| Attachment | Description |
|------------|-------------|
| BUG-005_Employee_List_Loading.png | Screenshot showing Employee List in loading state |
| BUG-005_Network_Response_Time.png | Chrome DevTools screenshot showing search request duration |
| BUG-005_Performance_Test_Results.png | Screenshot or document containing recorded response-time measurements |
| BUG-005_Employee_Search.har | HAR file containing Employee List search network traffic |
| BUG-005_Performance_Reproduction.mp4 | Screen recording demonstrating delayed search response |

---

# Notes

- Performance measurements should be repeated using a stable network connection.
- Browser network throttling must remain disabled during baseline testing.
- Client-side rendering time and server-side response time should be measured separately.
- Verify whether response time increases as the number of employee records increases.
- Compare first-search performance with subsequent searches.
- Verify whether the same behavior occurs after clearing browser cache.
- Test using Google Chrome, Mozilla Firefox, and Microsoft Edge.
- Review server logs and database query execution times during investigation.
- Re-run performance testing after optimization to confirm the response time remains within the agreed threshold.

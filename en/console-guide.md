## AI Service > AI Fashion > Console User Guide

The console provides a dashboard where you can create or delete services and monitor indexing status and search statistics.<br>
AI Fashion's camera search and similar item recommendations features are provided as APIs, and the detailed features can be implemented by referring to the [Service API Guide](./service-api-guide).

The following describes how to use the console.

## Service List

### Create Services
1. Click **Create Service**.
2. Enter the service name.
   - Only lowercase letters, numbers, _ (underscore) and - (hyphen) are allowed in a service name.
   - It cannot start with a number, _ (underscore), or - (hyphen).
   - You can only enter 2 to 32 characters.
3. Click **Save**.

### Delete Services
1. From the service list, click the **Delete** button of the service to delete.
   - Services that are being indexed cannot be deleted.
2. When the **Delete** dialog box appears, click the **Confirm** button.

## Checking Indexing Status and Search Statistics

### Indexing Status
You can check the indexing status of the service selected in the service list.
- Up to 30 items are displayed in the order of the most recent logs.

### Search Statistics
If you click the **Search Statistics** tab, you can view the query count (QC, the number of API calls) of Camera Search and Similar Item Recommendations in charts.
- You can view the search QC during the last 3 months starting from the previous day.
- You can download a file in .xls, .csv, .png, and .jpeg format.

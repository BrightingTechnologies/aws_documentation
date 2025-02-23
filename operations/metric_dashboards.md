# Creating Dashboards in CloudWatch

## Purpose

This document provides guidelines for creating and configuring CloudWatch dashboards to monitor and visualize metrics and logs. It includes steps to create a dashboard, add widgets, and best practices for effective monitoring.

## Contents

1. [Steps to Create a CloudWatch Dashboard](#steps-to-create-a-cloudwatch-dashboard)
2. [Example Dashboard Widgets](#example-dashboard-widgets)
   - [Display Recent Error Logs](#display-recent-error-logs)
   - [Show HTTP Status Code Distribution](#show-http-status-code-distribution)
   - [Monitor Response Times Over Time](#monitor-response-times-over-time)
3. [Best Practices](#best-practices)

## Steps to Create a CloudWatch Dashboard

1. Open the **AWS Management Console**.
2. Navigate to **CloudWatch**.
3. In the left pane, select **Dashboards**.
4. Click **Create dashboard**.
5. Enter a name for your dashboard and click **Create**.
6. Choose a widget type (e.g., Line, Bar, Number, Logs, or Text).
7. Select a CloudWatch log group or metric to display in the widget.
8. Configure the visualization settings and apply filters if needed.
9. Click **Add widget**.
10. Repeat steps 6-9 to add more widgets as needed.
11. Click **Save dashboard**.

## Example Dashboard Widgets

### Display Recent Error Logs

1. Add a **Logs Insights** widget.
2. Use the following query:

    ```sql
    fields @timestamp, @message
    | filter @message like /ERROR/
    | sort @timestamp desc
    | limit 10
    ```

3. Configure the widget to display results in a table format.

### Show HTTP Status Code Distribution

1. Add a **Bar Chart** widget.
2. Use the following query:

    ```sql
    stats count(*) by statusCode
    ```

3. Configure the widget to group by `statusCode`.

### Monitor Response Times Over Time

1. Add a **Line Chart** widget.
2. Use the following query:

    ```sql
    fields @timestamp, responseTime
    | stats avg(responseTime) as avgResponseTime by bin(5m)
    ```

3. Set the Y-axis to `avgResponseTime`.

## Best Practices

- Use filters to reduce query execution time.
- Use `bin()` for grouping logs by time intervals.
- Limit query results to avoid performance issues.
- Optimize retention settings to manage costs effectively.
- Organize dashboards by service or application for better visibility.

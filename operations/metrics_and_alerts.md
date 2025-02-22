# Serverless Workload Health Metrics, Logs, and Alerts Documentation

## 1. Introduction

This document outlines the process for defining, collecting, and analyzing workload health metrics, exporting standard application logs for error tracking, and defining operational thresholds to generate alerts for serverless architectures. These practices ensure that serverless workloads, primarily using AWS Lambda, SQS, SNS, S3, and API Gateway, are performing optimally, any issues are detected early, and necessary actions are taken for seamless operations.

## 2. Defining and Collecting Workload Health Metrics for Serverless Applications

### 2.1. Workload Health Metrics Overview

Serverless architectures require a different approach to health metrics, as resources like compute and storage scale automatically. Key metrics help ensure the serverless components are functioning as expected, and performance issues can be proactively addressed.

**Key Workload Health Metrics for Serverless to Track:**

**Lambda Metrics Examples:**

In the following table we have listed some of the more important Lambda metrics.

**Full list is available at:** [Lambda Monitoring Metrics Types](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-metrics-types.html)

| **Metric Name**                 | **Description**                                                                                                                        |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Duration**                    | The amount of time it takes for Lambda functions to execute.                                                                           |
| **Error count**                 | The number of failed invocations due to errors or timeouts.                                                                            |
| **Throttles**                   | The number of times Lambda functions are throttled due to concurrency limits.                                                          |
| **IteratorAge** (stream-based)  | The age of the last record read by the Lambda function when processing SQS or DynamoDB streams.                                        |
| **Async event age**             | Time between Lambda successfully queues the event and when the function is invoked. Increase in this metric indicates execution errors |
| **Total concurrent executions** | Number of functions that are processing events                                                                                         |
| **Async events dropped**        | Number of events dropped without executing the function successfully                                                                   |

**Establishing baseline values**

1. **Understanding the Application**

   - What is the primary function of this Lambda?
   - Is it user-facing or a background process?
   - How critical is its performance to the overall system?

2. **Performance Expectations**

   - What is the expected execution time under normal conditions?
   - How does performance change under peak loads?
   - How often does the function experience cold starts?

3. **Error Handling & Tolerance**

   - What is an acceptable error rate?
   - What types of failures are most likely to occur?
   - How do errors impact downstream services?

4. **Scaling and Traffic Patterns**

   - How does invocation frequency change over time?
   - What are the peak and off-peak usage periods?
   - How should concurrency limits be set?

5. **Resource Utilization**

   - How much memory does the function typically use?
   - Are there frequent out-of-memory errors?
   - Is the allocated memory optimized for performance and cost?

6. **Cost Considerations**

   - What is the cost impact of different execution times?
   - Are there optimizations that could reduce Lambda execution costs?

7. **Alerting and Monitoring**
   - What threshold values should trigger warnings or alerts?
   - What metrics should be monitored continuously?
   - How quickly should alerts be acted upon?

**SQS Metrics:**

In the following table we have listed some of the more important SQS metrics.

**Full list of SQS metrics:** [Amazon SQS Metrics](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html)

| **Metric Name**                         | **Description**                                                                                                                    |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Approximate age of oldest message**   | Age of the oldest message. Messages older than configurable duration will be deleted. Maximum age of message is 14 days            |
| **Approximate age of visible messages** | Number of messages to be processed. Increase in this metric suggests that the messages are lingering in queue longer than expected |
| **Number of empty receives**            | Shows how many times has the polling mechanism returned empty result                                                               |

**SNS Metrics:**

**Full list of metrics is available at:** [Amazon SNS Monitoring](https://docs.aws.amazon.com/sns/latest/dg/sns-monitoring-using-cloudwatch.html)

| **Metric Name**                            | **Description**                                                                |
| ------------------------------------------ | ------------------------------------------------------------------------------ |
| **Delivery Failures**                      | The number of failed attempts to deliver SNS messages to subscribed endpoints. |
| **Message Count**                          | Total number of messages published to SNS topics.                              |
| **Number of messages with bad attributes** | Number of messages with malformed structure.                                   |

**API Gateway Metrics:**

**Complete list of metrics** [API Gateway Metrics](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-metrics-and-dimensions.html)

| **Metric Name**        | **Description**                                              |
| ---------------------- | ------------------------------------------------------------ |
| **Request Count**      | Total number of API requests processed.                      |
| **Latency**            | The amount of time taken for an API request to be processed. |
| **5xx/4xx Error Rate** | The rate of client and server errors from API Gateway.       |

### 2.2. Collecting Metrics with AWS Services

AWS offers several tools to collect, monitor, and analyze metrics in serverless architectures:

- **Amazon CloudWatch:** Monitors Lambda, SQS, SNS, S3, and API Gateway metrics. Custom CloudWatch metrics can be created for Lambda to monitor application-specific health metrics.
- **AWS X-Ray:** Helps trace Lambda executions and API Gateway requests, providing deeper insights into performance bottlenecks and issues across serverless functions.
- **AWS CloudTrail:** Tracks and logs API activity in services like Lambda, SQS, SNS, and S3 for operational insights and security auditing.

### 2.3. Best Practices for Collecting Metrics

- Enable Lambda logging and X-Ray tracing to monitor execution and latency in real-time.
- Use CloudWatch Dashboards to visualize key metrics such as Lambda errors, queue length, and API Gateway latency.
- Monitor SNS delivery failures to ensure reliable message delivery.
- Implement CloudWatch custom metrics where necessary to track application-specific metrics beyond standard AWS metrics.

## 3. Exporting Application Logs for Error Tracking and Troubleshooting

### 3.1. Application Logs Overview

Application logs are essential for troubleshooting and error resolution in a serverless environment. These logs provide visibility into Lambda invocations, API calls, and events in SQS, SNS, and S3.

**Types of Logs to Export:**

- **Lambda Logs:** Logs related to Lambda invocations, including errors, execution time, and custom log statements. These logs are captured in Amazon CloudWatch Logs.
- **API Gateway Logs:** Logs detailing incoming requests, response times, and errors from API Gateway, useful for monitoring API performance.
- **SQS Logs:** Logs showing the receipt, processing, and deletion of messages in SQS queues.
- **SNS Logs:** Logs that detail message publication and delivery attempts.
- **S3 Logs:** Access logs related to requests for objects in S3.

### 3.2. Exporting Logs Using AWS Services

- **CloudWatch Logs:** Capture and export Lambda logs, API Gateway logs, and custom logs for serverless applications.
- **S3 Logging:** Enable S3 access logs to track user requests to your S3 buckets. This can be directed to a specific S3 bucket for analysis.
- **API Gateway Logging:** Enable access logging and execution logging for API Gateway to monitor HTTP request and response data.
- **AWS Lambda Logging:** Automatically sends logs to CloudWatch Logs. You can configure log retention policies to manage log data.

### 3.3. Best Practices for Exporting Logs

- Ensure structured logging (e.g., JSON format) for easier parsing and filtering.
- Centralize logs from Lambda, API Gateway, and other services using CloudWatch Logs groups.
- Use CloudWatch Logs Insights for querying and analyzing log data for troubleshooting.
- Enable log retention policies to manage storage and ensure compliance.

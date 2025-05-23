# Application Logs for Error Tracking and Troubleshooting

## Purpose

This document provides guidelines for effective application logging to ensure efficient error tracking and troubleshooting in a serverless environment. It covers the use of Brightings Infrastructure Utilities for consistent logging, the setup of CloudWatch Log Groups, and the streaming of logs to Elastic/OpenSearch.

## Contents

1. [Application Logs Overview](#11-application-logs-overview)
2. [How to Perform Application Logging](#how-to-perform-application-logging)
3. [CloudWatch Log Groups](#2-cloudwatch-log-groups)
   - [Lambda Log Groups](#21-lambda-log-groups)
   - [ECS Task Log Groups](#22-ecs-task-log-groups)
4. [Streaming Application Logs to Elastic/OpenSearch](#3-streaming-application-logs-to-elasticopensearch)

## 1. Application Logs for Error Tracking and Troubleshooting

### 1.1. Application Logs Overview

Application logs are essential for troubleshooting and error resolution in a serverless environment. These logs provide visibility into Lambda invocations, API calls, and events in SQS, SNS, and S3.

## How to Perform Application Logging

To ensure effective log search capabilities in both CloudWatch and third-party services, it is essential to maintain a consistent approach to application logging. For this purpose, always use the following methods from the Brightings Infrastructure Utilities package:

- `logError`
- `logDebug`
- `logWarn`
- `logInfo`

Following examples apply to all the logging functions:

```javascript
logInfo('operation-name', {
    message: 'Log message',
    logField1,
    logField2,
    logField3...
})
```

operation-name: use business flow name like 'create-order', 'publish-article' to track events across several classes
message: specific message for the class/function that logs the message
logFields: any identifier or field that can help track entities through the flow. Examples: entityId, status fields etc...

## 2. Cloudwatch Log Groups

A log stream is a sequence of log events that share the same source. Each separate source of logs in CloudWatch Logs makes up a separate log stream

A log group is a group of log streams that share the same retention, monitoring, and access control settings. You can define log groups and specify which streams to put into each group. There is no limit on the number of log streams that can belong to one log group.

### 2.1 Lambda Log Groups

When you create a new AWS Lambda function, Amazon CloudWatch automatically creates a Log Group for that function. This Log Group will contain all the log streams generated by the function's invocations. The default naming convention for the Log Group is /aws/lambda/<function-name>, where <function-name> is the name of your Lambda function.

If you don't see the Log Group immediately, it's usually because the function hasn't been invoked yet. Once the function runs and generates logs, the Log Group will appear in CloudWatch.

### 2.2 ECS Task Log Groups

For Amazon ECS (Elastic Container Service) tasks, the creation of CloudWatch Log Groups is not automatic by default. You need to set up logging when you define your ECS task definition. Here's how you can configure it:

Create a Log Group: Manually create a CloudWatch Log Group for your ECS tasks if one doesn't exist.

Update Task Definition:

In your ECS task definition, specify the log configuration under the logConfiguration parameter.

Use the awslogs driver to send logs from your ECS tasks to CloudWatch Logs.

Define the Log Group and the Log Stream prefix.

[AWS ECS CloudWatch Docs](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_awslogs.html)

## 3. Streaming Application logs to Elastic/OpenSearch

Brighting has internal tool called Logshipper available for installation from Serverless Application Repository in AWS.

When deployed this tool subscribes to log groups in cloudwatch via regex pattern and streams the application logs to target cluster.

Installation instructions are available within the Serverless Application Repository


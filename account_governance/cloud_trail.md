# CloudTrail Setup and Protection Guide

## Purpose

This document provides a step-by-step guide to creating an AWS CloudTrail and securing it against accidental deletion. CloudTrail helps in monitoring and logging AWS API activity for security, compliance, and operational auditing.

**Introduction to AWS CloudTrail**

### Overview of AWS CloudTrail

AWS CloudTrail is a service that provides governance, compliance, and operational and risk auditing for your AWS account. It enables you to track user activity and API usage across your AWS infrastructure by recording events such as console sign-ins, API calls, and changes to AWS resources. These logs can be stored in an Amazon S3 bucket, analyzed using AWS services like AWS CloudTrail Lake, or forwarded to security monitoring tools.

### Key Features of CloudTrail

- **Event Logging:** Records actions performed in AWS by users, services, and applications.
- **Multi-Region Support:** Allows you to enable logging across multiple AWS regions for a comprehensive security posture.
- **CloudTrail Insights:** Detects unusual activity patterns, such as spikes in API calls, to help identify security threats.
- **Integration with AWS Security Services:** Works with AWS Security Hub, Amazon GuardDuty, and AWS Lambda for automated threat response.
- **Long-Term Storage and Analysis:** Logs can be retained for compliance and used for forensic investigations.

### Importance of Securing CloudTrail

AWS CloudTrail plays a critical role in security monitoring and incident response. If misconfigured or disabled, attackers could operate undetected within your AWS environment. Key security best practices include:

- **Enabling CloudTrail Across All Regions:** Ensures that no unauthorized activity goes untracked in any region.
- **Encrypting Logs with AWS KMS:** Protects log integrity and prevents tampering.
- **Using Amazon S3 Bucket Policies:** Restricts access to CloudTrail logs, ensuring that only authorized personnel can view or modify them.
- **Enabling Multi-Factor Authentication (MFA) for CloudTrail Management:** Prevents unauthorized users from modifying or disabling the trail.
- **Monitoring CloudTrail Logs with AWS Config and GuardDuty:** Detects suspicious activities such as API calls from unexpected locations.
- **Setting Up Alerts for Anomalous Activity:** Using AWS CloudWatch Alarms to trigger notifications when critical changes occur.

## Step 1: Create a CloudTrail

1. **Navigate to the AWS CloudTrail Console**
   - Open the AWS Management Console.
   - Go to **CloudTrail** or visit [CloudTrail Console](https://console.aws.amazon.com/cloudtrail/).
2. **Create a New Trail**
   - Click **Create trail**.
   - Enter a **Trail Name** (e.g., `SecurityAuditTrail`).
3. **Choose Trail Scope**
   - Enable **Enable for all accounts in my organisations** (if using AWS Organizations).
4. **Choose S3 Bucket**
   - Select S3 bucket and select encryption with existing or new key
5. **Enable Log file validation**
6. **Enable Event Logging**

   - Select **Management Events** → Choose **Read/Write events** (`All` for full tracking).
   - (Optional) Enable **Data Events** for:
     - **S3:** To track object-level access.
     - **Lambda:** To track function execution.

7. Click **Next**, review settings, and then click **Create trail**.

---

## Step 2: Store Logs in an S3 Bucket

1. **Choose or Create an S3 Bucket**

   - Select an existing bucket or create a new one (e.g., `aws-cloudtrail-logs-youraccount`).
   - Ensure the bucket is in a secure AWS region.

2. **Enable Encryption and Security**

   - Enable **Server-Side Encryption (SSE-KMS)**.
   - Enable **Log File Integrity Validation**.

3. **Enable Versioning and Object Lock**
   - Enable **Bucket Versioning** (to retain log history even if deleted).
   - Enable **Object Lock (Governance Mode)** (prevents accidental deletion).

---

## Step 3: Apply an S3 Bucket Policy to Prevent Deletion

1. **Go to the S3 Console**

   - Open **S3** → Select the bucket used for CloudTrail logs.
   - Navigate to **Permissions** → **Bucket Policy**.

2. **Add the Following Bucket Policy**

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "DenyDeleteCloudTrailLogs",
         "Effect": "Deny",
         "Principal": "*",
         "Action": ["s3:DeleteObject", "s3:DeleteObjectVersion"],
         "Resource": "arn:aws:s3:::aws-cloudtrail-logs-youraccount/*",
         "Condition": {
           "StringNotEquals": {
             "aws:PrincipalArn": "arn:aws:iam::YOUR_ACCOUNT_ID:role/AdminRole"
           }
         }
       }
     ]
   }
   ```

   - Replace `YOUR_ACCOUNT_ID` with your AWS Account ID.
   - Replace `AdminRole` with an IAM role that can manage logs.

3. Click **Save** to apply the policy.

---

This setup ensures CloudTrail logs are **stored securely**, **cannot be accidentally deleted**, and **maintain compliance with security best practices**.

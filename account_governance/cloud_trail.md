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

---

## Step 1: Create log destination

Ideally log bucket will be placed in a Security account

1. **Choose or Create an S3 Bucket**

   - Select an existing bucket or create a new one (e.g., `aws-cloudtrail-logs-youraccount`).
   - Ensure the bucket is in a secure AWS region.
   - Ideally create the bucket in the Audit/Security account. In order to allow log writes attach a policy to this bucket
   ```json
   {
      "Version": "2012-10-17",
      "Statement": [
         {
               "Sid": "AWSCloudTrailAclCheck20150319",
               "Effect": "Allow",
               "Principal": {
                  "Service": "cloudtrail.amazonaws.com"
               },
               "Action": "s3:GetBucketAcl",
               "Resource": "arn:aws:s3:::personal-cloud-trail-logs"
         },
         {
               "Sid": "AWSCloudTrailWrite20150319",
               "Effect": "Allow",
               "Principal": {
                  "Service": "cloudtrail.amazonaws.com"
               },
               "Action": "s3:PutObject",
               "Resource": "arn:aws:s3:::personal-cloud-trail-logs/AWSLogs/*",
               "Condition": {
                  "StringEquals": {
                     "s3:x-amz-acl": "bucket-owner-full-control",
                     "aws:SourceOrgID": "YOUR_ORG_ID"
                  }
               }
         }
      ]
   }
   ```
   Org ID can be obtained by:
   ```bash
   aws organizations describe-organization --query 'Organization.Id' --output text
   ```
   Or by looking it up in the AWS Organizations Console
2. **Enable Encryption and Security**

   - Enable **Server-Side Encryption (SSE-KMS)**.
   - Enable **Log File Integrity Validation**.

3. **Enable Versioning and Object Lock**
   - Enable **Bucket Versioning** (to retain log history even if deleted).
   - Enable **Object Lock (Governance Mode)** (prevents accidental deletion).

---

## Step 2: Apply an S3 Bucket Policy to Prevent Deletion

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

   or if using Organizations

   Add SCP to the OU that prevents deletion of the cloudtrail. If you are not using Organizations this policy can be attached to any user or user group

   ```json
   {
      "Version": "2012-10-17",
      "Statement": [
         {
               "Sid": "ProtectCloudTrail",
               "Effect": "Deny",
               "Action": [
                  "cloudtrail:DeleteTrail",
                  "cloudtrail:StopLogging",
                  "cloudtrail:UpdateTrail",
                  "cloudtrail:PutEventSelectors",
                  "cloudtrail:DeleteEventSelectors"
               ],
               "Resource": "*"
         }
      ]
   }

   ```

   Add another SCP to the OU to protect the log bucket

   ```json
   {
      "Version": "2012-10-17",
      "Statement": [
         {
               "Sid": "ProtectCloudTrailBucket",
               "Effect": "Deny",
               "Action": [
                  "s3:DeleteObject",
                  "s3:DeleteObjectVersion",
                  "s3:DeleteBucket",
                  "s3:DeleteBucketPolicy",
                  "s3:PutBucketPolicy"
               ],
               "Resource": [
                  "arn:aws:s3:::your-cloudtrail-bucket",
                  "arn:aws:s3:::your-cloudtrail-bucket/*"
               ]
         }
      ]
   }
   ```

3. Click **Save** to apply the policy.

## Step 3: Create a CloudTrail

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
   - If a bucket is in different account Browse button will not work. Just enter the name of the bucket
5. **Enable Log file validation**
6. **Enable Event Logging**

   - Select **Management Events** → Choose **Read/Write events** (`All` for full tracking).
   - (Optional) Enable **Data Events** for:
     - **S3:** To track object-level access.
     - **Lambda:** To track function execution.

7. Click **Next**, review settings, and then click **Create trail**.

# Additional CloudTrails for Data Access Logging

You can create additional CloudTrails within specific AWS accounts when you need to:

1. Monitor data access events separately from management events
2. Track specific S3 bucket or Lambda function activity
3. Meet compliance requirements for certain workloads

Key considerations:
- Each additional trail may incur extra costs
- You can create up to 5 trails per region
- Data events are not enabled by default
- Trails can be configured to log to separate or shared S3 buckets

Best practice: Create additional trails only when you need granular control over data event logging, keeping in mind that organization-wide trails already capture management events.



## Notes

- SCPs cannot be applied to a management account.
- Admins in the management account can delete both
- S3 bucket and cloudtrail even tho they are protected, because Admins can go pass these protections
- SCPs apply to Admins in the non-management accounts
- You can create additional cloud trails in the specific account to log data access where needed

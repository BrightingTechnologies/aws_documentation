# Table of Contents

- [Purpose](#purpose)
- [Scope](#scope)
- [Encryption at rest](#encryption-at-rest)
  - [AWS Key Management Service](#1-aws-key-management-service-kms)
  - [Storage](#2-storage)
  - [Secure configuration management](#3-secure-configuration-management)
- [Encryption in transit](#encryption-in-transit)
- [AWS Well-Architected framework](#aws-well-architected-framework)


---

# Purpose

This document defines best practices and guidelines for setting up data encryption policy for data at rest and in transit.

# Scope

These best practices and guidelines apply to all projects under the management of [Company Name].

# Encryption at rest

Data at rest refers to digital information stored in non-volatile computer storage devices in various forms for any duration. This type of data faces risks from malicious actors attempting to access it. To protect data at rest from unauthorized access, modification or theft organizations need to implement data encryption.


## 1. AWS Key Management Service (KMS)

Used to create and manage encryption keys along with their access control, rotation and  lifecycle policies. There is no mechanism to export AWS KMS keys in plain text. Check if the [integration with AWS KMS](https://aws.amazon.com/kms/features/#AWS_Service_Integration) exists for the service you are working with.

Implementation steps:
1. Unless [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) is explicitly requested use the [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk)
2. Tightly control access to the key through the use of [key policies](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies-best-practices.html) and IAM policies
   1. Use key policies
   2. Limit CreateKey permission
   3. Specify KMS keys in an IAM policy
   4. Avoid "Resource": "*" in an IAM policy
3. If disabled, enable automatic key rotation (in the case of customer managed key its optional)
4. Use the [AWS Encryption SDK](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/introduction.html) with AWS KMS integration when your application needs to encrypt data client-side.
5. Enable [IAM Access Analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html) to automatically review and notify if there are overly broad AWS KMS key policies
6. Enable [Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/kms-controls.html) to receive notifications if there are misconfigured key policies, keys scheduled for deletion, or keys without automated rotation enabled.
7. Determine the logging level appropriate for your AWS KMS keys (since [calls to AWS KMS are logged](https://docs.aws.amazon.com/kms/latest/developerguide/logging-using-cloudtrail.html))
   1. Periodically audit the use of encryption keys to validate that the access control mechanisms on the keys are appropriately implemented 


## 2. Storage

Common use cases include encrypting Amazon EBS volumes, Amazon S3 buckets, and Amazon RDS databases

### 1. S3 bucket encryption
Enable server-side encryption with options like:
- Amazon S3-Managed Keys (SSE-S3)
- AWS KMS-Managed Keys (SSE-KMS)
- Customer-Provided Keys (SSE-C)

### 2. EBS encryption
Enable encryption for Elastic Block Store volumes.

### 3. RDS encryption
Enable encryption for databases.

## 3. Secure configuration management
Securely manage access keys, database credentials, and API tokens.



More info 

# Encryption in transit

## 1. AWS Certificate Manager (ACM)
Manage SSL/TLS certificates.

## 2. Load Balancers
Use HTTPS listeners to terminate SSL connections.

## 3. Amazon CloudFront
Enable HTTPS to secure data transfer.

# AWS Well-Architected framework


- [Protecting data at rest](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-at-rest.html)
- [Protecting data in transit](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-in-transit.html)

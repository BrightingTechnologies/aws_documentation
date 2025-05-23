# Table of Contents

- [Purpose](#purpose)
- [Scope](#scope)
- [Encryption at rest](#encryption-at-rest)
  - [Enforcing encryption at rest](#enforcing-encryption-at-rest)
    - [Service Control Policies (SCPs)](#1-service-control-policies-scps)
    - [IAM identity-based policy](#2-iam-identity-based-policy)
    - [Resource policy](#3-resource-policy)
  - [Configuring encryption for different AWS services](#configuring-encryption-for-different-aws-services)
    - [AWS Key Management Service](#1-aws-key-management-service-kms)
    - [AWS Secrets Manager](#2-aws-secrets-manager)
    - [Storage](#3-storage)
      - [Amazon Simple Storage Service (Amazon S3)](#1-amazon-simple-storage-service-amazon-s3)
      - [Amazon Elastic Block Store (EBS)](#2-amazon-elastic-block-store-ebs)
      - [Amazon Relational Database Service (RDS)](#3-amazon-relational-database-service-rds)
      - [Amazon DynamoDB](#4-amazon-dynamodb)


---

# Purpose

This document defines best practices and guidelines for setting up data encryption policy for data at rest.

# Scope

These best practices and guidelines apply to all projects under the management of [Company Name].

# Encryption at rest

Data at rest refers to digital information stored in non-volatile computer storage devices in various forms for any duration. 
This type of data faces risks from malicious actors attempting to access it. 
To [protect data at rest](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-at-rest.html) from unauthorized 
access, modification or theft organizations we need to enable and enforce encryption at rest.

## Enforcing encryption at rest

### 1. Service Control Policies (SCPs)

In the accounts that use AWS Organizations we can use Service Control Policies to enforce encryption at rest.

The following policy prevents all users from creating unencrypted AWS RDS cluster.
 
  ```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EnforceRDSClusterEncryption",
      "Effect": "Deny",
      "Action": [
        "rds:CreateDBCluster"
      ],
      "Resource": [
        "*"
      ],
      "Condition": {
        "Bool": {
          "rds:StorageEncrypted": "false"
        }
      }
    }
  ]
}
   ```

The following policy restricts all users from disabling the default Amazon EBS Encryption.
  ```json

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "RestrictDisablingOfEbsEncryption",
      "Effect": "Deny",
      "Action": [
        "ec2:DisableEbsEncryptionByDefault"
      ],
      "Resource": "*"
    }
  ]
}
   ```

### 2. IAM identity-based policy

Use IAM policies for individual users or groups to enforce encryption at rest.

The following example illustrates an IAM identity-based policy that authorizes principals to create only encrypted file systems:
  ```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EnforceEFSEncryption",
      "Effect": "Allow",
      "Action": "elasticfilesystem:CreateFileSystem",
      "Condition": {
        "Bool": {
          "elasticfilesystem:Encrypted": "true"
        }
      },
      "Resource": "*"
    }
  ]
}
   ```

### 3. Resource policy

Use resource policies to enforce encryption at rest at resource level (Amazon S3 buckets, Amazon SQS queues, VPC endpoints...).

The following example policy requires every object that is written to the bucket to be encrypted with server-side encryption using AWS Key Management Service (AWS KMS) keys (SSE-KMS):
  ```json
{
  "Version": "2012-10-17",
  "Id": "PutObjPolicy",
  "Statement": [{
    "Sid": "DenyObjectsThatAreNotSSEKMS",
    "Principal": "*",
    "Effect": "Deny",
    "Action": "s3:PutObject",
    "Resource": "arn:aws:s3:::amzn-s3-demo-bucket/*",
    "Condition": {
      "Null": {
        "s3:x-amz-server-side-encryption-aws-kms-key-id": "true"
      }
    }
  }]
}
   ```

The following example policy denies any objects from being written to the bucket if they aren’t encrypted with SSE-KMS by using a specific KMS key ID:
  ```json
{
  "Version": "2012-10-17",
  "Id": "PutObjPolicy",
  "Statement": [{
    "Sid": "DenyObjectsThatAreNotSSEKMSWithSpecificKey",
    "Principal": "*",
    "Effect": "Deny",
    "Action": "s3:PutObject",
    "Resource": "arn:aws:s3:::amzn-s3-demo-bucket/*",
    "Condition": {
      "ArnNotEqualsIfExists": {
        "s3:x-amz-server-side-encryption-aws-kms-key-id": "arn:aws:kms:us-east-2:111122223333:key/01234567-89ab-cdef-0123-456789abcdef"
      }
    }
  }]
}
   ```

List of services that support resource-based policies can be found [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html).

## Configuring encryption for different AWS services

### 1. AWS Key Management Service (KMS)

AWS KMS is used to create and manage encryption keys along with their access control, rotation and  lifecycle policies. AWS KMS keys can [encrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html) and [decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) sensitive data, such as configuration files, application secrets, or sensitive payloads. The encryption is performed using [symmetric](https://docs.aws.amazon.com/kms/latest/developerguide/kms-cryptography.html#symmetric-key-0ps) or [asymmetric](https://docs.aws.amazon.com/kms/latest/developerguide/kms-cryptography.html#asymmetric-key-ops) cryptography.

There are 2 types of AWS KMS keys:
- [AWS managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) - fully managed by AWS, the user does not have direct control over their creation or lifecycle
- [Customer managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) - explicitly created by the user in the AWS KMS console, CLI or SDK, also the user has full control over their creation, configuration, and lifecycle

You can work with the AWS KMS service using the following:
- [AWS Management Console](https://docs.aws.amazon.com/kms/latest/developerguide/accessing-kms.html#kms-console)
- [AWS Command Line Interface](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kms/index.html)
- [AWS KMS REST API](https://docs.aws.amazon.com/kms/latest/APIReference/Welcome.html)
- [AWS SDK](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/introduction.html)


Encryption best practices for AWS Key Management Service can be found [here](https://docs.aws.amazon.com/prescriptive-guidance/latest/encryption-best-practices/kms.html).


### 2. AWS Secrets Manager

Secrets Manager uses [encryption via AWS KMS](https://docs.aws.amazon.com/secretsmanager/latest/userguide/data-protection.html#encryption-at-rest) to protect the confidentiality of data at rest. Every secret in Secrets Manager is encrypted with a unique data key (and each data key is protected by a KMS key).

How to set up the customer managed key and use it for encryption of AWS Secrets Manager secret:
- In the AWS Management Console go to the AWS Key Management Service page
- Click 'Create a key' button
- In the first step you need to configure the key
  - For the key type pick 'Symmetric'
  - For the key usage pick 'Encrypt and decrypt'
  - In the advanced options, for the key material origin pick 'KMS'
  - In the advanced options, for the regionality origin pick 'Single-Region key'
- In the second step you need to add labels
  - For the alias input enter the display name for the key
  - Optional - for the description input enter the description for the key
  - Optional - for the tags input add as many tags as you need by entering the tag keys and the tag values
- Optional - in the third step you can define key administrative permissions
  - For the key administrators select the IAM users and roles authorized to manage this key via the KMS API
  - Depending on your use case, allow or deny key administrators to delete this key
- Optional - in the fourth step you can define key usage permissions
  - For the key users select the IAM users and roles authorized to use this key in cryptographic operations
  - Depending on your use case, for the other AWS accounts add another AWS account that can use this key
- Optional - in the fifth step you can preview and edit the key policy
  - To review the key policy statements for this key select 'Preview' in the toggle
  - To manually update this policy select 'Edit' in the toggle
- In the last step you need to review everything
  - Review all the configured properties and click on 'Finish' button when ready
- In the AWS Management Console go to the AWS Secrets Manager page
- Click 'Store a new secret' button
- For the encryption key choose the one that was created in the previous steps
- Proceed with the remaining steps needed for secret creation

How to set up the AWS managed key and use it for encryption of AWS Secrets Manager secret:
- AWS Managed Keys are automatically created by AWS by an AWS service integrated with AWS KMS
- In the AWS Management Console go to the AWS Secrets Manager page
- Click 'Store a new secret' button
- For the encryption key choose the AWS Managed Key
  - AWS Managed Keys have names that indicate the service they are associated with - choose the 'aws/secretsmanager' key
- Proceed with the remaining steps needed for secret creation

Important:
- Secrets Manager supports only [symmetric encryption KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#symmetric-cmks)

### 3. Storage

Here are key storage-wise use cases for data at rest encryption.

#### 1. Amazon Simple Storage Service (Amazon S3)

All Amazon S3 buckets have encryption configured by default, and all new objects that are uploaded to an S3 bucket are automatically encrypted at rest. Server-side encryption with [Amazon S3 managed keys (SSE-S3)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingServerSideEncryption.html) is the default encryption configuration for every bucket in Amazon S3.

To use a different type of encryption you can:
- Specify the [type of server-side encryption to use in your S3 PUT requests](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html#AmazonS3-PutObject-request-header-ServerSideEncryption)
- Set the [default encryption configuration in the destination bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-encryption.html?utm_source=chatgpt.com)
 
Other types of encryption include:

- [Server-side encryption with AWS KMS (SSE-KMS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html)
- [Dual-layer server-side encryption with AWS KMS keys (DSSE-KMS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingDSSEncryption.html)
- [Server-side encryption with customer-provided keys (SSE-C)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html#specifying-s3-c-encryption)

#### 2. Amazon Elastic Block Store (EBS)

Securing boot or data volumes attached to EC2 instances that store application data or OS files. [Amazon EBS encryption](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html) uses AWS KMS keys when creating encrypted volumes and snapshots. You can either:
- [Enable encryption by default](https://docs.aws.amazon.com/ebs/latest/userguide/encryption-by-default.html), Amazon EBS then automatically encrypts new volumes and snapshots using your default KMS key for EBS encryption
- [Enable encryption when you create an individual volume or snapshot](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html#new-encrypted-volumes), using either the default KMS key for Amazon EBS encryption or a symmetric customer managed encryption key

Important:
- Amazon EBS does not support asymmetric encryption KMS keys
- You cannot directly encrypt existing unencrypted volumes or snapshots

#### 3. Amazon Relational Database Service (RDS)

Amazon RDS can encrypt your Amazon RDS DB instances. Data that is encrypted at rest includes the underlying storage for DB instances, its automated backups, read replicas, backups/snapshots and all logs. Amazon RDS automatically [integrates with AWS KMS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.Keys.html) for key management.

Follow the [implementation steps](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html#Overview.Encryption.Enabling) in order to encrypt your DB instance.

Important:
- Have in mind that there are certain [limitations](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html#Overview.Encryption.Limitations) for Amazon RDS encrypted DB instances

#### 4. Amazon DynamoDB
Server-side [encryption at rest](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/EncryptionAtRest.html) is enabled by default on all DynamoDB table data and cannot be disabled.

DynamoDB uses keys stored in AWS KMS are used for encryption. Follow the [implementation steps](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/encryption.tutorial.html) in order to specify the encryption key on new tables and update the encryption keys on existing tables.

When creating a new table, you can choose one of the following AWS KMS key types to encrypt your table (you can switch between these key types at any time):
- [AWS owned key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-owned-cmk) – Default encryption type. The key is owned by DynamoDB
- [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) – The key is stored in your account and is managed by AWS KMS
- [Customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) – The key is stored in your account and is created, owned, and managed by you

Important:
- Have in mind that there are certain [limitations](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/encryption.usagenotes.html) in configuring encryption at rest for DynamoDB
- When creating a new DAX cluster with encryption at rest enabled, an AWS managed key will be used to encrypt data at rest in the cluster
- If your table has a sort key, some of the sort keys that mark range boundaries are stored in plaintext in the table metadata

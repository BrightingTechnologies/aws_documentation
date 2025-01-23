# Table of Contents

- [Purpose](#purpose)
- [Scope](#scope)
- [Encryption at rest](#encryption-at-rest)
  - [AWS Key Management Service](#1-aws-key-management-service-kms)
  - [AWS Secrets Manager](#2-aws-secrets-manager)
  - [Storage](#3-storage)
    - [Amazon Simple Storage Service (Amazon S3)](#1-amazon-simple-storage-service-amazon-s3)
    - [Amazon Elastic Block Store (EBS)](#2-amazon-elastic-block-store-ebs)
    - [Amazon Relational Database Service (RDS)](#3-amazon-relational-database-service-rds)
    - [Amazon DynamoDB](#4-amazon-dynamodb)
- [Encryption in transit](#encryption-in-transit)
- [AWS Well-Architected framework](#aws-well-architected-framework)


---

# Purpose

This document defines best practices and guidelines for setting up data encryption policy for data at rest and in transit.

# Scope

These best practices and guidelines apply to all projects under the management of [Company Name].

# Encryption at rest

Data at rest refers to digital information stored in non-volatile computer storage devices in various forms for any duration. This type of data faces risks from malicious actors attempting to access it. To [protect data at rest](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-at-rest.html) from unauthorized access, modification or theft organizations need to implement data encryption.

## 1. AWS Key Management Service (KMS)

AWS KMS is used to create and manage encryption keys along with their access control, rotation and  lifecycle policies. AWS KMS keys can [encrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html) and [decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) sensitive data, such as configuration files, application secrets, or sensitive payloads. The encryption is performed using [symmetric](https://docs.aws.amazon.com/kms/latest/developerguide/kms-cryptography.html#symmetric-key-0ps) or [asymmetric](https://docs.aws.amazon.com/kms/latest/developerguide/kms-cryptography.html#asymmetric-key-ops) cryptography.

You can work with the AWS KMS service using the following:
- [AWS Management Console](https://docs.aws.amazon.com/kms/latest/developerguide/accessing-kms.html#kms-console)
- [AWS Command Line Interface](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kms/index.html)
- [AWS KMS REST API](https://docs.aws.amazon.com/kms/latest/APIReference/Welcome.html)
- [AWS SDK](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/introduction.html)

How to use AWS KMS:
- Check if the [integration with AWS KMS](https://aws.amazon.com/kms/features/#AWS_Service_Integration) exists for the service you are working with
- Follow the [implementation steps](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/sec_protect_data_rest_key_mgmt.html#implementation-steps
) in order to create and set up the key
- Set up the [key policy](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html#intro-key-policy) for the key
- Follow the [best practices](https://docs.aws.amazon.com/prescriptive-guidance/latest/encryption-best-practices/kms.html)

## 2. AWS Secrets Manager

Secrets Manager uses [encryption via AWS KMS](https://docs.aws.amazon.com/secretsmanager/latest/userguide/data-protection.html#encryption-at-rest) to protect the confidentiality of data at rest. Every secret in Secrets Manager is encrypted with a unique data key (and each data key is protected by a KMS key).

Follow the [implementation steps](https://docs.aws.amazon.com/secretsmanager/latest/userguide/security-encryption.html#security-encryption-choose-key) in order to set up secret encryption and decryption in AWS Secrets Manager.

Important:
- Secrets Manager supports only [symmetric encryption KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#symmetric-cmks)

## 3. Storage

Here are key storage-wise use cases for data at rest encryption.

### 1. Amazon Simple Storage Service (Amazon S3)

All Amazon S3 buckets have encryption configured by default, and all new objects that are uploaded to an S3 bucket are automatically encrypted at rest. Server-side encryption with [Amazon S3 managed keys (SSE-S3)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingServerSideEncryption.html) is the default encryption configuration for every bucket in Amazon S3.

To use a different type of encryption you can:
- Specify the [type of server-side encryption to use in your S3 PUT requests](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html#AmazonS3-PutObject-request-header-ServerSideEncryption)
- Set the [default encryption configuration in the destination bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-encryption.html?utm_source=chatgpt.com)
 
Other types of encryption include:

- [Server-side encryption with AWS KMS (SSE-KMS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html)
- [Dual-layer server-side encryption with AWS KMS keys (DSSE-KMS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingDSSEncryption.html)
- [Server-side encryption with customer-provided keys (SSE-C)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html#specifying-s3-c-encryption)

### 2. Amazon Elastic Block Store (EBS)

Securing boot or data volumes attached to EC2 instances that store application data or OS files. [Amazon EBS encryption](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html) uses AWS KMS keys when creating encrypted volumes and snapshots. You can either:
- [Enable encryption by default](https://docs.aws.amazon.com/ebs/latest/userguide/encryption-by-default.html), Amazon EBS then automatically encrypts new volumes and snapshots using your default KMS key for EBS encryption
- [Enable encryption when you create an individual volume or snapshot](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html#new-encrypted-volumes), using either the default KMS key for Amazon EBS encryption or a symmetric customer managed encryption key

Important:
- Amazon EBS does not support asymmetric encryption KMS keys
- You cannot directly encrypt existing unencrypted volumes or snapshots

### 3. Amazon Relational Database Service (RDS)

Amazon RDS can encrypt your Amazon RDS DB instances. Data that is encrypted at rest includes the underlying storage for DB instances, its automated backups, read replicas, backups/snapshots and all logs. Amazon RDS automatically [integrates with AWS KMS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.Keys.html) for key management.

Follow the [implementation steps](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html#Overview.Encryption.Enabling) in order to encrypt your DB instance.

Important:
- Have in mind that there are certain [limitations](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html#Overview.Encryption.Limitations) for Amazon RDS encrypted DB instances

### 4. Amazon DynamoDB
[DynamoDB encryption at rest](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/EncryptionAtRest.html) provides enhanced security by encrypting all your data at rest using encryption keys stored in AWS KMS.

Follow the [implementation steps](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/encryption.tutorial.html) in order to specify the encryption key on new tables and update the encryption keys on existing tables.

When creating a new table, you can choose one of the following AWS KMS key types to encrypt your table (you can switch between these key types at any time):
- [AWS owned key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-owned-cmk) – Default encryption type. The key is owned by DynamoDB
- [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) – The key is stored in your account and is managed by AWS KMS
- [Customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) – The key is stored in your account and is created, owned, and managed by you

Important:
- Have in mind that there are certain [limitations](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/encryption.usagenotes.html) in configuring encryption at rest for DynamoDB
- When creating a new DAX cluster with encryption at rest enabled, an AWS managed key will be used to encrypt data at rest in the cluster
- If your table has a sort key, some of the sort keys that mark range boundaries are stored in plaintext in the table metadata

# Encryption in transit

## 1. AWS Certificate Manager (ACM)
Manage SSL/TLS certificates.

## 2. Load Balancers
Use HTTPS listeners to terminate SSL connections.

## 3. Amazon CloudFront
Enable HTTPS to secure data transfer.

## 4. S3

It is possible to encrypt your objects before you send them to Amazon S3 by using the [client-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingClientSideEncryption.html).

# AWS Well-Architected framework


- [Protecting data at rest](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-at-rest.html)
- [Protecting data in transit](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-in-transit.html)

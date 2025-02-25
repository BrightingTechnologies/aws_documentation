# Encryption in transit

- [Purpose](#purpose)
- [Scope](#scope)
- [Encryption in transit](#encryption-in-transit)
    - [Enforcing encryption in transit](#enforcing-encryption-in-transit)

---

# Purpose

This document defines best practices and guidelines for setting up data encryption policy for data in transit.

# Scope

These best practices and guidelines apply to all projects under the management of [Company Name].

# Encryption in transit

Data in transit is any data that is sent from one system to another. This includes communication 
between resources within your workload as well as communication between other services and your end users.
To [protect data in transit](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-in-transit.html)
organizations we need to enforce encryption in transit and provide the appropriate level of protection for communication
between resources.

## Enforcing encryption in transit

### 1. Security group

Configure a security group to only allow the HTTPS protocol to an application load balancer or Amazon EC2 instance.

### 2. Resource policy

Use resource policies to enforce encryption in transit at resource level (e.g. Amazon S3 buckets).

In the following example, the bucket policy explicitly denies HTTP requests:
  ```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "RestrictToTLSRequestsOnly",
    "Action": "s3:*",
    "Effect": "Deny",
    "Resource": [
      "arn:aws:s3:::amzn-s3-demo-bucket",
      "arn:aws:s3:::amzn-s3-demo-bucket/*"
    ],
    "Condition": {
      "Bool": {
        "aws:SecureTransport": "false"
      }
    },
    "Principal": "*"
  }]
}
   ```

List of services that support resource-based policies can be found [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html).

## Configuring encryption for different AWS services

### 1. S3
Configure secure access when uploading to Amazon S3 buckets: Use Amazon S3 bucket policy controls to enforce secure access to data.


--------------

## 1. AWS Certificate Manager (ACM)
Manage SSL/TLS certificates.

## 2. Load Balancers
Use HTTPS listeners to terminate SSL connections.

## 3. Amazon CloudFront
Enable HTTPS to secure data transfer.

## 4. S3

It is possible to encrypt your objects before you send them to Amazon S3 by using the [client-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingClientSideEncryption.html).

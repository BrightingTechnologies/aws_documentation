# Encryption in transit

- [Protecting data in transit](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-in-transit.html)


## 1. AWS Certificate Manager (ACM)
Manage SSL/TLS certificates.

## 2. Load Balancers
Use HTTPS listeners to terminate SSL connections.

## 3. Amazon CloudFront
Enable HTTPS to secure data transfer.

## 4. S3

It is possible to encrypt your objects before you send them to Amazon S3 by using the [client-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingClientSideEncryption.html).

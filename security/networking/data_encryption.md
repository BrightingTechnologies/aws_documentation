# Table of Contents

- [Purpose](#purpose)
- [Scope](#scope)
- [Encryption at rest](#1-encryption-at-rest)
- [Encryption in transit](#2-encryption-in-transit)
- [Encryption key management](#3-encryption-key-management)
- [Compliance and monitoring](#4-compliance-and-monitoring)


---

# Purpose

This document defines best practices and guidelines for setting up data encryption policy for data at rest and in transit.

# Scope

These best practices and guidelines apply to all projects under the management of [Company Name].

# Encryption at rest

## 1. AWS Key Management Service (KMS)
Use to create and manage encryption keys and control access and manage lifecycle policies for encryption keys.

## 2. AWS Secrets Manager
Securely manage access keys, database credentials, and API tokens.

## 3. Storage

### 1. S3 bucket encryption
Enable server-side encryption with options like:
- Amazon S3-Managed Keys (SSE-S3)
- AWS KMS-Managed Keys (SSE-KMS)
- Customer-Provided Keys (SSE-C)

### 2. EBS encryption
Enable encryption for Elastic Block Store volumes.

### 3. RDS encryption
Enable encryption for databases.

# Encryption in transit

## 1. AWS Certificate Manager (ACM)
Manage SSL/TLS certificates.

## 2. Load Balancers
Use HTTPS listeners to terminate SSL connections.

## 3. Amazon CloudFront
Enable HTTPS to secure data transfer.

# Encryption key management

# Compliance and monitoring

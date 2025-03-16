# Disaster Recovery

## Table of Contents
1. [Purpose](#purpose)
2. [Definition of a Disaster](#definition-of-a-disaster)
3. [Disaster Recovery Strategies in AWS](#disaster-recovery-strategies-in-aws)
   1. [Backup and Restore](#1-backup-and-restore)
   2. [Pilot Light](#2-pilot-light)
   3. [Warm Standby](#3-warm-standby)
   4. [Multi-Site Active/Active](#4-multi-site-activeactive)
4. [Comparison Table](#comparison-table)
5. [Lambda Disaster Recovery Considerations](#lambda-disaster-recovery-considerations)
   1. [Regional Availability](#regional-availability)
      1. [AZ Failures](#az-failures)
      2. [Regional Outages](#regional-outages)
      3. [Best Practices for Multi-Region Resilience](#best-practices-for-multi-region-resilience)
6. [Examples of Lambda Vulnerabilities in Disaster Recovery Scenarios](#examples-of-lambda-vulnerabilities-in-disaster-recovery-scenarios)
   1. [Lambda in a VPC with a Single Subnet (Single AZ)](#1-lambda-in-a-vpc-with-a-single-subnet-single-az)
   2. [Event Sources in Single AZ (MSK, DynamoDB Streams)](#2-event-sources-in-single-az-msk-dynamodb-streams)
   3. [Cross-Region IAM Role Dependencies](#3-cross-region-iam-role-dependencies)
   4. [API Gateway in a Single Region](#4-api-gateway-in-a-single-region)
   5. [Single-Region S3 Bucket Dependencies](#5-single-region-s3-bucket-dependencies)

## Purpose

This document explains various disaster recovery (DR) considerations for serverless architectures. For general cloud DR strategies, refer to: [Disaster recovery options in the cloud](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html).

## Definition of a Disaster

A **disaster** refers to any **unexpected event** that disrupts IT system operations, causing **data loss, service downtime, or degraded performance** beyond acceptable thresholds. Disasters can be categorized into:

1. **Natural Disasters** – Earthquakes, floods, hurricanes, etc.
2. **Infrastructure Failures** – Hardware malfunctions, network outages, power failures.
3. **Cybersecurity Incidents** – Data breaches, DDoS attacks, ransomware.
4. **Human Errors** – Accidental data deletion, misconfigurations.
5. **Application Failures** – Code bugs, API failures, system crashes.
6. **Regulatory Violations** – System shutdowns due to legal or compliance mandates.

A disaster is declared when the incident exceeds the organization's ability to restore services within normal operational procedures, requiring activation of a **disaster recovery plan (DRP)**.

---

## Disaster Recovery Strategies in AWS

## 1. **Backup and Restore**
### Description
- This is the simplest disaster recovery approach where data and application backups are regularly taken and stored in a secure location (e.g., Amazon S3, Glacier, or offsite storage or multiple regions).
- In the event of an outage, these backups are used to restore systems in a new or existing environment.

### RTO & RPO
- **RTO:** **Hours to days** (since full system restoration takes time).
- **RPO:** **Hours** (depends on backup frequency; the most recent backup is used).

### Use Case
- Cost-effective and ideal for non-critical workloads where occasional downtime is acceptable.

---

## 2. **Pilot Light**
### Description
- A minimal version of the system is always running in another region with essential services (like a small database replica and necessary configurations).
- In case of an outage, additional resources (e.g., compute instances, application servers) are quickly scaled up to restore full functionality.

### RTO & RPO
- **RTO:** **Minutes to hours** (depends on how quickly additional resources can be launched).
- **RPO:** **Minutes** (as data replication is happening frequently).

### Use Case
- Suitable for applications requiring faster recovery than backup & restore but without the full cost of warm standby.

---

## 3. **Warm Standby**
### Description
- A scaled-down version of the full system is continuously running in another region.
- During an outage, the system is quickly scaled up to handle production traffic.

### RTO & RPO
- **RTO:** **Minutes** (since the system is already running and just needs scaling).
- **RPO:** **Near real-time** (continuous or frequent data replication ensures minimal data loss).

### Use Case
- Ideal for business-critical applications that must recover quickly but do not require full capacity all the time.

---

## 4. **Multi-Site Active/Active**
### Description
- Full production systems are running in **multiple regions** simultaneously.
- Traffic is distributed across these regions using **Route 53 latency-based routing** or other load-balancing techniques.
- If one region goes down, the system continues operating without interruption.

### RTO & RPO
- **RTO:** **Near zero** (failover happens automatically).
- **RPO:** **Zero** (since all regions have real-time data synchronization).

### Use Case
- Best for mission-critical applications requiring **high availability** and no downtime (e.g., banking systems, global SaaS platforms).
- Most expensive approach due to **double** infrastructure costs.

---

## **Comparison Table**

| Strategy                  | RTO          | RPO          | Cost       | Complexity | Use Case  |
|---------------------------|-------------|-------------|-----------|------------|--------------|
| **Backup & Restore**       | Hours - Days | Hours        | Low        | Low        | Non-critical workloads            |
| **Pilot Light**           | Minutes - Hours | Minutes    | Medium     | Medium     | Faster recovery than backup & restore |
| **Warm Standby**          | Minutes      | Near real-time | High      | High       | Business-critical workloads       |
| **Multi-Site Active/Active** | Near zero | Zero | Very high | Very high | Mission-critical workloads        |

---

## Lambda Disaster Recovery Considerations

### Regional Availability

AWS Lambda is a **regional** service, meaning functions run in specific **AWS regions** and do not automatically replicate across multiple regions. AWS operates across **multiple Availability Zones (AZs)** within each region to ensure resilience. Lambda functions are designed to tolerate **AZ failures** but are vulnerable to **regional outages**.

#### **AZ Failures**  
Lambda functions operate across **multiple AZs** by default. If an AZ becomes unavailable, Lambda traffic is automatically routed to healthy AZs within the region, ensuring continued operation.

- **VPC Subnet Considerations**: If Lambda is deployed within a VPC using subnets restricted to a single AZ, a failure in that AZ will prevent Lambda from running.
    - **Best Practice**: Deploy Lambda in **multiple subnets across different AZs** for resilience against AZ failures.

#### **Regional Outages**  
Lambda functions are tied to a specific region. If an entire **region becomes unavailable**, Lambda functions in that region will be **unavailable**. AWS does not automatically failover to another region.

- **Key Considerations**: In a regional failure, Lambda functions in that region will stop executing until the region is restored.
- **Multi-Region Resilience**: To maintain availability during regional failures, deploy Lambda functions in multiple regions and implement a **cross-region failover strategy**.

#### **Best Practices for Multi-Region Resilience**:
- Use **Amazon Route 53** or **AWS Global Accelerator** for **DNS failover**.
- Deploy duplicate Lambda functions in secondary regions and trigger them via **cross-region events**.
- Use **multi-region event sources** (e.g., **SNS, SQS, Kinesis**) to trigger Lambda functions across regions.

---

## Examples of Lambda Vulnerabilities in Disaster Recovery Scenarios

While Lambda is fault-tolerant within a region, certain configurations can create vulnerabilities during AZ or regional outages. Here are some examples:

### 1. **Lambda in a VPC with a Single Subnet (Single AZ)**
- **Problem**: A Lambda function assigned to a VPC with a single subnet in one AZ becomes unavailable if that AZ fails.
- **Solution**: Deploy Lambda in **multiple subnets across different AZs**.

### 2. **Event Sources in Single AZ (MSK, DynamoDB Streams)**
- **Problem**: If the event source (e.g., **MSK**, **DynamoDB Streams**) is in a single AZ, a failure in that AZ will cause Lambda to fail due to unavailable events.
- **Solution**: Use **multi-AZ replication** for event sources.

### 3. **Cross-Region IAM Role Dependencies**
- **Problem**: If Lambda relies on IAM roles in another region, a region failure may prevent Lambda from accessing required permissions.
- **Solution**: Use IAM roles in the **same region** or replicate IAM roles **across regions**.

### 4. **API Gateway in a Single Region**
- **Problem**: Lambda invoked via **API Gateway** in a single region will fail if that region is down.
- **Solution**: Deploy **API Gateway in multiple regions** and use **Global Accelerator** or **Route 53** for traffic routing to the healthy region.

### 5. **Single-Region S3 Bucket Dependencies**
- **Problem**: If Lambda depends on an S3 bucket in a single region and that region experiences an outage, Lambda will fail to access the bucket.
- **Solution**: Use **cross-region S3 replication** or store critical configurations in services like **AWS Systems Manager Parameter Store**.
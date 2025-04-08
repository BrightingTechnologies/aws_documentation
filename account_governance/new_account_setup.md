# AWS Account Creation Guide

## Purpose

Purpose of this document is to define steps needed to create a new customer AWS account.

## Prerequisites

- A pre-approved dedicated business email address
- Pre-approved AWS customer support plan option (decided by customer)
- Access to customer payment method
- Pre-approved list of people for root access with their MFA devices
- Pre-approved list of people with administrator access

## Stakeholder Responsibilities

### RACI Matrix

R - Responsible (Does the work)
A - Accountable (Final approving authority)
C - Consulted (Subject matter experts)
I - Informed (Kept up-to-date)

### Key Stakeholders

- **Customer IT Lead**: Oversees the technical requirements and business alignment
- **AWS Admin**: Handles technical implementation and configuration
- **Security Team**: Ensures security compliance and best practices
- **Finance**: Manages billing and cost-related aspects
- **Legal**: Provides compliance and regulatory oversight

### Brighting's Responsibilities

As a managed service provider, Brighting typically covers:

1. **AWS Admin Role** ✓

   - Handles technical implementation
   - Creates and configures the AWS account
   - Sets up IAM Identity Center
   - Configures CloudTrail
   - Implements AWS Organizations
   - Documents access and procedures

2. **Security Team Role** ✓
   - Ensures security compliance
   - Oversees MFA setup
   - Manages root credentials storage
   - Reviews security configurations
   - Validates compliance requirements

### Customer's Responsibilities

The customer typically covers:

1. **Customer IT Lead**

   - Provides business email approval
   - Makes support plan decisions
   - Serves as primary point of contact
   - Provides emergency contacts

2. **Finance**

   - Handles payment method setup
   - Manages cost center assignment
   - Oversees billing and support plan costs

3. **Legal**
   - Reviews compliance requirements
   - Validates legal agreements
   - Provides regulatory oversight

## Implementation Process

### 1. Account Creation

1. Open AWS website
2. Choose "Create an AWS account"
3. Enter root email address. Use pre-approved business e-mail, dedicated email address. Do not use personal business e-mail
4. Enter account name, and click "Next"
5. Enter verification code
6. Enter a strong password and immediately store credentials in a safe place
7. Choose "Business" for account type
8. Accept Customer Agreement and continue
9. Enter the information on the payment method and continue
10. Enter country and region data and continue
11. Select AWS Support plan
12. Complete signup

### 2. Security Setup

#### Root Account MFA Activation

1. Login to console as ROOT user
2. In the upper right corner of the console, click on the drop down and select "Security Credentials"
3. In the Multi-factor authentication section, assign devices for all required users. Up to 8 MFA devices can be assigned to the root user

### 3. Core Services Setup

For the core services setup refer to the following documents

- [CloudTrail setup and logging enablement](./cloud_trail.md)
- [AWS Organizations implementation](./organization_structure.md)

## Documentation and Compliance

### Account Documentation

- Root account credentials storage
- IAM user access documentation
- Emergency contacts list
- Support plan details

### Security Documentation

- MFA device assignments
- Security configurations
- Access policies

### Compliance Documentation

- Regulatory requirements
- Legal agreements
- Security standards compliance

## Appendices

### A. RACI Matrix Details

| Task/Deliverable             | Customer IT Lead | AWS Admin | Security Team | Finance | Legal |
| ---------------------------- | ---------------- | --------- | ------------- | ------- | ----- |
| Business Email Approval      | A/R              | I         | C             | I       | I     |
| Support Plan Selection       | A                | C         | C             | R       | I     |
| Payment Method Setup         | I                | I         | I             | A/R     | C     |
| Root Account Creation        | C                | A/R       | C             | I       | I     |
| MFA Device Assignment        | C                | R         | A             | I       | I     |
| Root Credentials Storage     | I                | R         | A             | I       | I     |
| IAM Identity Center Setup    | C                | R         | A             | I       | I     |
| CloudTrail Configuration     | I                | R         | A             | I       | I     |
| AWS Organizations Setup      | C                | R         | A             | I       | I     |
| Account Access Documentation | C                | R         | A             | I       | C     |
| Compliance Requirements      | C                | R         | A             | I       | C     |
| Cost Center Assignment       | C                | I         | I             | A/R     | I     |
| Emergency Contact Setup      | A/R              | C         | C             | I       | I     |

### B. Important Notes

- Only one person/team can be Accountable (A) for each task
- Multiple people/teams can be Responsible (R)
- The Accountable person must ensure all Responsible parties complete their tasks
- Consulted stakeholders must provide timely feedback when requested
- Informed stakeholders should be kept updated but don't need to provide input

### C. Matrix Usage Guidelines

1. Reference this matrix at the start of any new AWS account creation
2. Ensure all stakeholders understand their roles
3. Use this as a checklist to track progress
4. Document any deviations from standard responsibilities
5. Update the matrix as processes evolve

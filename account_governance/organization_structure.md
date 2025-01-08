# Table of Contents

- [Purpose](#purpose)
- [Scope](#scope)
- [AWS Organization Structure](#aws-organization-structure)
  - [Organizational Units (OUs)](#1-organizational-units-ous)
  - [Account Creation](#2-account-creation)
  - [Email Usage](#3-email-usage)
- [Security Best Practices](#security-best-practices)
  - [Multi-Factor Authentication (MFA)](#1-multi-factor-authentication-mfa)
  - [Identity and Access Management (IAM)](#2-identity-and-access-management-iam)
  - [AWS Single Sign-On (SSO)](#3-aws-single-sign-on-sso)
  - [Root Account Usage](#4-root-account-usage)
- [Billing and Cost Management](#billing-and-cost-management)
  - [Centralized Billing](#5-centralized-billing)
  - [Budgeting](#6-budgeting)
- [Monitoring and Logging](#monitoring-and-logging)
  - [CloudWatch and CloudTrail](#1-cloudwatch-and-cloudtrail)
  - [Centralized Logging](#2-centralized-logging)
- [Compliance and Auditing](#compliance-and-auditing)
  - [Guardrails and SCPs](#1-guardrails-and-scps)
  - [Regular Audits](#2-regular-audits)
- [Incident Response](#incident-response)
  - [Incident Handling](#1-incident-handling)
  - [Reporting](#2-reporting)
- [Review and Updates](#review-and-updates)
  - [Document Review](#1-document-review)
  - [Updates and Approvals](#2-updates-and-approvals)

---

# Purpose

This document defines best practices for utilizing AWS Organizations within [Company Name]. It includes guidelines for setting up accounts, managing security, and enforcing compliance to ensure efficient, secure, and organized use of AWS resources across multiple accounts.

# Scope

These best practices apply to all AWS accounts under the management of [Company Name], including production, development, and testing environments.

# AWS Organization Structure

## 1. Organizational Units (OUs)

- **Structure:** Use Organizational Units (OUs) to group accounts based on their function (e.g., production, development, testing) or business unit. Use separate accounts for management, security, and logging.
- **Recommended Setup:** For the recommended setup, refer to [AWS documentation](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/recommended-ous-and-accounts.html). At minimum, we should use:
  - **Security OU**
    - Log Archive account
    - Security account
  - **Infrastructure OU**
    - Shared Infrastructure Account
  - **Workloads OU**
    - Service 1 prod, Service 1 pre-prod
    - Service 2 prod, Service 2 pre-prod
- **Patterns for Organizing Accounts:** Refer to [AWS documentation](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/patterns-for-organizing-your-aws-accounts.html) for patterns for organizing AWS accounts.

## 2. Account Creation

- **Approval Process:** All new AWS accounts must be approved by the Cloud Governance Team. To request approval, submit a request via the internal ticketing system, including the purpose and expected usage of the new account.
- **Account Setup:**
  - Use AWS Control Tower to automate the setup of new accounts.
  - Use Account Factory to set up new accounts.
  - Apply baseline security policies and guardrails upon account creation.

### Account Creation Checklist

- [ ] Submit an account request to the Cloud Governance Team.
- [ ] Use AWS Control Tower to set up the new account.
- [ ] Apply baseline security policies and guardrails.

## 3. Email Usage

- **Primary Email Addresses:** Use dedicated, monitored email addresses for each AWS account. Example format: `aws-[account-name]@[companydomain].com`.
- **Shared Mailboxes:** Utilize shared mailboxes managed by IT for root account emails to ensure accessibility and monitoring.

# Security Best Practices

## 1. Multi-Factor Authentication (MFA)

- **MFA Requirement:** Enable MFA on all root accounts and IAM users.
- **MFA Devices:** Use hardware MFA devices or virtual MFA apps approved by the IT security team.

## 2. Identity and Access Management (IAM)

- **IAM Identiy Center:** Use Identity Center for managing user authentication, authorization and SSO across multiple AWS accounts
  - Define user groups
  - Define permission sets
  - Assign user groups and permission sets to AWS accounts
  - Add users to user groups
- **IAM Roles:** Use IAM roles instead of long-term IAM user credentials for cross-account access.
- **Least Privilege Principle:** Grant only the necessary permissions for each role or user.

## 3. AWS Single Sign-On (SSO)

- **SSO Integration:** Use AWS SSO to manage user access to AWS accounts within the organization.
- **Directory Integration:** Integrate AWS SSO with the company’s directory service (e.g., Active Directory).

## 4. Root Account Usage

Root accounts are only to be used in the cases described in the following documents:

- [Root User Tasks](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html#root-user-tasks)
- [Root Login in Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/root-login.html)

# Billing and Cost Management

## 5. Centralized Billing

- **Master Account:** Use a master account to consolidate billing across all AWS accounts.
- **Cost Allocation Tags:** Implement a standardized tagging policy to track and allocate costs.

## 6. Budgeting

- **Budgets:** Set up AWS Budgets for each account to monitor and control spending.
- **Alerts:** Configure alerts to notify relevant stakeholders when budgets are exceeded.

# Monitoring and Logging

## 1. CloudWatch and CloudTrail

- **CloudWatch:** Use AWS CloudWatch for real-time monitoring of resource usage and performance.
- **CloudTrail:** Enable AWS CloudTrail in all accounts to log API activity and detect anomalies.

## 2. Centralized Logging

- **Log Aggregation:** Use AWS services like S3 and Amazon Athena to centralize and analyze logs from all accounts.

# Compliance and Auditing

## 1. Guardrails and SCPs

- **Service Control Policies (SCPs):** Apply SCPs at the OU level to enforce compliance and restrict actions.
- **Guardrails:** Implement AWS Control Tower guardrails to ensure adherence to best practices.

## 2. Regular Audits

- **Audit Schedule:** Conduct regular audits of AWS accounts and OUs to ensure compliance with security and operational standards.
- **Third-Party Tools:** Consider using third-party tools for automated compliance checks and reporting.

# Incident Response

## 1. Incident Handling

- **Procedure:** Establish a clear incident response procedure for AWS accounts.
  - **Incident Response Flowchart:**
    ```
    Incident Detected → Assess Severity → Notify Response Team → Contain Incident → Investigate → Remediate → Document → Review
    ```
- **Contacts:** Maintain an up-to-date contact list for the incident response team.

## 2. Reporting

- **Documentation:** Document all incidents and responses for future reference and improvement.
- **Post-Incident Reviews:** Conduct post-incident reviews to identify and remediate gaps in the security posture.

# Review and Updates

## 1. Document Review

- **Review Schedule:** Review this document quarterly to ensure it remains up-to-date with the latest AWS features and best practices.
- **Responsibility:** The Cloud Governance Team is responsible for reviewing and updating this document.

## 2. Updates and Approvals

- **Change Management:** Use the company’s change management process for updating this document.
- **Approval:** Changes must be approved by the Cloud Governance Team and the IT Security Committee.

---

By following these best practices, [Company Name] will maintain a secure, well-organized, and cost-effective AWS environment. These guidelines help ensure that all AWS accounts are managed consistently and in alignment with organizational policies and security requirements.

# AWS Account Access

## Contents

1. [Logging in to AWS Console](#1-logging-in-to-aws-console)
   - [Access AWS Identity Center](#11-access-aws-identity-center)
     - [Navigate to the AWS Identity Center Portal](#111-navigate-to-the-aws-identity-center-portal)
     - [Select Account and Role](#112-select-account-and-role)
     - [Launch the Console](#113-launch-the-console)
2. [Obtain Temporary Credentials for AWS CLI](#2-obtain-temporary-credentials-for-aws-cli)
   - [Configure AWS CLI for AWS Identity Center](#21-configure-aws-cli-for-aws-identity-center)
   - [Use Temporary Credentials from AWS Identity Center for Programmatic Access](#22-use-temporary-credentials-from-aws-identity-center-for-programmatic-access)
     - [Obtain Temporary Credentials from AWS Identity Center](#221-obtain-temporary-credentials-from-aws-identity-center)
     - [Copy the Temporary Credentials](#222-copy-the-temporary-credentials)
     - [Configure AWS CLI with Temporary Credentials](#223-configure-aws-cli-with-temporary-credentials)
3. [Assume IAM Role for Elevated or Cross-Account Access](#3-assume-iam-role-for-elevated-or-cross-account-access)
   - [Why Assume a Role](#31-why-assume-a-role)
   - [How to Assume a Role via CLI](#32-how-to-assume-a-role-via-cli)
   - [Common Use Cases](#33-common-use-cases)
4. [Root Access](#4-root-access)
   - [When to use Root Access](#31-when-to-use-the-aws-root-account)

## Logging in to AWS Console

Logging in to AWS Console should be done exclusively with:

- **AWS Identity Center**
- **Temporary Credentials**

Never use/do the following for AWS access:

- **Root credentials**
- **Share access**
- **Unsafe public networks**
- **Avoid using browser autosave for credentials**

### Access AWS Identity Center (formerly AWS Single Sign-On)

#### 1. Navigate to the AWS Identity Center Portal

- Open the AWS Identity Center portal provided by your organization.
- Enter your corporate credentials (username and password).
- Complete Multi-Factor Authentication (MFA) if enabled.

#### 2. Select Account and Role

- Once authenticated, you will see a list of AWS accounts and roles you have access to.
- Select the appropriate account and role for the tasks you need to perform.

#### 3. Launch the Console

- Click on the "Management Console" link to launch the AWS Management Console in a new browser tab.
- You are now logged into the selected AWS account with the chosen role.

---

## Obtain Temporary Credentials for AWS CLI

### 1. Configure AWS CLI for AWS Identity Center

- Run the following command to start the configuration process:
  ```bash
  aws configure sso
  ```
- Follow the prompts to complete SSO setup.
- Use the following command to log in:
  ```bash
  aws sso login --profile your-profile-name
  ```

### 2. Use Temporary Credentials from AWS Identity Center for Programmatic Access

#### 1. Obtain Temporary Credentials from AWS Identity Center

- Sign in to the AWS Identity Center portal provided by your organization.
- Select the account and role you want to use.
- Click on **Programmatic access** to display the temporary credentials.

#### 2. Copy the Temporary Credentials

- Copy the **Access Key ID**, **Secret Access Key**, and **Session Token** provided under **Programmatic access**.

#### 3. Configure AWS CLI with Temporary Credentials

- Open your terminal and run:
  ```bash
  aws configure --profile temp-access
  ```
- Enter the temporary credentials when prompted.
- Test access:
  ```bash
  aws s3 ls --profile temp-access
  ```

---

# AWS Account Access

## Contents

1. [Logging in to AWS Console](#1-logging-in-to-aws-console)
   - [Access AWS Identity Center](#11-access-aws-identity-center)
     - [Navigate to the AWS Identity Center Portal](#111-navigate-to-the-aws-identity-center-portal)
     - [Select Account and Role](#112-select-account-and-role)
     - [Launch the Console](#113-launch-the-console)
2. [Obtain Temporary Credentials for AWS CLI](#2-obtain-temporary-credentials-for-aws-cli)
   - [Configure AWS CLI for AWS Identity Center](#21-configure-aws-cli-for-aws-identity-center)
   - [Use Temporary Credentials from AWS Identity Center for Programmatic Access](#22-use-temporary-credentials-from-aws-identity-center-for-programmatic-access)
     - [Obtain Temporary Credentials from AWS Identity Center](#221-obtain-temporary-credentials-from-aws-identity-center)
     - [Copy the Temporary Credentials](#222-copy-the-temporary-credentials)
     - [Configure AWS CLI with Temporary Credentials](#223-configure-aws-cli-with-temporary-credentials)
3. [Assume IAM Role for Elevated or Cross-Account Access](#3-assume-iam-role-for-elevated-or-cross-account-access)
   - [Why Assume a Role](#31-why-assume-a-role)
   - [How to Assume a Role via CLI](#32-how-to-assume-a-role-via-cli)
   - [Common Use Cases](#33-common-use-cases)

---

## Logging in to AWS Console

Logging in to AWS Console should be done exclusively with:

- **AWS Identity Center**
- **Temporary Credentials**

Never use/do the following for AWS access:

- **Root credentials**
- **Share access**
- **Unsafe public networks**
- **Avoid using browser autosave for credentials**

### Access AWS Identity Center (formerly AWS Single Sign-On)

#### 1. Navigate to the AWS Identity Center Portal

- Open the AWS Identity Center portal provided by your organization.
- Enter your corporate credentials (username and password).
- Complete Multi-Factor Authentication (MFA) if enabled.

#### 2. Select Account and Role

- Once authenticated, you will see a list of AWS accounts and roles you have access to.
- Select the appropriate account and role for the tasks you need to perform.

#### 3. Launch the Console

- Click on the "Management Console" link to launch the AWS Management Console in a new browser tab.
- You are now logged into the selected AWS account with the chosen role.

---

## Obtain Temporary Credentials for AWS CLI

### 1. Configure AWS CLI for AWS Identity Center

- Run the following command to start the configuration process:
  ```bash
  aws configure sso
  ```
- Follow the prompts to complete SSO setup.
- Use the following command to log in:
  ```bash
  aws sso login --profile your-profile-name
  ```

### 2. Use Temporary Credentials from AWS Identity Center for Programmatic Access

#### 1. Obtain Temporary Credentials from AWS Identity Center

- Sign in to the AWS Identity Center portal provided by your organization.
- Select the account and role you want to use.
- Click on **Programmatic access** to display the temporary credentials.

#### 2. Copy the Temporary Credentials

- Copy the **Access Key ID**, **Secret Access Key**, and **Session Token** provided under **Programmatic access**.

#### 3. Configure AWS CLI with Temporary Credentials

- Open your terminal and run:
  ```bash
  aws configure --profile temp-access
  ```
- Enter the temporary credentials when prompted.
- Test access:
  ```bash
  aws s3 ls --profile temp-access
  ```

---

## Assume IAM Role for Elevated or Cross-Account Access

### 1. Why Assume a Role

Assuming a role is used when:

- You need **elevated privileges** temporarily (e.g., Admin access).
- You need to **access another AWS account** (e.g., staging → prod).
- You want to **simulate a role** (e.g., EC2 role) without accessing the machine.
- Your identity is **federated (SSO)** and needs temporary AWS credentials.

This follows best practices of **least privilege**, **temporary access**, and **auditability**.

### 2. How to Assume a Role via CLI

1. **Ensure the role trust policy allows you to assume it**:

   - The target IAM role must have a trust relationship that permits your IAM identity (user or role) to assume it.
   - Example trust policy allowing a user from another account:
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": {
             "AWS": "arn:aws:iam::111122223333:user/YourUserName"
           },
           "Action": "sts:AssumeRole"
         }
       ]
     }
     ```
   - Example trust policy allowing a role from another account:
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": {
             "AWS": "arn:aws:iam::111122223333:role/YourSourceRole"
           },
           "Action": "sts:AssumeRole"
         }
       ]
     }
     ```
   - **To allow AWS Identity Center users**, include the role ARN from the session:
     ```json
     {
       "Effect": "Allow",
       "Principal": {
         "AWS": "arn:aws:iam::111122223333:role/aws-reserved/sso.amazonaws.com/YourSSORoleName"
       },
       "Action": "sts:AssumeRole"
     }
     ```
   - Always make sure the `Principal` ARN matches the identity that's calling `sts:AssumeRole`.

2. Your current identity must have `sts:AssumeRole` permissions.
3. Use the following command:

```bash
aws sts assume-role \
  --role-arn arn:aws:iam::123456789012:role/AdminRole \
  --role-session-name my-admin-session
```

4. Extract and export credentials:

```bash
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
```

5. Now you can run AWS CLI commands as the assumed role.

### 3. Common Use Cases

| Use Case                            | Description                                                                   | Benefit                                         |
| ----------------------------------- | ----------------------------------------------------------------------------- | ----------------------------------------------- |
| Cross-account Lambda deployment     | A CI/CD pipeline in Account A assumes a role in Account B to deploy resources | Cleanly separate environments and permissions   |
| Simulating EC2 Role                 | Assume the same role EC2 uses to test if it can access S3, etc.               | Test permissions without using the EC2 instance |
| Federated user access               | Your corporate login (e.g., Google or Okta) maps to an AWS role               | Secure SSO integration with AWS                 |
| Central security/audit role         | Security account assumes roles in other accounts to read CloudTrail logs      | Centralized, auditable access to sensitive logs |
| Cross-account CDK/Terraform deploys | One repo deploys to multiple AWS accounts via assumed roles                   | Centralized IaC with scoped access              |
| Read-only dashboards                | A web app assumes a role to pull S3 data for business users                   | Secure, temporary access through apps           |
| Break-glass admin role              | Temporarily assume admin privileges when something goes wrong                 | Secure emergency access with logs               |

---

Let us know if you need examples in Terraform, CDK, or IAM policy templates to configure trust relationships or permissions.

# Root Access

Root Access should be used only for essential administrative tasks that cannot be performed with IAM user or Identity Center

## 3.1 When to Use the AWS Root Account

- Setting Up AWS Organizations & Control Tower – Creating organizational units (OUs) and enabling AWS Control Tower.

- Billing & Account Management – Changing payment settings, closing the account, or viewing certain account details.

- Enabling/Disabling AWS Services – Some services like AWS IAM Access Analyzer and AWS Shield Advanced require root access for activation.

- Rotating Root Account Credentials – Updating or disabling the root password, access keys, and MFA settings.

- Requesting AWS Support for Critical Issues – Some AWS Support cases require root verification.

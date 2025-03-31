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
3. [Root Access](#3-configure-aws-cli-for-aws-identity-center)
    - [When to use Root Access](#31-when-to-use-the-aws-root-account)

## Logging in to AWS Console

Logging in to AWS Console should be done exclusively with:

- **AWS Identity Center**
- **Temporary Credentials**

Never use/do the following for AWS access

- **Root credentials**
- **Share access**
- **Unsafe public networks**
- **Avoid using browser autosave for credentials**

Steps to Follow:

# Access AWS Identity Center (formerly AWS Single Sign-On)

## 1. Navigate to the AWS Identity Center Portal

- Open the AWS Identity Center portal provided by your organization.
- Enter your corporate credentials (username and password).
- Complete Multi-Factor Authentication (MFA) if enabled.

## 2. Select Account and Role

- Once authenticated, you will see a list of AWS accounts and roles you have access to.
- Select the appropriate account and role for the tasks you need to perform.

## 3. Launch the Console

- Click on the "Management Console" link to launch the AWS Management Console in a new browser tab.
- You are now logged into the selected AWS account with the chosen role.

# Obtain Temporary Credentials for AWS CLI

## 1. Configure AWS CLI for AWS Identity Center

- Run the following command to start the configuration process:
  ```bash
  aws configure sso
  ```
- Follow the prompts to complete sso setup
- Use the following command to login
  ```bash
  aws sso login --profile your-profile-name
  ```

# Use Temporary Credentials from AWS Identity Center for Programmatic Access

## 1. Obtain Temporary Credentials from AWS Identity Center

- Sign in to the AWS Identity Center portal provided by your organization.
- Select the account and role you want to use.
- Click on **Programmatic access** to display the temporary credentials.

## 2. Copy the Temporary Credentials

- Copy the **Access Key ID**, **Secret Access Key**, and **Session Token** provided under **Programmatic access**.

## 3. Configure AWS CLI with Temporary Credentials

- Open your terminal and run the following command:
  ```bash
  aws s3 ls
  ```

# Root Access

Root Access should be used only for essential administrative tasks that cannot be performed with IAM user or Identity Center

## 3.1 When to Use the AWS Root Account
- Setting Up AWS Organizations & Control Tower – Creating organizational units (OUs) and enabling AWS Control Tower.

- Billing & Account Management – Changing payment settings, closing the account, or viewing certain account details.

- Enabling/Disabling AWS Services – Some services like AWS IAM Access Analyzer and AWS Shield Advanced require root access for activation.

- Rotating Root Account Credentials – Updating or disabling the root password, access keys, and MFA settings.

- Requesting AWS Support for Critical Issues – Some AWS Support cases require root verification.

# Purpose

Purpose of this document is to define steps needed to create a new customer AWS account.

## Contents

1. [Prerequisites](#prerequisites)
2. [Creating a Root Account](#creating-a-root-account)
3. [Activate MFA on a Root Account](#activate-mfa-on-a-root-account)

## Prerequisites

- A pre-approved dedicated business email address.
- Pre-approved AWS customer support plan option (decided by customer).
- Access to customer payment method.
- Pre-approved list of people for root access with their MFA devices.
- Pre-approved list of people with administrator access.

## Creating a Root Account

1. Open [AWS](https://aws.amazon.com/).
2. Choose "Create an AWS account".
3. Enter root email address. Use pre-approved business e-mail, dedicated email address. Do not use personal business e-mail.
4. Enter account name, and click "Next".
5. Enter verification code.
6. Enter a strong password and immediately store credentials in a safe place.
7. Choose "Business" for account type.
8. Accept Customer Agreement and continue.
9. Enter the information on the payment method and continue.
10. Enter country and region data and continue.
11. Select AWS Support plan.
12. Complete signup.

## Activate MFA on a Root Account

1. Login to console as ROOT user.
2. In the upper right corner of the console, click on the drop down and select "Security Credentials".
3. In the Multi-factor authentication section, assign devices for all required users. Up to 8 MFA devices can be assigned to the root user.

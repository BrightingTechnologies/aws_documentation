# AWS Identity and Access Management (IAM) Best Practices

## 1. Principle of Least Privilege

### 1.1 Overview

The principle of least privilege dictates that IAM principals (users, groups, and roles) should be granted only the permissions necessary to perform their job functions. This approach minimizes the potential impact of security incidents.

### 1.2 Implementation

- **Permission Scoping**: Define specific IAM policies that grant the minimal set of actions required for the principal's tasks.
- **Policy Evaluation**: Regularly review and refine IAM policies to ensure they align with current operational needs and remove any excessive permissions.

## 2. Avoiding Wildcards in Action and Resource Elements

### 2.1 Overview

Using wildcards (`*`) in the Action and Resource elements of IAM policies can lead to unintended access, increasing the risk of privilege escalation and data exposure.

### 2.2 Implementation

- **Action Element Specificity**:

  - Define actions explicitly instead of using `*`.
  - Example:
    ```json
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
    ```

- **Resource Element Specificity**:
  - Specify the exact resources that the principal should access.
  - Avoid using `*` in the Resource element, unless absolutely necessary for flexibility with caution and rigorous monitoring.
  - Example:
    ```json
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/specific-object"
    }
    ```

## 3. Policy Management

### 3.1 Use Managed Policies

- Prefer AWS managed policies for common permission sets to reduce the complexity of custom policy management.

### 3.2 Inline Policies

- Use inline policies for specific principals when unique permissions are needed.
- Limit the use of inline policies to avoid proliferation and manageability issues.

## 4. Policy Review and Audit

### 4.1 Regular Audits

- Schedule regular reviews of IAM policies to ensure compliance with the principle of least privilege.
- Utilize AWS IAM Access Analyzer to identify and remediate overly permissive policies.

### 4.2 Logging and Monitoring

- Enable AWS CloudTrail to log all IAM activities and monitor for any suspicious behavior.
- Set up alerts for policy changes to ensure unauthorized modifications are detected promptly.

## 5. Conclusion

Adhering to these best practices helps ensure that IAM principals operate with the least necessary privileges, reducing the attack surface and enhancing the security of AWS environments. By avoiding wildcards in the Action and Resource elements, organizations can enforce fine-grained access control and minimize risks.

# AWS Recommendations

Throughout the years AWS has developed the best practices for using IAM in the most secure way possible

Review them in their official documentation:

- [Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html?link_from_packtlink=yes)
- [Root User Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/root-user-best-practices.html)
- [Business Use Cases](https://docs.aws.amazon.com/IAM/latest/UserGuide/business-use-cases.html)

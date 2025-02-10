# Network Security Best Practices for Amazon VPC

## Internal Documentation

### 1. VPC Security Foundation

#### Basic Security Principles

- Implement multiple layers of defense
- Follow the principle of least privilege
- Segment networks based on function
- Enable logging and monitoring
- Regular security audits

#### VPC Design Fundamentals

- Create separate subnets for different tiers (web, app, database)
- Use private subnets for resources that don't need internet access
- Implement multiple Availability Zones for high availability
- Use CIDR blocks that allow for future growth

### 2. Security Groups Configuration

#### Internet-Facing Security Groups

```json
{
  "Description": "Web Tier Security Group",
  "SecurityGroupIngress": [
    {
      "IpProtocol": "tcp",
      "FromPort": 443,
      "ToPort": 443,
      "CidrIp": "0.0.0.0/0",
      "Description": "HTTPS from Internet"
    },
    {
      "IpProtocol": "tcp",
      "FromPort": 80,
      "ToPort": 80,
      "CidrIp": "0.0.0.0/0",
      "Description": "HTTP from Internet"
    }
  ]
}
```

Best Practices:

- Only allow necessary ports (e.g., 80, 443 for web servers)
- Use source IP restrictions where possible
- Remove default rules that allow all traffic
- Reference security groups instead of IP ranges for internal traffic

#### Internal Security Groups

```json
{
  "Description": "Application Tier Security Group",
  "SecurityGroupIngress": [
    {
      "IpProtocol": "tcp",
      "FromPort": 8080,
      "ToPort": 8080,
      "SourceSecurityGroupId": "sg-web-tier",
      "Description": "Allow traffic from web tier"
    }
  ]
}
```

Best Practices:

- Use security group references instead of CIDR ranges
- Implement strict ingress rules
- Document all rules and their purposes
- Regular review of rules and remove unused ones

### 3. Network ACLs (NACLs)

#### Implementation Strategy

- Use NACLs as a secondary defense layer
- Configure both inbound and outbound rules
- **Number rules for easy management (increment by 100)**:
  - Start your first allow/deny rule at rule number **100** (lowest number is evaluated first).
  - Reserve numbers in increments of 100 (like 100, 200, 300...) for future updates.
  - This spacing allows you to insert new rules in the correct sequence without needing to reorder all rules.

Example NACL Configuration:

```json
{
  "NetworkAclEntries": [
    {
      "RuleNumber": 100,
      "Protocol": 6,
      "RuleAction": "allow",
      "Egress": false,
      "CidrBlock": "10.0.0.0/16",
      "PortRange": {
        "From": 443,
        "To": 443
      }
    },
    {
      "RuleNumber": 200,
      "Protocol": -1,
      "RuleAction": "deny",
      "Egress": false,
      "CidrBlock": "0.0.0.0/0"
    }
  ]
}
```

Best Practices:

- Start with restrictive rules
- Include explicit deny rules
- Maintain documentation of rule numbers and purpose
- Regular audits of NACL rules

### 4. Additional Security Services

#### AWS Network Firewall

Implementation:

- Deploy in dedicated firewall subnets
- Create rule groups for different security requirements
- Enable logging for traffic analysis

#### AWS WAF (Web Application Firewall)

Use Cases:

- Protect web applications
- Block common attack patterns
- Implement rate limiting
- Filter malicious IP addresses

#### AWS Shield

Implementation:

- Enable for DDoS protection
- Configure with CloudFront for edge protection
- Set up alerting for potential attacks

### 5. Monitoring and Logging

#### Required Monitoring Configuration

1. Enable VPC Flow Logs

   ```bash
   aws ec2 create-flow-logs \
    --resource-type VPC \
    --resource-id vpc-1234567890abcdef0 \
    --traffic-type ALL \
    --log-destination-type cloud-watch-logs
   ```

2. Configure CloudWatch Alarms
3. Enable AWS Config for resource tracking
4. Implement GuardDuty for threat detection
5. Consider setting log rotation to S3 bucket for long term storage

### 6. Implementation Example: Three-Tier Web Application

#### Architecture Overview

```plaintext
Internet Gateway
│
▼
Public Subnet (Web Tier)
[Security Group: Allow 80,443 from Internet]
│
▼
Private Subnet (App Tier)
[Security Group: Allow 8080 from Web Tier]
│
▼
Private Subnet (Database Tier)
[Security Group: Allow 3306 from App Tier]
```

#### Security Controls Implementation

1. Web Tier Security:

   - Public subnet with Internet Gateway
   - Security group allowing only HTTP/HTTPS
   - NACL filtering invalid ports
   - WAF protection for web traffic

2. Application Tier Security:

   - Private subnet
   - Security group allowing only web tier traffic
   - No direct internet access
   - NAT Gateway for updates

3. Database Tier Security:
   - Private subnet
   - Security group allowing only app tier traffic
   - No internet access
   - Encryption at rest enabled

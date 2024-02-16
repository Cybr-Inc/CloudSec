## AWS-native security services

This page lists out all AWS-native security services. They are available to use through your AWS account to help secure your AWS resources and environments.

Each of these services offers a suite of features that won't be shown on this page but that will be explained in greater detail on other dedicated or relevant pages.

## Cheat Sheet
<figure markdown>
  ![Image title](https://cybr.com/wp-content/uploads/2023/10/aws-security-services-v3-1716x2048.jpeg){ width="500" loading=lazy }
    <figcaption>[ :octicons-link-external-16: Hi-res download](https://cybr.com/wp-content/uploads/2023/10/aws-security-services-v3-scaled.jpeg)</figcaption>
</figure>

## Identity and Access Management

### IAM (Identity and Access Management) 
Manage identities and access to AWS services and resources. This service lets you create IAM Users, Groups, Roles, Policies, and more.

### IAM Identity Center (formerly SSO)
Manage access to multiple AWS accounts and applications and connect to your corporate directory or the Directory Service.

Creating users through Identity Center is meant to replace creating IAM Users in most cases since it provides multiple benefits, including not having to manage more passwords or access keys.

### Cognito
Add user sign-up and sign-in to your web or mobile apps.

### Verified Permissions
Policy-based access control by using roles and attributes with Cedar (an open-source language for access control by AWS).

### Directory Service
Managed Microsoft Active Directory (AD) service to provide existing AD users & groups with access to AWS (can connect to Identity Center)

### RAM (Resource Access Manager)
Securely share resources across multiple accounts, like VPC subnets, private certificate authorities, etc...

### Organizations
Centrally manage multi-account AWS environments and enable services like Identity Center, Control Tower, and more.

## Detection and Response

### GuardDuty
Automated and continuous threat detection backed by machine learning. It can find account-level threats and workload threats.

### Inspector
Automated vulnerability management for instance, container, and serverless workloads. Scans for software vunerabilities and unintended network exposure.

### Security Hub
Automated compliance security checks and alerting to help you follow best practices. Aggregates security findings from multiple sources.

### Security Lake
Central location to store all of your security data. Then, you can analyze and visualize this data through Athena, OpenSearch, SageMaker, etc...

### Detective
Conduct security investigations by analyzing and visualizing security data related to security issues. It integrates with many services mentioned on this page.

### Config
Keeps an eye on configurations of your services and workloads, and enables automated remediation when deviations happen in order to remain compliant.

### CloudWatch
Monitor your account, applications, and resources on AWS, on-prem, or on other clouds. It collects all sorts of data, and not just security-related data. For example, it can report back on performance metrics.

### CloudTrail
Track API usage and user activity across all of your AWS accounts and regions. Enables incidient response (IR) and audits, and detects unusual activity. Integrates well with multiple other services on this list, including CloudWatch, Athena, EventBridge, and others.

### IoT Device Defender
Security for IoT devices and fleets. It can help you find vulnerabilities, malicious behavior, and it can enable alerting.

### Elastic Disaster Recovery
Disaster recovery (DR) service that lets you test, recover, and fail back cloud-based and on-prem applications.

## Network and Application Protection

### Firewall Manager
Centralizes managing firewall rules including for: AWS WAF, AWS Network Firewall, AWS Shield, Route 53 Resolver DNS Firewall, Security Groups, and third-party firewalls.

### Network Firewall
Deploy across your VPCs to control traffic flowing in and out.

### Shield
DDoS protection for your apps and AWS resources. Standard is free and protects against L3/L4 attacks. Advanced is an upgrade and protects against L3/L7 attacks.

### Verified Access
Validates corporate applicaiton requests before granting access and removes the need for a VPN.

### Web Application Firewall (WAF)
Protects web applications from common exploits (like SQLi, XSS, etc...) as well as bot traffic. Can sit in front of CloudFront, ALB, API Gateway, or AppSync.

### Route 53 Resolver DNS Firewall
Firewall for outbound DNS traffic for your VPCs. You can create deny lists and allow lists for domains and protect against common DNS threats.

## Data Protection

### Macie
Uses machine learning (ML) and pattern matching to help protect sensitive data at scale. For example, it can help detect personally identifiable information (PII) stored in Amazon S3 and trigger automated remediation with EventBrige and Security Hub workflows, for example.

### Key Management Service (KMS)
Create, manage, and control cryptographic keys for your applications and services. Used to encrypt or digitally sign data.

### CloudHSM
Single-tenant hardware security modules (HSMs) so you can generate and use your own ecryption keys in instances only used by you and not other customers. 

### Certificate Manager
SSL/TLS certificates you can create, deploy, and manage for use with AWS services and resources.

### Payment Cryptography
For organizations that process payments and need to secure the data with cryptographic functions and key management.

### Private Certificate Authority
Iusse and manage private certificates. Great for IAM Roles Anywhere (for example).

### Secrets Manager
Central management for secrets (passwords, private API keys, tokens, etc...). It works directly with KMS to encrypt secrets. You can use this service instead of having to hardcode secrets. For example, if you need to use or process a secret value in an AWS Lambda function, you don't have to hardcode it or pass it in the API call. You can instead give permissions to access the secret in Secrets Manager through a role and resource policy.

## Compliance

### Artifact
Issue and manage private certificates. Great for using with IAM Roles Anywhere, for example.

### Audit Manager
Centrally and continuously audit your AWS environments to assess risks and compliance

## Honorable mentions

These are either not considered strictly security services, they don't really fit in one of the other categories, or they are features of another service already mentioned. We're including them here because they're very important services/features that deserve a mention of their own:

- AWS Control Tower - Set up, operate, and enforce multi-account environments security best practices and controls
- AWS Systems Manager (SSM) - Manage your cloud resources for operations, app, change, and node management. Works great with Config as an example.
- IAM Access Analyzer - IAM feature that helps achieve least privilege by reviewing access, validating policies,

## Additional Resources

- [ :octicons-link-external-16: Introduction to AWS Security Course](https://cybr.com/courses/introduction-to-aws-security/) ^[Paid]^ ^[Sponsored]^
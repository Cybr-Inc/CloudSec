---
icon: material/alarm-light
---

Primary AWS-native threat detection services:

- GuardDuty - 
- Detective - 
- Security Hub - continuous monitoring against benchmarks configuration checks and best practices

A great way to understand what to look for with threat detection is to [study real-world incident reports](/aws/incident-response/real-world-case-studies/), because they will often share Indicators of Compromise (IoCs).

While this is not a comprehensive list, below is a list of some commonly used API actions during incidents by threat actors. Of course, just because one of these actions is taken in your account doesn't automatically mean you've been breached. These actions can all be legitimately used as well, otherwise they wouldn't be part of the AWS API. The tricky part is identifying when they're legitimately used and when they're not.

## IAM Configuration Changes

| API Call    | Explanation                          | Example of how this can be exploited |
| ----------- | ------------------------------------ | ------------------------------------ |
| `iam:ChangePassword`       | Changes the password of the IAM user who is calling this operation | |
| `iam:CreateUser`       | Creates a new IAM user for your AWS account | |
| `iam:CreateRole`    | Creates a new role for your AWS account | |
| `iam:CreateGroup`     | Creates a new group | |
| `iam:AddUserToGroup` | Adds a user to a group with potentially higher privileges | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamaddusertogroup) |
| `iam:AttachUserPolicy` | Attaches the specified managed policy to the specified user | |
| `iam:AttachRolePolicy` | Attaches the specified managed policy to the specified IAM role | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamattachrolepolicy) |
| `iam:AttachGroupPolicy` | Attaches the specified managed policy to the specified IAM group | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamattachuserpolicy) |
| `iam:PutUserPolicy` | Adds an inline policy for a specified user | [Example](aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamputuserpolicy) |
| `iam:PutGroupPolicy` | Adds an inline policy for a specified IAM group | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/) |
| `iam:PutRolePolicy` | Adds an inline policy for a specified IAM role | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamputrolepolicy) |
| `iam:ListAccessKeys` | Returns information about the access key IDs associated with the specified IAM user | |
| `iam:CreatePolicyVersion` | Creates a new version of the specified managed policy | |
| `iam:CreateLoginProfile` | Enables console login for a user with a password set by the threat actor. Only possible if the user doesn't already have a console login created | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamcreateloginprofile) |
| `iam:UpdateLoginProfile` | Changes the password for the specified IAM user to a password of the threat actor's choosing | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamupdateloginprofile) |
| `iam:CreateAccessKey` | Creates a new AWS secret access key and corresponding AWS access key ID for the specified user | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/) |
| `iam:UpdateAccessKey` | Changes the status of the specified access key from Active to Inactive, or vice versa. | |
| `iam:DeactivateMFADevice` | Deactivates the specified MFA device and removes it from association with the username for which it was originally enabled | [Example](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamdeactivatemfadevice) |

## Sources and more info

- [:octicons-link-external-16: AWS Compromised IAM Credentials Playbook](https://github.com/aws-samples/aws-customer-playbook-framework/blob/main/docs/Compromised_IAM_Credentials.md)
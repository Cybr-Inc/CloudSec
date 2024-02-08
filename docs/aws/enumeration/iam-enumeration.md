## About IAM
UPDATE INFO

## AWS IAM Enumeration CLI commands
Retrieves general account information about including IAM users, groups, roles, and policies, and their relationships to one another. 

These are meant to be non-destructive enumeration commands. They only retrieve information, they do not modify resources (unless otherwise specified). [ :octicons-link-external-16: Official AWS Documentation](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/index.html)

Note: most of these commands have many more options than are shown, but they are optional and left out because they're not that useful most of the time and only required for special use cases. Refer to the docs above for more details.

Legend:

- Each line that starts with `aws` is the command
- The word immediately following `aws` denotes the service (ie: `aws sts` is calling the [ :octicons-link-external-16: STS](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sts/index.html) service, `aws iam` is calling the [ :octicons-link-external-16: IAM](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/index.html) service)

## General commands

### Get-caller-identity
`#!cli aws sts get-caller-identity`

??? example annotate "Example output"

    ``` json
    {
        "UserId": "AIDAT6ZKEI3E3J2XL4E5B",
        "Account": "921234892411",
        "Arn": "arn:aws:iam::921234892411:user/sm-enumeration-Julie" // (1)!
    }
    ```

1. `:user/` tells us this is a user identity and not a role, which would instead be `:role/`

The "whoami" in AWS.

Returns details about the IAM user or role whose credentials are used to call the operation. While not an IAM command (it's an STS command), this is often the first command that gets used for IAM enumeration which is why it's included here. 

No policy can deny issuing this command since it gives the same information when access is denied. [ :octicons-link-external-16: More on that here](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sts/get-caller-identity.html).

### Get-access-key-info
`#!cli aws sts get-access-key-info --access-key-id <value>`

Provide the AWS account ID for the given access key ID. Only provide the access key ID, not the secret access key.

Note: if an access key ID starts with `AKIA`, then it means it's a long-term credential. If it starts with `ASIA`, then it means they are temporary creentials that were created by STS operations

### Get-account-authorization-details
`#!cli aws iam get-account-authorization-details` 

General IAM account information

Gives a snapshot of the configuration of IAM permissions (users, groups, roles, and  policies) and their relationships in your account.

## User Enumeration

### List-users
`#!cli aws iam list-users`

List users in the AWS account.

### List-Service-specific-credentials
`#!cli aws iam list-service-specific-credentials` 

Get service-specific credentials associated with the IAM user.

### Get-user
`#!cli aws iam get-user --user-name <username>`

Get user's metadata. Includes permissions boundaries!

### List-access-keys
`#!cli aws iam list-access-keys [--user-name <value>]`

List existing access keys for a specific user (your current user being the default if no value is specified)

### List-user-policies
`#!cli aws iam list-user-policies --user-name <username>`

Get inline policies for a user.

### Get-user-policy
`#!cli aws iam get-user-policy --user-name <username> --policy-name <policyname>`

Get details about an inline policy.

### List-attached-user-policies
`#!cli aws iam list-attached-user-policies --user-name <username>`

Get attached (managed) policies (instead of inline policies). These can be either AWS-managed or Customer-managed policies.

### Get-login-profile
`#!cli aws iam get-login-profile --user-name`

Checks to see if the user has a login profile (password to log into the AWS Console). Returns the UserName and CreatedDate, which reflects the creation date of the password

## Groups Enumeration
### List-groups
`#!cli aws aws iam list-groups`

Get a list of groups in the AWS account.

### List-groups-for-user
`#!cli aws iam list-groups-for-user --user-name <username>`

Get groups related to a user.

### Get-group
`#!cli aws iam get-group --group-name <name>`

Get a group's details.

### List-group-policies
`#!cli aws iam list-group-policies --group-name <username>`

Get inline policies of a group.

### Get-group-policy
`#!cli aws iam get-group-policy --group-name <username> --policy-name <policyname>`

Get inline policies of a group. ([The difference between inline and managed policies](/aws/iam/about-iam/))

### List-attached-group-policies
`#!cli aws iam list-attached-group-policies --group-name <name>`

Get attached (managed) policies for a group (instead of inline policies). These can be either AWS-managed or Customer-managed policies. ([Inline vs. managed, and AWS-managed vs. Customer-managed policies](/aws/iam/about-iam/))

## Enumerate Roles
### List-roles
`#!cli aws iam list-roles`

Get roles in the AWS account.

### Get-role
`#!cli aws iam get-role --role-name <role-name>`

Get details about a specific role.

### List-role-policies
`aws iam list-role-policies --role-name <name>`

Get inline policies of a role. ([Inline vs. managed policies](/aws/iam/about-iam/))

### Get-role-policy
`aws iam get-role-policy --role-name <name> --policy-name <name>`

Get inline policy details.

### List-attached-role-policies
`aws iam list-attached-role-policies --role-name <role-name>`

Get attached (managed) policies for a role (instead of inline policies). These can be either AWS-managed or Customer-managed policies

## Enumerate policies
### List-policies
`aws iam list-policies [--only-attached] [--scope Local]`

List policies [--only-attached] is optional and will list policies only if they're attached to an IAM user/group/role

`[--scope local]` is optional and will only list policies that are Customer-managed, not AWS-managed. `--scope` AWS would be the opposite

### List-policies
`aws iam list-policies-granting-service-access --arn <identity> --service-namespaces <svc>`

Get list of policies that a user/group/role can use to access a specific service.

Example: `aws iam list-policies-granting-service-access --arn arn:aws:iam::123456789012:user/UserName --service-namespaces eks` for EKS

### Get-policy
`aws iam get-policy --policy-arn <policy_arn>`

Get details for a specific policy.

### List-policy-versions
`aws iam list-policy-versions --policy-arn <arn>`

Returns information about policy versions for a specific managed policy, including which one is the current default version.

### Get-policy-version
`aws iam get-policy-version --policy-arn <arn:aws:iam::123456789012:policy/example-policy> --version-id <v1 or v2 or ...>`

Get details for a specific policy version since policies can have multiple versions.

### List-entities-for-policy
`aws iam list-entities-for-policy --policy-arn <value>`

Lists all the IAM users/groups/roles that the managed policy is attached to.

## MFA
### List-mfa-devices
`aws iam list-mfa-devices [--user-name <username>]`

Get list of all MFA devices for a specific user. If no user is provided, AWS auto determines the user based on the access key ID.

### List-virtual-mfa-devices
`aws iam list-virtual-mfa-devices [--assignment-status <value>]`

Get list of all MFA devices in the AWS account

Can use optional `--assignment-status` to filter by Assigned, Unassigned, or Any (the default)

## Learn how to enumerate IAM Hands-On
<figure markdown>
  ![Image title](https://cybr.com/wp-content/uploads/2024/01/IAM-Enumerate.jpeg){ width="300" loading=lazy }
    <figcaption>[:octicons-link-external-16: 🧪 Intro to AWS IAM Enumeration >](https://cybr.com/courses/iam-privilege-escalation-labs/lessons/lab-introduction-to-aws-iam-enumeration/)</figcaption>
</figure>
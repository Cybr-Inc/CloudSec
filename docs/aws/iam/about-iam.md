---
icon: material/information
---

# About IAM

# IAM Users

There are two primary types of users in AWS (when it comes to managing AWS accounts):

- IAM Users
- [Identity Center users](/aws/iam/identity-center/)

For this specific section, we’ll focus on just explaining IAM Users. Check out the link above for info on Identity Center users.

IAM Users are created through the [ :octicons-link-external-16: IAM service](https://aws.amazon.com/iam/) and they are entities that give you an identity in your AWS account. 

That identity/user will have permissions either assigned directly to it, and/or through groups (that we’ll talk about in a second). 

You can give these users long-term credentials like a username and password, and they can use that password to log into the AWS console. Or, you can also give them access keys which can be used to authenticate via the API or CLI.

As much as possible, it's recommended to avoid using long-term credentials in favor of short-term credentials, which you can typically do through IAM Roles (explained below).

??? info "How to create IAM Users"

    **Through the AWS Console:**

    Go to IAM -> Users -> Create User

    **Through the AWS CLI:**

    ```
    aws iam create-user --user-name <value>
    ```

    [ :octicons-link-external-16: More info](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-user.html)

# IAM Groups

Groups are used to group users. This is great for keeping similar users together. For example, if you have 5 developers on one team, you could create a `Developers` group for them, and you could assign them all the same policies by applying one or more IAM policies to that single group. 

Then, if one of the developers leaves the company or changes teams, you simply remove them from that group and they automatically lose permissions related to the `Developers` group.

??? info "How to create IAM Groups"

    **Through the AWS Console:**

    Go to IAM -> Groups -> Create Group

    **Through the AWS CLI:**

    ```
    aws iam create-group --group-name <value>
    ```

    [:octicons-link-external-16: More info](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-group.html)

# IAM Roles

Roles are completely different. They are similar to users in the sense that you can give permissions to roles by assigning them policies, but that's pretty much where the similarity ends because users can have long-term credentials (passwords and access keys) while roles use short-term credentials only (access key with a session token that expires). They do not have usernames and passwords.

??? info "Common misconception about roles"

    A common misconception about roles is that they don't use access keys. They use access keys, they just don't use _long-term access keys_. I think this misconception came about because AWS documentation tends to use the terms "access key credentials" with "long-term" a lot, and they don't tend to use the terms "access key credentials" when they're describing "short-term credentials." 
    
    Technically speaking, short-term creds do use access keys, but the access keys are a little bit different and have to be used _with a session token_, and that session token expires after a set amount of time. The amount of time depends and is either set by you (up to a capped amount) or set by AWS depending on a couple of situations ([:octicons-link-external-16: more info here](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html#id_roles_use_view-role-max-session)). Once they expire, they can never be re-used.

    Here's an example of short term credentials to demonstrate:

    ```
    aws sts assume-role --role-arn "arn:aws:iam::111111111111:role/CloudTrailReadOnly" --role-session-name AWSCLISession
    {
        "Credentials": {
            "AccessKeyId": "ASIAVYQBCJKCYYM7HTGL",
            "SecretAccessKey": "AdPb8A7DtoWmtJF4ebVYuPpYSYC+UiQBjLph515S",
            "SessionToken": "IQoJb3JpZ2luX2VjEKj//////////wEaCXVzLWVhc3QtMSJHMEUCIQD25DxnIM0wRAprdUcLDkt1npDipZ1ELAWA69ATNl6WkwIge5sYdgfeNBBptibOOAQjtug/pLKTebecUIe9ZjlNuQMqowIIgP//////////ARAAGgwzOTYyMTI5ODAzNTciDLF4Tx0Kq9nhRZDFTyr3AVsoQMAPAS/sxsDVVkLl7vOMlLcE26bHPEoD8wkIzmkC3RxmrLss99JldJ/MccAtejB4CoIsjwsvO8HNouttn3dC2zJK1nqUlIauhMK0HZ2E6V4sR+VPx91oRLacAYa2XNh820FryQ1+B2pUt45bbhZg8xU+X0HDiaApCFIngKZ/6kKR1oipGWuaRJSiUKTL4ubmpVTn0ztfZimeTtEW26UXmFgtCK3F7vB5EY9/7i+1k19W+5bManGvtlP0s1ykDZX4VSRwaPkctgABTwIFhmoSdeu8pnMFmokzjlR9jRNtso9tkULH/6+qqr3d+b3QN/0rk12l2F4wufKvrgY6nQFRY9gP6Wq2f4CXuzUEUoRYumZoLy7cy+WrqqrVFLqpVZiu2Zae1FZ2AoSSGLPt16MudR//FdV8T1nen6812o71qX8KRTXaYFLeDS4p1bwL7LCade/P7WTCkr9DZKB7GLn723H3qqTaVY5xai4v5qbejjo8kMOadHPDNAI7CAn/S/bTupVBrxFY66+HaPwAb/jsN/6QB555rHx9yIKD",
            "Expiration": "2024-02-14T00:20:25+00:00"
        },
        "AssumedRoleUser": {
            "AssumedRoleId": "AROAVYQBCJKCRAYSM2QHS:AWSCLISession",
            "Arn": "arn:aws:sts::111111111111:assumed-role/CloudTrailReadOnly/AWSCLISession"
        }
    }
    ```

    Those credentials have long expired, but to use them, you would have set the `AccessKeyId`, `SecretAccessKey`, and `SessionToken`.

??? tip "How can you tell if credentials you found are short-term or long-term?"

    If you come across AWS access keys, since short-term credentials also use `AccessKeyId` and `SecretAccessKey`, a way you can tell what they are is by how they start:

    Long-term `AccessKeyId` starts with `AKIA...`

    Short-term `AccessKeyId` stats with `ASIA...` (view an example above in the tooltip titled "Common misconception about roles")

Instead, roles can be assumed by users or by other services to do stuff. For example, if you are part of the incident response team and there's an incident in one of your AWS accounts, you could assume the `SecurityAnalystRole` to perform analysis, and that role would have access to services like CloudTrail and Athena. Whenever you assume that analyst role, you renounce whatever permissions your prior user has while you're assuming that role, but you can switch back to your prior permissions at any time or whenever your temporary credentials expire (if they don't get renewed).

Or as another example, if you're running an EC2 instance with a web application on it that needs to access data in Amazon S3, you would give that instance a role with permissions to access S3 and it would use that role to get access whenever it needs it.

So basically think of roles as hats that you can wear that give you (a user) or AWS resources different permissions whenever they are needed...assuming that the user or service has permissions to assume the role.

??? info "How to create IAM Roles"

    **Through the AWS Console:**

    Go to IAM -> Roles -> Create Role

    **Through the AWS CLI:**

    ```
    aws iam create-role --role-name <value> --assume-role-policy-document
    ```

    [ :octicons-link-external-16: More info](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html)

??? info "How to use IAM Roles"

    [More info here](/aws/iam/roles/)

## IAM Users vs. Roles
Roles in AWS are completely different from both users and groups. To help explain their differences, let’s look at an analogy.

Think of a corporate building’s badge access control system.

When you become an employee, you are given a badge with your name and some other information. That badge is unique to you, and it gives you access to general parts of the building like the cafeteria and restrooms, and also more specific areas like your office, things like that. Think of this badge as an AWS user.

Let’s also say that you’re part of the IT team, and of course, your company also has a Finance team, an HR team, Marketing team, etc…each of those teams are groups of employees, or in AWS terms, groups of users. So think of those departments as groups.

Employees in the HR group will have very different access and permissions than employees in the IT team, and so groups in AWS help us assign permissions to multiple similar users all at once instead of requiring individual permissions for each and every user which would be a management nightmare.

But now let’s say that *you*, specifically, are one of only a few employees in the IT team that have access to a certain section of the data center. That section contains classified data related to military personnel, which requires that you have a certain type of clearance, and so most employees (even in the IT team) don’t have access to that section.

For security purposes, instead of granting your badge access to that section, you have to check in at a kiosk using your badge, and you have to make a log entry for the security guard to then hand you a temporary access pin to that restricted section. You need your badge (which in our scenario represents your credentials) in order to log that entry and receive your temporary access pin. Think of that temporary access pin as an IAM Role.

That role, or access pin, only grants you access to the restricted section for up to 1 hour, at which point the security guard would go find you and escort you out. If you need more time, you would have to renew your access and get a different temporary access pin to get another hour in that restricted area. This all leaves a paper trail that can later be audited.

Also, while you are in the restricted area, you are not allowed to bring in your badge, instead it stays with the security guard at the checkpoint, and the computer systems within that area will not let you authenticate into systems that you typically have access to. You temporarily give up the access you had as a user with your badge while you are assuming that role.

As soon as you exit and log out in the entry book, the security guard grants you your badge back, and you regain your prior user permissions, but lose the access you had with the role.

This is an analogy, so there are a couple of flaws with it and it’s not perfect, but the general idea works.


# IAM Policies
Regardless of whether you are creating/using IAM Users, Groups, and/or Roles, you will need to grant these identities permissions by using policies.

Policies in AWS are JSON documents that dictate what is allowed or denied.

Policies can be very complex, which is part of what can lead to vulnerabilities (like [PrivEscs](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/))

## Inline Policies
Inline policies are created for a single IAM identity (whether it be a user, group, or role).

For example, I can create two IAM Users: Mark and Jane, and I can provide them each with their own inline policies.

While inline policies have a time and place, they're typically discouraged because they can't be re-used since they are assigned to a single identity. So even if Mark and Jane do the exact same job and require the exact same permissions, you're now having to manage two separate policies that do the same thing. Instead, it would be better to:

- Create a managed policy (which we explain below)
- Add them to an IAM Group

Keep in mind that identities can have both inline and managed policies at the same time.

??? info "How to create inline policies"

    **Through the AWS Console:**

    You can create inline policies a few different ways through the console. 
    
    1. While creating a user/group/role, you will be asked to grant it permissions and you will have the ability to create an inline policy
    2. For existing users/group/role, you can add inline policies. Example: go to IAM -> Users -> Click on a user -> Add Permissions -> Create inline policy 

    **Through the AWS CLI:**

    ```
    aws iam put-user-policy --user-name <value> --policy-name <value> --policy-document <value>
    ```

    [:octicons-link-external-16: More info](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/put-user-policy.html)

## Managed Policies
Managed policies are standalone policies that can be attached to identities (users/groups/roles), and they can also be detached at any time. This is a big difference compared to inline policies which can only be used for and by a single identity.

You can think of inline policies as being permanently bonded to an identity, whereas managed policies are “pluggable” modules.

If we go back to our two IAM Users: Mark and Jane, and they do the exact same job, then we can create a single managed policy and attach it either to both identities, or to a group that they both belong to. By doing that, they will receive the permissions outlined in that managed policy.

### Customer-managed policies
Whenever we create managed policies in our AWS account, we are creating customer-managed policies.

??? info "How to create customer-managed policies"

    **Through the AWS Console:**

    You can create inline policies a few different ways through the console. 
    
    1. Go to IAM -> Policies -> Create Policy

    **Through the AWS CLI:**

    ```
    aws iam create-policy --policy-name <value> --policy-document <Value>
    ```

    You then need to attach the policy to an entity.

    For users:

    ```
    aws iam attach-user-policy --user-name <value> --policy-arn <value> 
    ```

    For groups:

    ```
    aws iam attach-group-policy --group-name <value> --policy-arn <value> 
    ```

    For roles:

    ```
    aws iam attach-role-policy --role-name <value> --policy-arn <value> 
    ```

    [:octicons-link-external-16: More info (create-policy)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-policy.html)
    [:octicons-link-external-16: More info (AWS IAM CLI)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/index.html#cli-aws-iam)

AWS also offers pre-made policies that, you guessed it, are called AWS-managed policies.

### AWS-managed policies
AWS-managed policies are still managed policies, but they are pre-created by AWS and available by default to every single AWS account.

You can see the entire listed of AWS-managed policies [ :octicons-link-external-16: here](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/policy-list.html) and examples include:

- [ :octicons-link-external-16: AdministratorAccess](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AdministratorAccess.html)
- [ :octicons-link-external-16: AmazonS3ReadOnlyAccess](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AmazonS3ReadOnlyAccess.html)

AWS-managed policies can provide a great starting point and will sometimes grant exactly the permissions you need...but oftentimes they should be copied and modified to [achieve least privilege](/aws/iam/least-privilege/).

??? info "How to use AWS-managed policies"

    **Through the AWS Console:**

    You don't create AWS-managed policies. They are already created by AWS for you to use and you can find them here:
    
    1. Go to IAM -> Policies -> Filter by Type -> "AWS managed" or "AWS managed - job function"
    2. Select the policy -> Actions -> Attach -> select the entity

    **Through the AWS CLI:**

    Find the ARN of the policy through the console, CLI, or documentation.

    For users:

    ```
    aws iam attach-user-policy --user-name <value> --policy-arn <value> 
    ```

    For groups:

    ```
    aws iam attach-group-policy --group-name <value> --policy-arn <value> 
    ```

    For roles:

    ```
    aws iam attach-role-policy --role-name <value> --policy-arn <value> 
    ```

    [:octicons-link-external-16: More info (AWS IAM CLI)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/index.html#cli-aws-iam)

## Identity vs. Resource Policies
Just in case your head isn't spinning enough already, there are two more types of policies that you need to know about and understand:

1. Identity policies
2. Resource policies

### Identity policies

In simple terms, identity policies (or identity-based policies) are created and used for identities (users/roles/groups).

### Resource policies

In simple terms, resource policies (or resource-based policies) are created and used for specific resources like S3 buckets, KMS keys, and much more...

### Why the need for both? When to use which?

Using both can seem unnecessary and more work for no extra benefit at first glance, but there are cases where they are incredibly valuable in creating additional layers of defense.

!!! example "S3 bucket policy example:"

    S3 buckets can have bucket policies which are resource-based policies, and they determine who/what can access the bucket's information and objects stored within the bucket. Even if IAM User `Christophe` has full administrative permissions, if the bucket policy says that `Christophe` can't access its contents, then he won't be able to. In fact, it's possible to lock yourself out of S3 objects if you set up a `Deny All` S3 bucket policy, and it requires using user `root` to change...so be careful with overly restrictive resource policies. 

!!! example "KMS encryption example:"

    S3 encrypts data server-side by default (SSE-S3), but you can also enable AWS-KMS encryption (SSE-KMS) which will use KMS keys to encrypt the data server-side. One key benefit of doing this (even though it's an extra cost) is because KMS Keys can have resource policies (KMS Key Policy), and if you don't have access as per the KMS Key Policy, then you can't decrypt the objects even if you technically have access to the objects through the API. Extra layer of security.
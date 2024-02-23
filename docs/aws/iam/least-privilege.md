# About least privilege

The concept of least privilege is about providing only the permissions required to perform a task. That's much easier said than done, however, and the journey to getting there typically starts off by providing more permissions than necessary (while being reasonable), and then refining over time. This page contains tips, tricks, and steps to help with that.

## Tips for getting to least-privilege permissions

### Step 1: Create roles for users to assume to perform specific tasks

[Roles](roles.md) have many benefits, including helping us achieve least privilege. For example, if you have access to a admin-level user, but your regular day-to-day tasks only require you to access EKS, using an admin-level user for those day-to-day tasks is _not_ following least privilege.

Instead, you can create a role that you would assume to perform those day-to-day EKS tasks, and if you ever need admin permissions for one-off use cases or for emergency purposes, then you can always revert back.

### Step 2: Start with AWS-managed policies and refine over time

Figuring out what permissions are needed can be challenging and can take a lot of time. To help, you can start off using AWS-managed policies which are available in every AWS account. These are almost never going to give you least-privilege permissions since they're designed for broad use cases, but they can be a helpful starting point.

Then, over time, you can transition to customer-managed policies that you customize from the AWS-managed policy by removing resources or actions, adding conditions, etc...

A helpful way to do that is by using Access Analyzer to create policies based on access activity, [as explained below](#about-access-analyzer).

### Step 3: Regularly audit and remove unused users, roles, permissions, policies, and credentials

Drift happens over time, even to the best of us. Things change, resources get created and forgotten about, people leave, credentials get improperly rotated, and the list goes on. It will happen, and so it's important to reguarly set up audits where you go in and look for unused resources. In the case of least privilege, we're specifically looking for unused users, roles, permissions, and credentials (passwords or access keys).

You can do that by looking at IAM's [:octicons-link-external-16: last accessed information](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_access-advisor-view-data.html).

If it's not being used, unless it's a resource created for emergency situations and you haven't had any emergencies (hurray!) then it probably highlights an area for improvement regarding least privilege.

## About Access Analyzer
AWS IAM Access Analyzer is a security tool designed to help organizations identify and manage unintended resource access in their AWS environments. It enables users to analyze permissions granted through IAM policies, resource policies, and Access Control Lists (ACLs) to determine if they result in overly permissive or unintended access to AWS resources.

## IAM Access Analyzer policy generation
Generate policies based on access activity by having IAM Access Analyzer review AWS CloudTrail logs and then automatically generate a policy template for the permissions that entity used during that time range.

!!! info "More info"

    [ :octicons-link-external-16: https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_generate-policy.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_generate-policy.html)

!!! tip "Tips"

    - It's generally easier to start off granting more privileges than necessary (within reason) and then reducing over time since that will significantly reduce the number of times your team members reach out asking for more permissions. The risk here, of course, is granting far more permissions than necessary to people who might go rogue or who become compromised (leaked credentials, malware, phishing, etc) and then forgetting to go back and refine
    - Implement regular access audits and tweaking over time. This is not a one-and-done situation

## Access Analyzer Cheat Sheet
<figure markdown>
  ![Image title](https://cybr.com/wp-content/uploads/2024/02/access-analyzer.jpg){ width="500" loading=lazy }
    <figcaption>[ :octicons-link-external-16: Hi-res download](https://cybr.com/wp-content/uploads/2024/02/access-analyzer.jpg)</figcaption>
</figure>

## Check-access-not-granted
This feature and command of IAM Access Analyzer enables us to check newly created IAM policies against a deny list before we deploy the policy to AWS or production.

For example, you can run the command:
```
    aws accessanalyzer check-access-not-granted \
        --policy-document <value> \ // (1)!
        --access <value> \ // (2)!
        --policy-type <value> \ // (3)!
```

1. JSON policy to check. This can either be inline or a file path
2. Deny list of permissions. Can use shorthand or JSON
3. IDENTITY_POLICY or RESOURCE_POLICY (_[Identity vs. Resource policy](/aws/iam/about-iam/)_)

1. `:user/` tells us this is a user identity and not a role, which would instead be `:role/`

??? example annotate "Example command & output"

    ``` json
    [cloudshell-user ~]$ aws accessanalyzer check-access-not-granted \
        --policy-document file://policy.json \
        --access actions=iam:PutUserPolicy,iam:PutGroupPolicy \
        --policy-type IDENTITY_POLICY

    {
        "result": "FAIL",
        "message": "The policy document grants access to perform one or more of the listed actions.",
        "reasons": [
            {
                "description": "One or more of the listed actions in the statement with index: 1.",
                "statementIndex": 1 // (1)!
            }
        ]
    }
    ```

1. Indicating a match for iam:PutUserPolicy

!!! info 

    _Requires CLI 2.13.39 or later_

    More info: [ :octicons-link-external-16: https://awscli.amazonaws.com/v2/documentation/api/latest/reference/accessanalyzer/check-access-not-granted.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/accessanalyzer/check-access-not-granted.html)

## Check-no-new-access
This feature and command of IAM Access Analyzer enables us to check and see if new permissions are added to a policy before deploying to AWS. Think of it as a diff checker.

```
aws accessanalyzer check-no-new-access \
    --new-policy-document <value> \ // (1)!
    --existing-policy-document <value> \ // (2)! 
    --policy-type <value> // (3)! 
```

1. The new JSON policy document to check
2. The existing policy document to compare
3. IDENTITY_POLICY or RESOURCE_POLICY (_[Identity vs. Resource policy](/aws/iam/about-iam/)_)

??? example annotate "Example command & output"

    ``` json
    [cloudshell-user ~]$ aws accessanalyzer check-no-new-access \
        --new-policy-document file://new_policy.json \
        --existing-policy-document file://existing_policy.json \
        --policy-type IDENTITY_POLICY

        {
            "result": "FAIL",
            "message": "The modified permissions grant new access compared to your existing policy.",
            "reasons": [
                {
                    "description": "New access in the statement with index: 1.",
                    "statementIndex": 1
                }
            ]
        }
    ```

!!! info 
    
    _Requires CLI 2.13.39 or later_

    More info: [ :octicons-link-external-16: https://awscli.amazonaws.com/v2/documentation/api/latest/reference/accessanalyzer/check-no-new-access.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/accessanalyzer/check-no-new-access.html)

## Sources and more info

- [:octicons-link-external-16: AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege)
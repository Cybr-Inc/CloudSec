---
icon: octicons/repo-locked-16
---

# About least privilege

The concept of least privilege is about providing only the permissions required to perform a task. That's much easier said than done, however, and the journey to getting there typically starts off by providing more permissions than necessary (while being reasonable), and then refining over time. This page contains tips, tricks, and steps to help with that.

## IAM Best Practices

<figure markdown>
  ![Image title](https://cybr.com/wp-content/uploads/2024/02/iam-best-practices-checklist-scaled.jpeg){ width="500" loading=lazy }
    <figcaption>[:octicons-link-external-16: Hi-res download](https://cybr.com/wp-content/uploads/2024/02/iam-best-practices-checklist-scaled.jpeg)</figcaption>
</figure>

The above cheat sheet includes many best practices for AWS IAM, with more than half being related to achieving least privilege. Let's take a closer look:

- **(6) Apply least-privilege permissions:** You can start with broader permissions (while being reasonable), and then refine over time using IAM Access Analyzer and manual reviews.
- **(7) Get started with AWS managed policies and move toward least-privilege permissions:** AWS managed policies are almost always overly permissive, but they can be a good starting point to limit access based on job requirements/usage
- **(8) Use IAM Access Analyzer to generate least-privilege policies based on access activity:** AWS IAM Access Analyzer is a tool that can help identify and manage unintended resource access in AWS environments. It can analyze permissions granted through IAM policies, resource policies, and ACLs, to determine if they result in overly permissive or unintended access to your AWS resources.
- **(9) Regularly review and remove unused users, roles, permissions, policies, and credentials:** IAM provides last accessed information to identify users, roles, permissions, policies, and credentials that are no longer needed and should be removed or deactivated.
- **(10) Use conditions in IAM policies to further restrict access:** Conditions let you specify when access should be granted for actions, resources, and principals. Refer to our cheat sheets: [:octicons-link-external-16: Create a least privilege S3 bucket policy](https://cybr.com/cloud-security/create-a-least-privilege-s3-bucket-policy/) and [:octicons-link-external-16: Anatomy of an AWS IAM Policy](https://cybr.com/cloud-security/anatomy-of-an-aws-iam-policy-cheat-sheet/) for more information and practical examples.
- **(11) Verify public and cross-account access to resources with IAM Access Analyzer:** Another great use case for IAM Access Analyzer is that it can help analyze, identify, review, and monitor public or cross-account permissions for your resources.
- **(12) Use IAM Access Analyzer to validate your IAM policies to ensure secure and functional permissions:** IAM Access Analyzer has two other awesome features: check-access-not-granted and check-no-new-access. The first is a command that enables checking newly created IAM policies against a deny list before deploying the policy to AWS. The second enables checking to see if new permissions are added to a policy before deploying to AWS. Think of it as a diff checker.
- **(13) Establish permissions guardrails across multiple accounts:** Using multiple accounts isn’t just for large organizations. Account separation is the ultimate form of segmentation in AWS. Use AWS Organizations to manage accounts and use SCPs for permissions guardrails. SCPs are used in addition to identity-based or resource-based policies for users/roles/resources in the individual accounts.
- **(14) Use permissions boundaries to delegate permissions management within an account:** Permissions boundaries are similar to other managed policies, except they restrict access by setting the maximum permissions allowed. They don’t grant access on their own. They can be used for both IAM users and roles, and the user/role can only perform actions allowed by both the identity-based policy and permissions boundary.

Now let's go through and look at tips, tricks, and tools to achieve these best practices.

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
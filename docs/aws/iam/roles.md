---
icon: material/account-hard-hat
---

## About Roles

IAM Roles are similar to [users](/aws/iam/about-iam/#iam-users) in the sense that you can give permissions to roles by assigning them policies, but that's pretty much where the similarity ends.

### Difference #1 (compared to users)

Users can have long-term credentials (passwords and access keys) but roles *cannot* have usernames and passwords, and while they *technically* use access keys, they use access keys *with session tokens* that expire after a set amount of time. These are known as “temporary credentials” in AWS, and you'll see people use the term “short-term credentials” to mean the same thing. 

For example:

- An IAM User can make API requests to an AWS service using a pair of just an `aws_access_key_id` and an `aws_secret_access_key`. These keys live forever until you either disable them or delete them.
- With IAM Roles, instead, we need to issue API requests using an `aws_access_key_id`, an `aws_secret_access_key`, and an `aws_session_token`

The default session token duration is 12 hours, but you can configure them to last anywhere from 15 minutes to 36 hours. That way, even if an attacker somehow comes across those credentials 3 days later, they will be expired and won't work instead of remaining permanently available.

### Difference #2 (compared to users)

Another key difference is that roles can be assumed by users or by other services to do their jobs. For example, if you are part of the incident response team and there's an incident in one of your AWS accounts, you could assume the `SecurityAnalystRole` to perform analysis, and that role would only have access to services like Amazon CloudTrail and Amazon Athena. 

Whenever you assume that analyst role, you renounce whatever permissions your prior user has while you're assuming that role, but you can switch back to your prior permissions at any time or whenever your temporary credentials expire (unless you renew them).

Or as another non-user example, if you've got an EC2 instance with a web application on it that needs to access data in Amazon S3, you would give that instance a role with permissions to access S3 and it would use that role to get access whenever it needs it through temporary credentials — all automatically.

## Roles are hats you can wear for different jobs

You can think of roles as hats that you can wear to give yourself (a user) or AWS resources different permissions whenever they are needed...assuming that the user or service has permissions to assume the role in the first place (determined by [trust policies](/aws/iam/roles/#trust-relationship-policy-documents-aka-trust-policies-and-how-theyre-different-from-permissions-policies)).

This gives you multiple benefits:

- Using roles designed for specific tasks makes it significantly easier to achieve least privilege
- Least privilege is important for multiple security reasons including:
    - Preventing accidental deletions/modifications
    - Making it much harder for attackers who somehow gain access to do damage since they will need to figure out how to escalate privileges
- Roles also provide a clear audit trail, explained below
- Roles can be used for cross-account access, which means that if your organization is running multiple AWS accounts (a common practice even for smaller businesses), users can assume roles for day-to-day tasks (again following least privilege) and this is known as *delegation*

Going back to this for a minute: “roles also provide a clear audit trail.”

We mentioned using a `SecurityAnalystRole` to analyze an incident. By having one of your employees (a user) assume that role to perform the analysis; even if they then switch to a `SecurityBreakGlassRole` to perform containment and eradication measures, we can have a much easier time tracing back what actions were taken when and by who since the actions will be logged separately for both roles. We can more easily trace every step taken while assuming the analyst role, and then every step taken while assuming the break glass role.

## Common scenarios for using roles

### Enable cross-account access

For our first scenario, let’s say that you work for an IT consulting company. Another company is migrating to the cloud from on-prem and has hired your services to help them architect and secure their deployments for 3 months.

You will only require a certain level of access to a handful of services including:

- Amazon’s Elastic Kubernetes Service, or EKS
- Specific Amazon S3 buckets
- and AWS Lambda

Instead of going in and creating an IAM User for you, the organization can create an IAM Role in the AWS account that has those resources, and you can assume that cross-account role to perform your job functions.

Using trust policies that we’ll explain in greater detail in another lesson, you can set up roles to be assumable by identities from other AWS accounts into your AWS account, and you can not only implement very tight permissions, but you can also set up monitoring & alerting to keep a very close eye on those roles.

Also, the organization hiring you doesn’t have to worry about creating or managing passwords or access keys for you. All they have to do is create the role, assign permissions to that role, and that’s it. Then they can delete the role or revoke your access whenever they want.

### Access workloads within AWS

Roles aren’t just useful for users, though. As another scenario, roles can be assumed by AWS services running your workloads.

For example, if you are running an EC2 instance with an application hosted on it, and that application needs to access files in Amazon S3, you can provide a role for the EC2 instance to assume through an instance profile, and the application on the EC2 instance will receive access to the S3 data through that role.

Zero need for hardcoded credentials, and no need to manage any credentials for your AWS workloads.

These roles that can be assumed by AWS services are called service roles.

### Access workloads running outside of AWS

Now, you can even use roles *outside* of AWS to provide workloads running outside of AWS access to your AWS resources without requiring hardcoded credentials! This is a relatively new feature at the time of writing, and it’s a very important one. It's called [:octicons-link-external-16: IAM Roles Anywhere](https://docs.aws.amazon.com/rolesanywhere/latest/userguide/introduction.html), and it makes use of certificates and certificate authorities to grant resources outside of AWS access to AWS resources using roles.

This is great for on-prem workloads, or even for deployment pipelines. Like if you are using GitLab for your repositories and deployments, you can use IAM Roles Anywhere while the application is in GitLab and before it gets deployed to AWS, and avoid hardcoding credentials which can be leveraged by attackers if they get access to your GitLab.

### Grant access to AWS services

We talked about how roles can be used by your AWS workloads when running in AWS to access other AWS services. Sometimes, though, there are AWS services that need to perform actions in your account behind the scenes, and that requires providing them with something called a service-linked role *(not to be confused with service roles we talked about above...I know...I didn't come up with this...!)*.

Examples of this include services like:

- AWS Config
- Security Hub
- Inspector
- Etc…

Those services need roles that *they can assume* to do their job without your interaction, and those are service-linked roles. We’ll talk more about that a little bit later on in the course, but I wanted to introduce that as another scenario where roles are commonly used.

## How to use Roles in the AWS Console

Coming soon

## How to use Roles in the AWS CLI

Coming soon

### Good approach

### Better approach

### Best approach
For the best approach, check out [ :octicons-link-external-16: aws-vault](/aws/iam/open-source-tools/) in our list of open source tools.

## Trust relationship policy documents aka trust policies (and how they're different from permissions policies)

Coming soon

## AWS service roles

Coming soon


## AWS service-linked roles

Coming soon

[ :octicons-link-external-16: Helpful answer from StackOverflow](https://stackoverflow.com/questions/65970798/aws-service-role-vs-service-link-role)

## Tools that make using IAM Roles easier

- aws-vault

## Sources

- A lot of the content on this page comes from Cybr's course: [:octicons-link-external-16: "Practical Guide to AWS IAM Roles"](https://cybr.com/courses/practical-guide-to-aws-iam-roles/) ^[Free&Paid]^ ^[Sponsored]^
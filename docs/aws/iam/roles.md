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
For the best approach, check out our list of open source tools below.

## Trust relationship policy documents aka trust policies

Whenever you create a role through the AWS console, the first option is to select a trusted entity. You can select between:

- AWS service
- AWS account
- Web identity
- SAML 2.0 Federation
- Custom trust policy

Let's talk about what these options are and what they mean, and we'll break down each of these options one-by-one.

### What are trusted entities?

The way that we set who or what can assume our roles when we configure them is by writing trust policies. Trust policies are [resource-based policies](/aws/iam/about-iam/#resource-policies) that are attached directly to a role, and they dictate who we trust to assume the role. 

Roles can be assumed by:

- IAM Users
- Identity Center Users
- Other Roles
- AWS Accounts (well, identities/resources within those accounts)
- AWS Services

Here's an example of a trust policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111111111111:root"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "Bool": {
                    "aws:MultiFactorAuthPresent": "true"
                }
            }
        }
    ]
}
```

We have:

- An `"Effect": "Allow"`
- A `"Principal": { "AWS": "arn..."}`
- An `"Action": "sts:AssumeRole"`
- With a `Condition` checking for whether MFA was used to authenticate or not

The `Principal` in these trust policies is what tells AWS *who* or *what* can assume this role. In the case of the above example, we have an `AWS Account ID` ARN of `111111111111` and it also has `:root` at the end. 

This policy is telling AWS that only resources from that account can assume this role, and only if they used MFA to authenticate.

`:root` here doesn't mean that you have to be the root user of account `111...`, it instead means all resources in that account. 

### Changing & restricting the principal

We ideally would want to refine this a little bit more and only allow certain users or roles to be able to assume this role instead of entire accounts. To do that, we would update the principal. Let’s see how.

To change it from `:root` to your user, you can do that by updating the principal ARN:

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::111111111111:user/christophe-demo"
			},
			"Action": "sts:AssumeRole",
			"Condition": {
				"Bool": {
					"aws:MultiFactorAuthPresent": "true"
				}
			}
		}
	]
}
```

For a single user, you can add `:user/username`. If you had multiple users you wanted to add, you would add them as an array like this:

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"AWS": [
                    "arn:aws:iam::111111111111:user/christophe-demo", "arn:aws:iam::111111111111:user/username-2"
                ]
			},
			"Action": "sts:AssumeRole",
			"Condition": {
				"Bool": {
					"aws:MultiFactorAuthPresent": "true"
				}
			}
		}
	]
}
```

Or, if you wanted to grant access to assume this role from another role — typically because the role is from another account - then you would replace the ARN to this:

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::111111111111:role/admin"
			},
			"Action": "sts:AssumeRole",
			"Condition": {
				"Bool": {
					"aws:MultiFactorAuthPresent": "true"
				}
			}
		}
	]
}
```

With a `:role/roleName`.

### How to create trust policies

#### In the AWS console

In the AWS console, whenever you go to create a role (IAM -> Roles -> Create a role), the first options you select will auto generate a trust policy for you, or you can always select the option to create a custom policy.

#### With the CLI

You set the trust role policy when you create the role through the `--assume-role-policy-document <value>` option:

```
aws iam create-role --role-name <value> --assume-role-policy-document <value>
```

[ :octicons-link-external-16: More info about the `create-role` command](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html)

### Trust policies are not permissions policies

A role needs to have both a trust policy _and_ at least 1 permissions policy to be useable. 

The trust policy dictates who/what can assume the role.

The permissions policy dictates what the assumer has access to.

### AWS Service

This trusted entity type let's you select an AWS service like `EC2` or `Lambda`, for example, and it will change our available use cases.

Whenever you select this option, you are creating what’s called an *AWS Service Role*. A service role is a role that an AWS service can assume to perform actions and access resources. 

For example, if you want EC2 instances to be able to access data stored in Amazon S3, you would want a trust policy that enables those instances to assume the role, and then you would want to add one or more permissions policies that grant access to the correct data in S3. The trust policy could look something like this:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sts:AssumeRole"
            ],
            "Principal": {
                "Service": [
                    "ec2.amazonaws.com"
                ]
            }
        }
    ]
}
```

### Web Identity

For now, let’s skip over `AWS account` since we’re already familiar with that one, and let’s click on `Web identity`.

This option allows users federated by an external web identity provider to assume the role and perform actions in this account. 

By selecting it, we get access to a dropdown where we can `Choose a provider`.

We have options for:

- Login with Amazon
- Amazon Cognito
- Facebook
- Google

So for example, if someone were to log in through Amazon Cognito, they can assume this role to — say, access Amazon S3 images through your mobile application — just to name an example.

### SAML 2.0 federation

The next option we have is SAML 2.0 federation. SAML is a standard that allows an identity provider to authenticate users and pass identity and security information to a service provider. That’s a fancy way of saying that with SAML, you can enable single sign-on for your users with SAML-enabled apps and services.  

This is a much larger topic that we won’t cover in this course, but this would be the option to select if you are creating a role for something related to SAML 2.0 federation.

### Custom trust policy

Finally we have `Custom trust policy` where we get an editor to manually configure or write our policy. This is an option if you have an edge case that’s not satisfied by the other options, or if you just know what you need and want and it would be faster for you to handwrite it then go through the provided options.

The other options are really just the AWS console user interface making it easier for you to configure your trust policy, but we could do all of this by hand, of course, as we can see with this option.

Also, if you were creating a role via the AWS CLI, API, or using Infrastructure as Code, you would have to pass in a custom trust policy like this.


## Roles best practices

!!! success "Roles best practices"

    - Require MFA in your trust policy - this requires that you successfully authenticate through MFA before you can assume a role
    - Use an external ID if the role is going to be used by a third party

## AWS service roles

Service roles are roles that can be assumed by a service to access other services or resources in AWS.

As an example, the trust policy for a service role might look like this:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
                "Service": [
                    "codedeploy.amazonaws.com"
                ]
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

This gives the service `codedeploy.amazonaws.com` the ability to assume the role.


## AWS service-linked roles

_Service-linked_ roles are directly linked to an AWS service. That service can assume the role to perform actions, similar to how services or people can assume _service_ roles. The main difference is that _service-linked_ roles are *owned* by the service that they belong do. You can view them in your account, but you cannot edit them or their permissions, even if you are an administrator. To delete them, you would have to delete the related services since those services wouldn't function properly without their role.

So why would we want that? When does this make sense, and why not just use service roles instead since we can manage those and we have more control over them?

### Why use service-linked roles?

Service-linked roles are predefined by the service and they include all of the required permissions for that service to be able to call other AWS services, which helps make setting up that service easier because you don’t have to think about the required permissions. 

The linked service defines the permissions of its service-linked roles, and usually, only that service can assume that role. Service-linked roles can't be assumed or attached to any other IAM entity.

[ :octicons-link-external-16: Helpful answer from StackOverflow](https://stackoverflow.com/questions/65970798/aws-service-role-vs-service-link-role)

## Tools that make using IAM Roles easier

- [ :octicons-link-external-16: aws-vault](https://github.com/99designs/aws-vault) - A vault for securely storing and accessing AWS credentials in development environments
- [ :octicons-link-external-16: CommonFate's Granted](https://docs.commonfate.io/granted/getting-started) - Command line interface (CLI) tool which simplifies access to cloud roles and allows multiple cloud accounts to be opened in your web browser simultaneously. The goals of Granted are:

## Sources

- A lot of the content on this page comes from Cybr's course: [:octicons-link-external-16: "Practical Guide to AWS IAM Roles"](https://cybr.com/courses/practical-guide-to-aws-iam-roles/) ^[Free&Paid]^ ^[Sponsored]^
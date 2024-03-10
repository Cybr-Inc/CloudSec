---
icon: material/identifier
---

## About IAM Identity Center
[ :octicons-link-external-16: Identity Center](https://aws.amazon.com/iam/identity-center/) used to be known as AWS SSO, and this gives us a hint about what the service is and what it does (if you're familiar with the concept of Single Sign-On).

Identity Center is a service that centralizes user identity access management for AWS resources and applications, especially (but not limited to) when dealing with multiple AWS accounts. It can make it easier to manage user access to various AWS accounts and even third-party applications.

Identity Center enables organizations to set up a single sign-on (SSO) experience for their users, allowing them to access multiple AWS accounts and applications using a single set of credentials.

### Identity Sources
You can use three types of identity sources:

- Identity Center directory - this is the default identity source when enabling Identity Center, and its the AWS-native approach. Recommended unless you are already using AD or an external IdP (great for individuals or small businesses)
- Active Directory - Recommended if your organization uses AWS Managed Microsoft Active Directory or a self-managed AD installation.
- External identity provider - Recommended if your or uses something like Okta or Microsoft Entra ID

Once set up, your users can use an AWS access portal URL to access their assigned AWS resources and applications. The URL will look something like this: `https://cloudsecexample.awsapps.com/start` (if using a custom subdomain; or random characters/digits if not)


## How to enable Identity Center

1. In your console, search for and go to "IAM Identity Center"
2. Follow the on-screen setup steps, starting with "Confirm your identity source" (refer to the identity source explanation above)

Helpful resources:

- [ :octicons-link-external-16: (Console) AWS temporary credential best practice for developer access - console setup](https://vincenttjia.com/aws-temporary-credential-best-practice-for-developer-access-console-setup)

- [ :octicons-link-external-16: (Terraform) AWS temporary credential best practice for developer access](https://vincenttjia.com/aws-temporary-credential-best-practice-for-developer-access)


## Difference between IAM Users/Groups and Identity Center Users/Groups
There's a lot of confusion out there about the differences between IAM Users and IAM Groups versus Identity Center Users and Groups. It's understandable -- the names are very similar, and they all live under the IAM service. So what are their differences, and when should you use which?

While IAM Users still have some use cases where they make sense to use, the general best practice is moving towards not using IAM Users and instead using Identity Center Users.

There are multiple reasons that it's now the recommended approach.

One reason is because it prevents us from having to create a bunch of users with brand new credentials. If your organization is using Microsoft Active Directory, for example, why force your employees to create brand new usernames and passwords to do their jobs in AWS? Why not let them use their existing credentials to then receive temporary credentials that they can assume? That's a big part of what Identity Center offers.

A second reason is that IAM Users need to be created _per account_, whereas Identity Center identities can be managed in a centralized account to grant access to all of your accounts without re-creating a bunch of users, and once again, having to have a username and password for each user in each account.

A third reason is because once you authenticate through the AWS access portal, you can easily get temporary credentials and you can use those credentials with the AWS CLI or SDKs to access resources in your AWS account(s). It removes the need to set up long-term access keys. 
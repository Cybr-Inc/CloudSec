---
icon: material/account-clock
---

I (Christophe) have a nasty habbit of sticking to doing things I'm familiar with and resisting change. So even though AWS has launched options that make using long-term access keys almost a thing of the past over the last few years, I still had some access keys being used for a couple of use cases. So I decided to roll up my sleeves and work on removing every long-term access key from all of my AWS accounts. Here's some advice for how you can do it too.

## Alternative to access keys

Instead of using long-term access keys, we can use [ :octicons-link-external-16: temporary security credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) through the AWS Security Token Service (STS).

Temporary credentials work almost the same as long-term access keys but with two major differences:

1. Temporary security credentials are short-term and expire after anywhere from a few minutes to hours ([ :octicons-link-external-16: length of time depends](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getfederationtoken)). After they’ve expired, they no longer work and can’t be re-used.
2. Temporary security credentials are generated only when they are needed instead of being stored with the user. New credentials can be generated as they expire, or even before they expire, and you don’t have to revoke them when they’re no longer needed

You can use these temporary security credentials with IAM roles and identity federation ([ :octicons-link-external-16: more info here](/aws/iam/identity-center/)), which means that once you get rid of access keys, you can start to get rid of (most) IAM Users.

## Remove unused long-term access keys

- Find them by looking at your [ :octicons-link-external-16: credentials report](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_finding-unused.html#finding-unused-access-keys)
- You can also use AWS Config with [ :octicons-link-external-16: `iam-user-unused-credentials-check`](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_finding-unused.html#finding-unused-access-keys) for an automated and regular approach, but Config can get expensive
- Config also integrates with Security Hub controls

The [ :octicons-link-external-16: CIS Benchmark](https://docs.aws.amazon.com/securityhub/latest/userguide/iam-controls.html#iam-8) recommends removing access keys that haven't been used in 90 days, but that's up to your comfort level and could be shorter. 

## Prevent creating new access keys

This one might hurt a bit for those still defaulting to using them, but it's also a very effective approach at changing old habits. Prevent people from creating new access keys entirely.

An easy way of doing this is by creating this policy:

```
{ 
    "Version": "2012-10-17", 
    "Statement": { 
        "Effect": "Deny", 
        "Action": "iam:CreateAccessKey", 
        "Resource": "*" 
    } 
}
```

You can apply this as an [ :octicons-link-external-16: SCP](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html) organization-wide. 

This will also help protect against the [iam:CreateAccessKey PrivEsc](/aws/security-assessments/pentesting-red-teaming/privilege-escalation/iam-privilege-escalation/#iamcreateaccesskey).

## Remove all other access keys and IAM users

The above steps are easy wins, but this next step is where it gets more challenging. The path to being long-term access key free is to then look for:

- AWS resources using access keys - use IAM Roles instead
- Find external applications using long-term access keys - use IAM Roles Anywhere for workloads, and Identity Center / SSO for user access
- Change how your humans access your AWS environments - with Identity Center
- Prevent the creationg of new users - by applying an SCP that applies to all of your AWS accounts and that prevents `iam:CreateUser`

Start with removing unused long-term access keys and preventing creation of new keys first, and then go through this section. Keep in mind too that some use cases may still require IAM Users, so some organizations may not be able to completely eliminate them. However, they need to become the exception, not the norm.
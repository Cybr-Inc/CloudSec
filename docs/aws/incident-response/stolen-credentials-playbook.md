---
icon: material/form-textbox-password
---

# About this playbook
My AWS access keys were exposed and are being exploited by a threat actor. What do I do?!?! Step 1: Don’t panic! There are playbooks that can help you in this situation.

## Cheat Sheet
<figure markdown>
  ![Image title](https://cybr.com/wp-content/uploads/2024/01/ir-playbook-iam_credentials_exposure-scaled.jpeg){ width="500" loading=lazy }
    <figcaption>[:octicons-link-external-16: Hi-res download](https://cybr.com/wp-content/uploads/2024/01/ir-playbook-iam_credentials_exposure-scaled.jpeg)</figcaption>
</figure>


# The playbook
AWS has multiple Incident Response playbooks you can access for free and has one exactly for this scenario. Not only can this help you in an emergency situation, but these playbooks have important information that can help you study for the AWS Certified Security Specialty exam.

Let’s take a closer look at what I mean with the IAM credential exposure playbook

Incident response playbook for IAM credentials exposure and exploitation

📚 This playbook tells you what to do step-by-step if you believe you have compromised credentials that were used by an actor to create resources, modify configurations, establish persistence, etc…

At a high level, you want to figure out:

- 💥 Vulnerabilities exploited
- 💥 Exploits and tools observed
- 💥 Actor’s intent
- 💥 Actor’s attribution
- 💥 Damage inflicted to the environment and business

To ultimately achieve your recovery point: returning to the original and hardened configuration

You can get there by following response steps from NIST:

- ✅ Analysis
- ✅ Containment
- ✅ Eradication
- ✅ Recovery
- ✅ Post-incident activity

This isn’t always a linear process. For example, once you’ve performed basic analysis and identified it’s not a false positive, you may want to contain by disabling exposed credentials before moving on to further analysis.

In this playbook, AWS recommends using Amazon Athena for most of the analysis. Athena can perform advanced querying across multiple AWS sources like:

- ALB
- CloudFront
- CloudTrail
- GuardDuty
- and a lot more…

Directly from Amazon S3 by using SQL. It basically kind of turns your S3 data into an SQL database without directly modifying the S3 data (if that makes sense – if not, let me know in the comments below).

While not mentioned in the linked playbook, you could even visualize query results in QuickSight.

From this resource alone, you can learn:

- 📌 What AWS services might be involved in incident response
- 📌 Data pipelines for IR (ie: CloudTrail -> S3 -> Athena -> QuickSight)
- 📌 What Athena is used for and what it can do
- 📌 Different categories of containment and steps in AWS (depending on different factors and scenarios)
- 📌 What types of IAM roles you might use in this situation

These are all potential topics you can expect to see on your AWS Certified Security Specialty exam, and of course, they’re important topics to know when working within AWS if you’re part of a response team.

Sources and more info:

1. [ :octicons-link-external-16: IAM credential exposure playbook](https://github.com/aws-samples/aws-incident-response-playbooks-workshop/blob/main/playbooks/credential_exposure/IAM_credential_exposure.md)
2. [ :octicons-link-external-16: AWS Security Incident Response Guide](https://docs.aws.amazon.com/whitepapers/latest/aws-security-incident-response-guide/containment.html)
3. [ :octicons-link-external-16: NIST 800-61](https://csrc.nist.gov/pubs/sp/800/61/r2/final)
4. [AWS Incident Response with CloudTrail and Athena Course](https://cybr.com/courses/beginners-guide-to-aws-cloudtrail-for-security/) ^[Paid][Sponsored]^
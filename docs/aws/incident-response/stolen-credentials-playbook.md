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


## The playbook for IAM credentials exposure and exploitation
AWS has multiple Incident Response playbooks you can access for free and [has one exactly for this scenario](https://github.com/aws-samples/aws-incident-response-playbooks-workshop/blob/main/playbooks/credential_exposure/IAM_credential_exposure.md). These can be helpful in an emergency situation, but also in taking pre-emptive action and in helping you develop your own playbooks. 

At a high level, you want to figure out:

- 💥 Vulnerabilities exploited
- 💥 Exploits and tools observed
- 💥 Actor’s intent
- 💥 Actor’s attribution
- 💥 Damage inflicted to the environment and business

To ultimately achieve your recovery point: returning to the original plus hardened configuration.

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
- and more

Directly from Amazon S3 by using SQL. It basically kind of turns your S3 data into an SQL database without directly modifying the S3 data.

Of course, you can use other tools. Maybe you've only got CloudTrail Lake up and running, in which case you may have limited visibility since you may not be pulling the same type of data. Or, maybe you've got a third party SIEM running.

## Common causes of compromised access keys
100% of compromised access keys happen because you're using access keys. This might seem obvious, but you should really be [moving away from using access key](/aws/iam/get-rid-of-access-keys/).

- Phishing (social engineering in general)
- Leaked environment variables
- Hardcoded credentials in code
- Malware

Other possibilities include:

- SSRF (Server-Side Request Forgery)
- Breached 3rd party
- Internal threat actors

## Steps

1. [ANALYSIS] Validation - Validate alert by checking ownership of exposed credential
2. [ANALYSIS] Scope - Gather info about what's happened, inventory resources involved, determine it's not a false positive
3. [ANALYSIS] Impact - What's the business impact?
4. [CONTAINMENT] How you contain here depends on a lot of factors, highlighted in the cheat sheet above
5. [ERADICATION] ie: delete affected IAM Users, resources, etc... then apply security updates and hardening
6. [RECOVERY] If needed / as needed. ie: re-create destroyed resources, restore data, etc...
7. [POST-INCIDENT ACTIVITY] Recommendations include automating containment and eradication steps, saving queries used to investigate for future use, eliminating long-term credentials, and enabling additional alerts

---

## Sources and more info:

1. [ :octicons-link-external-16: IAM credential exposure playbook](https://github.com/aws-samples/aws-incident-response-playbooks-workshop/blob/main/playbooks/credential_exposure/IAM_credential_exposure.md)
2. [ :octicons-link-external-16: AWS Security Incident Response Guide](https://docs.aws.amazon.com/whitepapers/latest/aws-security-incident-response-guide/containment.html)
3. [ :octicons-link-external-16: NIST 800-61](https://csrc.nist.gov/pubs/sp/800/61/r2/final)
4. [AWS Incident Response with CloudTrail and Athena Course](https://cybr.com/courses/beginners-guide-to-aws-cloudtrail-for-security/) ^[Paid][Sponsored]^
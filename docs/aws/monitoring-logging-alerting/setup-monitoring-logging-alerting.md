---
icon: material/wrench-cog-outline
---

# Setting up monitoring, logging, and alerting for security

Before you dedicate a significant amount of time doing this, ask yourself first if it's worth the effort to build from scratch. There are many solutions out there that already exist and that may satisfy your requirements, or that will provide a solid starting point. If that's the case, it may be best to use those pre-existing solutions instead, so that you can focus on your core competency.

For example, the [ :octicons-link-external-16: AWS Security Survival Kit (ASSK)](https://github.com/zoph-io/aws-security-survival-kit) provides a great starting point. But, if you really want to build out your own solution or if you want to add on to something like the ASSK because maybe it doesn't monitor or alert for something you want/need, then read on.

## CloudTrail

First things first, we need to make sure your CloudTrail settings are in order.

CloudTrail is the heart of AWS logging. It gathers activity in your AWS environment, and it saves some (or all) of it, depending on how you have it configured. This enables other AWS or third-party services to then act on these CloudTrail-generated logs.

If you're not already familiar with CloudTrail, this free and short course is a must:

<iframe width="560" height="315" src="https://www.youtube.com/embed/1ZKuwyATV3c?si=EJGKNpg60cC4FhcW" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Otherwise, here's a TL;DW or quick refresher.

Coming soon

## CloudWatch

If CloudTrail is the heart of logging, then CloudWatch is the part of the brain that can make sense of the data being collected by CloudTrail.

While CloudTrail does offer some features that let you query the logged data that it's collecting (like Event History and CloudTrail Lake), Lake is a relatively recent feature and it's quite expensive. 

CloudWatch has been around for a long time, and it has _a lot_ more functionality out of the box. You can use it to create metrics, set up alarms based on those metrics, create visual dashboards with graphs, and run queries against data sets.

On top of that, CloudWatch has a generous free tier so it's great for smaller companies or pilot projects.

## EventBridge

Continuing with our anatomy analogies, if CloudTrail is the heart and CloudWatch is part of the brain, then EventBridge is the central nervous system.

EventBridge is a large service that you can use for quite a few things, but in the context of this page, it really only reacts to certain events and then either pushes that event to another service (like CloudWatch or SNS), or first _transforms_ the event's data and _then_ pushes it to another service.

This means we can use EventBridge to send out alerts through SNS to an email address or phone number. We can also have it clean up the event information so that it's easier for the recipient to understand what the alert is about.

Here's an example:

```
COMING SOON
```

## Athena

While the above solutions alone can give you a great starting point for monitoring, logging, and alerting in AWS, you can take it a step further by using a service like Athena which can help you with incident response. We have a separate and dedicated section on the site for incident response though, so while we're mentioning it here, we won't go into further detail on this page.
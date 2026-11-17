# Week 11: Monitoring and Change Evidence

## HarborTech Ticket Summary
TKT-2026-0011: Riverside Goods reported degraded website performance following a change window and suspected that the web tier Auto Scaling Group no longer matched its approved capacity settings. The objective of this investigation is to reconstruct the incident using monitoring, audit, log, and configuration evidence to determine what changed, when the change occurred, and how it relates to the observed service degradation. The evidence indicates that an Auto Scaling Group configuration change reduced web-tier capacity from its approved state. Shortly afterward, application response times increased significantly, application logs reported only one healthy target, and a CloudWatch latency alarm entered the ALARM state. The ticket focuses on correlating CloudTrail, AWS Config, CloudWatch metrics, CloudWatch logs, and CloudWatch alarms to determine whether the configuration change is related to the website slowdown while avoiding unsupported attribution to any specific individual or process. The primary change-management concern is that the web tier configuration was modified before the performance degradation was detected, requiring an evidence-based review to establish the incident timeline, identify the most strongly supported cause, and document any remaining uncertainties.

## Client Impact
This issue impacts Riverside Goods by slowing website performance and reducing the quality of service experienced by customers. As response times increase, users may experience delays when accessing the website, potentially affecting customer satisfaction and business operations.
Unauthorized or unapproved configuration changes can reduce application capacity and create service disruptions, while delayed detection allows performance issues to persist longer before corrective action is taken. Weak audit evidence can also make investigations more difficult by slowing the process of determining what changed and when it occurred. In this incident, the reduction in web-tier capacity was followed by increased response times and alarm activity, demonstrating the importance of monitoring, audit logs, and configuration tracking for maintaining service availability and supporting timely incident response.

## AWS Services Involved

- Amazon CloudWatch
- CloudWatch Metrics
- CloudWatch Logs
- CloudWatch Alarms
- AWS CloudTrail
- AWS Config
- Amazon Athena
- Amazon EC2 Auto Scaling
- Auto Scaling Groups (ASG)
- Target Response Time Metrics
- Monitoring
- Audit Evidence
- Configuration History
- Incident Response
- Event Correlation
- Log Analysis
- Change Management
- Capacity Management
- Resource Baselines
- Virtualized Infrastructure
- Assumed Role Sessions
- API Activity

### Summary

This investigation used AWS monitoring, auditing, logging, and configuration tracking services to reconstruct an unknown infrastructure change and its impact on application performance. CloudWatch metrics, logs, and alarms established the incident window and confirmed degraded website performance. CloudTrail provided audit evidence showing an Auto Scaling Group update, while AWS Config verified the before-and-after configuration state. Auto Scaling concepts were central to the investigation because web-tier capacity was reduced before the performance degradation occurred. By correlating monitoring, audit, log, and configuration evidence, the incident timeline was reconstructed and a supported conclusion was reached while preserving unresolved attribution questions as assumptions.
## Virtualization Connection
Explain how monitoring and audit services provide visibility into software-defined infrastructure by recording resource behavior, configuration changes, and management activity.

## Evidence Reviewed

### CloudWatch Metrics

The evidence packet shows the `TargetResponseTime` metric increasing at **14:07:00**, rising from **0.3 seconds** to **2.4 seconds**. This is the first direct monitoring evidence of degraded website performance and establishes when the slowdown became observable.

### CloudWatch Alarm History

At **14:09:00**, the `rg-web-latency-high` CloudWatch alarm transitioned from **OK** to **ALARM**. The alarm entered the ALARM state after the latency threshold was exceeded for two consecutive datapoints, confirming that the elevated response times were sustained rather than a brief spike.

### Application or System Logs

At **14:07:30**, the application log recorded the warning:

```text
WARN upstream response 2.38 s, healthy targets 1
```

The log confirms that only one healthy target was available while elevated response times were being observed.

### CloudTrail Management Events

At **14:05:12**, CloudTrail recorded an `UpdateAutoScalingGroup` management event. The event was performed by an **assumed role session** associated with the web operations role and targeted the web-tier Auto Scaling Group.

### AWS Config Resource History

At **14:06:03**, AWS Config recorded a configuration change for `rg-web-asg`. The recorded configuration reflects a reduction in the Auto Scaling Group capacity settings and provides an authoritative record of the resource state after the update.

### Configuration Changes

The evidence packet shows the following capacity changes:

| Setting | Before | After |
|----------|----------|---------|
| Desired Capacity | 3 | 1 |
| Max Size | 4 | 1 |

These values recorded by AWS Config match the values contained in the CloudTrail request parameters.

### Event Timestamps

| Time | Evidence |
|--------|----------|
| 14:05:12 | CloudTrail `UpdateAutoScalingGroup` API call |
| 14:06:03 | AWS Config records Auto Scaling Group configuration change |
| 14:07:00 | CloudWatch target response time increases |
| 14:07:30 | Application log reports one healthy target |
| 14:09:00 | CloudWatch latency alarm enters ALARM state |

### User or Role Identity

The CloudTrail event identifies the actor as an **assumed role session** associated with the web operations role. The evidence packet does not identify a specific individual and does not distinguish between user-driven activity, automation, or another authorized service using the role.

### API Actions

The recorded API action was:

```text
UpdateAutoScalingGroup
```

The request parameters set:

```text
desiredCapacity = 1
maxSize = 1
```

The API action directly corresponds to the Auto Scaling Group settings later recorded by AWS Config.

### Resource State Before and After a Change

The affected resource was the `rg-web-asg` Auto Scaling Group.

| Attribute | Before Change | After Change |
|-----------|--------------|-------------|
| Desired Capacity | 3 | 1 |
| Max Size | 4 | 1 |

AWS Config confirms the resource no longer matched its previous approved capacity configuration after the change.

### Athena Query Results

No Athena query results were included in the supplied evidence packet. Therefore, no Athena-generated evidence was used to support the incident reconstruction.

### Incident Timeline

At **14:05:12**, CloudTrail recorded an `UpdateAutoScalingGroup` API call that reduced Auto Scaling Group capacity. At **14:06:03**, AWS Config confirmed the corresponding configuration change from a desired capacity of 3 to 1 and a maximum size of 4 to 1. At **14:07:00**, CloudWatch metrics showed target response time increasing from 0.3 seconds to 2.4 seconds. At **14:07:30**, application logs reported only one healthy target serving requests. At **14:09:00**, the `rg-web-latency-high` alarm entered the ALARM state after sustained elevated latency. The evidence shows that the capacity reduction occurred before the observed performance degradation and alarm activity.

## Operational Analysis
State the incident window and reconstruct the timeline in event order.
Correlate monitoring, audit, and configuration evidence to identify what happened and when.
Separate your supported findings from your assumptions and list any unresolved questions.

## Recommendation
Recommend the next operational action supported by the evidence.
Explain whether the situation requires continued monitoring, configuration review, containment, correction, or another approved response.

## Escalation Notes
Document any suspected unauthorized activity, production configuration change, security concern, or unresolved condition that requires escalation.
If no escalation is required, state that clearly.

## Lessons Learned
Explain what Week 11 taught you about monitoring, audit trails, configuration history, incident timelines, and evidence-based troubleshooting.

## Professional Vocabulary
Define the important Week 11 terms in your own words.
Include terms such as:
- Monitoring
- Metric
- Log
- Alarm
- CloudWatch
- CloudTrail
- AWS Config
- Audit Trail
- Configuration History
- Incident Timeline
- Evidence Correlation

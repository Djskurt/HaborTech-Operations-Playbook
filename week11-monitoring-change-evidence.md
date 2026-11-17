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
### Incident Window

The incident became observable at **14:07:00** when CloudWatch metrics showed target response time increasing from **0.3 seconds** to **2.4 seconds**. The issue was confirmed at **14:09:00** when the `rg-web-latency-high` alarm entered the **ALARM** state after sustained elevated latency. Based on the available evidence, the incident window begins no later than **14:07** and continues through at least **14:09**.

### Reconstructed Timeline

At **14:05:12**, CloudTrail recorded an `UpdateAutoScalingGroup` API call from an assumed role session. The request set the Auto Scaling Group `desiredCapacity` to **1** and `maxSize` to **1**.

At **14:06:03**, AWS Config recorded the corresponding configuration change for `rg-web-asg`, confirming that the desired capacity changed from **3 to 1** and the maximum size changed from **4 to 1**.

At **14:07:00**, CloudWatch metrics showed target response time increasing from **0.3 seconds** to **2.4 seconds**, indicating degraded website performance.

At **14:07:30**, application logs reported:

```text
WARN upstream response 2.38 s, healthy targets 1
```

This confirms that only one healthy target was serving traffic during the performance degradation.

At **14:09:00**, the `rg-web-latency-high` CloudWatch alarm transitioned from **OK** to **ALARM**, confirming that the elevated response times persisted long enough to breach monitoring thresholds.

### Supported Findings

The evidence shows that the web-tier Auto Scaling Group capacity was reduced before website performance degraded. CloudTrail recorded the capacity change, AWS Config confirmed the before-and-after configuration state, application logs showed only one healthy target, and CloudWatch metrics and alarms documented the resulting increase in response latency. The strongest supported finding is that the reduction of Auto Scaling Group capacity from its approved configuration to a single-instance configuration contributed to the website slowdown observed by Riverside Goods.

### Assumptions and Unresolved Questions

The evidence does not identify who or what initiated the change. CloudTrail records show that an assumed role session performed the API action, but the available records do not determine whether the change originated from a specific user, an automated process, or another AWS service. Because attribution cannot be verified from the evidence packet alone, the origin of the change remains an unresolved question and should be treated as an assumption rather than a confirmed finding.

## Recommendation

The evidence supports conducting a configuration review of the `rg-web-asg` Auto Scaling Group and comparing its current settings against the approved capacity baseline. Because the reduction in capacity occurred immediately before the observed performance degradation, the configuration change should be reviewed by the responsible cloud operations team to determine whether it was authorized and whether corrective action is required. Continued monitoring of CloudWatch metrics, logs, and alarms is recommended to track application performance and verify that response times remain within expected thresholds. Escalation to a cloud operations analyst or engineer is appropriate for validation of the approved configuration and evaluation of any rollback or remediation activities. No containment actions, account restrictions, or attribution decisions are supported by the available evidence. The origin of the change should remain under review until additional evidence confirms whether the action was performed by a user, automation process, or another authorized service.

## Escalation Notes
As a cloud operations intern, I do not have authority to approve, reverse, or modify production configurations. Based on the evidence, this incident should be escalated to the appropriate cloud operations analyst or engineer for review.
The escalation is required because the rg-web-asg Auto Scaling Group was changed from its approved capacity configuration, and the change occurred immediately before degraded application performance was observed. The operations team should verify whether the change was authorized and determine whether the current configuration remains appropriate for the workload. No evidence currently supports escalating this incident as a security event. The available records show that an assumed role session performed the configuration change, but the evidence does not identify a specific individual or determine whether the action originated from a user, automation process, or another authorized service. This unresolved attribution question should be included in the escalation for further review. No direct production actions were taken as part of this investigation. The recommendation is limited to escalation, configuration validation, and continued monitoring by personnel authorized to manage production resources.
## Lessons Learned
Week 11 reinforced the importance of using multiple evidence sources to investigate incidents and avoid making assumptions. Monitoring data from CloudWatch metrics and alarms helped establish when the performance issue became visible, while application logs provided additional context about the state of the environment during the incident. The investigation also demonstrated the value of audit trails and configuration history. CloudTrail showed when a configuration change was made, and AWS Config confirmed the exact before-and-after state of the affected resource. By correlating these records with monitoring and log data, it was possible to reconstruct the incident timeline and identify the most strongly supported explanation for the website slowdown. Most importantly, the exercise emphasized evidence-based troubleshooting. Rather than starting with a suspected cause or individual, the investigation focused on what the records could prove, the order in which events occurred, and which conclusions were supported by multiple sources. Any gaps in attribution remained clearly identified as assumptions or unresolved questions instead of being treated as confirmed facts.

## Professional Vocabulary
### Monitoring

Monitoring is the process of continuously watching systems and applications to identify performance issues, failures, or unusual behavior before they become larger problems. It helps operations teams understand how services are performing over time.

### Metric

A metric is a numerical measurement collected from a system, application, or resource. Metrics help show trends and changes in performance, such as whether response times, CPU usage, or network traffic are increasing or decreasing.

### Log

A log is a record of activity generated by an application, operating system, or cloud service. Logs provide detailed information about what was happening at a specific moment and can help investigators understand the events surrounding an incident.

### Alarm

An alarm is an automated notification that triggers when a monitored value crosses a defined threshold. It helps alert administrators to conditions that may require attention, but it does not explain why the condition occurred.

### CloudWatch

Amazon CloudWatch is AWS's monitoring platform used to collect metrics, logs, and alarms from cloud resources and applications. It helps teams detect performance issues and track the health of their environments.

### CloudTrail

AWS CloudTrail records actions performed within an AWS environment. It helps investigators determine what activity occurred, when it happened, and which identity or service initiated the action.

### AWS Config

AWS Config tracks the configuration of supported AWS resources and records how those configurations change over time. It allows teams to compare previous and current resource settings during investigations.

### Audit Trail

An audit trail is the historical record of actions performed within a system. It provides evidence that can be reviewed to understand what activity took place and helps support accountability during investigations.

### Configuration History

Configuration history is the record of how a resource's settings have changed over time. It helps teams determine when a resource was modified and compare previous settings to current ones.

### Incident Timeline

An incident timeline is a chronological sequence of events related to an issue. By placing records in time order, investigators can better understand what happened first, what followed, and how events may be related.

### Evidence Correlation

Evidence correlation is the process of comparing information from different sources and connecting related events to build a complete picture of what happened. Instead of relying on a single record, investigators look at metrics, logs, audit records, alarms, and configuration history together to verify whether they support the same conclusion. For example, if a configuration change is recorded in CloudTrail, the same change appears in AWS Config, and performance issues are later observed in CloudWatch, those separate records can be correlated to show how the events are related. Evidence correlation helps ensure that conclusions are based on multiple supporting facts rather than assumptions or isolated observations.

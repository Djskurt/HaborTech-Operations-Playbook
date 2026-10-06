# Week 5: Scaling, Load Balancing, and DNS

## HarborTech Ticket Summary
TKT-2026-0005: This ticket focused on evaluating whether the Riverside Goods application is prepared for an expected seasonal traffic increase. 
The environment currently depends on a single EC2 instance and a single public endpoint, creating both capacity and availability concerns. 
The goal was to investigate AWS evidence related to Auto Scaling, Elastic Load Balancing, 
and Route 53 and determine which controls are supported by evidence and which assumptions still require verification.

## Client Impact
The client expects a significant increase in traffic during an upcoming promotion. 
Previous peak utilization reached 92% CPU, indicating that the current server is already operating near its limits during busy periods. 
If traffic increases further, users may experience slow performance, failed requests, or service interruptions. 
Because the application relies on a single server and a single endpoint, any failure could affect all users and business operations.

## Provided Ticket Evidence
The following information was supplied by HarborTech and should be treated as ticket evidence rather than AWS evidence I personally collected:

- Previous promotion traffic reached 92% CPU utilization.
- Promotion traffic is expected to increase significantly.
- Proposed Auto Scaling design: minimum 2, desired 2, maximum 6 instances.
- Proposed Application Load Balancer reports both test targets healthy.
- A secondary recovery endpoint is available.
- Route 53 failover has not been confirmed.

## AWS Commands Used
Auto Scaling Investigation
```bash
aws autoscaling describe-auto-scaling-groups \
  --query 'AutoScalingGroups[].{Name:AutoScalingGroupName,Min:MinSize,Desired:DesiredCapacity,Max:MaxSize}' \
  --output table
```
```bash
aws autoscaling describe-auto-scaling-groups \
  --query 'AutoScalingGroups[].{Name:AutoScalingGroupName,Min:MinSize,Desired:DesiredCapacity,Max:MaxSize}' \
```
Output:
```text
[]
```
Load Balancing Investigation
```bash
aws elbv2 describe-target-groups \
  --query 'TargetGroups[].{Name:TargetGroupName,TargetGroupArn:TargetGroupArn,Protocol:Protocol,Port:Port}' \
  --output table
```
```bash
aws elbv2 describe-target-groups \
  --query 'TargetGroups[].{Name:TargetGroupName,TargetGroupArn:TargetGroupArn,Protocol:Protocol,Port:Port}' \
```
Output:
```text
[]
```
Route 53 Investigation 
```bash
aws route53 list-hosted-zones \
  --query 'HostedZones[].{Name:Name,Id:Id,Private:Config.PrivateZone}' \
  --output table
```
```bash
aws route53 list-hosted-zones \
  --query 'HostedZones[].{Name:Name,Id:Id,Private:Config.PrivateZone}' \
```
Output:
```text
[]
```
## AWS Evidence Collected
Record the relevant values returned by your own AWS environment. Do not paste entire responses when a few values communicate the evidence.

## Virtualization Connection
Explain how scaling and load balancing allow virtual compute capacity to expand, contract, and distribute workloads without relying on one physical server.

## Operational Analysis
Explain what the combined ticket evidence and AWS evidence show about capacity, target health, traffic distribution, DNS routing, and remaining unknowns.

## Recommendation
Recommend the next operational action supported by the evidence. Explain how it addresses the identified risk while considering availability and cost.

## Escalation Notes
Document any production scaling, load balancer, DNS, or configuration change that requires approval or additional support. If no escalation is required, state that clearly.

## Lessons Learned
Explain what Week 5 taught you about evidence-based scaling, load balancing, health checks, DNS, and cloud operations.

## Professional Vocabulary
Define the important Week 5 terms in your own words, such as elasticity, scalability, load balancer, target group, health check, Auto Scaling group, launch template, desired capacity, Route 53, and failover.

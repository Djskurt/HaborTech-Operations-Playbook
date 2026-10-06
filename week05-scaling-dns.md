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
The AWS CLI investigations returned the following results:

- No Auto Scaling Groups returned.
- No Target Groups returned.
- No Route 53 Hosted Zones returned.

These results indicate that I was unable to verify Auto Scaling, load balancing, target health configuration, or Route 53 failover capabilities in my assigned AWS environment.
## Virtualization Connection
Virtualization allows compute resources to be treated as flexible and replaceable rather than tied to a specific physical server. 
Auto Scaling can automatically add or remove EC2 instances as demand changes, allowing capacity to expand and contract based on workload requirements. 
Load balancing distributes requests across multiple virtual servers so that no single instance must handle all traffic. 
Together, these services improve scalability, resilience, and operational flexibility while reducing dependence on a single physical system.

## Operational Analysis
The ticket evidence supports a capacity concern because previous utilization reached 92% CPU and additional traffic is expected during the promotion. 
The proposed Auto Scaling design would help address this risk by allowing additional instances to be launched when demand increases.

However, my AWS investigation returned no Auto Scaling Groups, meaning I could not verify that scalable capacity is currently configured.

The ticket also suggests that healthy test targets exist behind an Application Load Balancer. However, my AWS investigation returned no Target Groups, 
so I was unable to verify load-balancing configuration or target health in the assigned environment.

The ticket states that a secondary recovery endpoint is available, but my AWS investigation returned no Route 53 Hosted Zones. 
As a result, I could not verify DNS records, health checks, failover routing policies, or any Route 53 failover configuration.

Based on the combined evidence, the capacity risk and traffic-distribution risk are supported. DNS failover readiness remains unverified because the required AWS evidence was not present in the environment.

## Recommendation
I recommend validating and implementing Auto Scaling and Application Load Balancing controls to address the capacity and availability risks identified in the ticket. 
Auto Scaling would provide additional capacity during periods of increased demand, 
while load balancing would distribute requests across multiple healthy instances rather than relying on a single endpoint.

At this time, I do not recommend claiming Route 53 failover readiness because the AWS investigation did not verify hosted zones, health checks, or failover routing policies. 
Additional DNS configuration review and testing should be completed before failover capability is considered operational.

These recommendations address the identified risks while allowing HarborTech to scale resources only when needed, helping balance availability requirements with operational costs.

## Escalation Notes
Implementation of Auto Scaling Groups, Application Load Balancers, Route 53 failover records, DNS routing policies, and related production configuration changes should follow the organization's established change-management and approval procedures.

As an intern, I can recommend further validation and implementation based on the evidence collected, but approval and execution of production architecture changes should be performed by authorized personnel.

## Lessons Learned
This investigation reinforced the importance of separating ticket assumptions from AWS evidence. Auto Scaling, load balancing, and DNS failover solve different operational problems and should not be treated as interchangeable solutions. Auto Scaling addresses capacity, load balancing distributes traffic across healthy resources, and Route 53 can provide DNS-level failover when properly configured. Cloud operations decisions should be based on verified evidence rather than assumptions about what may already be deployed.

## Professional Vocabulary

Elasticity
 The ability of cloud resources to automatically increase or decrease based on demand.

Scalability
 The ability of a system to handle increased workload by adding resources.

Load Balancer
 A service that distributes incoming traffic across multiple servers.

Target Group
 A collection of resources, such as EC2 instances, that receive traffic from a load balancer.

Health Check
 A test used to determine whether a resource is healthy and able to receive traffic.

Auto Scaling Group (ASG)
 A service that automatically launches or terminates EC2 instances to maintain a desired level of capacity.

Launch Template
 A reusable configuration that defines how new EC2 instances should be created.

Desired Capacity
 The number of instances an Auto Scaling Group attempts to maintain during normal operation.

Minimum Capacity
 The lowest number of instances that an Auto Scaling Group keeps running.

Maximum Capacity
 The highest number of instances an Auto Scaling Group can launch.

Route 53
 AWS's DNS service used to route users to application endpoints.

Failover
 The process of automatically redirecting traffic from an unhealthy primary resource to a healthy backup resource.

Target Health
 The status indicating whether a registered target is healthy enough to receive traffic from a load balancer.

Availability
 The ability of a service to remain operational and accessible to users.

Redundancy
 The use of multiple resources so that service can continue if one component fails.

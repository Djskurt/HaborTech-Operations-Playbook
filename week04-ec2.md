# Week 4: EC2 Evidence Lab

## HarborTech Ticket Summary
TKT-2026-0004: HarborTech received request from client known as Riverside Goods to deploy a new EC2 web server for an inventory application. The web server was not accessible through its public IPv4 address. 
The objective was to identify the cause of the connectivity issue using evidence-based troubleshooting, apply the smallest supported corrective action, verify functionality, and document the complete investigation.

## Client Impact
Users could not access the Riverside Goods website through HTTP. 
The outage prevented customers from reaching the organization's public-facing web page, resulting in loss of service availability. 
Although the EC2 instance was running, the website remained inaccessible until the network access issue was resolved.

## Environment and Resource Names
- Platform: Amazon EC2
- Instance Name: riverside-web-ec2_instance
- Operating System: Amazon Linux
- Web Server: Apache HTTP Server (httpd)
- Access Method: AWS Systems Manager Session Manager
- Metadata Service: IMDSv2
- Security Control: EC2 Security Group
- Security group name: riverside-web-djs443
- Instance Name Tag: RiversideGoods
- Resource Type: EBS-backed EC2 instance
- Troubleshooting Tool: AWS CloudShell


## AWS Documentation Evidence


## CloudShell Command Record


## Baseline Evidence


## Root-Cause Analysis


## Corrective Action


## Verification Evidence


## IMDSv2 and Guest Evidence


## Stop/Start Lifecycle Test


## Cleanup Evidence


## Escalation and Change-Control Notes


## Lessons Learned


## Professional Vocabulary


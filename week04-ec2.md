# Week 4: EC2 Evidence Lab

## HarborTech Ticket Summary
TKT-2026-0004: HarborTech received request from client known as Riverside Goods to deploy a new EC2 web server for an inventory application. The web server was not accessible through its public IPv4 address. 
The objective was to identify the cause of the connectivity issue using evidence-based troubleshooting, apply the smallest supported corrective action, verify functionality, and document the complete investigation.

## Client Impact
Users could not access the Riverside Goods website through HTTP. 
The outage prevented customers from reaching the organization's public-facing web page, resulting in loss of service availability. 
Although the EC2 instance was running, the website remained inaccessible until the network access issue was resolved.

## Environment and Resource Names
- Instance Name: riverside-web-ec2_instance
- Instance ID: i-03c3eaae0b12be279
- Operating System: Amazon Linux
- Web Server: Apache HTTP Server (httpd)
- Access Method: AWS Systems Manager Session Manager
- Metadata Service: IMDSv2
- Security Control: EC2 Security Group
- Security group name: riverside-web-djs443
- Security group ID: sg-03092df2d1bb85055
- Resource Type: EBS-backed EC2 instance
- Troubleshooting Tool: AWS CloudShell

## AWS Documentation Evidence
### SOURCE 1 -- SECURITY GROUPS
- Document title: Amazon EC2 security groups for your EC2 instances
- PDF page: 3133
- URL: https://docs.amazonaws.cn/en_us/AWSEC2/latest/UserGuide/ec2-security-groups.html
- Exact quote: “A security group acts as a virtual firewall for your EC2 instances to control incoming and outgoing traffic.”
- The problem indicated that users needed web access to the EC2-hosted website. This source confirms that security groups control inbound traffic, HTTP (port 80) rules necessary for users to reach the web server.
### Source 2 --  USER DATA
- Document title: Run commands when you launch an EC2 instance with user data input
- PDF page: 1757
- URL: URL: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html
- Exact quote: “When you launch an Amazon EC2 instance, you can pass user data to the instance that is used to perform automated configurato run scripts after the instance starts.”
- The problem showed that user data was intended to install and configure Apache during instance startup. This source confirms that user data can rscripts, but it does not prove the web service successfully installed or remained running after launch.
### Source 3 -- IMDSV2 OR EC2 LIFECYCLE
- Document title: Use instance metadata to manage your EC2 instance
- PDF page: 1663
- URL: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html
- Exact quote: “Instance metadata is data about your instance that you can use to configure or manage the running instance."
- The trouble ticket required validating information about the running EC2 instance. This source confirms that instance metadata can be used to manage the instance, supporting the verification steps performed during the investigation.

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


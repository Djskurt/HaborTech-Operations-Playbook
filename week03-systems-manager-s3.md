# Week 3: Systems Manager and S3

## HarborTech Ticket Summary
TKT-2026-0003: HarborTech received a request from Bright Path Nonprofits to evaluate more efficient methods for managing cloud operations and hosting public-facing content. 
The organization was performing routine administrative tasks across five EC2 instances using manual processes, resulting in approximately 90 minutes of reptitive administrative work,  creating unnecessary operational overhead and increasing the potential for inconsistent system management. 
Bright Path also required a simple public resource page containing program hours, images, and downloadable forms, and wanted to determine whether a dedicated web server was necessary to support the requirement.

The objective of the investigation was to determine whether AWS Systems Manager could reduce administrative effort through centralized management and automation 
while using Amazon S3 Static Website Hosting to deliver public content without deploying and maintaining an additional web server.
## Client Impact
Bright Path Nonprofits is experiencing operational inefficiencies due to repeated manual administration across multiple EC2 instances. 
Performing routine maintenance individually on each server increases administrative workload, consumes valuable staff time, and creates opportunities for inconsistent configurations and human error. 
As the environment grows, this approach becomes more difficult to manage and support efficiently.

The organization also requires a simple public resource page consisting of static content such as HTML, images, contact information, and downloadable forms. 
Deploying and maintaining an additional EC2 instance for this purpose would introduce unnecessary costs and administrative responsibilities, including operating system patching, security updates, monitoring, and server maintenance.
## AWS Services Involved
The following AWS Services and features were involved:

-AWS Systems Manager

-AWS Systems Manager Run Command

-AWS Systems Manager State Manager

-AWS Systems Manager Session Manager

-AWS Systems Manager Inventory

-AWS Systems Manager Parameter Store

-Amazon S3

-Amazon S3 Static Website Hosting

-AWS CLI

-AWS CloudShell

-AWS Security Token Service (STS)


AWS Systems Manager was evaluated as the centralized management solution for administering EC2 instances. 
It provides a single management plane for operational tasks, automation, configuration management, and remote administration. 
Run Command was reviewed as the preferred method for executing maintenance tasks across multiple EC2 instances without requiring administrators to log in to each server individually.
State Manager was evaluated for maintaining desired configurations across managed instances. It can automatically apply and enforce configuration settings, helping prevent configuration drift.
Session Manager was investigated as a secure remote administration tool. It allows administrators to access managed instances through Systems Manager without opening inbound SSH ports.
Inventory was reviewed as a method for collecting information about managed instances. This information supports operational visibility and compliance efforts.
Parameter Store was evaluated for centralized storage of configuration values, operational settings, and application parameters that may be shared across multiple systems.

Amazon S3 was investigated as an object storage service capable of storing website files, images, documents, and downloadable resources without requiring a traditional file server.
S3 Static Website Hosting was evaluated as the recommended solution for Bright Path's public resource page. 
Since the website consists entirely of static content, S3 provides a simpler and lower-maintenance alternative to hosting the site on an EC2 instance.

During the investigation, CloudShell and the AWS CLI were used to create and manage S3 resources, upload website content, configure static website hosting, verify AWS account identity with STS, and validate the successful deployment of the public resource page.
## Virtualization Connection
This investigation focused on selecting the appropriate management and hosting solutions for Bright Path Community Services. 
Virtualization allows workloads to run on EC2 virtual machines while abstracting the underlying physical infrastructure, enabling administrators to focus on managing operating systems and applications rather than hardware. 
AWS Systems Manager extends this abstraction by providing a centralized management layer for multiple EC2 instances through capabilities such as Run Command, Session Manager, Inventory, and Parameter Store. 
This allows administrative tasks to be performed from a single management interface instead of accessing each virtual machine individually. The investigation also demonstrated that not every workload requires a virtual machine. 
For Bright Path's static resource page, Amazon S3 provides an abstraction from traditional server infrastructure by hosting content directly from object storage, eliminating the need to deploy, secure, and maintain an additional EC2 instance for a simple static website.

## Evidence Reviewed
Bright Path Community Services currently performs routine maintenance activities manually across multiple EC2 instances. 
Administrative tasks require staff to access systems individually to perform updates, verify configurations, and complete operational checks. 
This approach increases administrative effort and can lead to inconsistencies between systems.

The investigation involved five EC2 instances that require regular maintenance and administrative oversight. 
Because the same tasks are performed repeatedly across several systems, the environment was evaluated as a candidate for centralized management through AWS Systems Manager.

To support AWS Systems Manager capabilities, the following managed node requirements were reviewed:

-SSM Agent installation and operational status on each EC2 instance.

-Appropriate IAM instance profile permissions required for Systems Manager.

-Connectivity to AWS Systems Manager service endpoints.

-Successful registration of instances as managed nodes within Systems Manager.


- Managed node requirements
- Systems Manager feature fit
- Interactive versus non-interactive access needs
- Configuration or parameter needs
- Static content requirements
- Evidence available through the AWS environment

## Operational Analysis
Explain which tasks are better suited for centralized management, automation, interactive access, or static object hosting.
Support your analysis with the evidence you reviewed.

## Recommendation
Recommend the AWS service or feature that best fits each identified operational need.
Explain why the recommendation is appropriate for the workload.

## Escalation Notes
Document any prerequisite, permission, configuration, or environment issue that requires additional approval or support.
If no escalation is required, state that clearly.

## Lessons Learned
Explain what Week 3 taught you about centralized systems management, safe automation, reducing manual work, and selecting the right service model.

## Professional Vocabulary
Define the important Week 3 terms in your own words.
Include terms such as:
- Systems Manager
- Managed Node
- Run Command
- Session Manager
- Inventory
- Parameter Store
- Automation
- Static Website Hosting
- Object Storage
- Management Plane

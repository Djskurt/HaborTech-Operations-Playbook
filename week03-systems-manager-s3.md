# Week 3: Systems Manager and S3

## HarborTech Ticket Summary
TKT-2026-0003: HarborTech received a request from Bright Path Nonprofits to evaluate more efficient methods for managing cloud operations and hosting public-facing content. 
The organization was performing routine administrative tasks across five EC2 instances using manual processes, resulting in approximately 90 minutes of reptitive administrative work, creating unnecessary operational overhead and increasing the potential for inconsistent system management. 
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

- AWS Systems Manager

- AWS Systems Manager Run Command

- AWS Systems Manager State Manager

- AWS Systems Manager Session Manager

- AWS Systems Manager Inventory

- AWS Systems Manager Parameter Store

- Amazon S3

- Amazon S3 Static Website Hosting

- AWS CLI

- AWS CloudShell

- AWS Security Token Service (STS)


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
This allows administrative tasks to be performed from a single management interface instead of accessing each virtual machine individually. 

The investigation also demonstrated that not every workload requires a virtual machine. 
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

Several AWS Systems Manager capabilities were evaluated based on operational requirements:

-Run Command for centralized execution of recurring administrative tasks.

-State Manager for maintaining desired configurations across multiple instances.

-Session Manager for secure remote administration and troubleshooting.

-Inventory for collecting software and configuration information.

-Parameter Store for centralized storage of configuration data and operational parameters.

Evidence showed that not all administrative activities require direct server access.
Session Manager is the preferred solution for interactive administration and troubleshooting.
Run Command is better suited for non-interactive tasks that can be executed consistently across multiple systems without logging into each instance.
The investigation identified a need for centralized configuration management to improve consistency across systems. 
AWS Systems Manager Parameter Store was reviewed as a solution for securely storing and managing operational settings, configuration values, and parameters used by multiple resources.

Bright Path required a public resource page containing:

-HTML content

-Images

-Contact information

-Downloadable forms and documents

#The following evidence was reviewed in the AWS environment through AWS CLI and AWS CloudShell:
-AWS Region was verified to be us-east-1
-AWS Security Token Service (STS) was used to verify the AWS account and IAM identity associated with the CloudShell session:
Input Command: aws sts get-caller-identity Output Command: {"UserId"AROAY****************: ":user539*****=Daniel_J._Scurek","Account": "5730********", "Arn": "arn:aws:sts::5730********:assumed-role/voclabs/user539****=Daniel_J._Scurek"}
## Operational Analysis
Explain which tasks are better suited for centralized management, automation, interactive access, or static object hosting.
Support your analysis with the evidence you reviewed.

## Recommendation
I recommend HarborTech should implement AWS Systems Manager Run Command as the primary solution for Bright Path Nonprofit's recurring EC2 maintenance activities. Run Command allows administrators to execute commands across multiple managed instances from a centralized interface, reducing repetitive manual work and improving operational consistency. Before implementation, all target instances must be configured as managed nodes with the SSM Agent installed, appropriate IAM permissions assigned, and connectivity to Systems Manager service endpoints verified.

For configuration management, AWS Systems Manager Parameter Store should be consider for use to centrally store operational settings and configuration values. This approach reduces configuration drift and simplifies administration across multiple systems.

When direct server access is required for troubleshooting or diagnostics, AWS Systems Manager Session Manager should be recommended for use instead of traditional SSH access. Session Manager provides secure, controlled access while reducing the exposure associated with open management ports.

For Bright Path Nonprofit's public resource page, Amazon S3 Static Website Hosting is the recommended solution. The website consists entirely of static content and does not require server-side processing, databases, or application services. Hosting the site in Amazon S3 eliminates the need to deploy and maintain an additional EC2 instance, reducing operational overhead while meeting the organization's requirements.

## Escalation Notes
During validation of the Bright Path Nonprofit static website deployment, the Amazon S3 website endpoint returned a 403 Forbidden response. Based on the evidence reviewed, the website files were successfully uploaded, static website hosting was configured, and the endpoint was generated correctly. The access issue appears to be related to Learner Lab public-access restrictions and sandbox security controls, not a deployment failure.

At this time, no further action should be taken to bypass the Learner Lab restrictions. If this workload were deployed in a production environment, administrator approval would be required to configure the necessary bucket policies, public-read permissions, and public-access settings needed to make the website publicly accessible.

Additionally, before implementing the recommended AWS Systems Manager solution for EC2 maintenance, verification is required that all target instances meet the managed-node prerequisites. Any instance that does not have the SSM Agent installed, the appropriate IAM role attached, or connectivity to AWS Systems Manager endpoints should be reviewed and remediated by the systems administration team before automation is enabled.

No other environment, configuration, or permission issues were identified during the investigation. The remaining recommendations can proceed once the above prerequisites and access requirements have been reviewed by authorized administrators.
## Lessons Learned
Week 3 demonstrated the value of centralized systems management for improving operational efficiency and consistency across multiple EC2 instances. 
AWS Systems Manager provides a secure and scalable way to manage systems, automate repetitive administrative tasks, and reduce the need for direct server access. 
The investigation also highlighted the importance of safe automation, ensuring that managed node prerequisites, permissions, and connectivity requirements are verified before automation is implemented.

Additionally, the lab reinforced the importance of selecting the right service model for a workload. For Bright Path Nonprofit's static website requirement, Amazon S3 Static Website Hosting provided a simpler and lower-maintenance solution than deploying an additional EC2 instance. Choosing services that align with workload requirements can reduce operational overhead, improve scalability, and allow administrators to focus on higher-value tasks.

## Professional Vocabulary
-  Systems Manager
  -An AWS management service that provides a unified interface for administering compute resources, executing remote operations, maintaining configuration compliance, collecting inventory data, and automating operational workflows across an AWS environment.
-  Managed Node
-A managed node is a system that is enrolled in AWS Systems Manager and can be managed remotely through centralized AWS operational tools and automation services.
-  Run Command
-Is a Systems Manager tool that executes remote administrative commands and scripts on managed nodes from a central AWS management service.
-  Session Manager
-Provides secure remote access to managed nodes.
-  Inventory
-Collects configuration and software information from managed nodes.  
-  Parameter Store
-Securely stores and manages configuration data and secrets.  
-  Automation
-A feature that helps complete repetitive tasks without requiring someone to do them manually.  
-  Static Website Hosting
-Hosting a website made of files like HTML, CSS, and images without requiring a web server.  
-  Object Storage
-A way of storing data as individual files, called objects, that can be easily accessed and managed.  
-  Management Plane
-The part of a system used to control and manage resources.

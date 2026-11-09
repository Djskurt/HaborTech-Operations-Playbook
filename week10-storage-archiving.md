# Week 10: Storage and Archiving

## HarborTech Ticket Summary
TKT-2026-0010: The Client known as, Bright Path Community Serivces is experiencing a storage protection and data lifecycle management issue. The client's newest documented EC2 application disk snapshot is 19 days old despite a maximum acceptable data loss of 24 hours, creating a potential gap between the documented backup state and the required recovery-point objective (RPO). In addition, the organization requires three Linux servers to access shared working files simultaneously, uploaded documents to be stored using object storage, and completed records to be retained for seven years while being rarely accessed after the first 90 days.

The investigation focused on evaluating AWS storage services that align with these requirements. CloudShell evidence demonstrated the successful creation of an Amazon EBS volume and a completed EBS snapshot, confirming the availability of volume-level backup capabilities. Additional evidence showed successful storage of an uploaded file in Amazon S3, server-side encryption of the object, enabled bucket versioning, and the ability to define lifecycle policies for long-term archival storage. Based on the ticket requirements and collected evidence, the investigation assessed Amazon EBS for application disk recovery, Amazon EFS for shared Linux file access, Amazon S3 for uploaded document storage, and lifecycle-based archival storage for seven-year record retention.

## Client Impact
The storage and retention issues identified in TKT-2026-0010 could expose Bright Path to increased risk of data loss, operational disruption, and compliance challenges. The client's newest documented application disk snapshot is 19 days old even though the maximum acceptable data loss window is 24 hours. If an application failure, accidental deletion, ransomware event, or storage corruption were to occur, recovery efforts could require restoring data that is significantly older than the organization's recovery objective, potentially resulting in the loss of recent business information and extended downtime.

The requirement for three Linux servers to access the same working files also creates an operational challenge if an inappropriate storage model is used. Without a shared file system designed for concurrent access, users and applications may experience inconsistent data, duplicate file copies, or workflow interruptions that affect productivity and collaboration.

The client's uploaded documents must be stored as object data and retained records must remain accessible for seven years. Improper storage selection or retention management could lead to difficulties locating historical records, increased storage costs, or premature deletion of information that the organization is required to keep. Because completed records are rarely accessed after 90 days, failing to implement an appropriate archival strategy could also result in unnecessary long-term storage expenses while providing little operational benefit.

## AWS Services Involved
- AWS STS (Security Token Service)
- AWS CloudShell
- AWS CLI
- Amazon EC2
- Amazon EBS (Elastic Block Store)
- EBS Snapshots
- Amazon S3
- S3 Object Storage
- S3 Versioning
- S3 Lifecycle Policies
- Amazon S3 Glacier Storage Classes
- Amazon EFS (Elastic File System)
- Recovery Point Objective (RPO)
- Block Storage
- Object Storage
- Shared File Storage
- Data Retention
- Data Archiving

### Summary

TKT-2026-0010 involved several AWS storage, backup, and data lifecycle services. AWS STS and AWS CloudShell were used to verify the investigation environment and execute AWS CLI commands. Amazon EBS and EBS Snapshots were evaluated for application disk backup and recovery capabilities, Amazon S3 was used to investigate object storage and data protection features, and Amazon EFS was identified as the appropriate shared file storage solution for multiple Linux servers. The investigation also focused on recovery-point objectives, long-term retention requirements, versioning, lifecycle 

## Virtualization Connection
Explain how VPC networking creates software-defined network boundaries, routes, gateways, and traffic controls for virtual cloud resources.

## Evidence Reviewed
Document the evidence you reviewed, such as:
- VPC CIDR
- Subnet CIDR
- Subnet type
- Route table entries
- Internet gateway attachment
- NAT gateway configuration
- Security group rules
- Network ACL rules
- Public or private IP addressing
- Application port
- Flow log evidence
- Connectivity test results

## Operational Analysis
Explain what the evidence shows about the network path.
Identify whether the issue involves routing, addressing, gateway configuration, subnet design, security groups, network ACLs, or another network layer.

## Recommendation
Recommend the next operational action supported by the evidence.
Explain how the recommendation addresses the identified connectivity problem without making unnecessary network changes.

## Escalation Notes
Document any production routing, gateway, security group, network ACL, or architecture change that requires approval or additional support.
If no escalation is required, state that clearly.

## Lessons Learned
Explain what Week 9 taught you about VPC design, network paths, layered traffic controls, and evidence-based network troubleshooting.

## Professional Vocabulary
Define the important Week 9 terms in your own words.
Include terms such as:
- VPC
- Subnet
- Route Table
- Internet Gateway
- NAT Gateway
- Security Group
- Network ACL
- CIDR
- Public Subnet
- Private Subnet

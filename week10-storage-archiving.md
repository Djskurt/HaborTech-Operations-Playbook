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
Cloud storage and archiving services use virtualization to provide storage resources without requiring organizations to manage physical hardware. During this investigation, Amazon EBS provided virtual block storage for application data, Amazon S3 provided object storage for uploaded files, and lifecycle policies were evaluated for archival storage. These services allow storage, backup, retention, and recovery processes to be managed through software-defined cloud resources, improving scalability, availability, and cost efficiency.

## Evidence Reviewed
### Client Ticket Evidence

**Client Findings Provided**

```text
Ticket TKT-2026-0010

- Bright Path has an EC2 application disk whose newest documented snapshot is 19 days old.
- The maximum acceptable data loss is 24 hours.
- Three Linux servers require concurrent access to shared working files.
- Uploaded documents require object storage.
- Completed records are rarely accessed after 90 days.
- Records must be retained for seven years.
```

**Investigation Boundaries**
```text
- The 19-day-old snapshot is client ticket evidence and is not a resource within the AWS Learner Lab environment.
- CloudShell practice resources were created solely for investigation and evidence collection.
- Practice resources do not remediate or modify the client environment.
- Only disposable resources created during the investigation were used.
```

**Operational Significance**

The client evidence identified four storage-related requirements that guided the investigation. First, the documented 19-day-old snapshot raised concerns regarding compliance with the client's 24-hour recovery-point objective (RPO). Second, the requirement for concurrent file access across three Linux servers indicated a need for shared file storage. Third, uploaded documents required an object-storage solution capable of managing file-based data. Finally, completed records required a long-term archival strategy capable of retaining data for seven years while minimizing storage costs for information that is rarely accessed after 90 days.
### AWS Environment Verification

**CloudShell Commands**

```bash
aws sts get-caller-identity

aws --version

aws configure get region

aws configure list

echo "$AWS_DEFAULT_REGION"
```

**CLI Evidence**

```json
{
    "UserId": "AROAY**************:user539****=Daniel**._Scurek",
    "Account":**[REDACTED]",
    "Arn":**arn:aws:sts::[REDACTED]:assumed-r**e/vocl**s/user539****=Daniel_J._Scurek"
}
```

```text
aws-cli/2.39.4 Python/3.14.8 Linux/6.1.189-236.400.amzn2023.x86_64 exec-env/CloudShell exe/x86_64.amzn.2023
```

```text
NAME       : VALUE                    : TYPE             : LOCATION
profile    : <not set>                : None             : None
access_key : ****************ZFHG     : container-role   :
secret_key : ****************zfTr     : container-role   :
region     : us-east-1                : env              : ['AWS_REGION', 'AWS_DEFAULT_REGION']
```

```text
us-east-1
```

The evidence confirmed that the investigation was conducted within the AWS Academy CloudShell environment using the `voclabs` role in the `us-east-1` Region.

---
### EBS Volume Investigation

**CloudShell Commands**

```
aws ec2 describe-volumes --region us-east-1 --query "Volumes[*].[VolumeId,State,Size,AvailabilityZone,VolumeType]"

aws ec2 describe-availability-zones --region us-east-1 --query "AvailabilityZones[*].ZoneName" --output table

aws ec2 create-volume \
  --availability-zone us-east-1a \
  --size 1 \
  --volume-type gp3 \
  --tag-specifications "ResourceType=volume,Tags=[{Key=Purpose,Value=IT4560Week10}]" \
  --region us-east-1
```

**CLI Evidence**

```text
No approved volume exists.
```
```text
us-east-1a
us-east-1b
us-east-1c
us-east-1d
us-east-1e
us-east-1f
```
```
{
    "VolumeId": "vol-0141ef89bb00176e4",
    "VolumeType": "gp3",
    "Size": 1,
    "AvailabilityZone": "us-east-1a",
    "State": "available",
    "Iops": 3000,
    "Throughput": 125,
    "MultiAttachEnabled": false,
    "Encrypted": false,
    "Tags": [
        {
            "Key": "Purpose",
            "Value": "IT4560Week10"
        }
    ]
}
```
The investigation created and validated a disposable 1 GiB gp3 EBS volume for storage and recovery testing.


## Operational Analysis


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

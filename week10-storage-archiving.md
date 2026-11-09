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

### EBS Snapshot and Recovery

**CloudShell Commands**
```bash
aws ec2 create-snapshot \
  --volume-id vol-0141ef89bb00176e4 \
  --description "IT4560 Week10 practice" \
  --tag-specifications "ResourceType=snapshot,Tags=[{Key=Purpose,Value=IT4560Week10}]" \
  --region us-east-1

aws ec2 describe-snapshots \
  --snapshot-ids snap-0f89c8a99d95448f2 \
  --region us-east-1
```

**CLI Evidence**

Initial snapshot creation:
```text
{
    "SnapshotId": "snap-0f89c8a99d95448f2",
    "VolumeId": "vol-0141ef89bb00176e4",
    "State": "pending",
    "Description": "IT4560 Week10 practice"
}
```

Snapshot completion:

```text
{
    "SnapshotId": "snap-0f89c8a99d95448f2",
    "VolumeId": "vol-0141ef89bb00176e4",
    "State": "completed",
    "Progress": "100%",
    "CompletionTime": "2026-11-09T16:57:56.065000+00:00"
}
```

The snapshot reached a completed state and provided a validated recovery point for the practice volume.

### Amazon S3 Object Storage
**CloudShell Commands**
```bash
BUCKET=djs443-week10-$(date +%s)

aws s3api create-bucket \
  --bucket $BUCKET \
  --region us-east-1

printf "IT4560 Week10 practice\n" > week10-sample.txt

aws s3 cp week10-sample.txt s3://$BUCKET/week10-sample.txt --region us-east-1

aws s3api head-object \
  --bucket $BUCKET \
  --key week10-sample.txt \
  --region us-east-1
```

**CLI Evidence**

Bucket creation:
```JSON
{
    "Location": "/djs443-week10-1794243869",
    "BucketArn": "arn:aws:s3:::djs443-week10-1794243869"
}
```
Object upload:
```text
upload: ./week10-sample.txt to s3://djs443-week10-1794243869/week10-sample.txt
```

Object inspection:
```
{
    "LastModified": "2026-11-09T17:12:10+00:00",
    "ContentLength": 23,
    "ContentType": "text/plain",
    "ServerSideEncryption": "AES256",
    "Metadata": {}
}
```

The object was successfully stored in Amazon S3 and encrypted at rest using AES256 server-side encryption.
### S3 Versioning and Lifecycle Investigation
**CloudShell Commands**
```bash
aws s3api get-bucket-versioning \
  --bucket $BUCKET \
  --region us-east-1

aws s3api put-bucket-versioning \
  --bucket $BUCKET \
  --versioning-configuration Status=Enabled \
  --region us-east-1

aws s3api get-bucket-versioning \
  --bucket $BUCKET \
  --region us-east-1

python3 -m json.tool lifecycle.json

aws s3api get-bucket-lifecycle-configuration \
  --bucket $BUCKET \
  --region us-east-1
```

**CLI Evidence**

Versioning enabled:
```
{
    "Status": "Enabled"
}
```
Validated lifecycle proposal:
```JSON
{
    "Rules": [
        {
            "ID": "Week10ArchiveProposal",
            "Status": "Enabled",
            "Filter": {
                "Prefix": "archive/"
            },
            "Transitions": [
                {
                    "Days": 90,
                    "StorageClass": "GLACIER"
                }
            ]
        }
    ]
}
```

Lifecycle inspection:
```text
aws: [ERROR]: An error occurred (NoSuchLifecycleConfiguration) when calling the GetBucketLifecycleConfiguration operation: The lifecycle configuration does not exist
```
Versioning was successfully enabled, while no lifecycle policy was currently applied to the practice bucket to successfully replicate client issue in Learn Lab environment.

### Resource Cleanup Verification

**CloudShell Commands**
```bash
aws s3api list-object-versions \
  --bucket $BUCKET \
  --region us-east-1

aws s3 rm s3://$BUCKET/week10-sample.txt --region us-east-1

aws s3api delete-bucket \
  --bucket $BUCKET \
  --region us-east-1

aws s3api delete-object \
  --bucket $BUCKET \
  --key week10-sample.txt \
  --version-id null \
  --region us-east-1

aws s3api delete-object \
  --bucket $BUCKET \
  --key week10-sample.txt \
  --version-id lZPaHahtRE2zzNmSAxrK2ER2JB688OK6 \
  --region us-east-1

aws ec2 delete-snapshot \
  --snapshot-id snap-0f89c8a99d95448f2 \
  --region us-east-1

aws ec2 delete-volume \
  --volume-id vol-0141ef89bb00176e4 \
  --region us-east-1

aws ec2 describe-volumes \
  --volume-ids vol-0141ef89bb00176e4 \
  --region us-east-1
```

**CLI Evidence**

Versioned Object inspection:
```JSON
{
    "Versions": [
        {
            "Key": "week10-sample.txt",
            "VersionId": "null",
            "IsLatest": false
        }
    ],
    "DeleteMarkers": [
        {
            "Key": "week10-sample.txt",
            "VersionId": "lZPaHahtRE2zzNmSAxrK2ER2JB688OK6",
            "IsLatest": true
        }
    ]
}
```
Object deletion:
```text
delete: s3://djs443-week10-1794243869/week10-sample.txt
```
Bucket deletion attempt:
```text
aws: [ERROR]: An error occurred (BucketNotEmpty) when calling the DeleteBucket operation: The bucket you tried to delete is not empty. You must delete all versions in the bucket.
```
Version-specific cleanup:
```JSON
{
    "VersionId": "null"
}
```
```JSON
{
    "DeleteMarker": true,
    "VersionId": "lZPaHahtRE2zzNmSAxrK2ER2JB688OK6"
}
```
Volume cleanup verification:
```text
aws: [ERROR]: An error occurred (InvalidVolume.NotFound) when calling the DescribeVolumes operation: The volume 'vol-0141ef89bb00176e4' does not exist.
```
The cleanup process successfully removed the versioned S3 content, snapshot resources, and disposable EBS volume after verification identified and resolved remaining object versions and delete markers.

### Summary
The investigation began with client evidence showing that the newest documented EC2 application disk snapshot was 19 days old despite a 24-hour recovery-point objective. Additional requirements included shared file access for three Linux servers, object storage for uploaded documents, and seven-year retention for records that become infrequently accessed after 90 days.

Using AWS CloudShell in the us-east-1 Region, a disposable 1 GiB gp3 EBS volume was created and inspected. A snapshot of the volume was successfully created and verified as completed, demonstrating EBS backup and recovery capabilities. Amazon S3 was then used to upload and inspect a sample object, confirming object storage functionality and AES256 server-side encryption.

Further testing enabled S3 versioning and validated a lifecycle policy proposal that would transition archived data to Glacier storage after 90 days. Although no lifecycle configuration was applied to the bucket, the investigation demonstrated how lifecycle management can support long-term archival requirements.

Finally, cleanup procedures removed all disposable resources created during testing. The process required additional version-specific deletions because S3 versioning retained an object version and delete marker after the initial object deletion. Overall, the investigation provided evidence for evaluating backup, object storage, file sharing, retention, and archival solutions that align with the client's requirements.
## Operational Analysis
The evidence shows that no single AWS storage service satisfies all of the client's requirements. Amazon EBS is the appropriate solution for the EC2 application disk because the investigation successfully created volume vol-0141ef89bb00176e4 and a completed snapshot, demonstrating block storage backup and recovery capabilities. However, the client's documented 19-day-old snapshot must be reviewed against the required 24-hour recovery-point objective. Amazon EFS is the best fit for the shared working files requirement because it supports concurrent file access by multiple Linux servers. Amazon S3 is the appropriate solution for uploaded documents, as the investigation successfully uploaded and inspected an encrypted object using object storage. For records that must be retained for seven years but are rarely accessed after 90 days, Amazon S3 lifecycle policies and Glacier storage classes provide a cost-effective archival solution while supporting long-term retention. Overall, the evidence supports using Amazon EBS for application disk recovery, Amazon EFS for shared Linux file access, Amazon S3 for uploaded documents, and Amazon S3 Glacier for long-term archival storage.

## Recommendation
Based on the evidence, the next operational action should be to verify and update the client's EC2 application disk backup schedule to meet the documented 24-hour recovery-point objective. The client ticket states that the newest documented snapshot is 19 days old, while the investigation demonstrated that EBS snapshots can successfully create recovery points, as shown by completed snapshot `snap-0f89c8a99d95448f2` with 100% progress. Before any production changes are made, HarborTech should validate the client's current snapshot schedule, retention settings, and recovery requirements to determine whether backups are being created within the required timeframe. Additionally, HarborTech should recommend **Amazon EFS** for shared Linux working files, **Amazon S3** for uploaded documents, and **Amazon S3 lifecycle policies with Glacier storage classes** for records that must be retained for seven years. These recommendations align directly with the client's documented storage requirements and are supported by the storage capabilities demonstrated during the investigation. No production migrations, lifecycle policies, or retention changes should be implemented until the client reviews and approves the proposed storage strategy.

## Escalation Notes
An escalation is required because the client ticket identifies a potential gap between the documented backup state and the required recovery objective. The client's newest documented EC2 application disk snapshot is 19 days old despite a maximum acceptable data loss threshold of 24 hours. As an intern, I do not have authority to review, modify, or implement production backup schedules. This finding should be escalated to a cloud administrator or storage team for verification of the current snapshot schedule, retention configuration, and recovery-point objective compliance. No additional escalations are required based on the CloudShell investigation. The evaluation of Amazon EBS, Amazon EFS, Amazon S3, versioning, and lifecycle management was performed using disposable lab resources and produced no operational issues requiring immediate action. Any future implementation of shared file storage, object storage migration, lifecycle policies, archival storage, or retention controls should follow HarborTech's normal change management and approval processes before being applied to the client environment.
## Lessons Learned
Week 10 reinforced that different storage requirements require different AWS storage services. I learned that **Amazon EBS** is designed for block storage workloads and supports recovery through EBS snapshots, which can be used to create point-in-time backups of application disks. The investigation also demonstrated that successfully creating a snapshot does not automatically prove compliance with a recovery-point objective and that backup schedules must be verified against business requirements.
I also learned that **Amazon S3** is optimized for object storage and provides additional data protection features such as versioning and lifecycle management. Enabling versioning showed how deleted objects can still leave behind object versions and delete markers, requiring additional cleanup actions before a bucket can be removed. Lifecycle policies demonstrated how data can be transitioned to lower-cost archival storage such as Glacier after a defined period.
Finally, I learned the importance of selecting the correct storage model based on access requirements. Amazon EBS supports application disks, Amazon EFS supports shared file access across multiple Linux servers, and Amazon S3 with Glacier storage classes supports long-term archival and retention requirements. Together, these services provide a flexible approach to backup, recovery, storage management, and long-term data retention in AWS.

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

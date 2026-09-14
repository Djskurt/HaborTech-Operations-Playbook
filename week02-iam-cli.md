# Week 2: IAM and AWS CLI Investigation

## HarborTech Ticket Summary
TKT-2026-0002: Riverside Goods reported Marcus Webb, an inventory coordinator, was able to authenticate with IAM user credentials to Riverside Good AWS, but request to list the inventory bucket returns an error of Access Denied. The onboarding record shows the IAM user exists. No job-function group membership and no directly attached permission policy are listed. Riverside Goods requested that Marcus be attached to AmazonS3FullAccess as to quickly resolve ticket.
## Client Impact
Marcus is currently blocked from performing his required inventory duties, specifically listing the riverside-inventory bucket, reading report objects, and uploading approved files. This reduces client productivity in inventory work. 
## AWS Services Involved
Investigation required the following AWS services and concepts:

- AWS Identity and Access Management (IAM)
- IAM users and policies
- Amazon S3
- AWS CloudShell
- AWS Command Line Interface (AWS CLI)
- AWS Security Token Service (STS)
## Virtualization Connection
AWS Identity and Access Management acts as a logical barrier to protect cloud resources.  
## Evidence Reviewed

## Operational Analysis

## Recommendation

## Escalation Notes

## Lessons Learned

## Professional Vocabulary 


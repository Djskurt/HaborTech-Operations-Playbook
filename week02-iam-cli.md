# Week 2: IAM and AWS CLI Investigation

## HarborTech Ticket Summary
TKT-2026-0002: Riverside Goods reported Marcus Webb, an inventory coordinator, was able to authenticate with IAM user credentials to Riverside Good AWS, but request to list the inventory bucket returns an error of Access Denied. 
The onboarding record shows the IAM user exists. No job-function group membership and no directly attached permission policy are listed. Riverside Goods requested that Marcus be attached to AmazonS3FullAccess as to quickly resolve ticket.
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

Marcus's was attempting to access Amazon S3 and was blocked by IAM. Amazon S3 held the riverside-inventory that Marcus was trying to access. AWS CloudShell and AWS CLI were used to gather evidence pertaining to why IAM was blocking Marcus's request. 
An API call was made to AWS Security Token Service (STS) to return details of identity in IAM and to get the Amazon resource name (ARN). 

## Virtualization Connection
Because cloud infrastructure is virtualized into software-defined storage, access to virtual resources is enforced through identity and authorization policies at the API control plane. 
Marcus’s successful authentication verified his identity, but without explicit IAM permissions, the virtual control plane blocked him from accessing the riverside-inventory bucket.
## Evidence Reviewed

## Operational Analysis

## Recommendation

## Escalation Notes

## Lessons Learned

## Professional Vocabulary 


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
An API call was made to AWS Security Token Service (STS) to return details of identity in and to get the Amazon resource name (ARN). 

## Virtualization Connection
Because cloud infrastructure is virtualized into software-defined storage, access to virtual resources is enforced through identity and authorization policies at the API control plane. 
Marcus’s successful authentication verified his identity, but without explicit IAM permissions, the virtual control plane blocked him from accessing the riverside-inventory bucket.
## Evidence Reviewed
The following evidence was reviewed:

-Successful AWS authentication by Marcus.
-AccessDenied Error for Amazon S3 of bucket riverside-inventory from Marcus.
-Output from 'aws sts get-caller-identity'.
-LabRole's IAM Role ARN.
-Output from 'aws iam get-role --role-name LabRole'.
-Output from 'aws iam list-attached-role-policies --role-name LabRole'.
-Output from 'aws iam list-role-policies --role-name LabRole'.
-Contents surrounding the trust relationship, managed policies, and inline policy names for LabRole.

The evidence reviewed includes the ARN for LabRole along with its trust relationship in AssumeRolePolicyDocument, attached managed policies, and inline policy names. 
These CLI inspections were conducted to determine if Marcus was covered by any of these existing policies or trust boundaries. Ultimately, the results confirm he lacks the necessary permissions, proving that his AccessDenied error stems from missing, targeted authorization rules.
## Operational Analysis
The evidence confirms a clear breakdown between identity verification and action authorization within the cloud environment. 
While Marcus Webb successfully authenticated to the AWS Management Console using valid credentials, his request to access the riverside-inventory bucket resulted in an explicit AccessDenied error. 
A review of onboarding records and CLI identity inspections reveals that Marcus lacks any assigned IAM group memberships, attached managed policies, or inline permission rules. 
Because AWS IAM enforces a strict default-deny model, authentication alone does not grant resource access. Summarily saying, his identity completely lacks the explicit authorization policy required to execute s3:ListBucket, s3:GetObject, and s3:PutObject actions on the inventory bucket.
## Recommendation
HarborTech should recommend attaching Marcus to a dedicated IAM group bound to a custom, least-privilege policy. 
This grants him only s3:ListBucket, s3:GetObject, and s3:PutObject permissions restricted specifically to the riverside-inventory bucket and its contents.
## Escalation Notes
Please escalate this request to authorized HarborTech team member to approve and deploy a customer-managed least-privilege policy attached to a dedicated IAM group. 
Recommend that the approved administrator to assign Marcus Webb to the group, granting him only the necessary s3:ListBucket, s3:GetObject, and s3:PutObject permissions for the riverside-inventory bucket.
## Lessons Learned
This investigation reinforced that AWS IAM operates on a strict default-deny model, where authentication verifies who a user is, but authorization requires explicit permission policies to grant access. 
Through AWS CLI auditing commands, such as inspecting caller identities, role trust relationships, and policy bindings, I learned to systematically trace permission gaps back to missing authorization rather than authentication failures. 
Investigating these access issues demonstrated the importance of enforcing least privilege by rejecting broad managed policies in favor of scoped, customer-managed policies attached to job-function IAM groups.
## Professional Vocabulary 
- Authentication
  -The evidence you present to a system to verify your identity such as username and password.
- Authorization
  -The process of checking what someone is allowed to do once their identity is known.
- IAM
  -The logical guardrails that decides what the user can do across the cloud environment.
- Policy
  -The rulebook, usually written in code, to govern how the resources, identities, and traffic operate within the cloud platform. 
- Least Privilege
  -The bare minimum access the user, application, or system is given to complete assigned jobs.  
- AccessDenied
  -An error response given when an entity such as a user requests an action, but the system determines they do not meet required permissions to perform said action. 
- AWS CLI
  -A terminal-based utility for aws that allows to use text commands instead of clicking around in a web browser interface. 
- CloudShell
  -The browser-based terminal embedded in the AWS management console that inherits our current AWS web console login credentials. 
- Caller Identity
  -The principal identity asking who they are and the response given to that question.
- Resource Scope
  -Where the resource lives in the cloud hierarchy.
- ARN
  -A unique string identifier that is attached to specific resources across AWS. 

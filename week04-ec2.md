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
- AMI ID: ami-0fef201115eefe936
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
### Verification of AWS Identity
 
Verify the AWS account and role being used
 
```bash
aws sts get-caller-identity
```
 
Output:
 
```json
{
"UserId": "AROAY***************:user539****=Daniel_J._S***ek",
"Account": "5730********"*** "Arn": "arn:***:sts::5730********:assumed-role/***labs/user539****=Daniel_J._Scure***}
```
 
 
### Verification of AWS Region, VPC, and Subnet
 
```bash
 aws configure list
```
Output:
text
region us-east-1

 
```bash
 aws ec2 describe-vpcs
```
 
Output:***``text***cId: vpc-0a6ee************
 
```bash
aws ec2 describe-subnets
```
output:
 
```text
172.31.16.0/20
```


### Creation of Security Group*(Without HTTP Access)
 
Create a security group that intentionally does not allow inbouod TCP port 80 traffic

```bash
 aws ec2 create-security-group \
--group name riverside-web-djs443 \
--description "Riverside Goods Web SG" \
--vpc-id vpc-0a6ee************
```
 
### Initial Security Group Inspection
 
Inspect the newly created security group
 
```bash
aws ec2 describe-security-groups \
--group-ids sg-03092df2d1bb85055
```

relevant output:
 
```json
{
"groupId":*"sg-03092df2d1bb85055",
"groupname": "riverside-web-sg-djs443",
* "Description": "Riverside Goods W*b SG",
"VpcId": "vpc-0a6ee74e66*efe209",
 
"IpPermissions": [],
 
* "IpPermissionsEgress": [
{
* "*pProtocol": "-1",
"IpRanges"* [
{
"*idrIp": "0.0.0.0/0"
}
* ]
}
]
}
```
### Interpretation
The security group permitted all outbound traffic but contained no inbound rules. The empty `IpPermissions` section c*nfirmed that HTTP traffic on TCP p*rt 80 was not allowed.

 
## Launch Amazon Linux EC2 Instance
 
Launch the instance using the custom security group:
 
```bash
aws ec2 run-instan*es \
--image-id ami-0fef201115eefe*36 \
--instance-type t3.micro \
--*ey-name vockey \
--ebs-optimized \*--network-interfaces '{"AssociateP*blicIpAddress":true,"DeviceIndex*:0,"Groups":["sg-03092df2d1bb850*5"]}' \
--credit*specification '{"CpuCredits":"unli*ited"}' \
--tag-specifications '{"*esourceType":"instance","Tags":[{"*ey":"Name","Value":"riverside-web-*c2_instance"}]}' \
--iam-instance-*rofile '{"Arn":"arn:aws:iam::5730********:instance-profile/LabInstanc*Profile"}' \
--metadata-options '{*HttpEndpoint":"enabled","HttpPutRe*ponseHopLimit":2,"HttpTokens":"req*ired"}' \
--count 1
```
 
Relevant output:
 
```json
{
"ReservationId*: "r-0e5b7b6f20c77b619",
"OwnerI*": "5730********"
}
``*
 
---
 
### Wait for Instance to Re*ch Running State
 
```bash*aws ec2 wait instance-running \
--instance-ids i-03c3eaae0b12be279
``*
 
verify instance status:
 
```bash
aws ec2 describe-instance-status \
--instance-ids i-03c3eaae0b12be279
``*
 
Relevant output:
 
```json
{
"instanceId*: "i-03c3eaae0b12be279",
* "InstanceState": {
"Name": "running"
},
"InstanceStatus": {
* "Status": "ok"
},
* "SystemStatus": {
"Status": "*k"
}
}
```
 
### Interpretation

The EC2 instance successfully reached the***Running** state and both AWS status checks passed. This confirmed that the AWS infrastructure and guest operating system were healthy. However, these results did not prove that users could reach the web application through the network.
 


### Retrieve Public IPv4 Address
 
```bash
aws ec2 describe-instances \
-*instance-ids i-03c3eaae0b12be279
```
 
Output:
 
```text
50.16.xxx.xxx
```
 
### Initial Connectivity Test
 
```bash
curl http://50.16.xxx.xxx
```
 
Output:
 
```text
curl: (7) Failed to connect to 50.1*.xxx.xxx:80 after 0 ms: Could not *onnect to server
```
 
### Interpretation
 
The failed*HTTP test confirmed that the application was not reachable through the expected user path. Combined with the security group evidence showing no inbound TCP port 80 *ule, this supported the conclusion*that network access was being bloc*ed before requests could reach Apa*he.

### EC2 Instance command record (BASH)

Create the user data script:
 
```bash
nano userdata.sh
```
 
Script contents:
 
```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl enable httpd
systemctl start httpd
 
echo "<h1>Riverside Goods</h1>" > /var/www/html/index.html
```
 
Verify the script contents:
 
```bash
cat userdata.sh
```
 
Output:
 
```html
<h1>Riverside Goods</h1>
```
 
---
 
### File Execution and Local Host Testing
 
Add executable permissions:
 
```bash
sudo chmod +x userdata.sh
```
 
Execute the script:
 
```bash
sudo ./userdata.sh
```
 
Test locally from the instance:
 
```bash
curl http://127.0.0.1
```
 
Output:
 
```html
<h1>Riverside Goods</h1>
```
 
Verify Apache service status:
 
```bash
sudo systemctl status httpd
```
 
Output:
 
```text
httpd.service - The Apache HTTP Server
Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
Active: active (running) since Mon 2026-09-27 18:05:17 UTC; 6 min ago
```
 
### Interpretation
 
The successful localhost test confirmed that Apache was serving the expected web page from the instance itself. The `httpd` service status showed the web server was running, providing evidence that the application layer was functioning correctly before external network troubleshooting was performed.
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


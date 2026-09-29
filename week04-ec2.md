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
The security group permitted all outbound traffic but contained no inbound rules. The empty `IpPermissions` section confirmed that HTTP traffic on TCP port 80 was not allowed.

 
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


### Initial Connectivity Test (Inside AWS CloudShell)
 
```bash
curl http://50.16.xxx.xxx
```
 
Output:
 
```text
curl: (7) Failed to connect to 50.1*.xxx.xxx:80 after 0 ms: Could not *onnect to server
```
 
### Interpretation
 
The failed HTTP test confirmed that the application was not reachable through the expected user path. Combined with the security group evidence showing no inbound TCP port 80 rule, this supported the conclusion that network access was being blocked before requests could reach Apache.

## Baseline Evidence
- Evidence A Instance ID and AMI ID:

Evidence A proves the EC2 instance was created and AWS assigned it a specific instance ID and AMI ID. 
It also identifies the Amazon Machine Image (AMI) that was used to launch instance. 
It does not prove that the instance is running correctly, that Apache was installed, or that the website is accsssible.

- Evidence B Initial public IPv4 address:
  
Evidence B proves the instance was assigned a public IPv4 address that can potentially be reached from the internet. 
It does not prove network connectivity, that HTTP traffic is allowed, or that a web server is listening on port 80.

- Evidence C Status checks:
  
Evidence C proves the EC2 system status and instance status checks passed, indicating that AWS infrastructure and the operating system are functioning normally.
It does not prove that Apache is installed, that the web application is running, or that users can successfully access the website.

- Evidence D Security group before fix:
  
Evidence D proves that the security group was created. The security group configuration did not contain an inbound rule allowing TCP port 80 traffic. 
It does not prove that the security group is the only cause of the issue because of other factors, such as web server configuration or operating system firewall rules could also prevent access

- Evidence E Failed HTTP test:
  
Evidence E proves the initial HTTP request to the instance was unsuccessful and the website could not be reached at the time of testing. 
It does not prove the exact root cause of the failure. Additional evidence is required to determine whether the issue is related to the security group, Apache service, user data execution, routing, or another configuration problem.

## Root-Cause Analysis
The investigation determined that the EC2 instance was healthy and Apache had successfully installed and started according to the system log. The confirmed configuration issue was the absence of an inbound HTTP rule in the attached security group.

Because no evidence suggested operating system failure, Apache failure, or AMI issues, rebuilding the instance was not justified. Root cause points to Missing inbound TCP port 80 rule in the security group.
## Corrective Action
A single inbound rule allowing HTTP traffic was added to the existing security group.
```bash
aws ec2 authorize-security-group-ingress \
--group-id sg-03092df2d1bb85055 \
--protocol tcp \
--port 80 \
--cidr 0.0.0.0/0
```

No changes were made to the operating system, Apache configuration, user data, AMI, or instance type.

## Verification Evidence
### Security Group verification
```bash
aws ec2 describe-security-groups --group-ids sg-03092df2d1bb85055
```
output:

```bash
aws ec2 describe-security-groups --group-ids sg-03092df2d1bb85055
{
 "SecurityGroups": [
 {
 "GroupId": "sg-03092df2d1bb85055",
 "IpPermissionsEgress": [
 {
 "IpProtocol": "-1",
 "UserIdGroupPairs": [],
 "IpRanges": [
 {
 "CidrIp": "0.0.0.0/0"
 }
 ],
 "Ipv6Ranges": [],
 "PrefixListIds": []
 }
 ],
 "VpcId": "vpc-0a6ee74e66defe209",
 "SecurityGroupArn": "arn:aws:ec2:us-east-1:573077417977:security-group/sg-03092df2d1bb85055",
 "OwnerId": "573077417977",
 "GroupName": "riverside-web-sg-djs443",
 "Description": "Riverside Goods Web SG",
 "IpPermissions": [
 {
 ],
 "VpcId": "vpc-0a6ee74e66defe209",
 "SecurityGroupArn": "arn:aws:ec2:us-east-1:573077417977:security-group/sg-03092df2d1bb85055",
 "OwnerId": "573077417977",
 "GroupName": "riverside-web-sg-djs443",
 "Description": "Riverside Goods Web SG",
 "IpPermissions": [
 {
 "IpProtocol": "tcp",
 "FromPort": 80,
 "ToPort": 80,
 "UserIdGroupPairs": [],
 "IpRanges": [
 {
 "CidrIp": "0.0.0.0/0"
 }
 ],
 "Ipv6Ranges": [],
 "PrefixListIds": []
 }
 ]
 }
 ]
}
(END)
```

The security group displayed an inbound TCP port 80 rule after remediation.

HTTP Verification
```bash
curl http://50.16.xxx.xxx
```
Output:

```text
<h1>Riverside Goods</h1>
```

The successful response confirmed external connectivity to the web service.

## IMDSv2 and Guest Evidence

Retrieve IMDSv2 Token
```bash
TOKEN=$(curl -X PUT \
"http://169.254.169.254/latest/api/token" \
-H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
```
Retrieve Instance ID
```
curl \
-H "X-aws-ec2-metadata-token: $TOKEN" \
http://169.254.169.254/latest/meta-data/instance-id
```
output
```text
 i-03c3eaae0b12be279
```

This verified the instance identity from inside the guest operating system rather than from the AWS control plane.

## Stop/Start Lifecycle Test
Before stop:
```bash
aws ec2 describe-instances --instance-ids i-03c3eaae0b12be279
```
instance ID: i-03c3eaae0b12be279

Public IP: 50.16.xxx.xxx

Webpage test
```
curl http://50.16.xxx.xxx
<h1>Riverside Goods</h1>
```

Stopping the instance

```bash
aws ec2 stop-instance --instance-ids i-03c3eaae0b12be279
```

```bash
aws ec2 wait instance-stopped --instance-ids i-03c3eaae0b12be279
```

```bash
aws ec2 describe-instances --instance-ids i-03c3eaae0b12be279
 "Monitoring": {
 "State": "stopped"
```

Starting the instance
```bash
aws ec2 start-instances --instance-ids i-03c3eaae0b12be279
```

```bash
aws ec2 wait instance-running --instance-ids i-03c3eaae0b12be279
```

```bash
aws ec2 describe-instances --instance-ids i-03c3eaae0b12be279
```
instance ID: i-03c3eaae0b12be279
Public IP: 54.236.xxx.xx

Webpage test

```bash
curl http://54.236.xxx.xx
```
output 
```text
<h1>Riverside Goods</h1>
```
### Observation
The instance ID remained the same and the website content persisted because the operating system and web files were stored on the EBS-backed root volume.
The auto-assigned public IPv4 address changed after the stop/start cycle because an Elastic IP was not associated with the instance.

## Cleanup Evidence
Instance termination

```bash
aws ec2 terminate-instances --instance-ids i-03c3eaae0b12be279
```
output
```text
{
 "TerminatingInstances": [
 {
 "InstanceId": "i-03c3eaae0b12be279",
 "CurrentState": {
 "Code": 32,
 "Name": "shutting-down"
 },
 "PreviousState": {
 "Code": 16,
 "Name": "running"
```

```bash
aws ec2 wait instance-terminated --instance-ids i-03c3eaae0b12be279
```
```bash
aws ec2 describe-instances --instance-ids i-03c3eaae0b12be279
```
```output
SecondaryInterfaces": [],
 "InstanceId": "i-03c3eaae0b12be279",
 "ImageId": "ami-0fef201115eefe936",
 "State": {
 "Code": 48,
 "Name": "terminated"
```

## Escalation and Change-Control Notes


## Lessons Learned


## Professional Vocabulary


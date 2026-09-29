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
### Verification of AWS identity:
$ aws sts get-caller-identity 
"UserId": "AROAY***************:user539****=Daniel_J._Scurek", 
"Account": "5730********", 
"Arn": "arn:aws:sts::5730********:assumed-role/voclabs/user539****=Daniel_J._Scurek" 
### Verification of AWS region/subnet/vpc-ID:
$ aws configure list 
region : us-east-1
$ aws ec2 describe-vpcs  
VpcId": "vpc-0a6ee************
$ aws ec2 describe-subnets 
172.31.16.0/20
### Creation of Security Group riverside-web-djs443 (Without Port 80)
$ aws ec2 create-security-group \ 
--group-name riverside-web-djs443 \ 
--description "Riverside Goods Web SG" \ 
--vpc-id vpc- 0a6ee************
### Initial inspection of security group:
$ aws ec2 describe-security-groups \ 
--group-ids sg- 03092df2d1bb85055  

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
"SecurityGroupArn": "arn:aws:ec2:us-east-1:5730********:security-group/sg-03092df2d1bb85055", 
"OwnerId": "5730********", 
"GroupName": "riverside-web-sg-djs443", 
"Description": "Riverside Goods Web SG", 
"IpPermissions": [] 
} 
] 
} 
(END) 
### Launching of instance/status check:
$ aws ec2 run-instances --image-id 'ami-0fef201115eefe936' --instance-type 't3.micro' --key-name 'vockey' --ebs-optimized --network-interfaces '{"AssociatePublicIpAddress":true,"DeviceIndex":0,"Groups":["sg-03092df2d1bb85055"]}' --credit-specification '{"CpuCredits":"unlimited"}' --tag-specifications '{"ResourceType":"instance","Tags":[{"Key":"Name","Value":"riverside-web-ec2_instance"}]}' --iam-instance-profile '{"Arn":"arn:aws:iam::5730********:instance-profile/LabInstanceProfile"}' --metadata-options '{"HttpEndpoint":"enabled","HttpPutResponseHopLimit":2,"HttpTokens":"required"}' --private-dns-name-options '{"HostnameType":"ip-name","EnableResourceNameDnsARecord":true,"EnableResourceNameDnsAAAARecord":false}' --count '1' 

-instance-profile '{"Arn":"arn:aws:iam::5730********:instance-profile/LabInstanceProfile"}' --metadata-options '{"HttpEndpoint":"enabled","HttpPutResponseHopLimit":2,"HttpTokens":"required"}' --private-dns-name-options '{"HostnameType":"ip-name","EnableResourceNameDnsARecord":true,"EnableResourceNameDnsAAAARecord":false}' --count '1' { "ReservationId": "r-0e5b7b6f20c77b619", "OwnerId": "5730********", "Groups": [], "Instances": [ { "Architecture": "x86_64", "ReservationId": "r-0e5b7b6f20c77b619", "OwnerId": "5730********", "Groups": [], "Instances": [ { "Architecture": "x86_64", "BlockDeviceMappings": [], "ClientToken": "4478dde0-a38e-4620-8d00-a3f6ddc2a1a2", "EbsOptimized": true, "EnaSupport": true, 
$ aws ec2 wait instance-running \ 
--instance-ids i-03c3eaae0b12be279
$ aws ec2 describe-instance-status \ 
--instance-ids i-03c3eaae0b12be279  
{ 
"InstanceStatuses": [ 

 { 
"AvailabilityZone": "us-east-1b", 
"AvailabilityZoneId": "use1-az4", 
 "Operator": { 
"Managed": false, 
"HiddenByDefault": false 
}, 
            "InstanceId": "i-03c3eaae0b12be279", 
            "InstanceState": { 
                "Code": 16, 
                "Name": "running" 
            }, 
            "InstanceStatus": { 
                "Details": [ 
                    { 
                        "Name": "reachability", 
                      "Status": "passed" 
                    } 
                    
], 
"Status": "ok" 
            }, 
            "SystemStatus": { 
                "Details": [ 
                    { 
                        "Name": "reachability", 
                        "Status": "passed" 
                    } 
                    
  ], 
                "Status": "ok" 
            }, 
### Instance Public IPv4 address:
50.16.166.229 
## EC2 Instance Command Record (BASH)
### User Data Creation and verification
$ nano userdata.sh
#!/bin/bash 
yum update -y 
yum install -y httpd 
systemctl enable httpd 
systemctl start httpd 
echo ---html
"<h1>Riverside Goods</h1>" > /var/www/html/index.html
---
EOF
$ cat userdata.sh
<h1>Riverside Goods</h1>
### File execution and local host testing:
$ sudo chmod +x userdata.sh
$ sudo ./userdata.sh
$ curl http://127.0.0.1
 ---html
 <h1>Riverside Goods</h1>
 ---
sudo systemctl status httpd
httpd.service - The Apache HTTP Server
 Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
 Active: active (running) since Mon 2026-09-27 18:05:17 UTC; 6min ago
 
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


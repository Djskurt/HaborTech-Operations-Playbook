# Week 9: VPC Networking and Troubleshooting

## HarborTech Ticket Summary
TKT-2026-0009: The client's desired outcome is to make a public-facing web application accessible to internet users over TCP port 80. Although the EC2 instance is running, the web service is listening, and the configured security controls appear to allow the required traffic, users are currently unable to reach the application from the internet. The evidence indicates that the most likely issue is a missing or unverified routing path between the internet and the subnet hosting the web server. The instance has a public IPv4 address and the VPC has an attached internet gateway, but the associated route table only shows the local VPC route (10.42.0.0/16 to local). No default route (0.0.0.0/0 to the internet gateway) is shown in the evidence packet. As a result, the client's desired outcome of providing public internet access to the application cannot be verified because the required network path from internet users to the EC2 instance is not fully supported by the available routing evidence. The routing layer is therefore the most likely point preventing connectivity.

## Client Impact
The client's desired outcome is to provide public access to the web application on TCP port 80. If routing, subnet design, or traffic-filtering controls are incorrectly configured, users will be unable to reach the application even when the EC2 instance and web service are operating normally. For HarborTech, 
this can result in application downtime, failed customer connections, increased support requests, and disruption of normal business operations. Because public accessibility depends on a complete network path, any failure in routing, subnet configuration, or security controls can prevent users from accessing the service and reduce the reliability of HarborTech's online presence.

## AWS Services Involved
Identify the AWS services and concepts involved in the investigation, such as:
- Amazon VPC
- Subnets
- Route Tables
- Internet Gateway
- NAT Gateway
- Security Groups
- Network ACLs
- CIDR Blocks
- Public and Private Subnets
- VPC Flow Logs

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

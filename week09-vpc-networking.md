# Week 9: VPC Networking and Troubleshooting

## HarborTech Ticket Summary
TKT-2026-0009: The client's desired outcome is to make a public-facing web application accessible to internet users over TCP port 80. Although the EC2 instance is running, the web service is listening, and the configured security controls appear to allow the required traffic, users are currently unable to reach the application from the internet. The evidence indicates that the most likely issue is a missing or unverified routing path between the internet and the subnet hosting the web server. The instance has a public IPv4 address and the VPC has an attached internet gateway, but the associated route table only shows the local VPC route (10.42.0.0/16 to local). No default route (0.0.0.0/0 to the internet gateway) is shown in the evidence packet. As a result, the client's desired outcome of providing public internet access to the application cannot be verified because the required network path from internet users to the EC2 instance is not fully supported by the available routing evidence. The routing layer is therefore the most likely point preventing connectivity.

## Client Impact
The client's desired outcome is to provide public access to the web application on TCP port 80. If routing, subnet design, or traffic-filtering controls are incorrectly configured, users will be unable to reach the application even when the EC2 instance and web service are operating normally. For HarborTech, 
this can result in application downtime, failed customer connections, increased support requests, and disruption of normal business operations. Because public accessibility depends on a complete network path, any failure in routing, subnet configuration, or security controls can prevent users from accessing the service and reduce the reliability of HarborTech's online presence.

## AWS Services Involved

### AWS Services
- Amazon EC2
- Amazon VPC
- Subnets
- Route Tables
- Internet Gateway (IGW)
- NAT Gateway
- Security Groups
- Network ACLs (NACLs)
- VPC Flow Logs

### Networking Concepts
- CIDR Blocks
- Public IPv4 Addressing
- Public Subnets
- Private Subnets
- Route Table Entries
- Internet Gateway Attachment
- NAT Gateway Configuration
- Security Group Rules
- Routing

### Summary
For **TKT-2026-0009**, the investigation focuses on the AWS networking components that affect connectivity between an internet client and the web application. The primary areas of analysis are **routing, subnet design, internet connectivity, and traffic filtering**, with particular attention given to the relationship between the VPC, subnet, route table, internet gateway, security group, and network ACL evidence provided in the ticket.

## Virtualization Connection
Explain how VPC networking creates software-defined network boundaries, routes, gateways, and traffic controls for virtual cloud resources.

## Evidence Reviewed

### Required Traffic Path

The required traffic path identified in the simulated evidence packet was:

```text
Internet Client to TCP 80 to EC2 Web Server
```

All evidence was evaluated against this path to determine whether a complete route existed between the internet and the application.

### EC2 Instance Status

**Simulated Evidence Packet**

```text
EC2: running
Service listening on TCP 80
Public IPv4 198.51.100.24
```

**Practice Environment Evidence**

```json
{
  "InstanceId": "i-0d25992d6c616afbe",
  "InstanceType": "t3.micro",
  "State": {
    "Name": "running"
  },
  "PrivateIpAddress": "10.42.1.25",
  "PublicIpAddress": "34.227.29.209"
}
```

The recreated EC2 instance successfully entered the running state and mirrored the simulated scenario by receiving a public IPv4 address.

### VPC CIDR

**Simulated Evidence Packet**

```text
VPC: vpc-0a91
CIDR: 10.42.0.0/16
```

**Practice Environment Evidence**

```json
{
  "Vpc": {
    "CidrBlock": "10.42.0.0/16",
    "State": "pending",
    "Tags": [
      {
        "Key": "Name",
        "Value": "TKT-2026-0009-VPC"
      }
    ]
  }
}
```

The recreated VPC used the same CIDR range provided in the simulated evidence packet.

### Subnet CIDR

**Simulated Evidence Packet**

```text
Subnet: subnet-0a91
```

**Practice Environment Evidence**

```json
{
  "Subnet": {
    "SubnetId": "subnet-00df3a0d13110bf32",
    "CidrBlock": "10.42.1.0/24",
    "State": "available",
    "AvailabilityZone": "us-east-1a",
    "MapPublicIpOnLaunch": false
  }
}
```

The subnet was created within the 10.42.0.0/16 VPC address space and hosted the recreated web server.

### Subnet Type

**Simulated Evidence Packet**

```text
Internet Gateway attached
Public IPv4 assigned
Route table:
10.42.0.0/16 to local
```

**Practice Environment Evidence**

```json
{
  "MapPublicIpOnLaunch": false,
  "PublicIpAddress": "34.227.29.209"
}
```

The instance received a public IPv4 address, but route-table evidence did not demonstrate a default route to the Internet Gateway. Based on available evidence, the subnet could not be confirmed as public.

### Route Table Entries

**Simulated Evidence Packet**

```text
Associated route table rtb-0a91

10.42.0.0/16 to local (active)
```

**Practice Environment Evidence**

```json
{
  "RouteTableId": "rtb-04ba158e4cf6270ac",
  "Routes": [
    {
      "DestinationCidrBlock": "10.42.0.0/16",
      "GatewayId": "local",
      "State": "active"
    }
  ]
}
```

The recreated route table matched the simulated evidence and contained only the local VPC route.

No default route was present:

```text
0.0.0.0/0 to Internet Gateway
```

### Route Table Association

**Practice Environment Evidence**

```json
{
  "RouteTableAssociationId": "rtbassoc-0edfd82368f847abd",
  "RouteTableId": "rtb-04ba158e4cf6270ac",
  "Main": true,
  "State": "associated"
}
```

The route table was actively associated as the VPC's main route table.

### Internet Gateway Attachment

**Simulated Evidence Packet**

```text
Internet gateway igw-0a91 attached to vpc-0a91
```

**Practice Environment Evidence**

```json
{
  "InternetGateway": {
    "InternetGatewayId": "igw-0f1096ece905518c9",
    "Attachments": [],
    "Tags": [
      {
        "Key": "Name",
        "Value": "TKT-2026-0009-IGW"
      }
    ]
  }
}
```

Internet Gateway:

```text
igw-0f1096ece905518c9
```

was created and subsequently attached to the VPC. This matched the simulated evidence packet and demonstrated gateway availability at the VPC level.

### NAT Gateway Configuration

**Simulated Evidence Packet**

```text
No NAT gateway information provided.
```

**Practice Environment Evidence**

```text
No NAT Gateway was deployed.
```

A NAT Gateway was not required because the investigation focused on inbound HTTP traffic from internet clients to a public-facing web application.

### Security Group Rules

**Simulated Evidence Packet**

```text
Security group sg-0a91:
Inbound TCP 80 from 0.0.0.0/0 allowed
```

**Practice Environment Evidence**

```json
{
  "SecurityGroupRuleId": "sgr-088715db83eb307af",
  "GroupId": "sg-0c0deeae195121756",
  "IpProtocol": "tcp",
  "FromPort": 80,
  "ToPort": 80,
  "CidrIpv4": "0.0.0.0/0"
}
```

Configured rule:

```text
Protocol: TCP
Port: 80
Source: 0.0.0.0/0
```

This matched the simulated evidence and supported inbound web traffic.

### Network ACL Rules

**Simulated Evidence Packet**

```text
Network ACL:
Inbound TCP 80 allowed
Outbound TCP 1024-65535 allowed
```

**Practice Environment Evidence**

```json
{
  "NetworkAclId": "acl-037303c3f089a536c",
  "IsDefault": true
}
```

```text
Inbound Rule 100:
Allow All
0.0.0.0/0

Outbound Rule 100:
Allow All
0.0.0.0/0
```

The default Network ACL permitted traffic in both directions and did not indicate a filtering issue.

### Public or Private IP Addressing

**Simulated Evidence Packet**

```text
Public IPv4: 198.51.100.24
```

**Practice Environment Evidence**

```json
{
  "PrivateIpAddress": "10.42.1.25",
  "PublicIpAddress": "34.227.29.209"
}
```

The recreated instance received both a private IPv4 address and a public IPv4 address, demonstrating that public addressing alone does not prove internet reachability.

### Application Port

**Simulated Evidence Packet**

```text
TCP Port 80
```

The required application port for the investigation was TCP port 80 and was used throughout the traffic-path analysis.

### Flow Log Evidence

**Simulated Evidence Packet**

```text
No flow log evidence provided.
```

**Practice Environment Evidence**

```text
No VPC Flow Logs were configured or collected.
```

No packet-level acceptance or rejection evidence was available.

### Connectivity Test Results

**Simulated Evidence Packet**

```text
No connectivity retest provided.
```

**Practice Environment Evidence**

```text
Internet Gateway attached
Public IPv4 assigned
Security Group permits TCP 80
Network ACL permits traffic
Route table contains only:
10.42.0.0/16 to local
```

No route-table evidence demonstrated a default route to the Internet Gateway. This recreated the primary condition described in the HarborTech evidence packet and supported further analysis of the routing layer as the earliest unsupported component of the required traffic path.

### Practice Environment Comparison

The AWS Learner Lab environment was used only to recreate and better understand the conditions described in the Client's problem. Findings from the practice environment should not be represented as production evidence.

The HarborTech analysis relied on the simulated VPC evidence packet. The recreated environment demonstrated that an attached Internet Gateway, a public IPv4 address, permissive Security Group rules, and permissive Network ACL rules do not alone establish internet reachability when route-table evidence does not show a valid path to the Internet Gateway.
## Operational Analysis
Explain what the evidence shows about the network path.
Identify whether the issue involves routing, addressing, gateway configuration, subnet design, security groups, network ACLs, or another network layer.

## Recommendation
Based on the available evidence, the next operational action is to verify whether the subnet associated with the EC2 instance has an active default route of **0.0.0.0/0 to the Internet Gateway** and confirm that the subnet is associated with the expected route table.
This recommendation addresses the most likely connectivity issue without making unnecessary network changes. The EC2 instance is running, the web application is expected to be listening on TCP port 80, the Security Group allows inbound TCP 80 from **0.0.0.0/0**, and the Network ACL permits the required traffic. These controls support the intended communication path and do not currently indicate a filtering problem.

The route-table evidence, however, shows only:

```text
10.42.0.0/16 to local
```

No evidence was provided demonstrating a default route to the Internet Gateway. As a result, the first unsupported assumption is that the subnet is public and capable of receiving internet traffic.

The recommended verification is to review the subnet association and route-table configuration and determine whether a route exists for:

```text
0.0.0.0/0 to Internet Gateway
```

If the route is missing and a public-facing web application is the intended design, a routing change should be submitted through the appropriate approval process before implementation. After route verification or correction, a new connectivity test should be performed from an external client to the web server on TCP port 80 to confirm end-to-end reachability.

This recommendation follows the evidence collected during the investigation and focuses on the earliest network layer where support for the required traffic path is missing, while avoiding unnecessary changes to Security Groups, Network ACLs, or other network controls that currently support the intended traffic flow.

## Escalation Notes

At the current stage of the investigation, no production changes have been implemented. The evidence reviewed supports additional verification before any routing, gateway, security, or architecture modifications are performed.

If verification confirms that the subnet intended to host the public-facing application lacks a default route of:

```text
0.0.0.0/0 to Internet Gateway
```

then a production routing change would require approval before implementation. This change would affect network connectivity and should follow established change management procedures.

No evidence currently supports modifying:

- Security Group rules
- Network ACL rules
- NAT Gateway configuration
- VPC CIDR assignments
- Subnet CIDR assignments
- Application configuration

The Security Group and Network ACL evidence provided in the ticket support the required TCP port 80 traffic flow and do not indicate a filtering issue. As a result, modifying those controls would not be supported by the evidence collected during this investigation.

If route-table verification confirms that the required default route already exists, additional escalation may be required to investigate other layers of the traffic path, including subnet associations, connectivity testing, application behavior, or additional network controls not included in the evidence packet.

At this time, the only potential escalation identified is a production routing change if route verification confirms that internet connectivity is not supported by the existing route-table configuration.
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

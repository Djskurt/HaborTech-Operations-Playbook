# Week 6: Containers and Serverless Computing

## HarborTech Ticket Summary
TKT-2026-0006: Bright Path Community Services currently runs a daily reporting task and a small API on a single EC2 instance that remains online even when little work is occurring. The client's applications are functioning correctly, but they continue to incur the operational responsibilities associated with maintaining a virtual machine, including operating-system and runtime management.
The compute-model problem is determining whether each workload still requires a continuously running server or whether a different execution model would provide a better operational fit. The evaluation compares virtual machines, containers, and serverless services based on workload timing, triggers, state requirements, scaling needs, administrative overhead, and resource utilization. The reporting task runs once each morning, completes quickly, and maintains no local state, while the API receives intermittent requests throughout the day. The objective is to identify the best-fit compute model for each workload, determine which responsibilities could move to AWS, identify which responsibilities would remain with HarborTech and the client, and provide an evidence-based recommendation rather than assuming all workloads should use the same service.

## Client Impact
An inefficient compute model can increase costs for the client by keeping resources running when little work is occurring and paying for capacity that is not being fully utilized. It can also affect reliability because HarborTech and the client must continue managing operating-system maintenance, patching, and server administration, which increases the risk of configuration issues or service disruptions.

A poorly matched model may limit scalability by relying on fixed server resources that require manual management as demand changes. It also increases operational effort by requiring ongoing maintenance regardless of workload activity. Choosing a compute model that aligns with workload behavior can improve reliability, optimize costs, support scaling needs, and reduce the management burden placed on both HarborTech and the client by shifting appropriate infrastructure responsibilities to AWS.

## AWS Services Involved
Identify the AWS services and concepts involved in the investigation, such as:
- Amazon EC2 
- Containers 
- Amazon ECS 
- Serverless Computing 
- AWS Lambda 
- Amazon API Gateway 
- Event-Driven Computing 
- Amazon EventBridge 
- IAM Execution Roles 
- VPC-Connected Lambda 
- Monitoring and Logs 
- Amazon CloudWatch
- AWS CloudShell
- AWS CLI
- AWS Step Functions
- Compute Models (VM, Container, Serverless)
- Stateless Workloads
- Cost Optimization
- Scalability and Resource Utilization
### Summary 
These AWS services and concepts were used to evaluate the most appropriate compute model for Bright Path Community Services' reporting task and API workload. The investigation compared virtual machine, container, and serverless options based on workload timing, triggers, state requirements, infrastructure control, scalability, cost, and operational responsibility. AWS CloudShell and the AWS CLI were used to interact with AWS services and validate configurations, while services such as Lambda, API Gateway, EventBridge, ECS, and CloudWatch were examined to understand how managed and serverless services could reduce idle infrastructure and administrative overhead. The evaluation focused on determining which responsibilities could move to AWS, which responsibilities would remain with HarborTech and the client, and which compute model best fit each workload based on the available evidence.
## Virtualization Connection
Explain how containers and serverless computing represent different levels of abstraction from traditional virtual machines and reduce direct infrastructure management.

## Evidence Reviewed
Document the evidence you reviewed, such as:
- Workload duration and frequency
- Current server utilization
- Trigger or event pattern
- Runtime and dependency requirements
- IAM permissions
- API request behavior
- Lambda logs and errors
- VPC connectivity requirements
- Workflow steps and failure points
- Monitoring evidence

## Operational Analysis
Explain what the evidence shows about the current compute model.
Identify whether the workload is better suited for a virtual machine, container, serverless function, API-driven service, or coordinated workflow.

## Recommendation
Recommend the compute approach that best fits the workload.
Explain how the recommendation addresses the operational need and responsibility tradeoff.

## Escalation Notes
Document any permission, networking, application, deployment, or production change that requires approval or additional support.
If no escalation is required, state that clearly.

## Lessons Learned
Explain what Week 6 taught you about containers, serverless computing, APIs, event-driven workloads, and selecting the right compute model.

## Professional Vocabulary
Define the important Week 6 terms in your own words.
Include terms such as:
- Container
- Serverless Computing
- AWS Lambda
- API Gateway
- Execution Role
- Event Trigger
- Step Functions
- VPC-Connected Lambda
- Runtime
- Abstraction
- Stateless Workloads – Workloads that do not require local state between executions, making them
- Operational Responsibility Model – Evaluation of which responsibilities remain with HarborTech and the client versus which responsibilities move to AWS.
- Application Dependencies – Determining whether software requirements can run in Lambda, containers, or require full operating-system control on a virtual machine.
- Scheduled Execution – Execution pattern used by the daily reporting workload.
- API-Based Workloads – Request-driven execution pattern used by the small REST API.

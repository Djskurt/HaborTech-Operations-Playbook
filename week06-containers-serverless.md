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
Containers and serverless computing both reduce infrastructure management compared to a traditional virtual machine. 
In the current EC2 model, HarborTech and the client are responsible for maintaining the guest operating system, applying patches, managing runtime dependencies, and troubleshooting the server environment.
Containers increase the level of abstraction by packaging the application and its dependencies while sharing a host operating-system kernel. 
This reduces operating-system overhead and simplifies application deployment, but some infrastructure and platform management responsibilities still remain.
Serverless computing increases abstraction further by removing the need to manage servers altogether. 
Services such as Lambda run code only when triggered by a request, event, or schedule, allowing AWS to manage the underlying infrastructure, scaling, and operating-system maintenance.
As the compute model moves from virtual machines to containers and then to serverless execution, direct infrastructure management decreases and more operational responsibility shifts to AWS. 
This allows HarborTech and the client to focus more on application functionality, configuration, security, and monitoring rather than maintaining continuously running servers.

## Evidence Reviewed
Document the evidence you reviewed, such as:
#### Workload Evidence
The reporting task executes once per day, runs for approximately 20 seconds, and does not require a persistent server process. The API receives intermittent requests throughout the day. Both workloads currently share a single EC2 instance that remains online even when little work is occurring, resulting in long periods of low utilization.
#### Compute Model Evaluation
The reporting task was compared against EC2, ECS/Fargate, and Lambda before deployment activities began. The workload's short runtime, scheduled execution pattern, and lack of persistent state indicated that serverless execution was the strongest initial candidate. The primary factor that could change this recommendation would be evidence of a continuously running process, persistent state requirement, or dependency on direct operating-system access.
#### AWS Identity Verification
```bash
aws sts get-caller-identity
```
Output:
```JSON
{
  "UserId": "AROAY****************:user539****=Daniel_J._Scurek",
  "Account": "REDACTED",
  "Arn": "arn:aws:sts::REDACTED:assumed-role/voclabs/user539****=Daniel_J._Scurek"
}
```
This confirmed authenticated access through the Learner Lab environment and verified the active AWS identity before resource discovery and deployment activities.
#### AWS Resource Discovery
Read-only discovery commands were executed to identify existing compute resources:
```bash
aws lambda list-functions --region us-east-1
```
```bash
aws ecs list-clusters --region us-east-1
```
Existing Lambda functions were successfully returned. The ECS discovery command returned:
```JSON
{
  "clusterArns": []
}
```
The empty list indicated that no ECS clusters were deployed in the region at the time of the investigation.
#### IAM and Permission Evidence
Testing was performed using the existing LabRole as required by the AWS Learner Lab environment. The investigation confirmed that creating a new administrative IAM role was not permitted and that all deployment and testing activities should use the preexisting role.
#### Lambda Proof-of-Concept Deployment
The Lambda proof of concept was created and deployed using the following commands:
```bash
mkdir -p ~/ht-week06 && cd ~/ht-week06
```
```bash
python3 -m zipfile -c function.zip lambda_function.py
```
```bash
ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
```
```bash
FUNCTION_NAME="ht-w6-djs443-event"
```
```bash
aws lambda create-function \
--function-name "$FUNCTION_NAME" \
--runtime python3.12 \
--role "arn:aws:iam::${ACCOUNT_ID}:role/LabRole" \
--handler lambda_function.lambda_handler \
--zip-file fileb://function.zip \
--timeout 10 \
--memory-size 128 \
--region us-east-1
```
```bash
aws lambda wait function-active-v2 \
--function-name "$FUNCTION_NAME" \
--region us-east-1
```
```bash
aws lambda get-function-configuration \
--function-name "$FUNCTION_NAME" \
--region us-east-1
```
Configuration evidence confirmed:
- Runtime: Python 3.12
- Handler: lambda_function.lambda_handler
- Role: LabRole
- Memory: 128 MB
- Timeout: 10 seconds
- Package Type: Zip

Successful deployment demonstrated that the reporting workload could execute within a managed serverless runtime.
#### Successful Invocation Evidence
A valid test event was created:
```bash
printf '%s' '{"department":"operations"}' > valid.json
```
The function was invoked with:
```bash
aws lambda invoke \
--function-name ht-w6-djs443-event \
--payload fileb://valid.json \
response.json
```
Lambda returned a successful invocation and the response file contained:
```JSON
{
  "statusCode": 200,
  "body": "{\"report\":\"ready\",\"department\":\"operations\"}"
}
```
This confirmed successful function execution and successful application processing when valid input was provided.
#### Failed-Test Evidence
An invalid test event was created with the required field omitted:
```bash
printf '%s' '{}' > invalid.json
```
The function was invoked again:
```bash
aws lambda invoke \
--function-name ht-w6-djs443-event \
--payload fileb://invalid.json \
error-response.json
```
Lambda reported a successful invocation:
```JSON
{
  "StatusCode": 200
}
```
However, the application response contained:
```JSON
{
  "statusCode": 400,
  "body": "{\"error\":\"department is required\"}"
}
```
#### Failure Analysis
The failed test demonstrated that Lambda successfully executed the function because the invocation returned StatusCode: 200. The returned application response of 400 showed that the request failed validation because the required department field was missing. This confirmed the issue was application-level validation rather than a Lambda service failure or infrastructure problem.
#### Monitoring and Validation Evidence
Configuration data, invocation responses, status codes, and application outputs were reviewed throughout testing. Evidence confirmed successful deployment, successful execution with valid input, predictable validation behavior with invalid input, and proper operation of the serverless runtime.
#### Remaining Validation Needs
No evidence indicated a requirement for persistent local storage, host-level troubleshooting, custom operating-system packages, VPC connectivity, or continuously running background processes. Additional validation should confirm dependency requirements, integration constraints, runtime limits, and any need for direct operating-system control before implementation or migration decisions are approved.

## Operational Analysis
The evidence shows the current EC2 model is functional but inefficient because the reporting task runs for approximately 20 seconds once per day and the API receives only intermittent requests, while the server remains online and requires ongoing operating-system maintenance and administration.
The reporting task is best aligned with AWS Lambda because it is scheduled, short-lived, and stateless. The API is best aligned with an API Gateway and Lambda design because requests are event-driven and do not require a continuously running server. A container-based model remains a viable alternative if future dependency or packaging requirements exceed Lambda capabilities. A virtual machine would be justified only if future validation identifies a need for continuous processing, custom operating-system packages, or regular host-level administration.
Based on the available evidence, serverless execution provides the best operational fit for both workloads while reducing infrastructure-management responsibilities and idle resource utilization.

## Recommendation
The evidence supports a serverless recommendation for both workloads. The daily reporting task should be implemented with AWS Lambda because it runs once per day, completes in approximately 20 seconds, and maintains no persistent state between executions. The small API should be implemented using API Gateway and Lambda because requests are intermittent and do not require a continuously running server.
This approach addresses the operational need by aligning compute resources with actual workload activity rather than keeping an EC2 instance online during long periods of inactivity. AWS would assume responsibility for the underlying infrastructure, operating-system maintenance, and scaling of the execution environment. HarborTech and the client would remain responsible for application code, configuration, integrations, IAM permissions, security controls, monitoring, and troubleshooting application-level issues.
This recommendation assumes the workloads do not require persistent server processes, custom operating-system packages, or regular host-level administration. Any migration planning, production API changes, or final architecture decisions should be escalated to the appropriate HarborTech engineering and architecture teams before implementation.

## Escalation Notes
No immediate escalation is required for the evaluation itself because this investigation is limited to recommending a compute model based on workload evidence. As an intern, my role is to assess the workload and provide a recommendation rather than approve or implement changes.
If the recommendation is accepted, any migration from EC2 to Lambda, API Gateway, containers, or another compute platform should be escalated to the appropriate HarborTech engineering and architecture teams for review and approval. Production API modifications, deployment planning, IAM permission changes, networking changes, VPC integration requirements, and architecture decisions also require approval before implementation.
The recommendation further assumes that the reporting task and API do not require persistent server processes, custom operating-system packages, host-level administration, or other dependencies that would prevent serverless adoption. Additional validation of application dependencies, integration requirements, and production operational needs should be completed before any implementation decision is approved.

## Lessons Learned
Week 6 reinforced that compute models should be selected based on workload evidence rather than a one-size-fits-all approach. Containers and serverless computing reduce infrastructure management compared to traditional virtual machines, but each model addresses different operational requirements.

The investigation demonstrated how event-driven workloads can use services such as API Gateway and Lambda to run only when triggered, reducing idle resources and administrative overhead. 
It also highlighted the importance of separating API requests from application execution and understanding which responsibilities remain with HarborTech versus those managed by AWS.

Most importantly, the investigation showed that workload timing, state requirements, dependencies, control needs, and operational responsibility should drive compute-model recommendations. 
Short-lived, stateless workloads are strong candidates for serverless execution, while workloads requiring greater infrastructure control may be better suited for containers or virtual machines.

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

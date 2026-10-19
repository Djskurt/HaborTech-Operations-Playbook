# Week 7: Managed Database Services

## HarborTech Ticket Summary
TKT-2026-0007: HarborTech received a client advisory request from a café business operating a transactional MySQL database on an Amazon EC2 instance. The database is functioning correctly, but the client is experiencing increased operational overhead related to guest operating system maintenance, database patching, backup management, storage administration, and recovery planning.
The workload uses a relational data model with structured tables, SQL queries, and transaction processing. The purpose of this assessment was to investigate the current database operating model, document workload requirements, compare applicable AWS database services, and identify factors that should be evaluated before making a future database modernization decision.
As a HarborTech Cloud Operations Intern, I documented the existing environment, gathered infrastructure and database evidence, deployed a test Amazon RDS MariaDB environment, migrated application data, and validated application functionality within the AWS Academy learner lab environment.

## Client Impact
Database availability affects the café's ability to process orders, retrieve data, and support daily operations. An outage or failed recovery event could disrupt application access and business transactions.
Performance issues can increase application response times and negatively affect the user experience. Migration activities also introduce risk because application connectivity, database compatibility, and data integrity must be maintained throughout the transition.
An incorrect database service choice could increase costs, add operational complexity, limit scalability, or require unnecessary application changes. Therefore, workload requirements, performance needs, availability objectives, and migration constraints must be validated before any production database decision is made.

## AWS Services Involved
- AWS Services
- Amazon EC2
- Amazon RDS (MariaDB)
- Amazon Aurora
- Amazon DynamoDB
- AWS Systems Manager Parameter Store
- Amazon CloudWatch
- Amazon VPC
### AWS Concepts
- Relational Databases
- NoSQL Databases
- MySQL/MariaDB Database Engines
- SQL Queries
- Transaction Processing
- Managed Database Services
- Database Migration
- Database Backups and Recovery
- Database Connectivity
- High Availability
- Availability Zones
- Multi-AZ Design
- Application Compatibility
- Migration Risk Analysis
- Shared Responsibility Model
### Networking and Security Components
- Security Groups
- Subnets
- DB Subnet Groups
### Summary
TKT-2026-0007 involved evaluating database modernization options for a café business operating a transactional MySQL workload on Amazon EC2. The investigation focused on managed database services, migration activities, database connectivity, monitoring, backup and recovery responsibilities, workload compatibility, networking requirements, security controls, and the operational differences between self-managed and managed database platforms.

## Virtualization Connection
A database running directly on an Amazon EC2 instance operates on top of virtualized infrastructure, but the customer remains responsible for managing the guest operating system, database software, patching, backups, storage planning, monitoring, and recovery processes. While AWS manages the underlying physical hardware and virtualization layer, the customer is responsible for administering the server and database environment.

Managed database services such as Amazon RDS abstract additional layers of infrastructure management beyond virtualization. AWS manages the underlying compute resources, operating system maintenance, database software installation, automated backups, patching, storage provisioning, and many operational tasks associated with running the database platform. The customer remains responsible for database design, application integration, access management, query performance, and data management, but no longer manages the underlying database server directly.

In TKT-2026-0007, the client's operational burden stemmed from maintaining a self-managed MySQL database on an EC2 virtual machine. The investigation evaluated whether moving to a managed database service could reduce administrative responsibilities while maintaining support for the existing relational database workload and application requirements.

## Evidence Reviewed
Document the evidence you reviewed, such as:
- Database engine and workload type
- Data structure
- Query or access patterns
- Availability requirements
- Backup and recovery needs
- Database connectivity
- Security group rules
- Migration requirements
- Engine compatibility
- Schema conversion needs
- Current administrative workload
- Cost or scaling considerations

## AWS Academy Activity: Actions and Results
Record commands or exact console actions, observed outputs or statuses, and verification results. Explain what each result demonstrates. Do not include secrets.

## Operational Analysis
Explain what the evidence shows about the database workload.
Identify whether the operational need is best addressed by a relational database, NoSQL database, managed database service, migration approach, or another supported option.

## Recommendation
Recommend the database service or operational approach that best fits the workload.
Explain how the recommendation addresses the identified requirements and responsibility tradeoffs.

## Escalation Notes
Document any database migration, production connectivity, availability, permission, or configuration issue that requires approval or additional support.
If no escalation is required, state that clearly.

## Lessons Learned
Explain what Week 7 taught you about managed databases, workload fit, availability, migration planning, and selecting database services based on evidence.

## Professional Vocabulary
Define the important Week 7 terms in your own words.
Include terms such as:
- Relational Database
- NoSQL
- Amazon RDS
- Amazon Aurora
- Amazon DynamoDB
- Multi-AZ
- Database Migration Service
- Schema Conversion
- Managed Service
- Workload Fit

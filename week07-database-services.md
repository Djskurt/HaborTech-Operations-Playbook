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
### Database Engine and Workload Type
The client workload uses a transactional relational database hosted on an Amazon EC2 instance. The application uses structured tables, SQL queries, and transaction processing to support café ordering operations. Migration activities targeted a MariaDB-compatible Amazon RDS instance to maintain workload compatibility.

### Data Structure
The workload stores structured relational data. Validation of the migrated database confirmed product records containing product names, descriptions, pricing information, product group identifiers, and image references.

### Query and Access Patterns
The application accesses structured relational data through SQL queries. Migration validation included querying the product table after data restoration.

Command:
```
select * from product;
Observed output:

+----+---------------------------+
|  1 | Croissant                 |
|  2 | Donut                     |
|  3 | Chocolate Chip Cookie     |
|  4 | Muffin                    |
|  5 | Strawberry Blueberry Tart |
|  6 | Strawberry Tart           |
|  7 | Coffee                    |
|  8 | Hot Chocolate             |
|  9 | Latte                     |
+----+---------------------------+
```
The query returned nine product records from the migrated database.

### Availability Requirements

Database availability is critical because the application depends on product and order data. RDS availability was verified after deployment.

Command:
```
aws rds describe-db-instances \
--db-instance-identifier MomPopCafeDBInstance \
--query "DBInstances[*].[Endpoint.Address,AvailabilityZone,PreferredBackupWindow,BackupRetentionPeriod,DBInstanceStatus]"
```
Observed output:

```text
[
    [
        "mompopcafedbinstance.c6dlrml3nvep.us-east-1.rds.amazonaws.com",
        "us-east-1a",
        "09:09-09:39",
        1,
        "available"
    ]
]
```
### Backup and Recovery Requirements
The client identified backup execution, backup verification, and recovery plannng as operational burdens.

Observed output:

Preferred Backup Window: 09:09-09:39
Backup Retention Period: 1
### Database Connectivity
Database connectivity was validated through direct MySQL connections, application testing, and Amazon CloudWatch monitoring.

AWS Management Console Path:

Amazon RDS → Databases → mompopcafedbinstance → Monitoring → DB Connections
Application Validation Path:

AWS Systems Manager → Parameter Store → /mompopcafe/dbUrl → Edit
Updated value:

mompopcafedbinstance.c6dlrml3nvep.us-east-1.rds.amazonaws.com
Database Connection Command:
```
mysql --user=root \
--host=mompopcafedbinstance.c6dlrml3nvep.us-east-1.rds.amazonaws.com \
mom_pop_db
```
Validation Query:
```
select * from product;
```
Observed Results:

DB Connections while connected: 1

DB Connections after disconnecting: 0

Website loaded successfully after updating the
/mompopcafe/dbUrl parameter.

Orders before migration: 9

Orders after migration: 9
The DB Connections metric confirmed successful communication between the application and the Amazon RDS database. Website functionality, preserved order history, and successful SQL query results verified that the application was using the migrated RDS database.
### Security Group Rules
A dedicated security group was reated to protect the Amazon RDS dtabase and restrict MySQL access to the application security group.

command:
```
aws ec2 authorize*security-group-ingress \
--group-id sg-0989f7a6c8f2e3fe9 \
--protocol tcp \
--port 3306 \
--source-group sg-0c6bd8d2be9fc8938
```
Observed output:

Rule added successfully to allow TCP port 3306 traffic from security group sg-0c6bd8d2be9fc8938.
Migration Requirements
Migration required creation of a database backup, restoration o Amazon RDS, application reconfiguration, and validation testing.

Commands:
```
mysqldump --databases mom_pop_db --add-drop-database > mompopdb-backup.sql
--host=mompopcafedbinstanc*.c6dlrml3nvep.us-east-1.rds.amazonaws.com \
< mompopdb-backup.sql
```
Observed results:
```text
mysqldump completed successfully.

Database restore completed successfully.
```

### Engine Compatibility

An engine compatibility issue was identified during deployment.

Command:
```bash
aws rds create-db-instance \
--db-instance-identifier MomPopCafeDBInstance \
--engine mariadb \
--engine-version 10.6.14
Observed output:
```
An error occurred (InvalidParameterCombination) when calling the CreateDBInstance operation:

Cannot find version 10.6.14 for mariadb
Investigation command:
```
aws rds describe-db-engine-versions \
--engine mariadb \
--query "DBEngineVersions[*].EngineVersion"
```
Observed output (partial):
```
10.5.28
10.5.29
10.6.22
10.6.23
10.6.24
10.6.25
10.6.27
10.6.28
```
The deployment was successfully completed using MariaDB version 10.6.28.

### Schema Conversion Requirements

Schema conversion was not required because both the source database and the target database used MariaDB-compatible database engines. The migration was completed using a native database backup and restore process rather than converting schemas between different database platforms.

Migration Commands:

```bash
mysqldump --databases mom_pop_db --add-drop-database > mompopdb-backup.sql
```

```bash
mysql \
--host=mompopcafedbinstance.c6dlrml3nvep.us-east-1.rds.amazonaws.com \
< mompopdb-backup.sql
```

Observed Results:

```text
mysqldump completed successfully.

Database restore completed successfully.

select * from product;
```

The successful restoration and validation of all nine product records confirmed that schema conversion was unnecessary for this migration because database compatibility was maintained between the source and target environments.
### Current Administrative Workload
The client reported operational effort associated with:

- Guest operating system maintenance
- Database patching
- Backup execution
- Backup verification
- Storage management
- Recovery planning
These responsibilities prompted the evaluation of managed database services.
### Cost and Scaling Considerations

The client's primary concern was the operational burden associated with managing a self-hosted database on Amazon EC2 rather than an immediate performance or scalability issue. Current evidence did not include transaction volume, growth projections, performance baselines, recovery objectives, or budget constraints.

Observed Environment:

```text
Source Database Platform:
Amazon EC2-hosted MySQL/MariaDB

Target Database Platform:
Amazon RDS MariaDB

Source Instance Type:
t2.small

Target Database Instance Class:
db.t3.micro
```

Observed Administrative Requirements:

- Guest operating system maintenance
- Database patching
- Backup execution
- Backup verification
- Storage management
- Recovery planning

Limitations:
The investigation did not include workload growth projections, cost modeling, transaction volume metrics, recovery objective requirements, or long-term scaling forecasts. Additional validation of workload demand, performance requirements, availability objectives, and budget considerations would be required before making a production database modernization recommendation.
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

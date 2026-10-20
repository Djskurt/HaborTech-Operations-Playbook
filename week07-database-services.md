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

### Evidence Entry 1 - Create RDS Networking Components

#### Action or Command

Created two private subnets and a database subnet group required for the Amazon RDS deployment.

```bash
aws ec2 create-subnet \
--vpc-id vpc-07674b4a4bc6175d9 \
--cidr-block 10.200.2.0/23 \
--availability-zone us-east-1a
```

```bash
aws ec2 create-subnet \
--vpc-id vpc-07674b4a4bc6175d9 \
--cidr-block 10.200.10.0/23 \
--availability-zone us-east-1b
```

```bash
aws rds create-db-subnet-group \
--db-subnet-group-name "MomPopCafeDB Subnet Group" \
--db-subnet-group-description "DB subnet group for Mom & Pop Cafe" \
--subnet-ids subnet-0c7ff9cc7cb0288b0 subnet-036c3d3a0ce14a88c
```

#### Actual Result

```text
Private Subnet 1 SubnetId:
subnet-0c7ff9cc7cb0288b0

Private Subnet 2 SubnetId:
subnet-036c3d3a0ce14a88c
```

#### Interpretation

The required Amazon RDS networking components were successfully created. Two private subnets were provisioned in separate Availability Zones and used to support the database subnet group required for the RDS deployment.

#### Limitations

This evidence confirms supporting infrastructure creation but does not verify successful database deployment, connectivity, or application functionality.

---

### Evidence Entry 2 - Create and Validate Amazon RDS Instance

#### Action or Command

Attempted to create the Amazon RDS MariaDB instance using the lab-specified engine version, identified a version compatibility issue, and deployed the database using a supported version.

```bash
aws rds create-db-instance \
--db-instance-identifier MomPopCafeDBInstance \
--engine mariadb \
--engine-version 10.6.14
```

```bash
aws rds describe-db-engine-versions \
--engine mariadb \
--query "DBEngineVersions[*].EngineVersion"
```

```bash
aws*rds create-db-instance \
--db-inst*nce-identifier MomPopCafeDBInstanc* \
*-engine mariadb \
--engine-version*10.6.28
*``

```bash
aws rds describe-db-in*tances \
--db-instance-identifier *omPopCafeDBInstance \
--query "DBInstances[*].[Endpoint.Address,AvailabilityZone,PreferredBackupWindow,BackupRetentionPeriod,DBInstanceStatus]"
```

#### Actual Result

```text
An error occurred (InvalidParameterCombination) when calling the CreateDBInstance operation:

Cannot find version 10.6.14 for mariadb
```

Supported engine versions included:

```text
10.5.28
10.5.29
10.6.22
10.6.23
10.6.24
10.6.25
10.6.27
10.6.28
```

Deployment verification:

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

#### Interpretation

The initial deployment failed because the MariaDB version specified in the lab was no longer available. After reviewing available versions, the database was successfully deployed using MariaDB version 10.6.28. The output confirmed that the RDS instance became available and provided the endpoint, backup window, and retention settings.

#### Limitations

Successful deployment confirms service availability but does not verify successful migration of application data or application connectivity.

---

### Evidence Entry 3 - Migrate and Validate Database Data

#### Action or Command

Created a backup of the source database, restored the backup to Amazon RDS, and validated the migrated data.

```bash
mysqldump --databases mom_pop_db --add-drop-database > mompopdb-backup.sql
```

```bash
mysql \
--host=mompopcafedbinstance.c6dlrml3nvep.us-east-1.rds.amazonaws.com \
< mompopdb-backup.sql
```

Validation query:

```sql
select * from product;
```

#### Actual Result

```text
+----+---------------------------+-----------------------------------------------------------------+-------+---------------+-----------------------------------------+
| id | product_name              | description                                                     | price | product_group | image_url                               |
+----+---------------------------+-----------------------------------------------------------------+-------+---------------+-----------------------------------------+
|  1 | Croissant                 | Fresh, buttery and fluffy... Simply delicious!                  |
|  2 | Donut                     | We have more than half-a-dozen flavors!                         |
|  3 | Chocolate Chip Cookie     | Made with Swiss chocolate with a touch of Madagascar vanilla    |
|  4 | Muffin                    | Banana bread, blueberry, cranberry or apple                     |
|  5 | Strawberry Blueberry Tart | Bursting with the taste and aroma of fresh fruit                |
|  6 | Strawberry Tart           | Made with fresh ripe strawberries and a delicious whipped cream |
|  7 | Coffee                    | Freshly-ground black or blended Columbian coffee                |
|  8 | Hot Chocolate             | Rich and creamy, and made with real chocolate                   |
|  9 | Latte                     | Offered hot or cold and in various delicious flavors            |
+----+---------------------------+-----------------------------------------------------------------+-------+---------------+-----------------------------------------+
```

#### Interpretation

The backup and restore process completed successfully and the migrated database returned all expected product records. The query results confirmed that application data was successfully migrated from the EC2-hosted database to the Amazon RDS instance.

#### Limitations

Validation confirmed the product table contents but did not independently verify every table, relationship, transaction history record, or production workload scenario.

---

### Final Verification

#### Verification Action

Updated the application database endpoint through:

```text
AWS Management Console
→ Systems Manager
→ Parameter Store
→ /mompopcafe/dbUrl
→ Edit
```

Updated parameter value:

```text
mompopcafedbinstance.c6dlrml3nvep.us-east-1.rds.amazonaws.com
```

Opened the application:

```text
http://ec2-54-152-15-53.compute-1.amazonaws.com/mompopcafe
```

Reviewed Order History and validated application functionality.

Connected to the Amazon RDS database and executed:

```sql
select * from product;
```

Monitored active database connections through:

```text
AWS Management Console
→ Amazon RDS
→ Databases
→ mompopcafedbinstance
→ Monitoring
→ DB Connections
```

#### Actual Verification Result

```text
Website loaded successfully.
```

```text
Orders before migration: 9
Orders after migration: 9
```

Product table query output:

```text
+----+---------------------------+-----------------------------------------------------------------+-------+---------------+-----------------------------------------+
| id | product_name              | description                                                     | price | product_group | image_url                               |
+----+---------------------------+-----------------------------------------------------------------+-------+---------------+-----------------------------------------+
|  1 | Croissant                 | Fresh, buttery and fluffy... Simply delicious!                  |  1.50 |             1 | images/Croissants.jpg                   |
|  2 | Donut                     | We have more than half-a-dozen flavors!                         |  1.00 |             1 | images/Donuts.jpg                       |
|  3 | Chocolate Chip Cookie     | Made with Swiss chocolate with a touch of Madagascar vanilla    |  2.50 |             1 | images/Chocolate-Chip-Cookies.jpg       |
|  4 | Muffin                    | Banana bread, blueberry, cranberry or apple                     |  3.00 |             1 | images/Muffins.jpg                      |
|  5 | Strawberry Blueberry Tart | Bursting with the taste and aroma of fresh fruit                |  3.50 |             1 | images/Strawberry-&-Blueberry-Tarts.jpg |
|  6 | Strawberry Tart           | Made with fresh ripe strawberries and a delicious whipped cream |  3.50 |             1 | images/Strawberry-Tarts.jpg             |
|  7 | Coffee                    | Freshly-ground black or blended Columbian coffee                |  3.00 |             2 | images/Coffee.jpg                       |
|  8 | Hot Chocolate             | Rich and creamy, and made with real chocolate                   |  3.00 |             2 | images/Cup-of-Hot-Chocolate.jpg         |
|  9 | Latte                     | Offered hot or cold and in various delicious flavors            |  3.50 |             2 | images/Latte.jpg                        |
+----+---------------------------+-----------------------------------------------------------------+-------+---------------+-----------------------------------------+
```

Amazon RDS monitoring results:

```text
DB Connections while connected: 1

DB Connections after disconnecting: 0
```

RDS database status:

```text
available
```

#### Conclusion and Limitations

Verification confirmed that the Mom & Pop Café application was successfully reconfigured to use the Amazon RDS MariaDB instance instead of the local database hosted on the EC2 server. The website loaded normally after the Parameter Store update, Order History remained unchanged at 9 records before and after migration, the product table returned all expected records, and Amazon RDS monitoring confirmed active database connectivity.

Testing was performed within the AWS Academy learner lab environment only. Backup recovery validation, failover testing, production workload performance testing, recovery objective validation, cost modeling, and production cutover approval were not performed. Any production migration or architectural change would require additional validation and approval by authorized personnel.

## Operational Analysis
The evidence shows that the Mom & Pop Café workload is a transactional relational database workload that relies on structured data, SQL queries, and application transactions. Migration validation confirmed that application records, product data, and order history were successfully maintained throughout the database migration process. The primary issue identified was operational overhead rather than database functionality. The client reported increased administrative effort related to operating system maintenance, database patching, backup management, verification activities, storage administration, and recovery planning. Testing demonstrated that the workload remained functional after migration from a self-managed database on Amazon EC2 to an Amazon RDS MariaDB instance. The migration required no schema conversion because the source and target environments were MariaDB-compatible. Database backup, restoration, connectivity updates, and application validation were completed successfully, and monitoring confirmed active database connectivity following the migration. Based on the collected evidence, the workload aligns with a relational database platform and the operational challenges are most closely associated with database administration responsibilities rather than workload compatibility or data model limitations.

## Recommendation
The evidence supports a managed relational database approach for this workload. Migration testing confirmed compatibility with Amazon RDS MariaDB, preserved application functionality, maintained existing order and product data, and reduced reliance on managing database infrastructure on Amazon EC2.
This recommendation addresses the client's operational requirements by retaining support for structured relational data, SQL queries, and transaction processing while reducing responsibilities associated with operating system maintenance, database patching, backup management, storage administration, and recovery planning. The primary tradeoff is that AWS assumes responsibility for managing the underlying database platform and infrastructure, while the client remains responsible for database design, application integration, access management, data integrity, and workload-specific configuration. Additional validation of performance, availability, recovery requirements, growth expectations, and budget considerations should be completed before any production implementation is approved.

## Escalation Notes
No escalation was required during the AWS Academy activity because the database migration, application reconfiguration, connectivity validation, and monitoring tasks were completed successfully within the learner lab environment. One issue was encountered when the lab-specified MariaDB version (10.6.14) was unavailable for deployment. The issue was investigated using the AWS CLI, a supported MariaDB version was identified, and the RDS instance was successfully deployed using version 10.6.28. While no active issues remain, any future production implementation would require additional review and approval. Areas requiring further support include production migration planning, recovery and backup policy validation, performance and capacity testing, network security review, application change management, cost analysis, and high-availability design verification. Any production database migration or architecture change should be reviewed and approved by appropriate technical and business stakeholders before implementation.
## Lessons Learned
Week 7 reinforced the importance of selecting database solutions based on workload requirements and operational evidence rather than assumptions. The investigation showed that workload characteristics such as structured relational data, SQL queries, and transaction processing are key factors when evaluating database options. The activity also demonstrated how managed database services can reduce operational responsibilities by shifting infrastructure maintenance, patching, backup management, and platform administration away from the customer while maintaining application functionality. This highlighted the tradeoff between managing a database directly on a virtual machine and using a managed service. Another important lesson was the value of migration planning and validation. Successful migration required infrastructure preparation, compatibility verification, data migration, application reconfiguration, and post-migration testing. The MariaDB version compatibility issue encountered during deployment emphasized the need to validate dependencies and supported configurations before implementation. Finally, the activity showed that database recommendations should be supported by evidence collected from workload analysis, migration testing, application validation, and operational requirements. Effective database decisions require balancing workload fit, availability needs, operational responsibilities, and long-term management considerations.
## Professional Vocabulary

### Relational Database

A database that stores data in tables made up of rows and columns. Relationships can be created between tables, and data is typically accessed using SQL queries.

### NoSQL

A database model designed for data that does not fit traditional table structures. NoSQL databases often store data as documents, key-value pairs, or other flexible formats.

### Amazon RDS

A managed AWS database service that automates tasks such as backups, patching, maintenance, and database administration for supported database engines.

### Amazon Aurora

A managed relational database service built for high performance and compatibility with MySQL and PostgreSQL.

### Amazon DynamoDB

A fully managed NoSQL database service designed for fast performance, scalability, and flexible data structures.

### Multi-AZ

A high-availability configuration that places database resources across multiple Availability Zones to improve resilience and reduce downtime.

### Database Migration Service (DMS)

An AWS service used to migrate data from one database to another with minimal disruption to applications.

### Schema Conversion

The process of converting database structures, tables, and database objects from one database engine format to another.

### Managed Service

A cloud service where the provider manages much of the infrastructure, maintenance, updates, and operational tasks on behalf of the customer.

### Workload Fit

The process of matching a workload's requirements, data structure, performance needs, and operational goals to the most appropriate database service.


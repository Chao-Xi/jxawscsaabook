# **L6 AWS Database Services Cheat Sheet**



## **1 Relational Database Service – RDS**

* provides Relational Database service
* supports MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server, and the new, MySQL-compatible Amazon Aurora DB engine
* as it is a managed service, shell (root ssh) access is not provided
* **manages backups, software patching, automatic failure detection, and recovery**
* supports use initiated manual backups and snapshots
* **daily automated backups with database transaction logs enables Point in Time recovery up to the last five minutes of database usage**
* **snapshots** are user-initiated storage volume snapshot of DB instance, backing up the **entire DB instance and not just individual databases** that can be restored as a independent RDS instance

### **1-1 RDS Security**

* support encryption at rest using KMS as well as encryption in transit using SSL endpoints
* supports IAM database authentication, **which prevents the need to store static user credentials in the database, because authentication is managed externally using IAM**.
* supports Encryption only during creation of an RDS DB instance
* **existing unencrypted DB cannot be encrypted** and y**ou need to create a  snapshot, created a encrypted copy of the snapshot and restore as encrypted DB**
* supports **Secret Manager for storing and rotating secrets**
* for encrypted database
	* **logs, snapshots, backups, read replicas are all encrypted as well**
	* cross region replicas and snapshots does not work across region (Note – this is possible now with latest AWS enhancement)

### **1-2 Multi-AZ deployment**

* **<mark>provides high availability and automatic failover support and is NOT a scaling solution</mark>**
* maintains a **synchronous standby replica in a different AZ**
* transaction success is returned only if the commit is successful both on the primary and the standby DB
* Oracle, PostgreSQL, MySQL, and MariaDB DB instances use **Amazon technology**, while SQL Server DB instances use SQL **Server Mirroring**
* snapshots and backups are taken from standby & eliminate I/O freezes
* during automatic failover, its seamless and **RDS switches to the standby instance and updates the DNS record to point to standby**
* failover can be **forced** with the Reboot with failover option


### **1-3 Read Replicas**

* uses the PostgreSQL, MySQL, and MariaDB DB engines’ built-in replication functionality to create a separate Read Only instance
* updates are **asynchronously** copied to the Read Replica, and data might be stale
* can help scale applications and reduce read only load
* **<mark>requires automatic backups enabled</mark>**
* replicates all databases in the source DB instance
* for disaster recovery, can be promoted to a full fledged database
* can be **created in a different region** for disaster recovery, migration and low latency across regions
* <mark>**can’t** create encrypted read replicas from unencrypted DB or read replica</mark>

RDS does not support all the features of underlying databases, and if required the database instance can be launched on an EC2 instance

### **1-4 RDS Components**

* DB parameter groups contains engine configuration values that can be applied to one or more DB instances of the same instance type for e.g. SSL, max connections etc.
* **Default DB parameter group cannot be modified, create a custom one and attach to the DB**
* Supports static and dynamic parameters
	* changes to **dynamic parameters are applied immediately** (irrespective of apply immediately setting)
	* **changes to static parameters are NOT applied immediately and require a manual reboot**.

### **1-5 RDS Monitoring & Notification**

* integrates with **CloudWatch and CloudTrail**
* CloudWatch provides metrics about **CPU utilization from the hypervisor for a DB instance, and Enhanced Monitoring gathers its metrics from an agent on the instance**
* Performance Insights is a database performance tuning and monitoring feature that helps illustrate the **database’s performance and help analyze any issues that affect it**
* supports **RDS Event Notification which uses the SNS to provide notification** when an RDS event like creation, deletion or snapshot creation etc occurs


## **2 Aurora**

* is a relational database engine that combines the speed and reliability of high-end commercial databases with the simplicity and cost-effectiveness of open source databases
* is a managed services and handles time-consuming tasks such as provisioning, patching, backup, recovery, failure detection and repair
* **is a proprietary technology from AWS (not open sourced)**
* **provides PostgreSQL and MySQL compatibility**
* is “AWS cloud optimized” and claims 5x performance improvement
	* over MySQL on RDS, over 3x the performance of PostgreSQL on RDS
* **scales storage automatically** in increments of **10GB, up to 64 TB** with no impact to database performance. Storage is striped across 100s of volumes.
* **no need to provision storage in advance**.
* provides **self-healing storage**. Data blocks and disks are continuously scanned for errors and repaired automatically.
* provides **instantaneous failover**
* **replicates each chunk of my the database volume six ways across three Availability Zones** i.e. 6 copies of the data across 3 AZ
	* requires 4 copies out of 6 needed for writes
	* requires 3 copies out of 6 need for reads
* **costs more than RDS (20% more) – but is more efficient**

### **2-1 Read Replicas**

* **can have 15 replicas while MySQL has 5, and the replication process is faster (sub 10 ms replica lag)**
* share the same data volume as the primary instance in the same AWS Region, there is virtually no replication lag
* **supports Automated failover for master in less than 30 seconds**
* **supports Cross Region Replication using either physical or logical replication**.

### **2-2 Security**

* supports Encryption at rest using KMS
* supports Encryption in flight using SSL (same process as MySQL or Postgres)
* Automated backups, snapshots and replicas are also encrypted
* Possibility to authenticate using IAM token (same method as RDS)
* supports protecting the instance with security groups
* does not support SSH access to the underlying servers

### **2-3 Aurora Serverless**

* provides automated database Client instantiation and on-demand  autoscaling based on actual usage
* provides a relatively simple, **cost-effective option for infrequent, intermittent, or unpredictable workloads**
* automatically starts up, shuts down, and scales capacity up or down based on the application’s needs. No capacity planning needed
* **Pay per second, can be more cost-effective**

### **2-3 Aurora Global Database**

* allows a single Aurora database to **span multiple AWS regions.**
* **provides Physical replication**, which uses dedicated infrastructure that leaves the databases entirely available to serve the application
* **supports 1 Primary Region (read / write)**
* replicates across up to 5 secondary (read-only) regions, **replication lag is less than 1 second**
* **supports up to 16 Read Replicas per secondary region**
* **<mark>recommended for low-latency global reads and disaster recovery with an RTO of < 1 minute</mark>**
* **failover is not automated** and if the primary region becomes unavailable, a secondary region can be manually removed from an Aurora Global Database and promote it to take full reads and writes. 
	* Application needs to be updated to point to the newly promoted region.

### **2-4 Aurora Backtrack**

* Backtracking “rewinds” the DB cluster to the specified time
* Backtracking performs in place restore and does not create a new instance. There is a minimal downtime associated with it.

### **2-5 Aurora Clone feature** 

* allows quick and cost-effective creation of Aurora Cluster duplicates
* **supports parallel or distributed query using Aurora Parallel Query**, which refers to the ability to push down and distribute the computational load of a single query across thousands of CPUs in Aurora’s storage layer.


## **3 DynamoDB**

* fully managed **NoSQL database service**
* synchronously **replicates data across three facilities in an AWS Region**, giving high availability and data durability
* runs exclusively on **SSDs** to provide high I/O performance
provides **provisioned table reads and writes**
* **automatically partitions, reallocates and re-partitions the data** and provisions **additional server capacity as data or throughput changes**
* creates and maintains **indexes for the primary key attributes** for efficient access of data in the table

### **3-1 supports Secondary Indexes**

* allows **querying attributes other then the primary key attributes without impacting performance.**
* are automatically **maintained as sparse objects**

### **3-2 Local vs Global secondary index**

* **shares partition key + different sort key <mark>vs</mark> different partition + sort key**
* search limited to partition <mark>vs</mark> across all partition
* unique attributes <mark>vs</mark> non unique attributes
* linked to the base table vs independent separate index
* only created during the base table creation <mark>vs</mark> can be created later
* cannot be deleted after creation <mark>vs</mark> can be deleted
* consumes provisioned throughput capacity of the base table <mark>vs</mark> independent throughput
* returns all attributes for item <mark>vs</mark> only projected attributes
* Eventually or Strongly <mark>vs</mark> Only Eventually consistent reads
* size limited to 10Gb per partition vs unlimited

### **3-3 DynamoDB Consistency**

* provides **Eventually consistent (by default) or Strongly Consistent** option to be specified during an read operation
* supports Strongly consistent reads for few operations like Query, GetItem, and **BatchGetItem** using the **ConsistentRead** parameter

### **3-4 DynamoDB Throughput Capacity**

* supports On-demand and Provisioned read/write capacity modes
* Provisioned mode requires the number of reads and writes per second as required by the application to be specified
* On-demand mode provides flexible billing option capable of serving thousands of requests per second without capacity planning

### **3-4 DynamoDB Global Tables**

DynamoDB Global Tables provide **multi-master, cross-region replication** capability of DynamoDB to support data access locality and regional fault tolerance for database workloads.

### **3-4 DynamoDB Streams**

provides a time-ordered sequence of item-level changes made to data in a table

### **3-5 DynamoDB Time to Live (TTL)**

* enables a per-item timestamp to determine when an item expiry
* expired items are deleted from the table without **consuming any write throughput.**
* supports **cross region replication** using DynamoDB streams which leverages Kinesis and provides **time-ordered sequence of item-level changes** and can help for lower RPO, lower RTO disaster recovery

### **3-6 DynamoDB Accelerator (DAX)**

* is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement – from **milliseconds** to microseconds – even at millions of requests per second.

* supports **triggers** to allow execution of **custom actions or notifications based on item-level updates**
* Data Pipeline jobs with EMR can be used for disaster recovery with higher RPO, lower RTO requirements

## **4 ElastiCache**

* managed web service that provides in-memory caching to deploy and run Memcached or Redis protocol-compliant cache clusters

### **4-1 ElastiCache with Redis**

* like RDS, supports **Multi-AZ, Read Replicas and Snapshots**
* Read Replicas are created across AZ within same region using **Redis’s asynchronous replication technology**
* Multi-AZ differs from RDS as there is no standby, but **if the primary goes down a Read Replica is promoted as primary**
* **Read Replicas cannot span across regions**, as RDS supports
* **cannot be scaled out and if scaled up cannot be scaled down**
* **allows snapshots for backup and restore**
* **AOF** can be enabled for **recovery scenarios**, to recover the data in case the node fails or service crashes. But it does not help in case the underlying hardware fails
* **Enabling Redis Multi-AZ as a Better Approach to Fault Tolerance**

### **4-2 ElastiCache with Memcached**

* **can be scaled up by increasing size and scaled out by adding nodes**
* nodes can span **across multiple AZs** within the same region
* **cached data is spread across the nodes**, and a node failure will always result in some data loss from the cluster
* **supports auto discovery**
* **every node should be homogenous** and of same instance type

### **4-3 ElastiCache Redis vs Memcached**

* complex data objects <mark>vs</mark> simple key value storage
* persistent <mark>vs</mark> non persistent, pure caching
* automatic failover with Multi-AZ <mark>vs</mark> Multi-AZ not supported
* scaling using Read Replicas <mark>vs</mark> using multiple nodes
* backup & restore supported <mark>vs</mark> not supported

can be used state management to keep the web application stateless

## **5 Redshift**

fully managed, fast and powerful, petabyte scale data warehouse service

uses replication and continuous backups to enhance availability and improve data durability and can automatically recover from node and component failures

provides Massive Parallel Processing (MPP) by distributing & parallelizing queries across multiple physical resources

columnar data storage improving query performance and allowing advance compression techniques


**only supports Single-AZ deployments** and the nodes are available within the same AZ, if the AZ supports Redshift clusters

**spot instances are <mark>vs</mark> an option**

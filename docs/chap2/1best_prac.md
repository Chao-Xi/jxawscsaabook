# **L1 AWS Best Practices**

## **1、AWS Design Principles**

### **1-1 Scalability**

While AWS provides virtually unlimited on-demand capacity, the architecture should be designed to take advantage of those resources

There are two ways to scale an IT architecture

**Vertical Scaling**

* takes place through increasing specifications of an individual resource for e.g. **updating EC2 instance type with increasing RAM, CPU, IOPS, or networking capabilities**
* will eventually hit a limit, and is not always a cost effective or highly available approach

**Horizontal Scaling**

* takes place through increasing number of resources for e.g. adding more EC2 instances or EBS volumes
* can help leverage the elasticity of cloud computing
* not all the architectures can be designed to distribute their workload to multiple resources
* applications designed should be **stateless**,
	* that needs no knowledge of previous interactions and stores no session information
	* capacity can be increased and decreased, after running tasks have been drained

* State, if needed, can be implemented using
	* Low latency external store, for e.g. **DynamoDB, Redis, to maintain state information**
	* **Session affinity**, for e.g. **ELB sticky sessions,** to bind all the transactions of a session to a specific compute resource. However, it cannot be guaranteed or take advantage of newly added resources for existing sessions

* Load can be distributed across multiple resources using
	* **Push model, for e.g. through ELB where it distributes the load across multiple EC2 instances**
	* **Pull model, for e.g. through SQS or Kinesis where multiple consumers subscribe and consume**

* Distributed processing, for e.g. using EMR or Kinesis, helps process large amounts of data by dividing task and its data into many small fragments of works


## **2、Disposable Resources Instead of Fixed Servers**

Resources need to be treated as temporary disposable resources rather than fixed permanent on-premises resources before

AWS focuses on the concept of **Immutable infrastructure**

* servers once launched, is never updated throughout its lifetime.
* updates can be performed on a new server with latest configurations,
* this ensures resources are always in a consistent (and tested) state and easier rollbacks


AWS provides multiple ways to instantiate compute resources in an automated and repeatable way

### **2-1 Bootstraping**

scripts to configure and setup for e.g. using data scripts and cloud-init to install software or copy resources and code


### **2-2 Golden Images**
 
* a snapshot of a particular state of that resource,
* faster start times and removes dependencies to configuration services or third-party repositories


### **2-3 Containers**

* AWS support for docker images through Elastic Beanstalk and ECS
* Docker allows packaging a piece of software in a Docker Image, which is a standardized unit for software development, containing everything the software needs to run: code, runtime, system tools, system libraries, etc


### **2-4 Infrastructure as Code**

* AWS assets are programmable, techniques, practices, and tools from software development can be applied to make the whole infrastructure reusable, maintainable, extensible, and testable.
* AWS provides services like **CloudFormation, OpsWorks for deployment**


## **3、Automation**

AWS provides various automation tools and services which help improve system’s stability, efficiency and time to market.


**Elastic Beanstalk**

a PaaS that allows quick application deployment while handling resource provisioning, load balancing, auto scaling, monitoring etc


**EC2 Auto Recovery**

* creates CloudWatch alarm that monitors an EC2 instance and automatically recovers it if it becomes impaired.
* A recovered instance is identical to the original instance, including the instance ID, private & Elastic IP addresses, and all instance metadata.
* Instance is migrated through reboot, in memory contents are lost.

**Auto Scaling**

* allows maintain application availability and scale the capacity up or down automatically as per defined conditions


**CloudWatch Alarms**

* allows SNS triggers to be configured when a particular metric goes beyond a specified threshold for a specified number of periods

**CloudWatch Events**

* allows real-time stream of system events that describe changes in AWS resources

**OpsWorks**

* **allows continuous configuration through lifecycle events that automatically update the instances’ configuration** to adapt to environment changes.
* Events can be used to trigger **Chef recipes** on each instance to perform specific configuration tasks

**Lambda Scheduled Events**

* allows Lambda function creation and direct AWS Lambda to execute it on a regular schedule.


## **4、Loose Coupling**

AWS helps loose coupled architecture that *reduces interdependencies, a change or failure in a component does not cascade to other components*

### **4-1 Asynchronous Integration**

* does not involve **direct point-to-point interaction** but **usually through an intermediate durable storage layer for e.g. SQS, Kinesis**
* decouples the components and introduces additional resiliency
* suitable for any interaction that doesn’t need an immediate response and an ack that a request has been registered will suffice

### **4-2 Service Discovery**

* allows new resources to be launched or terminated at any point in time and discovered as well for e.g. **using ELB as a single point of contact with hiding the underlying instance details or Route 53 zones to abstract load balancer’s endpoint**


### **4-3 Well-Defined Interfaces**

* allows various components to interact with each other through specific, **technology agnostic interfaces for e.g. RESTful apis with API Gateway** 


## **5、Databases**

AWS provides different categories of database technologies

### **5-1 Relational Databases (RDS)**

* normalizes data into well-defined tabular structures known as tables, which consist of rows and columns
* provide a powerful query language, flexible indexing capabilities, strong integrity controls, and the ability to combine data from multiple tables in a fast and efficient manner
* allows vertical scalability by **increasing resources and horizontal scalability using Read Replicas for read capacity and sharding or data partitioning for write capacity**
* provides High Availability using Multi-AZ deployment, where data is synchronously replicated


### **5-2 NoSQL Databases (DynamoDB)**

* provides databases that trade some of the query and transaction capabilities of relational databases for a more flexible data model that seamlessly scales horizontally
* perform data partitioning and replication to scale both the reads and writes in a horizontal fashion
* DynamoDB service synchronously replicates data across **three facilities in an AWS region** to provide fault tolerance in the event of a server failure or Availability Zone disruption

### **5-3 Data Warehouse (Redshift)**

* Specialized type of relational database, optimized for analysis and reporting of large amounts of data
* Redshift achieves efficient storage and optimum query performance through a combination of massively parallel processing (MPP), columnar data storage, and targeted data compression encoding schemes
* Redshift achieves efficient storage and optimum query performance through a combination of **massively parallel processing (MPP)**, columnar data storage, and targeted data compression encoding schemes
* **Redshift MPP architecture enables increasing performance by increasing the number of nodes in the data warehouse cluster**


## **6、Removing Single Points of Failure**

AWS provides ways to implement redundancy, automate recovery and reduce disruption at every layer of the architecture

AWS supports redundancy in the following ways


### **6-1 Standby Redundancy**

* When a resource fails, functionality is recovered on a **secondary resource using a process called failover**.
* **Failover will typically require some time** before it completes, and during that period the resource remains unavailable.
* Secondary resource can either be launched automatically only when needed (to reduce cost), or **it can be already running idle (to accelerate failover and minimize disruption)**.
* Standby redundancy is often used for stateful components such as relational databases.
 
 
### **6-2 Active Redundancy**

* requests are distributed to multiple redundant compute resources, if one fails, **the rest can simply absorb a larger share of the workload**.
* Compared to standby redundancy, it can achieve better utilization and affect a smaller population when there is a failure.


**AWS supports replication**


### **6-3 Synchronous replication**

* acknowledges a transaction after it has been durably stored in both the primary location and its replicas.
* protects data integrity from the event of a primary node failure
* **used to scale read capacity for queries that require the most up-to-date data (strong consistency)**.
* compromises performance and availability

### **6-4 Asynchronous replication**

* decouples the primary node from its replicas at the expense of introducing replication lag
* **used to horizontally scale the system’s read capacity** for queries that can tolerate that replication lag.

### **6-5 Quorum-based replication**

* combines synchronous and asynchronous replication to overcome the challenges of large-scale distributed database systems
* Replication to multiple nodes can be managed by defining a minimum number of nodes that must participate in a successful write operation

### **6-6 AWS provide services to reduce or remove single point of failure**

* Regions, Availability Zones with multiple data centers
* ELB or Route 53 to configure health checks and mask failure by routing traffic to healthy endpoints
* Auto Scaling to automatically replace unhealthy nodes
* EC2 auto-recovery to recover unhealthy impaired nodes
* S3, DynamoDB with data redundantly stored across multiple facilities
* Multi-AZ RDS and Read Replicas
* ElastiCache Redis engine supports replication with automatic failover

# **L2 AWS DynamoDB(unfinished)**

## **1 AWS DynamoDB**

### **1-1 AWS DynamoDB**

* Amazon DynamoDB is a fully managed NoSQL database service that
	* makes it simple and cost-effective to store and retrieve any amount of data and serve any level of request traffic.
	* provides fast and predictable performance with seamless scalability

* DynamoDB enables customers to offload the administrative burdens of operating and scaling distributed databases to AWS, without having to worry about hardware provisioning, setup and configuration, replication, software patching, or cluster scaling.
* **DynamoDB tables do not have fixed schemas, and table consists of items and each item may have a different number of attributes.**
* DynamoDB synchronously replicates data across three facilities in an AWS Region, giving high availability and data durability.
* DynamoDB supports fast in-place updates. A numeric attribute can be incremented or decremented in a row using a single API call
* **DynamoDB uses proven cryptographic methods to securely authenticate users and prevent unauthorized data access**
* Durability, performance, reliability, and security are built in, with SSD (solid state drive) storage and automatic 3-way replication.
* **DynamoDB supports two different kinds of primary keys:**
	* **Partition Key (previously called the Hash key)**
		* A simple primary key, composed of one attribute
		* DynamoDB uses the partition key’s value as input to an internal hash function; the output from the hash function determine the partition where the item will be stored.
		* No two items in a table can have the same partition key value.
	* **Partition Key and Sort Key (previously called the Hash and Range key)**
		* A composite primary key composed of two attributes. The first attribute is the partition key, and the second attribute is the sort key.
		* DynamoDB uses the partition key value as input to an internal hash function; the output from the hash function determines the partition where the item will be stored.
		* All items with the same partition key are stored together, in sorted order by sort key value.
		* Combination of partition key and sort key must be unique
		* It is possible for two items to have the same partition key value, but those two items must have different sort key values.

* DynamoDB Secondary indexes
	* add flexibility to the queries, without impacting performance.
	* are automatically maintained as sparse objects, items will only appear in an index if they exist in the table on which the index is defined making queries against an index very efficient

* DynamoDB throughput and single-digit millisecond latency makes it a great fit for gaming, ad tech, mobile, and many other applications
* **ElastiCache can be used in front of DynamoDB in order to offload high amount of reads for non frequently changed data**

### **1-2 DynamoDB Consistency**

* Each DynamoDB table is automatically stored in the three geographically distributed locations for durability
* Read consistency represents the manner and timing in which the successful write or update of a data item is reflected in a subsequent read operation of that same item
* DynamoDB allows user to specify whether the read should be eventually consistent or strongly consistent at the time of the request
	* **Eventually Consistent Reads (Default)**
		* Eventual consistency option maximizes the read throughput.
		* Consistency across all copies is usually reached within a second
		* However, an eventually consistent read might not reflect the results of a recently completed write.
		* Repeating a read after a short time should return the updated data.
		* DynamoDB uses eventually consistent reads, by default.
	* **Strongly Consistent Reads**
		* Strongly consistent read returns a result that reflects all writes that received a successful response prior to the read
		* Strongly consistent reads are 2x the cost of Eventually consistent reads
		* Strongly Consistent Reads comes with disadvantages
			* A strongly consistent read might not be available if there is a network delay or outage. In this case, DynamoDB may return a server error (HTTP 500).
			* Strongly consistent reads may have higher latency than eventually consistent reads.
			* Strongly consistent reads are not supported on global secondary indexes.
			* Strongly consistent reads use more throughput capacity than eventually consistent reads.
* Read operations (such as **GetItem, Query, and Scan**) provide a ConsistentRead parameter, if set to true, DynamoDB uses strongly consistent reads during the operation.
* Query, GetItem, and BatchGetItem operations perform eventually consistent reads by default
	* Query and GetItem operations can be forced to be strongly consistent
	* Query operations cannot perform strongly consistent reads on Global Secondary Indexes
	* BatchGetItem operations can be forced to be strongly consistent on a per-table basis


### **1-3 DynamoDB Advanced Topics**

* DynamoDB Streams,
* Triggers,
* Cross Region Replication,
* DAX,
* VPC Endpoints etc.

### **1-4 DynamoDB Performance**

* Automatically scales horizontally
* runs exclusively on Solid State Drives (SSDs).
	* SSDs help achieve the design goals of predictable low-latency response times for storing and accessing data at any scale.
	* SSDs High I/O performance enables it to serve high-scale request workloads cost efficiently, and to pass this efficiency along in low request pricing
* allows provisioned table reads and writes
	* Scale up throughput when needed
	* Scale down throughput four times per UTC calendar day
* automatically partitions, reallocates and re-partitions the data and provisions additional server capacity as the
	* table size grows or
	* provisioned throughput is increased
* Global Secondary indexes (GSI)
	* can be created upfront or added later

### **1-5 DynamoDB Security**

* **Fine Grained Access Control (FGAC) gives a high degree of control over data in the table**
* FGAC helps control who (caller) can access which items or attributes of the table and perform what actions (read/write capability).
* FGAC is integrated with IAM, which manages the security credentials and the associated permissions.

### **1-6 DynamoDB Encryption**

* **Data in Transit Encryption**
	* can be done by encrypting sensitive data on the client side or using encrypted connections (TLS)
	* All the data in DynamoDB is encrypted in transit (except the data in DAX)
	* communications to and from DynamoDB use the HTTPS protocol, which protects network traffic by using SSL/TLS encryption.

* **DynamoDB supports Encryption at rest**
	* Encryption at rest is enabled on all DynamoDB table data and cannot be disabled
	* E**ncryption at rest enables encryption for the data persisted (data at rest) in the DynamoDB tables.**
	* E**ncryption at rest includes the base tables, primary key, local and global secondary indexes, streams, global tables, backups, and DynamoDB Accelerator (DAX) clusters**
	* Encryption at rest automatically integrates with AWS KMS for managing the keys used for encrypting the tables.
	* **Encryption at rest also supports AWS owned CMK, AWS managed CMK, and Customer managed CMK**
	* Encryption at rest can be enabled only for a new table and not for an existing table
	* **Encryption once enabled for a table, cannot be disabled**
	* **DynamoDB streams can be used with encrypted tables and are always encrypted with a table-level encryption key**
	* **On-Demand Backups of encrypted DynamoDB tables are encrypted using S3’s Server-Side Encryptio**n
	* Encryption at rest encrypts the data using 256-bit AES encryption.

### **1-7 DynamoDB Encryption Client**

* DynamoDB Encryption Client is a software library that helps protect the table data before sending it to DynamoDB.
* Encrypting the sensitive data in transit and at rest helps ensure that the plaintext data isn’t available to any third party, including AWS.
* Encryption Client encrypts attribute values which can be controlled, but does not encrypt entire table, attribute names or primary key

### **1-8 DynamoDB Costs**

* Index Storage
	* **DynamoDB is an indexed data store**
		* Billable Data = Raw byte data size + 100 byte per-item storage indexing overhead
* **Provisioned throughput**
	* Pay flat, hourly rate based on the capacity reserved as the throughput provisioned for the table
	* one Write Capacity Unit provides one write per second for items < 1KB in size.
	* one Read Capacity Unit provides one strongly consistent read (or two eventually consistent reads) per second for items < 4KB in size.
	* Provisioned throughput charges for every 10 units of Write Capacity and every 50 units of Read Capacity.
* **Reserved capacity**
	* Significant savings over the normal price
	* Pay a one-time upfront fee
* DynamoDB also charges for storage, backup, replication, streams, caching, data transfer out.


### **1-9 Intro QAs**


1. Which of the following are use cases for Amazon DynamoDB? Choose 3 answers
	* Storing BLOB data.
	* **Managing web sessions**
	* **Storing JSON documents**
	* **Storing metadata for Amazon S3 objects**
	* Running relational joins and complex updates.
	* Storing large amounts of infrequently accessed data.
2. You are configuring your company’s application to use Auto Scaling and need to move user state information. Which of the following AWS services provides a shared data store with durability and low latency?
	* AWS ElastiCache Memcached (does not allow writes)
	* Amazon Simple Storage Service (does not provide low latency)
	* Amazon EC2 instance storage (not durable)
	* **Amazon DynamoDB**
3. Does Dynamo DB support in-place atomic updates?
	* It is not defined
	* No
	* **Yes**
	* It does support in-place non-atomic updates
4. What is the maximum write throughput I can provision for a single Dynamic DB table?
	* 1,000 write capacity units
	* 100,000 write capacity units
	* **Dynamic DB is designed to scale without limits, but if you go beyond 10,000 you have to contact AWS first**
	* 10,000 write capacity units
5. For a DynamoDB table, what happens if the application performs more reads or writes than your provisioned capacity?
	* Nothing
	* requests above the provisioned capacity will be performed but you will receive 400 error codes.
	* requests above the provisioned capacity will be performed but you will receive 200 error codes.
	* **requests above the provisioned capacity will be throttled and you will receive 400 error codes**.
6. In which of the following situations might you benefit from using DynamoDB? (Choose 2 answers)
	* You need fully managed database to handle highly complex queries
	* **You need to deal with massive amount of “hot” data and require very low latency**
	* **You need a rapid ingestion of clickstream in order to collect data about user behavior**
	* Your on-premises data center runs Oracle database, and you need to host a backup in AWS cloud

7. Does Amazon DynamoDB support both increment and decrement atomic operations?
	* No, neither increment nor decrement operations.
	* Only increment, since decrement are inherently impossible with DynamoDB’s data model.
	* Only decrement, since increment are inherently impossible with DynamoDB’s data model.
	* **Yes, both increment and decrement operations**.
8. What is the data model of DynamoDB?
	* “Items”, with Keys and one or more Attribute; and “Attribute”, with Name and Value.
	* “Database”, which is a set of “Tables”, which is a set of “Items”, which is a set of “Attributes”.
	* **“Table”, a collection of Items; “Items”, with Keys and one or more Attribute; and “Attribute”, with Name and Value.**
	* “Database”, a collection of Tables; “Tables”, with Keys and one or more Attribute; and “Attribute”, with Name and Value.
9. In regard to DynamoDB, for which one of the following parameters does Amazon not charge you?
	* Cost per provisioned write units
	* Cost per provisioned read units
	* Storage cost
	* **I/O usage within the same Region**
10. Which statements about DynamoDB are true? Choose 2 answers.
	* DynamoDB uses a pessimistic locking model
	* **DynamoDB uses optimistic concurrency control**
	* **DynamoDB uses conditional writes for consistency**
	* DynamoDB restricts item access during reads
	* DynamoDB restricts item access during writes
11. Which of the following is an example of a good DynamoDB hash key schema for provisioned throughput efficiency?
	* **User ID, where the application has many different users**.
	* Status Code where most status codes is the same.
	* Device ID, where one is by far more popular than all the others.
	* Game Type, where there are three possible game types.
12. You are inserting 1000 new items every second in a DynamoDB table. Once an hour these items are analyzed and then are no longer needed. You need to minimize provisioned throughput, storage, and API calls. Given these requirements, what is the most efficient way to manage these Items after the analysis?
	* Retain the items in a single table
	* Delete items individually over a 24 hour period
	* **Delete the table and create a new table per hour**
	* Create a new table per hour
13. When using a large Scan operation in DynamoDB, what technique can be used to minimize the impact of a scan on a table’s provisioned throughput?
	* **Set a smaller page size for the scan**
	* Use parallel scans
	* Define a range index on the table
	* Prewarm the table by updating all items
14. In regard to DynamoDB, which of the following statements is correct?
	* An Item should have at least two value sets, a primary key and another attribute.
	* **An Item can have more than one attributes**
	* A primary key should be single-valued.
	* An attribute can have one or several other attributes.
15. Which one of the following statements is NOT an advantage of DynamoDB being built on Solid State Drives?
	* serve high-scale request workloads
	* low request pricing
	* high I/O performance of WebApp on EC2 instance (Not related to DynamoDB)
	* low-latency response times
16. Which one of the following operations is NOT a DynamoDB operation?
	* BatchWriteItem
	* DescribeTable
	* BatchGetItem
	* **BatchDeleteItem (DeleteItem deletes a single item in a table by primary key, but BatchDeleteItem doesn’t exist)**
17. What item operation allows the retrieval of multiple items from a DynamoDB table in a single API call?
	* GetItem
	* **BatchGetItem**
	* GetMultipleItems
	* GetItemRange
18. An application stores payroll information nightly in DynamoDB for a large number of employees across hundreds of offices. Item attributes consist of individual name, office identifier, and cumulative daily hours. Managers run reports for ranges of names working in their office. One query is. “Return all Items in this office for names starting with A through E”. Which table configuration will result in the lowest impact on provisioned throughput for this query? [PROFESSIONAL]
	* Configure the table to have a hash index on the name attribute, and a range index on the office identifier
	* **Configure the table to have a range index on the name attribute, and a hash index on the office identifier**
	* Configure a hash index on the name attribute and no range index
	* Configure a hash index on the office Identifier attribute and no range index
19. You need to migrate 10 million records in one hour into DynamoDB. All records are 1.5KB in size. The data is evenly distributed across the partition key. How many write capacity units should you provision during this batch load?
	* 6667
	* 4166
	* **5556** ( 2 write units (1 for each 1KB) * 10 million/3600 secs, refer link)
	* 2778
20. A meteorological system monitors 600 temperature gauges, obtaining temperature samples every minute and saving each sample to a DynamoDB table. Each sample involves writing 1K of data and the writes are evenly distributed over time. How much write throughput is required for the target table?
	* 1 write capacity unit
	* **10 write capacity units ( 1 write unit for 1K * 600 gauges/60 secs)**
	* 60 write capacity units
	* 600 write capacity units
	* 3600 write capacity units
21. You are building a game high score table in DynamoDB. You will store each user’s highest score for each game, with many games, all of which have relatively similar usage levels and numbers of players. You need to be able to look up the highest score for any game. What’s the best DynamoDB key structure?
	* HighestScore as the hash / only key.
	* **GameID as the hash key, HighestScore as the range key. (hash (partition) key should be the GameID, and there should be a range key for ordering HighestScore. )**
	* GameID as the hash / only key.
	* GameID as the range / only key.
22. You are experiencing performance issues writing to a DynamoDB table. Your system tracks high scores for video games on a marketplace. Your most popular game experiences all of the performance issues. What is the most likely problem?
	* DynamoDB’s vector clock is out of sync, because of the rapid growth in request for the most popular game.
	* **You selected the Game ID or equivalent identifier as the primary partition key for the table.** 
	* Users of the most popular video game each perform more read and write requests than average.
	* You did not provision enough read or write throughput to the table.
23. You are writing to a DynamoDB table and receive the following exception:” ProvisionedThroughputExceededException”. Though according to your Cloudwatch metrics for the table, you are not exceeding your provisioned throughput. What could be an explanation for this?
	* You haven’t provisioned enough DynamoDB storage instances
	* You’re exceeding your capacity on a particular Range Key
	* **You’re exceeding your capacity on a particular Hash Key (Hash key determines the partition and hence the performance)**
	* You’re exceeding your capacity on a particular Sort Key
	* You haven’t configured DynamoDB Auto Scaling triggers
# **L4 AWS Elasticsearch**

## **1 AWS Elasticsearch**

* Elasticsearch Service is a managed service that makes it easy to deploy, operate, and scale Elasticsearch clusters in the AWS Cloud.
* Elasticsearch is a popular open-source search and analytics engine for use cases such as log analytics, real-time application monitoring, and clickstream analytics

### **Elasticsearch provides**

* real-time, distributed search and analytics engine
* ability to provision all the resources for Elasticsearch cluster and launches the cluster
* easy to use cluster scaling options. 
	* Scaling Elasticsearch Service domain by adding or modifying instances, and storage volumes is an online operation that does not require any downtime.
* provides self-healing clusters, which automatically detects and replaces failed Elasticsearch nodes, reducing the overhead associated with self-managed infrastructures
* domain snapshots to back up and restore ES domains and replicate domains across AZs
* data durability
* enhanced security with IAM access control and security groups
* node monitoring
* **storage volumes for the data using EBS volumes**
* **Multiple geographical locations for your resources, known as regions and Availability Zones**
* ability to span cluster nodes across multiple AZs in the same region, known as **zone awareness**,  for high availability and redundancy.  Elasticsearch Service automatically distributes the primary and replica shards across instances in different AZs.
* dedicated master nodes to improve cluster stability
* data visualization using the Kibana tool
* integration with CloudWatch for monitoring ES domain metrics
* integration with CloudTrail for auditing configuration API calls to ES domains
* integration with S3, Kinesis, and DynamoDB for loading streaming data
* ability to handle structured and Unstructured data
* HTTP Rest APIs


## **2 Elasticsearch Domains**

* Elasticsearch Service domains are Elasticsearch clusters created using the Elasticsearch Service console, CLI, or API.
* Each domain is an Elasticsearch cluster in the cloud with the specified compute and storage resources.
* Enables you to create and delete domains, define infrastructure attributes, and control access and security.
* Elasticsearch Service automates common administrative tasks, such as performing backups, monitoring instances and patching software once the domain is running


## **3 Elasticsearch Security**

* Access to Elasticsearch Service management APIs for operations **such as creating and scaling domains are controlled with AWS IAM policies**.
* Elasticsearch Service domains can be configured to be **accessible with an endpoint within the VPC or a public endpoint accessible to the internet.**
* Network access for VPC endpoints is controlled with security groups and for public endpoints access can be granted or restricted by IP address.
* Elasticsearch Service provides user authentication via IAM and basic authentication using username and password.
* Authorization can be granted at the domain level (via Domain Access Policies) as well as at the index, document, and field level (via the fine-grained access control feature powered by Open Distro for Elasticsearch).
* Authorization can be granted at the domain level (via Domain Access Policies) as well as at the **index, document, and field level (via the fine-grained access control feature powered by Open Distro for Elasticsearch).**
* Fine-grained access control feature extends Kibana with read-only views and secure multi-tenant support.
* Elasticsearch Service supports an integration with Cognito, to allow your end-users to log-in to Kibana through enterprise identity providers such as Microsoft Active Directory using SAML 2.0, Cognito User Pools, and more
* Elasticsearch Service supports encryption at rest through AWS Key Management Service (KMS), **node-to-node encryption over TLS, and the ability to require clients to communicate of HTTPS**
* **<mark>Encryption at rest encrypts shards, log files, swap files, and automated S3 snapshots.</mark>**

## **4 AWS Certification Exam**

1. You need to perform ad-hoc analysis on log data, including searching quickly for specific error codes and reference numbers. Which should you evaluate first?

	* **AWS Elasticsearch Service (Elasticsearch Service (ES) is a managed service that makes it easy to deploy, operate, and scale Elasticsearch clusters in the AWS cloud. Elasticsearch is a popular open-source search and analytics engine for use cases such as log analytics, real-time application monitoring, and click stream analytics)**
	* AWS RedShift
	* AWS EMR
	* AWS DynamoDB

2. You are hired as the new head of operations for a SaaS company. Your CTO has asked you to make debugging any part of your entire operation simpler and as fast as possible. She complains that she has no idea what is going on in the complex, service-oriented architecture, because the developers just log to disk, and it’s very hard to find errors in logs on so many services. How can you best meet this requirement and satisfy your CTO?
	* Copy all log files into AWS S3 using a cron job on each instance. Use an S3 Notification Configuration on the <code>PutBucket</code> event and publish events to AWS Lambda. Use the Lambda to analyze logs as soon as they come in and flag issues.
	* Begin using CloudWatch Logs on every service. Stream all Log Groups into S3 objects. Use AWS EMR cluster jobs to perform adhoc MapReduce analysis and write new queries when needed.
	* Copy all log files into AWS S3 using a cron job on each instance. Use an S3 Notification Configuration on the <code>PutBucket</code> event and publish events to AWS Kinesis. Use Apache Spark on AWS EMR to perform at-scale stream processing queries on the log chunks and flag issues.
	* **Begin using CloudWatch Logs on every service. Stream all Log Groups into an AWS Elasticsearch Service Domain running Kibana 4 and perform log analysis on a search cluster. (AWS Elasticsearch with Kibana stack is designed specifically for real-time, ad-hoc log analysis and aggregation)**





















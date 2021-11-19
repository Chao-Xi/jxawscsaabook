# **L4 AWS Networking Services Cheat Sheet**


## **1 Virtual Private Cloud – VPC**

* helps define a logically isolated dedicated virtual network within the AWS
* provides control of **IP addressing using CIDR block from a minimum of /28 to maximum of /16 block size**
* supports IPv4 and IPv6 addressing
* can be extended by associating secondary IPv4 CIDR blocks to VPC

### **1-1 Components**


* **Internet gateway (IGW) provides access to the Internet**
* **Virtual gateway (VGW) provides access to on-premises** data center through **VPN** and **Direct Connect connections**
* <mark>VPC can have only one IGW and VGW</mark>
* **Route tables** determine where network traffic from subnet is directed
* Ability to create **subnet** with VPC CIDR block
* **A Network Address Translation (NAT) server** provides outbound Internet access for EC2 instances in private subnets
* **Elastic IP addresses** are static, persistent public IP addresses
* Instances launched in the VPC will have a **Private IP address** and can have a **Public or a Elastic IP** address associated with it
* **Security Groups and NACLs** help define security
* **Flow logs** – **Capture information about the IP traffic going to and from network interfaces in your VPC**


### **1-2 Tenancy option for instances**

* **shared**, by default, allows instances to be launched on shared tenancy
* **dedicated** allows instances to be launched on a dedicated hardware

### **1-3 Route Tables**

* defines rules, termed as routes, which determine where network traffic from the subnet would be routed
* Each VPC has a Main **Route table, and can have multiple custom route tables created**
* Every route table **contains a local route that enables communication within a VPC which cannot be modified or deleted**
* **Route priority is decided by matching the most specific route** in the route table that matches the traffic

### **1-4 Subnets**

* **map to AZs** and do not span across AZs
* have a CIDR range that is a portion of the whole VPC.
* **CIDR ranges cannot overlap** between subnets within the VPC.
* AWS **reserves 5 IP addresses in each subnet – first 4 and last one**
* Each subnet is associated with a route table which define its behavior
	* <mark>**Public subnets** – inbound/outbound Internet connectivity via IGW</mark>
	* <mark>**Private subnets** – outbound Internet connectivity via an NAT or VGW</mark>
	* <mark>**Protected subnets** – no outbound connectivity and used for regulated workloads</mark>

### **1-5 Elastic Network Interface (ENI)**

* a default ENI, eth0, is attached to an instance which **cannot be detached with one or more secondary detachable ENIs (eth1-ethn)**
* **has primary private, one or more** secondary private, public, Elastic IP address, security groups, MAC address and **source/destination check flag attributes associated**
* AN ENI in one subnet can be attached to an instance in the same or another subnet, in the same AZ and the same VPC
* **Security group membership of an ENI can be changed**
* with pre allocated Mac Address can be used for applications with special licensing requirements
	

### **1-6 Security Groups vs Network Access Control Lists**

* Stateful vs Stateless
* At instance level vs At subnet level
* **Only allows Allow rule** vs **Allows both Allow and Deny rules**
* Evaluated as a Whole vs Evaluated in defined Order

### **1-7 Elastic IP**

* is a **static IP address** designed for dynamic cloud computing.
* is **associated with AWS account**, and not a particular instance
* can be **remapped** from one instance to an other instance
* is **charged for non usage**, if not linked for any instance or instance associated is in stopped state


### **1-8 NAT**

* **allows internet access to instances in private subnet**
* performs the function of both address **translation and port address translation (PAT)**
* **needs source/destination check flag to be disabled as it is not actual destination of  the traffic**
* NAT gateway is a AWS managed NAT service that provides better availability, higher bandwidth, and requires less administrative effort
* **are not supported for IPv6 traffic**


### **1-9 Egress-Only Internet Gateways**

* outbound communication over IPv6 from instances in the VPC to the Internet, and prevents the Internet from initiating an IPv6 connection with your instances
* **supports IPv6 traffic only**


### **1-10 Shared VPCs**

* allows multiple AWS accounts to create their application resources, such as EC2 instances, RDS databases, Redshift clusters, and AWS Lambda functions, **into shared, centrally-managed VPCs**

### **1-11 VPC Peering**

* allows routing of traffic between the peer VPCs using **private IP addresses and no IGW or VGW required**
* No single point of failure and bandwidth bottlenecks
* **supports inter-region VPC peering**
* **IP space or CIDR blocks cannot overlap**
* **cannot be transitive**, one-to-one relationship between two VPC
* Only one between any two VPCs and have to be explicitly peered
* **Private DNS values cannot be resolved**
* Security groups from peered VPC can now be referred, however the VPC should be in the same region.

## **1-12 VPC Endpoints**

* enables you to privately connect VPC to supported AWS services and VPC endpoint services powered by PrivateLink
* **<mark>does not require a public IP address, access over the Internet, NAT device, a VPN connection or Direct Connect</mark>**
* **traffic between VPC & AWS service does not leave the Amazon network**
* are virtual devices.
* are horizontally scaled, redundant, and highly available VPC components that allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic.
* **Gateway Endpoints**
	* is a gateway that is a target for a specified route in the route table, used for traffic destined to a supported AWS service.
	* **only S3 and DynamoDB are currently supported**

* **Interface Endpoints**
	* is an elastic network interface with a private IP address that serves as an entry point for traffic destined to a supported service
	* services supported **API Gateway AWS CloudFormation, CloudWatch, CloudWatch Events, CloudWatch Logs AWS CodeBuild AWS CodeCommit AWS Config, EC2 API Elastic Load Balancing API, Elastic Container Registry, Elastic Container Service AWS Key Management Service, Kinesis Data Streams, SageMaker and, SageMaker Runtime, SageMaker Notebook Instance AWS Secrets Manager AWS Security Token Service AWS Service Catalog, SNS, SQS AWS Systems Manager**


## **2 CloudFront**


* provides low latency and high data transfer speeds for distribution of static, dynamic web, or streaming content to web users.
* delivers the content through a worldwide network of data centers called **Edge Locations or Point of Presence (PoPs)**
* keeps persistent connections with the origin servers so that the files can be fetched from the origin servers as quickly as possible.
* dramatically reduces the number of network hops that users’ requests must pass through
* supports multiple origin server options, like AWS hosted service for e.g. S3, EC2, ELB, or an on-premise server, which stores the original, definitive version of the objects
* single distribution can have multiple origins and Path pattern in a cache behavior determines which requests are routed to the origin
* Web distribution supports **static, dynamic web content, on-demand using progressive download & HLS, and live streaming video content**
* **supports HTTPS using either**
	* **dedicated IP address, which is expensive as a dedicated IP address is assigned to each CloudFront edge location**
	* Server Name Indication (SNI), which is free but supported by modern browsers only with the domain name available in the request header

* For E2E HTTPS connection,
	* **Viewers -> CloudFront needs either a self-signed certificate or a certificate issued by CA or ACM**
	* **CloudFront -> Origin needs certificate issued by ACM for ELB and by CA for other origins**


### **Security**

* **Origin Access Identity (OAI)** can be used to **restrict the content from S3 origin to be accessible from CloudFront only**

* **supports Geo restriction (Geo-Blocking)** to whitelist or blacklist countries that can access the content

* **Signed URLs**

	* to restrict access to individual files, for e.g., an installation download for your application.
	* users using a client, for e.g. a custom HTTP client, that doesn’t support cookies

* **Signed Cookies**

	* **provide access to multiple restricted files**, for e.g., *video part files in HLS format or all of the files in the subscribers’ area of a website*.
	* don’t want to change the current URLs

* **integrates with AWS WAF, a web application firewall that helps protect web applications from attacks by allowing rules configured based on IP addresses**, HTTP headers, and custom URI strings


supports GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE to get object & object headers, add, update, and delete objects

* only caches responses to GET and HEAD requests and, optionally, OPTIONS requests
* does not cache responses to PUT, POST, PATCH, DELETE request methods and these requests are proxied back to the origin

### **object removal from the cache**

* **would be removed upon expiry (TTL) from the cache, by default 24 hrs**
* can be invalidated explicitly, but has a cost associated, however, might continue to see the old version until it expires from those caches
* objects can be invalidated only for Web distribution
* **use versioning or change object name, to serve a different version**


### **supports adding or modifying custom headers before the request is sent to origin which can be used to**

* validate if a user is accessing the content from CDN
* identifying CDN from which the request was forwarded, in case of multiple CloudFront distributions
* for viewers not supporting CORS to return the Access-Control-Allow-Origin header for every request

supports Partial GET requests using range header to **download objects in smaller units improving the efficiency of partial downloads and recovery from partially failed transfers**

supports compression to compress and serve compressed files when viewer requests include Accept-Encoding: gzip in the request header

**supports different price classes to include all regions**, or only the least expensive regions and other regions without the most expensive regions


## **3 Direct Connect & VPN**

### **3-1 VPN**

* provide secure IPSec connections from on-premise computers or services to AWS over the Internet
* is quick to setup, is cheap however it depends on the Internet speed


### **3-2 Direct Connect**

* is a network service that provides an alternative to using Internet to utilize AWS services by using **private dedicated network connection**
* **provides Virtual Interfaces**
	* **Private VIF** to access instances within an **VPC via VGW**
	* **Public VIF** to access **non VPC services**

* **requires time to setup probably months**, and should not be considered as an option if turnaround time is less
* **does not provide redundancy**, **use either second direct connection or IPSec VPN connection**
* Virtual Private Gateway is on the AWS side and Customer Gateway is on the Customer side
* **route propagation is enabled on VGW and not on CGW**

### **3-2 Direct Connect vs VPN IPSec**

* Expensive to Setup and Takes time vs Cheap & Immediate
* Dedicated private connections vs Internet
* Reduced data transfer rate vs Internet data transfer cost
* Consistent performance vs Internet inherent variability
* Do not provide Redundancy vs Provides Redundancy

## **4 Route 53**

* Highly available and scalable DNS & Domain Registration Service
* Reliable and cost-effective way to route end users to Internet applications
* Supports **multi-region and backup architectures for High availability**. 
	* **ELB , limited to region, does not support multi region HA architecture**
* supports private Intranet facing DNS service
* **internal resource record sets only work for requests originating from within the VPC** and currently cannot extend to on-premise
* Global propagation of any changes made to the **DN records within ~ 1min**
* Route 53 to create an **alias resource record set** that points to ELB, S3, CloudFront. An alias resource record set is a Route 53 extension to DNS. 
	* It’s similar to a CNAME resource record set, but supports both for root domain – zone apex  e.g. example.com, and for subdomains for e.g. www.example.com.
* **<mark>CNAME resource record sets can be created only for subdomains and cannot be mapped to the zone apex record</mark>**
* **Route 53 Split-view (Split-horizon)** DNS enables you to access an internal version of your website using the same domain name that is used publicly

### **4-1 Routing polic**y

* Simple routing – simple round robin policy
* Weighted round robin – assign weights to resource records sets to specify the proportion for e.g. 80%:20%
* Latency based routing – **helps improve global applications as request are sent to server from the location with minimal latency**, is based on the latency and cannot guarantee users from the same geographic will be served from the same location for any compliance reasons
* **Geolocation routing – Specify geographic locations by continent**, country, state limited to US, is based on IP accuracy
* **Failover routing – failover to a backup site if the primary site fails and becomes unreachable**


**<mark>Weighted, Latency and Geolocation can be used for Active-Active while Failover routing can be used for Active-Passive multi region architecture</mark>**
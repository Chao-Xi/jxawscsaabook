# **L6 AWS Network Load Balancer – NLB**

## **1、AWS Network Load Balancer – NLB**

* Network Load Balancer operates at the **connection level (Layer 4), routing connections to targets – EC2 instances, and containers based on IP protocol data**.
* Network Load Balancer is suited for **load balancing of TCP traffic
*** Network Load Balancer is capable of **handling millions of requests per second while maintaining ultra-low latencies (~100 ms vs 400 ms for ALB)**
* Network Load Balancer is optimized to handle sudden and volatile traffic patterns while using a single static IP address per Availability Zone.
* **Network Load Balancer also <mark>supports TLS termination</mark>, preserves the source IP of the clients, and provides stable IP support and Zonal isolation.**
* **NLB supports long-running connections** that are very useful for WebSocket-type applications.
* NLB is integrated with other AWS services such as Auto Scaling, EC2 Container Service (ECS), and CloudFormation.
* NLB supports connections from clients over VPC peering, AWS-managed VPN, and third-party VPN solutions.
* **For TCP traffic**,
	* the load balancer selects a target using a flow hash algorithm based on the protocol, source IP address, source port, destination IP address, destination port, and TCP sequence number.
	* TCP connections from a client having different source ports and sequence numbers and can be routed to different targets.
	* **Each individual TCP connection is routed to a single target for the life of the connection.**

* **For UDP traffic**,
	* **the load balancer selects a target using a flow hash algorithm based on the protocol, source IP address, source port, destination IP address, and destination port.**
	* A UDP flow has the same source and destination, so it is consistently routed to a single target throughout its lifetime.
	* Different UDP flows have a different source IP addresses and ports, so they can be routed to different targets.

* **<mark>back-end server authentication is not supported</mark>**


## **2、Network Load Balancer Features**

### **2-1 Connection-based Layer 4 Load Balancing**

**<mark>Allows load balancing of both TCP and UDP traffic, routing connections to targets – EC2 instances, microservices, and containers.</mark>**

### **2-2 High Availability**

* is highly available.
* accepts incoming traffic from clients and distributes this traffic across the targets **within the same Availability Zone**.
* monitors the health of its registered targets and routes the traffic only to healthy targets
* if a health check fails and an unhealthy target is detected, it stops routing traffic to that target and reroutes traffic to remaining healthy targets.
* if configured with multiple AZs and if all the targets in a single AZ fail, it routes traffic to healthy targets in the other AZs

### **2-3 High Throughput**

* is designed to handle traffic as it grows and can load balance millions of requests/sec.
* can also handle sudden volatile traffic patterns.

### **2-4 Low Latency**

offers extremely low latencies for latency-sensitive applications.

### **2-5 Cross Zone Load Balancing**

* enable cross-zone loading balancing only after creating the NLB
* **is disabled, by default, and charges apply for inter-az traffic**.

### **2-6 Sticky Sessions**

* Sticky sessions (source IP affinity) are a mechanism to route requests from the same client to the same target.
* Stickiness is defined at the **target group level**

### **2-7 Load Balancing using IP addresses as Targets**

* allows load balancing of any application hosted in **AWS or on-premises using IP addresses of the application backends as targets**.
* allows load balancing to an application backend hosted on any IP address and any interface on an instance.
* **ability to load balance across AWS and on-premises resources helps migrate-to-cloud, burst-to-cloud or failover-to-cloud**
* applications hosted in on-premises locations can be used as targets over a Direct Connect connection and EC2-Classic (using ClassicLink).


### **2-8 Preserve source IP address**


* preserves client-side source IP allowing the back-end to see client IP address
* **Target groups can be created with target type as instance ID or IP address**.
	* If targets registered by instance ID, **the source IP addresses of the clients are preserved and provided to the applications**.
	* If register targets registered by IP address, **the source IP addresses are the private IP addresses of the load balancer nodes**.


### **2-9 Static IP support**

* **automatically provides a static IP per Availability Zone (subnet) that can be used by applications as the front-end IP of the load balancer**.
* Elastic Load Balancing creates a network interface for each enabled Availability Zone. Each load balancer node in the AZ uses this network interface to get a static IP address.
* Internet-facing load balancer can optionally associate one Elastic IP address per subnet.

### **2-10 Elastic IP support**

an Elastic IP per Availability Zone (subnet) can also be assigned, optionally, thereby providing a fixed IP.


### **2-11 Health Checks**

* supports both network and application target health checks.
* Network-level health check
	* is based on the overall response of the underlying target (instance or a container) to normal traffic.
	* target is marked unavailable if it is slow or unable to respond to new connection requests

* Application-level health check
	* is based on a specific URL on a given target to test the application health deeper

	
### **2-11 DNS Fail-over**

* integrates with Route 53
* **Route 53 will direct traffic to load balancer nodes in other AZs, if there are no healthy targets with NLB or if the NLB itself is unhealthy**
* if NLB is unresponsive, Route 53 will remove the unavailable load balancer IP address from service and direct traffic to an alternate 
* Network Load Balancer in another region.

## **3、Integration with AWS Services**


is integrated with other AWS services such as Auto Scaling, EC2 Container Service (ECS), CloudFormation, CodeDeploy, and AWS Config.

### **3-1 Long-lived TCP Connections**

supports long-lived TCP connections ideal for WebSocket type of applications

### **3-2 Central API Support**

* **uses the same API as Application Load Balancer**.
* enables you to work with target groups, health checks, and load balance across multiple ports on the same EC2 instance to support containerized applications.

### **3-3 Robust Monitoring and Auditing**

* integrated with CloudWatch to report Network Load Balancer metrics.
* CloudWatch provides metrics such as Active Flow count, Healthy Host Count, New Flow Count, Processed bytes, and more.
* integrated with CloudTrail to track API calls to the NLB

### **3-4 Enhanced Logging**

* use the Flow Logs feature to record all requests sent to the load balancer.
* Flow Logs capture information about the IP traffic going to and from network interfaces in the VPC
* **Flow log data is stored using CloudWatch Logs**

### **3-5 Zonal Isolation**

* is designed for application architectures in a single zone.
* can be enabled in a single AZ to support architectures that require zonal isolation
* **automatically fails-over to other healthy AZs, if something fails in an AZ**
* **it’s recommended to configure the load balancer and targets in multiple AZs for achieving high availability**


## **4 Advantages over Classic Load Balancer**

* **Ability to handle volatile workloads and scale to millions of requests per second, without the need of pre-warming**
* **Support for static IP/Elastic IP addresses for the load balancer**
* Support for registering targets by IP address, including targets outside the VPC (on-premises) for the load balancer.
* **<mark>Support for routing requests to multiple applications on a single EC2 instance</mark>**. 
	* A single instance or IP address can be registered with the same target group using multiple ports.
* Support for containerized applications. **Using Dynamic port mapping, ECS can select an unused port when scheduling a task and register the task with a target group using this port**.
* Support for monitoring the health of each service independently, as health checks are defined at the target group level and many CloudWatch metrics are reported at the target group level. Attaching a target group to an Auto Scaling group enables scaling each service dynamically based on demand


## **5 Network Load Balancer Pricing**

* charged for each hour or partial hour that an NLB is running and the number of Load Balancer Capacity Units (LCU) used per hour.
* An LCU is a new metric for determining NLB pricing
* An LCU defines the maximum resource consumed in any one of the dimensions (new connections/flows, active connections/flows, bandwidth and rule evaluations) the Network Load Balancer processes your traffic.


## **6、AWS Certification Exam Practice Questions**


1.A company wants to use load balancer for their application. However, the company wants to forward the requests **without any header modification**. What service should the company use?

* Classic Load Balancer
* **Network Load Balancer**
* Application Load Balancer
* Use Route 53

2.A company is hosting an application in AWS for third party access. The third party needs to whitelist the application based on the IP. Which AWS service can the company use in the whitelisting of the IP address?

* AWS Application Load Balancer
* AWS Classic Load balancer
* **AWS Network Load Balancer**
* AWS Route 53
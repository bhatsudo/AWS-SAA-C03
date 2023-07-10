## AWS-SAA-C03
Notes that might help you prepare for AWS Solutions Architect Associate Exam


I highly recommend these resources:

**Courses**: **[Ultimate AWS Certified Solutions Architect Associate SAA-C03](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/)
Practice exam:** [https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/)
****

These are the notes I prepared for the preparation of the exam. Sharing it so it may help any of you who are preparing for AWS SAA-C03!

**Contributing:**
If you find any errors or typos, feel free to raise a PR!

## AWS EC2 instance types

### Security group:

- All inbound traffic is **blocked** by default
- All outbound traffic is **authorized** by default

NOTE:
-  one Placement group is only for a single AZ
- **out-of-the-box metrics for EC2** – the disk ( read/writes ), CPU Utilization, network (high level) for memory utilization, etc, custom metrics need to be set up

- The EC2 instance launched from the oldest launch configuration will be terminated first in ASG

Get EC2’s enhanced network capabilities through Elastic Network Adapter ( ENA ).
Elastic Fabric Adapter ( EFA ) is similar to ENA with OS bypass functionality.
In the Windows instance, only ENA is supported. If you attach EFA, it will act as ENA.

| EC2 Instance Type | Description | Trick to Remember |
| --- | --- | --- |
| On-Demand Instances | Pay as you go | No commitment, like buying something at full price |
| Reserved Instances | Upfront payment for discounted hourly rates

Unused Standard Reserved Instances can later be sold at the Reserved Instance Marketplace.

Convertible Reserved Instances allow you to exchange for another convertible reserved instance of a different instance family. | Commitment to a lower price, like buying something in bulk |
| Spot Instances | Bid on unused EC2 capacity | Cheapest, but not always available, like a clearance sale |
| Dedicated Hosts | Physical server for your use | Maximum control and security, like renting a private room |
| dedicated instances | no customers will share your hardware |  |

different placement groups and their characteristics:

| Placement Group | Description | Limitations |
| --- | --- | --- |
| Cluster | Clusters instances into a low-latency group in a single Availability Zone | No instances can span across multiple AZs |
| Spread | Spreads instances across underlying hardware, limiting to a maximum of 7 instances per group per AZ | Instances are spread across different racks for higher fault tolerance |
| Partition | Spreads instances across many different partitions within an AZ, allowing scaling to hundreds of EC2 instances per group | Suitable for applications like Hadoop, Cassandra, and Kafka, which rely on different sets of racks for distributed processing |

different types of EBS volumes:

| EBS Volume Type | Description | Use Cases |
| --- | --- | --- |
| gp2 | General purpose SSD volume that balances price and performance for a wide variety of workloads | Suitable for most general-purpose applications and workloads |
| gp3 | General purpose SSD volume with enhanced performance and lower cost compared to gp2 | Suitable for workloads that require higher baseline performance and can benefit from cost optimization |
| io1 | Highest-performance SSD volume designed for mission-critical low-latency or high-throughput workloads | Suitable for databases, critical applications, and workloads requiring the highest level of performance and durability |
| io2 | An enhanced version of io1 with higher durability, more IOPS per volume, and a better price-to-performance ratio | Suitable for high-performance databases, critical business applications, and IO-intensive workloads |
| st1 | Low-cost HDD volume designed for frequently accessed, throughput-intensive workloads | Suitable for big data, data warehousing, log processing, and streaming workloads that require high throughput |
| sc1 | Lowest-cost HDD volume designed for less frequently accessed workloads | Suitable for infrequent access workloads, large data sets, and scenarios where cost optimization is a priority |

**EBS Multi-Attach** in the io1/io2 family allows multiple EC2 instances in the same AZ to concurrently read and write to the same high-performance volume, enabling **higher application availability for clustered Linux applications** (e.g., Teradata), supporting up to 16 EC2 instances, and requiring a cluster-aware file system.

**RAID 0:** Improve **performance**/throughput of storage volumes
**RAID 1:** Improve **data availability** by mirroring data in multiple volumes for critical applications

### **EFS:**

- EFS only works with Linux ( POSIX )

storage classes and their characteristics for EFS:

| Storage Class | Description | Use Cases |
| --- | --- | --- |
| Standard | Designed for frequently accessed files, offers high availability and durability with multi-AZ deployment | Suitable for production workloads and applications that require high performance and availability |
| Infrequent Access (EFS-IA) | Provides cost-effective storage with lower retrieval costs, designed for less frequently accessed files | Suitable for files that are accessed infrequently, where cost optimization is a priority. Can be enabled with a Lifecycle Policy |

### Scalability

**Vertical**: increase the size of the instance

**Horizontal**: add multiple instances

# Load Balancers

| Load Balancer Type | Description | Trick to Remember |
| --- | --- | --- |
| Classic Load Balancer (CLB) | Legacy load balancer that routes traffic based on either HTTP/HTTPS or TCP/SSL protocols | "Classic" refers to the older generation load balancer, still available but not the latest |
| Application Load Balancer (ALB) | Layer 7 load balancer that operates at the application level, intelligently routing traffic based on content and providing advanced features

Protocols supported: HTTP1, HTTP2, gRPC

Also supports weighted target groups routing ( similar to Route 53 weighted routing policy ) | Think of ALB as the "App-Level Balancer" that provides enhanced application-level routing and features |
| Network Load Balancer (NLB) | Layer 4 ( TCP/UDP )load balancer that operates at the transport layer, handling high volumes of traffic with ultra-low latency

BYOIP - Bring Your Own IP to use trusted IPs as Elastic IPs. So they are whitelisted. | "Network" emphasizes its focus on efficient network-level traffic balancing and low latency |
| Gateway Load Balancer (GWLB) | Layer 3 ( IP packets ) New addition to AWS load balancers, providing centralized management and scaling for 3rd party virtual appliances | Think of GWLB as the "Gateway Manager" that centralizes the management and scaling of virtual appliances |

The a**pplication Load balance**r can link to different **Target Groups** ( EC2s, Lambdas, ECS tasks, etc )

**Network LB** target groups include ( EC2s, IP addresses, ALBs ) 

**Cross Zone LB:** traffic is distributed across AZs **( Enabled by default only for ALB )**

**Without Cross Zone LB:** just distributed within AZ

**SNI ( Server Name Indication )** solves the problem of loading multiple SSL certificates onto one web server (to serve multiple websites) - **it does not work for ClassicLB**

**Connection Draining – for CLB
Deregistration Delay – for ALB & NLB**
Time to complete “in-flight requests” while the instance is de-registering or unhealthy

**Values:** 1-3600s, default 300s, can be disabled

**Sticky Sessions:** Requests coming from a client are always redirected to the same instance based on a cookie. ( Works with ALB - layer 7 )

# Auto Scaling Types

After a scaling activity happens, you are in the **cooldown period** **(default 300 seconds)**
• During the cooldown period, the ASG will not launch or terminate additional
instances (to allow for metrics to stabilize)

| Scaling Type | Description | Trick to Remember |
| --- | --- | --- |
| Scheduled Scaling | Scaling is based on a predefined schedule, allowing you to set specific scaling actions at predetermined times. | "Scheduled" is like setting an alarm clock for your scaling actions. |
| Predictive Scaling | Scaling is based on machine learning algorithms and predictive analysis to anticipate demand and scale proactively. | "Predictive" means it uses smart algorithms to forecast and scale ahead of time, like predicting the weather for planning your activities. |
| Simple Scaling | Scaling is based on a single scaling policy that uses a specific threshold to trigger scale-in or scale-out actions.

NOTE: you need to wait for the cooldown period to complete before initiating additional scaling activities | "Simple" refers to a straightforward approach with a single threshold for scaling actions. |
| Step Scaling | Scaling is based on multiple predefined scaling steps that trigger based on different thresholds.

Keyword: scaling adjustments | Think of "Step" as taking incremental steps with different thresholds for scaling actions. |
| Target Tracking Scaling | Scaling is based on a target value for a specific metric, automatically adjusting the capacity to maintain the target value. | "Target Tracking" means continuously tracking and adjusting to a specific target metric value. |

## AWS Databases

| Service | AWS RDS | AWS Aurora | AWS ElastiCache |
| --- | --- | --- | --- |
| Description | Managed relational database service | MySQL and PostgreSQL-compatible database | In-memory data store and cache service |
| Database | Supports various relational databases: | MySQL and PostgreSQL-compatible engine | Supports two engines: |
| Engines | MySQL, PostgreSQL, Oracle, SQL Server, and MariaDB | with performance improvements

ACID Compliant | Redis: In-memory key-value store
Memcached: In-memory object caching engine |
| Scaling | Vertical scaling (CPU, memory, storage) | Horizontal scaling (automated storage) | Horizontal scaling (sharding) |
|  | Read replicas for read scalability |  |  |
| Availability | Multi-AZ for high availability | Multi-AZ for high availability | Replication for high availability |
|  | Automated backups and automated software patching

Enable Multi-AZ failover for disaster events

This happens when:
Loss of availability of primary AZ/storage failure on the primary | Automated backups and automated software patching | Backup and restore functionality |
| Performance | Provisioned IOPS and storage options | High-performance storage | In-memory caching for faster access |
|  | Enhanced monitoring and performance |  |  |
|  | insights |  |  |
| Use cases | Web applications, mobile apps, | High-performance applications, | Caching frequently accessed data, |
|  | e-commerce platforms, analytics, | SaaS applications | session management |
|  | and content management systems |  | Gaming Leaderboards |
| Backups | 1- 35 days - automated

Unlimited days - manual snapshots | same | supports backups |
| Additional: | No downtime to convert from single AZ to multi-AZ

RDS Proxy: Pool DB connections for load balancing

Enforce IAM Authentication for DB and securely store credentials in AWS Secrets Manager

Enable Enhanced Monitoring in RDS for additional metrics like data on threads, processes, etc

in Multi-AZ, If RDS primary instance fails, CNAME is switched from primary to standby instance | - Reader endpoints ( for read load balancing )
- Writer endpoint ( to write to master )
- Custom endpoints ( Run analytical queries on specific replicas)

More Types:
- Aurora Serverless
- Aurora Multi-Master
- Aurora Global DB ( across regions for low latency and high availability-   RPO=1s RTO ≤1min )
- Aurora ML

Supports database cloning

Supports native functions/procedures that trigger lambdas

Supports highly transactional workloads OLTP

If you do not have an Aurora replica AND In case of primary failure, Aurora creates a new DB instance in the same AZ as the original instance and is done on a best-effort basis | Security using Redis AUTH

Sets password or token during cluster creation |

More databases

| Database | Explanation |
| --- | --- |
| Amazon DocumentDB | Like MongoDB, but on AWS |
| Amazon Neptune | Fully managed graph database |
| Amazon Keyspaces (for Apache Cassandra) | Cassandra is an open-source NoSQL distributed database

Keyspaces is  managed Apache Cassandra-compatible database service

Use cases: store IoT devices info, time-series data |
| Amazon QLDB | QLDB stands for ”Quantum Ledger Database”
A ledger is a book recording financial transactions

Use case: Crypto transactions |
| Amazon Timestream | Fully managed, fast, scalable, serverless time series database

Use cases: IoT apps, operational applications, real-time analytics |

## AWS Route 53 routing types

| Record Type | Description | Trick to Remember |
| --- | --- | --- |
| A | Maps a domain to an IPv4 address. | "A" for Address, mapping to IPv4. |
| AAAA | Maps a domain to an IPv6 address. | "AAAA" for IPv6, with each 'A' representing a group of hexadecimal digits in the address. |
| CNAME | Creates an alias that points to another domain name. | "CNAME" for creating a Canonical NAME alias. |
| NS | Specifies the authoritative name servers for the domain. | "NS" for Name Servers, indicating the authoritative servers. |

|  | Description | Limitations/Features |
| --- | --- | --- |
| CNAME | Points a hostname to any other hostname. | - Only for non-root domains (e.g., http://something.mydomain.com/) |
|  |  | - Enables aliasing to any hostname (e.g., http://app.mydomain.com/ => http://blabla.anything.com/) |
| Alias | Points a hostname to an AWS Resource. | - Works for the root domain and non-root domain (e.g., http://mydomain.com/) |
|  |  | - Free of charge |
|  |  | - Includes native health checks |
|  |  | can be used for (Zone Apex), e.g.:
http://example.com/ |
|  |  | Can’t set TTL
Can’t set the target as EC2 DNS  |

AWS Route 53 routing policies:

| Route 53 Routing Policy | Description | Example/Trick |
| --- | --- | --- |
| Simple Routing | Used when you have a single resource that performs the function for your domain. It responds to DNS queries with the same IP address for all requests. | Think of it as a simple, straightforward route where all traffic follows the same path. |
| Weighted Routing | Distributes traffic across multiple resources based on assigned weights. Useful for conducting A/B testing or gradually rolling out new deployments. | Imagine assigning weights to different options like balancing weights on a scale. |
| Latency Routing | Routes traffic to the resource with the lowest latency for the end user. Ideal for applications where reducing latency is a critical factor. | Think of it as choosing the route with the least traffic or the shortest distance for faster response times. |
| Failover Routing | Provides an active-passive configuration, routing traffic to a standby resource if the primary one fails. Enables high availability and fault tolerance. | Think of it as having a backup plan where traffic is automatically rerouted if the primary resource fails. |
| Geolocation Routing | Directs traffic based on the geographic location of the user. Helpful for delivering localized content or customizing user experience by region. | Imagine routing traffic based on where the user is located on a global map, directing them to the closest or most relevant resource. |
| Geoproximity Routing (Traffic Flow Only) | Routes traffic based on the geographic location of the user and resources, taking network latency and user-defined bias into account.

Example: when a larger portion of traffic should be routed to some region for example ( bias ) | Visualize drawing a proximity circle on a map, considering both distance and network performance to determine the best routing decision. |
| Multivalue Answer Routing | Returns multiple healthy records selected at random to distribute traffic across multiple resources, improving availability and fault tolerance. | Think of it as having multiple options and randomly picking one each time, spreading the traffic load across multiple resources. |
| Weighted Multivalue Answer Routing | Combines weighted routing with multivalue answer routing, allowing you to assign different weights to each resource. | Combine the concepts of assigning weights to multiple options and randomly selecting among them to distribute traffic accordingly. |

## AWS S3

### Tiers

| S3 Tier | Description | Access | Trick to Remember |
| --- | --- | --- | --- |
| S3 Standard | General-purpose storage with high durability, availability, and performance, suitable for frequently accessed data | Frequent | The standard go-to option |
| S3 Intelligent-Tiering | Automatically moves objects between two tiers (frequent access and infrequent access) based on changing access patterns, optimizing costs | Frequent/Infrequent | The smart assistant moves things around |
| S3 Standard-Infrequent Access (S3 Standard-IA) | For long-lived, but less frequently accessed data, with lower retrieval fees and higher per-GB storage cost | Infrequent | Storage option for things you don't use all the time |
| S3 One Zone-Infrequent Access (S3 One Zone-IA) | Same as S3 Standard-IA, but stores data in a single availability zone, reducing costs but increasing the risk of data loss | Infrequent | A cheaper option that comes with some added risk |
| S3 Glacier

Instant and Flexible retreival | For data archiving and long-term storage, with retrieval times ranging from minutes to hours, and lower storage costs

min storage 90 days | Infrequent | Storage option for things you don't need to access very often, but still want to keep around for a long time |
| S3 Glacier Deep Archive | Similar to S3 Glacier, but with retrieval times ranging from hours to days, and the lowest storage costs

min storage 180 days | Infrequent | Storage option for things you want to keep around for a really long time, but don't need to access very often |

**Note:** Before you transfer data from S3 standard to S3 IA, you must store them in s3 standard for at least 30 days

S3 can handle up to **3500 PUT requests/sec** and **5500 GET requests/second**

**More S3 features**

| Feature | Explanation |
| --- | --- |
| AWS S3 Replication | CRR (Cross Region Replication) - Versioning should be enabled; no chaining allowed |
|  | SRR (Same Region Replication) - Versioning should be enabled; no chaining allowed |
| Multi-Part Upload | Recommended for files > 100MB; must use for files > 5GB; can help parallelize uploads (speed up transfers) |
| S3 Transfer Acceleration | Increase transfer speed by transferring files to an AWS edge location; compatible with the multi-part upload |
| S3 Byte-Range Fetches | Parallelize GETs by requesting specific byte ranges; better resilience in case of failures |
| S3 Select & Glacier Select | Retrieve fewer data using SQL by performing server-side filtering; can filter by rows & columns; less network transfer, less CPU cost client-side |
| S3 Batch Operations | Perform bulk operations on existing S3 objects with a single request.
ex: Encrypt un-encrypted objects |

**S3 encryption types**

| S3 Encryption Type | Use Case | Trick to Remember |
| --- | --- | --- |
| SSE-S3 | Server-side encryption using S3-managed keys ( AES-256 )

x-amz-server-side-encryption header must be used | The encryption is managed by S3, so it's easy to use |
| SSE-KMS | Server-side encryption using KMS-managed keys

If you use SSE-KMS, you may be impacted by the KMS limits | The encryption is managed by KMS, providing more control and security |
| SSE-C | Server-side encryption using customer-provided keys

HTTPS must be used | The encryption is managed by the customer, providing maximum control and security |
| Client-Side Encryption | Client-side encryption before uploading to S3 | The encryption is done on the client side, providing additional security |
| In transit Encryption
( SSL/TLS)  | HTTP Endpoint – non-encrypted
HTTPS Endpoint – encryption in flight

Force Encryption in Transit using
aws:SecureTransport  set to true in the bucket policy | - |

| Concepts | Explanation |
| --- | --- |
| CORS / Cross-Origin Resource Sharing | If a client makes a cross-origin request on our S3 bucket, we need to enable the correct CORS headers.
The requests won’t be fulfilled unless the other origin allows for the requests, using CORS Headers (example: Access-Control-Allow-Origin). |
| S3 MFA Delete | Force users to generate a code on a device (usually a mobile phone or hardware) before doing important operations on S3 like deleting an object.
Versioning must be enabled for this! |
| S3 Access Logs | For audit purposes, you may want to log all access to S3 buckets. Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket. |
| S3 Pre-signed URL | Users given a pre-signed URL inherit the permissions of the user that generated the URL for GET / PUT. |
| S3 Glacier Vault Lock | Adopt a WORM (Write Once Read Many) model, for compliance and data retention. |
| S3 Object Lock | Block an object version for a specific amount of time.

Retention modes:
 - Compliance: Object versions can't be overwritten or deleted by any user, including the root user.
- Governance: Most users can't overwrite or delete an object version or alter its lock settings, some can.

Legal Hold:  Protect the object indefinitely, independent from the retention period (s3:PutObjectLegalHold IAM permission). |
| Access Points | To simplify security management. Has own DNS name.
Can also have VPC origin to access it only from within VPC |
| S3 Lambda | Use AWS Lambda Functions to
change the object before it is
retrieved by the caller application
use case: data format conversion |

Note: if you want to host the static website in S3, the bucket name must be the same as the domain name. Example: **hello.example.com** is the domain, the bucket name must be the same.

## AWS CloudFront

| Concept | Explanation | Misc notes |
| --- | --- | --- |
| Cloudfront origin  | S3 Bucket
or
Custom origin ( HTTP ) | OAI ( origin access identity ) for access/security but the new one is OAC ( Origin Access Control ) |
| CloudFront Geo Restriction | You can restrict who can access your distribution with Allowlist and Blocklist countries | use case: copyright laws |
| TTL  | In case you update the back-end
origin, CloudFront doesn’t know
about it and will only get the
refreshed content after the TTL has expired | So, can manually do cache validation |
- Improves performance for both cacheable content (such as images and videos)
- Dynamic content (such as API acceleration and dynamic site delivery)
- Content is served at the edge
- When using Cloudfront, for member-only content access features, can use **signed cookies ( for multiple files ) and Signed URLs for individual objects.**

**AWS Global Accelerator**

- Leverage the AWS internal network to route your application
- Two **AnycastIP** are created for your application
- Improves performance for a wide range of applications over TCP or UDP
- Good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP
- Good for HTTP use cases that require **static IP** addresses
- Good for HTTP use cases that require deterministic, fast regional failover
- Not affected by the client's **DNS caching ( because anycast/static IPs )**

**NOTE:** 

- Unicast IP: one server holds one IP address
- Anycast IP: all servers hold the same IP address and the client is routed to the nearest one

## AWS Snow Family

| Features | Snowcone | Snowball Edge | Snowmobile |
| --- | --- | --- | --- |
| Storage Capacity | 8 TB (4 TB usable) HDD / 14TB SSD | 80 TB usable HDD | Exabytes (100 PB) |
| Migration Size | Up to 8 TB, online and offline | Up to 80 TB, offline | Up to exabytes, offline |
| DataSync agent | Pre-installed | - | - |
| Storage Clustering | Up to 5 nodes | Up to 10 nodes | - |

Trick to Remember: Snowcone is the smallest device with 8 TB (4 TB usable) HDD and a pre-installed DataSync agent. Snowball Edge offers a larger storage capacity of 80 TB usable HDD and supports storage clustering with up to 10 nodes. Snowmobile is designed for massive data migrations, handling exabytes of data offline.

**Note:** you cannot directly transfer data from Snowball to S3 Glacier. You need to use the lifecycle policies of S3.

## Snow Family devices for edge computing:

| Snow Family Devices | Snowcone & Snowcone SSD | Snowball Edge - Compute Optimized | Snowball Edge - Storage Optimized |
| --- | --- | --- | --- |
| CPU | 2 CPUs | 104 vCPUs | Up to 40 vCPUs |
| Memory | 4 GB | 416 GiB | 80 GiB |
| Storage | - | 28 TB NVMe or 42 TB HDD | 80 TB |
| Optional GPU | - | Optional GPU | - |
| Object Storage Clustering | - | - | Available |
| EC2 Instance Support | Yes | Yes | Yes |
| AWS Lambda Support | Yes | Yes | Yes |
| Long-term Deployment | 1 and 3 years pricing | 1 and 3 years pricing | 1 and 3 years pricing |

## AWS Storage Summary

Here's a two-column table summarizing different AWS storage and data transfer services:

| Service | Description | Trick to Remember |
| --- | --- | --- |
| S3 | Object Storage | S3: giant bucket in the cloud for storing and retrieving any amount of data from anywhere on the web. |
| S3 Glacier | Object Archival | S3 Glacier: low-cost storage service for data archiving and long-term backup. |
| EBS volumes | Network storage for one EC2 instance at a time | EBS: virtual hard drive attached to EC2 instance. |
| Instance Storage | Physical storage for your EC2 instance (high IOPS) | Instance storage: temporary, high IOPS, low-latency performance for high-speed local storage. |
| EFS | Network File System for Linux instances, POSIX filesystem | EFS: shared file system in the cloud for multiple EC2 instances simultaneously. |
| FSx for Windows | Network File System for Windows servers
Protocols: SMB and NTFS | FSx for Windows: shared file system in the cloud for multiple Windows EC2 instances simultaneously. |
| FSx for Lustre | High-Performance Computing HPC Linux file system

Integrates seamlessly with S3

Note: can use this for hot storage and use S3 for cold storage | Can be used from on-premise servers |
| FSx for NetApp ONTAP | High OS Compatibility
NFS, SMB, iSCSI protocol | FSx for NetApp ONTAP: enterprise file storage service that supports multiple file protocols. |
| FSx for OpenZFS | Managed ZFS file system | managed file system based on ZFS technology that provides advanced data protection, storage efficiency, and performance tuning features. |
| Storage Gateway | S3 File Gateway: NFS and SMB

FSx File Gateway: Native access to Amazon FSx for Windows File Server

Volume Gateway: Block storage backed by EBS (cache & stored) - iSCSI protocol

Tape Gateway: backup processes using physical tapes - iSCSI protocol | Storage Gateway: hybrid cloud storage service that bridges on-premises environment and AWS cloud storage. |
| Transfer Family | FTP, FTPS, and SFTP interface on top of Amazon S3 or Amazon EFS

Use case: company wants to use SFTP file transfer in uploading business-critical documents | Transfer Family: fully managed service to transfer files over the internet, into and out of S3 |
| DataSync | Schedule data sync from on-premises to AWS, or AWS to AWS
A large amount of Data

Can directly move data from on-premises to S3 Glacier

Can sync with S3/EFS/FSx | DataSync: fully managed service to automate and accelerate data transfer.
 |
| Snowcone / Snowball / Snowmobile | Physical data transfer devices for moving large amounts of data to the cloud | Snowcone, Snowball, and Snowmobile: physical devices to transfer large amounts of data to and from AWS cloud storage. |
| Database | Specific workloads with indexing and querying | AWS database services: specialized tools for storing and retrieving data in specific formats and structures. |

## AWS Messaging

**SQS**

| Concept | Explanation |
| --- | --- |
| Message retention | 4-14 days ( default is 4 days ) |
| Process | Poll-consume-delete |
| Message Visibility Timeout | After a message is polled by a consumer, it becomes invisible to other consumers
• By default, the “message visibility timeout” is 30 seconds ( so if not processed within this time, it is processed twice! )

- If visibility timeout is high (hours), and the consumer crashes, re-processing will take time
- If visibility timeout is too low (seconds), we may get duplicates |
| Long polling | When a consumer requests messages from the queue, it can optionally “wait” for messages to
arrive if there are none in the queue

ReceiveMessageWaitTimeSeconds >0 |
| FIFO Queue | Preserves order of messages
Exactly-once sending

name of the queue ends with .fifo
throughput:
300msg/s without batching
3000msgs/s with batching

can’t directly convert normal queue to FIFO queue |
| Dead Letter Queue (DLQ ) | Actual SQS queue set for sending undeliverable messages. Recommended to set 14 day of the retention period (max) |

**SNS**

| Concept | Explanation |
| --- | --- |
| Process | Pub-Sub |
| Fan Out | One SNS topic and multiple SQS queues subscribe to this
Also works cross-region |
| FIFO topic | Preserves order
prevents duplication

name ends with .fifo. can only have FIFO queues as subscribers |
| Message Filtering | Add a filter policy to filter messages sent to SNS topic subscriptions  |

**Kinesis**

| Service | Functionality | Notes |
| --- | --- | --- |
| Kinesis Data Streams | - Capture data streams | 1-365 days retention
Capacity mode: Provisioned and On demand  |
|  | - Process data streams | Ingest real-time at scale |
|  | - Store data streams |  |
| Kinesis Data Firehose | - Load data streams into AWS data stores

Serverless | Load streaming data to S3, redshift, etc
Fully managed, near real-time |
| Kinesis Data Analytics | - Analyze data streams with SQL or Apache Flink |  |
| Kinesis Video Streams | - Capture video streams |  |
|  | - Process video streams |  |
|  | - Store video streams |  |

## AWS Container Based

| Service | Explanation |
| --- | --- |
| ECS | Amazon's container orchestration service for running and managing Docker containers.

Only supports role-based policies
does NOT support resource-based policies |
| EKS | Amazon's managed Kubernetes service for deploying and scaling containerized applications. |
| ECR | Amazon's Docker container registry service for storing and deploying container images. |

## AWS Serverless

| Service/Concept | Explanation |
| --- | --- |
| Lambda | Memory allocation: 128 MB – 10GB (1 MB increments)
• Maximum execution time: 900 seconds (15 minutes)
• Environment variables (4 KB)
• Disk capacity in the “function container” (in /tmp): 512 MB to 10GB
• Concurrency executions: 1000 (can be increased)

By default, your Lambda function is
launched outside your own VPC

VPC-enabled lambdas can’t access anything. Network settings necessary

Encryption of ENV variables is not by default. You need to enable encryption using KMS

Lambda Layer for reusable code across lambdas |
| Lambda@Edge | Many modern applications execute some form of logic at the edge

written in NodeJS or Python

Viewer Request/Response
Origin Request/Response |
| CloudFront Functions | Lightweight functions are written in JavaScript
• For high-scale, latency-sensitive CDN customizations

Viewer Request/Response |
| DynamoDB | NoSQL
Fully managed, highly available with replication across multiple AZs

Rapidly evolve schemas

PITR 35 days ( manual backup retention unlimited )

To increase DB performance by distributing workload: Use partition keys with high-cardinality attributes, which have a large number of distinct values for each item

ACID compliant |
| DynamoDB Accelerator (DAX) | Fully-managed, highly available, seamless in-memory cache for DynamoDB
• Help solve read congestion by caching
• Microseconds latency for cached data |
| DynamoDB – Stream Processing | Ordered stream of item-level modifications (create/update/delete) in a table

Invoke AWS Lambda on changes
Works only on already present items

 |
| DynaoDB global tables | Make a DynamoDB table accessible with low latency in multiple-regions
Active-Active replication

Enabling DynamoDB streams are prerequisite |
| AWS API Gateway | Scalable APIs
also supports WebSockets

link to Lambda/HTTP/AWS Service

Types:
Edge-Optimized ( default )
Regional ( clients within the region )
Private ( accessed with only VPC endpoint ENI ) |
| AWS Step Functions | Build a serverless visual workflow to
orchestrate your Lambda functions

( old: simple workflow service - SWF ) |
| Amazon Cognito | Give users an identity to interact with our web or mobile application

Cognito vs IAM: “hundreds of users”, ”mobile users”, “authenticate with SAML”

Cognito User Pools:
Sign-in functionality for app users.
Integrate with API Gateway & Application Load Balancer

Cognito Identity Pools (Federated Identities)
Get identities for “users” so they obtain temporary AWS credentials |
| Elastic beanstalk | Developer-centric way to deploy web applications without a lot of effort.

It is not serverless though! Beanstalk is free, but we need to pay for underlying instances. |

## AWS Analytics services

| AWS Data Analysis Service | Description |
| --- | --- |
| Amazon Athena | Interactive query service that allows you to analyze data in Amazon S3 using SQL queries.

Serverless query service
Exam Tip: analyze data in S3 using serverless SQL, using Athena

Federated Query: run a query on multiple types of databases  |
| Amazon QuickSight | Business intelligence (BI) service for visualizing and analyzing data with interactive dashboards. |
| Amazon Redshift | Fully-managed data warehousing service for analyzing large datasets using SQL and BI tools.

OLAP: OnLine Analytical Processing

( Don’t mix it up with OLTP, Aurora supports OLTP highly transactional workloads )

vs Athena: faster queries/joins/aggregations thanks to indexes

Has own storage component
Used for huge amounts of data

Use this when you already have a Redshift cluster. else use Athena |
| Redshift Spectrum | Query data that is already in
S3 without loading it
• Must have a Redshift cluster available to start the query |
| Amazon OpenSearch Service | successor to Amazon ElasticSearch. With OpenSearch, you can search any field, even partially matches

For ex: in DynamoDB, we can only search using primary key or indexes

Enable SQL plugin if needed |
| Amazon EMR 
Elastic MapReduce | Managed big data platform for processing and analyzing large amounts of data using Apache Hadoop, Spark, and other frameworks.

Use cases: data processing, machine learning, web indexing, big data |
| Amazon QuickSight | Serverless machine learning-powered business intelligence service to create interactive dashboards

Use case: Business Analytics |
| Amazon Kinesis | Real-time streaming data service for collecting, processing, and analyzing streaming data. |
| AWS Glue | Fully-managed extract, transform, and load (ETL) service for preparing and transforming data.

Serverless

Visual data preparation service that allows you to clean and normalize data for analytics and machine learning. |
| Kinesis Data Analytics (SQL application) | Real-time analytics on Kinesis Data Streams & Firehose using SQL |
| Kinesis Data Analytics for Apache Flink | Use Flink (Java, Scala or SQL) to process and analyze streaming data |
| Amazon Managed Streaming for Apache
Kafka (Amazon MSK) | Alternative to Amazon Kinesis. Fully managed Apache Kafka on AWS |
| AWS Data Pipeline | Orchestration service for automating the movement and transformation of data between different AWS services. |
| AWS Lake Formation | Service that simplifies the process of building, securing, and managing data lakes on AWS.

Stored in S3 |

## AWS Machine Learning Services

| AWS Service | Description |
| --- | --- |
| Rekognition | Service for image and video analysis, providing features such as face detection, labeling, and celebrity recognition. |
| Transcribe | Automatic speech recognition (ASR) service that converts audio files into written text, useful for generating subtitles or transcriptions. |
| Polly TTS | Text-to-speech (TTS) service that converts written text into lifelike speech in various languages and voices. |
| Translate | Neural machine translation service that provides fast and accurate language translations across different languages. |
| Lex | Service for building conversational interfaces, such as chatbots or conversational bots, using natural language understanding (NLU) capabilities. |
| Connect | Cloud-based contact center service that enables organizations to set up and manage customer contact centers in the cloud. |
| Comprehend | Natural language processing (NLP) service that can analyze and extract insights from text, such as sentiment analysis or keyphrase extraction. |
| Comprehend Medical | Detects and returns useful information in unstructured clinical text.
Uses NLP to detect Protected Health Information (PHI) |
| SageMaker | Fully-managed machine learning (ML) service that allows developers and data scientists to build, train, and deploy ML models at scale. |
| Forecast | Service for building accurate time series forecasts by leveraging machine learning techniques and automatic model optimization. |
| Kendra | Fully managed document search service
Machine learning-powered search service that enables organizations to build powerful search capabilities for their applications or websites. |
| Personalize | Service for delivering real-time personalized recommendations based on user behavior and preferences. |
| Textract | Optical character recognition (OCR) service that automatically detects and extracts text and data from documents, forms, and images. |

## AWS Audit and Monitoring

| Service | Description | Trick to Remember |
| --- | --- | --- |
| CloudWatch | Service for monitoring and collecting operational data in real time from AWS resources, applications, and services. It provides metrics, logs, and alarms to monitor performance and troubleshoot issues.

Sources: SDK, CloudWatch Logs Agent ( install in EC2 ), CloudWatch Unified Agent ( new version for additional metrics )

CloudWatch Container Insights
• ECS, EKS, Kubernetes on EC2, Fargate, needs agent for Kubernetes
• Metrics and logs
CloudWatch Lambda Insights
• Detailed metrics to troubleshoot serverless applications
CloudWatch Contributors Insights
• Find “Top-N” Contributors through CloudWatch Logs
CloudWatch Application Insights
• Automatic dashboard to troubleshoot your application and related AWS services

Note: can’t directly set CloudWatch Alarms to update the ECS task count for example. EventBridge is needed in between. | Think of it as watching over your cloud environment like a hawk. |
| CloudTrail | Service for logging and auditing API activity across your AWS account. It records and stores API calls, including who made the call, when it was made, and what resources were accessed. It enables governance, compliance, and security analysis.

Enable CloudTrail Insights to detect unusual activity in your account

Events are stored for 90 days in CloudTrail ( for more use S3/Athena )

By default log files are encrypted with S3-Server Side Encryption ( SSE-S3 )

Event types:
Management events: events that modify AWS resources
Data Events: Events that modify data | Think of it as a trail of breadcrumbs that helps you track and analyze who did what in your AWS account. |
| AWS Config | Service for assessing, auditing, and evaluating the configuration of AWS resources. It provides a detailed view of the configuration changes and compliance status of resources over time, helping with compliance auditing, troubleshooting, and security analysis.

Solves:
- Is there unrestricted SSH access to my security groups?
• Do my buckets have any public access?
• How has my ALB configuration changed over time?

Config Rules – Remediations possible | Think of it as a detailed map of your AWS resources and their configurations that helps you maintain compliance and security. |
| AWS Artifact  | go-to, central resource for compliance-related information that matters to you. It provides on-demand access to AWS’ security and compliance reports and selects online agreements. Reports available in AWS Artifact include our Service Organization Control (SOC) reports, Payment Card Industry (PCI) reports etc | Someone asks to provide a compliance-related report to management, use AWS artifact  |

While CloudWatch focuses on **real-time monitoring** and performance data, CloudTrail is focused on auditing API activity and providing logs for governance and compliance. AWS Config, on the other hand, is more about tracking and evaluating the configuration changes of AWS resources for compliance and security purposes.

# **AWS Identity**

| Service | Explanation |
| --- | --- |
| AWS Oranizations | Allows to manage multiple AWS accounts
The main account is the management account
Other accounts are member accounts

Security: Service Control Policies (SCP)
• IAM policies applied to OU or Accounts to restrict Users and Roles |
| AWS IAM Identity Center | successor to AWS Single Sign-On

One login (single sign-on) for all your
• AWS accounts in AWS Organizations
• Business cloud applications (e.g., Salesforce, Box, Microsoft 365, …)
• SAML2.0-enabled applications
• EC2 Windows Instances |
| AWS Directory Services | AWS Managed Microsoft AD
• Create your own AD in AWS, manage users
locally, supports MFA
• Establish “trust” connections with your on-premises AD
Can use both on-premise and AWS ADs
AD Connector
• Directory Gateway (proxy) to redirect to on-premises AD, supports MFA
• Users are managed on the on-premises AD only
Simple AD
• AD-compatible managed directory on AWS
• Cannot be joined with on-premises AD
use when you don't have on-premise AD |
| AWS Control Tower | Easy way to set up and govern a secure and compliant multi-account AWS environment based on best practices

Uses AWS Organization to create accounts

Preventive Guardrail – using SCPs (e.g., Restrict Regions across all your accounts)
Detective Guardrail – using AWS Config (e.g., identify untagged resources) |
| AWS Security Token Service ( STS ) | Service to provide trusted users with temporary security credentials that can control access to your AWS resources |

## AWS Security

| AWS Security Service | Basic Information | Trick to Remember |
| --- | --- | --- |
| AWS KMS | Key Management Service, fully managed

Identical KMS keys in different AWS Regions that can be used interchangeably ( not global but just replicated ) | Default security in most services |
| SSM Parameter Store | Secure storage for configuration and secrets
• Optional Seamless Encryption using KMS
• Serverless, scalable, durable, easy SDK | Better for most cases. Keyword is configuration |
| AWS Secrets Manager | Safely stores and manages sensitive information, such as API keys and database credentials.

• Capability to force rotation of secrets every X days | Better for RDS, new service |
| AWS Certificate Manager | Easily provision, manage and deploy TLS Certificates | Create or Import certs. |
| AWS WAF | Protects web applications from common attacks like SQL injection and XSS.

Layer 7
Rate-based rules for DDoS protection / geo-match to block countries

web Access Control List ( ACL ) can be configured in this service

Does not support NLB | "WAF" sounds like "whiff," as in a protective shield against malicious web traffic. |
| AWS Shield | Provides DDoS protection for AWS resources.

Shield Advanced is paid service. If using multiple accounts, use consolidated billing to save costs! | "Shield" provides protection against DDoS attacks, acting as a protective shield for your applications and resources. |
| AWS Firewall Manager | Centrally manages AWS WAF rules across multiple accounts for consistent protection. | Think of Firewall Manager as the "manager" overseeing the centralized management of AWS WAF rules across multiple accounts. |
| Amazon GuardDuty | Intelligent threat detection service that continuously monitors malicious activity. | "GuardDuty" is like having a vigilant guard keeping watch and detecting any suspicious or malicious activities in your AWS environment. |
| AWS Inspector | Assesses the security vulnerabilities of EC2 instances and provides recommendations for remediation.

Also for container images and lambda functions | Imagine AWS Inspector as an "inspector" that thoroughly examines your instances, analyzing their security vulnerabilities and providing insights for remediation. |
| Amazon Macie | Automatically discovers, classifies, and protects sensitive data stored in AWS.

Macie helps identify and alert you to sensitive data, such as personally identifiable information (PII) | Think of "Macie" as your personal detective (Mystery Macie) uncovering and classifying sensitive data to ensure its protection. |
| AWS CloudHSM | Provides secure hardware-based key storage and cryptographic operations.

physical device and user maintenance required | "CloudHSM" provides a cloud-based Hardware Security Module, like a digital "Fort Knox" for storing and managing cryptographic keys. |
| AWS Network Firewall | Protect entire VPC
level 3 to level 7 | Uses GW load balancer, and protects whole VPC |

## AWS WAF vs. Shield vs. Firewall Manager

| Feature | AWS Web Application Firewall (WAF) | Shield | Firewall Manager |
| --- | --- | --- | --- |
| Description | Helps protect web applications from common attacks like SQL injection, cross-site scripting (XSS), and more. It works at the application layer (Layer 7) of the OSI model. | Provides DDoS (Distributed Denial of Service) protection for AWS resources. | Centrally manages AWS WAF rules across multiple accounts and resources to ensure consistent protection. |
| Use Cases | - Protecting web applications from known vulnerabilities and attacks.
- Defending against Layer 7 attacks like SQL injection, XSS, etc. | - Protecting against DDoS attacks.
- Safeguarding applications and data from volumetric, state-exhaustion, and application-layer attacks. | - Managing AWS WAF rules centrally across multiple accounts.
- Streamlining the administration of AWS WAF rules. |
| Integration | - Works in conjunction with Amazon CloudFront and Application Load Balancer (ALB) to provide protection at the edge.
- Supports integration with AWS Lambda for custom rules and actions. | - Integrated with other AWS services, such as Elastic Load Balancer (ELB), CloudFront, and Route 53.
- Can be enabled on individual resources or entire accounts. | - Integrates with AWS Organizations and AWS Config for multi-account, multi-region management.
- Works with AWS WAF and AWS Shield to provide comprehensive security. |
| Pricing | - Pay-as-you-go model based on the number of web requests and data processed by WAF rules.
- Pricing details can be found on the AWS WAF Pricing page. | - No additional cost for using Shield Standard.
- Additional charges apply for Shield Advanced for added features and protection. | - No additional cost for using Firewall Manager itself.
- Regular AWS WAF and AWS Shield charges apply for the associated resources. |
| Certification Relevance | Important for securing web applications, understanding different attack vectors, and implementing appropriate security measures. | Important for protecting against DDoS attacks, securing availability, and implementing resilience measures for AWS resources. | Understanding Firewall Manager is crucial for managing AWS WAF rules across multiple accounts in an enterprise environment. |

## AWS VPC

| AWS VPC Component | Summary | Trick to Remember |
| --- | --- | --- |
| CIDR | IP range assigned to the VPC.

WW.XX.YY.ZZ/32 => one IP
0.0.0.0/0 => all IPs
192.168.0.0/26 =>192.168.0.0 – 192.168.0.63 (64 IPs)

(32-26)^2 | CIDR: "Create IP ranges." |
| VPC | Virtual Private Cloud that defines a list of IPv4 and IPv6 CIDR blocks. | VPC: "Virtual Private Cloud - the foundation of your network infrastructure in AWS." |
| Subnets | Tied to an Availability Zone (AZ) and defined by a CIDR block.

AWS reserves 5 IP addresses (first 4 & last 1) in each subnet, so when calculating the number of IP addresses needed, consider this | Subnets: "Slice and dice your VPC into smaller networks with unique CIDR blocks." |
| Internet Gateway | Provides IPv4 and IPv6 internet access at the VPC level.
Connects VPC → Internet
One VPC can be attached to one IGW | Internet Gateway: "The gateway to the World Wide Web for your VPC." |
| Route Tables | Edited to add routes from subnets to the Internet Gateway (IGW), VPC Peering Connections, VPC Endpoints, etc. | Route Tables: "Plot the paths to different destinations in your VPC network." |
| Bastion Host | Public EC2 instance used to SSH into and has SSH connectivity to EC2 instances in private subnets. | Bastion Host: "Your gateway to accessing private instances securely from the outside world." |
| NAT Instances | Provides internet access to EC2 instances in private subnets. Must be set up in a public subnet with the Source/Destination check disabled.

Outdated
Supports port forwarding
Can be used as a bastion server | NAT Instances: "The old guard of providing internet access to private instances." |
| NAT Gateway | Managed by AWS, provides scalable internet access to private EC2 instances (IPv4 only).

Single AZ, create multiple NAT GW for resiliency

no need for security groups
cannot be used as a bastion server | NAT Gateway: "AWS takes care of scaling your NAT for IPv4 traffic." |
| Private DNS + Route 53 | Enables DNS Resolution and DNS Hostnames within the VPC. | Private DNS + Route 53: "Navigating the internal network with DNS magic." |
| NACL | Stateless subnet rules for inbound and outbound traffic, including ephemeral ports.

Default accepts everything ( but supports allow/deny rules ). By default, the newly created NACL will DENY everything

The first rule drives the decision
ex: 100 has priority over 110

Clients connect to a defined port and expect a response on an ephemeral port ( Linux 32768 – 60999) | NACL: "Notorious Access Control List - blocking or allowing traffic at the subnet level." |
| Security Groups | Stateful, operate at the EC2 instance level.

Allows allow rules only ( no deny )
all rules are evaluated! | Security Groups: "Secure your instances with a virtual security force." |
| Reachability Analyzer | Performs network connectivity testing between AWS resources. | Reachability Analyzer: "Checking the paths and connections across your AWS network." |
| VPC Peering | Connects two VPCs with non-overlapping CIDR blocks in a non-transitive manner. | VPC Peering: "Bridging the gap between separate VPCs with overlapping interests." |
| VPC Endpoints | Provides private access to AWS services (S3, DynamoDB, CloudFormation, SSM) within a VPC.

Types:
Interface Endpoints ( ENI - private IP - paid ) are preferred for on-premises
Gateway Endpoint ( Provisions GW - free ) preferred for all other cases

An endpoint policy is needed to set up these

Note: VPC endpoint is region specific. Use VPC peering to connect between VPCs in different regions | VPC Endpoints: "VIP access to AWS services from within your VPC." |
| VPC Flow Logs | Captures ACCEPT and REJECT traffic at the VPC, subnet, or ENI level, aiding in identifying attacks (can be analyzed using Athena or CloudWatch Logs). | VPC Flow Logs: "Flowing logs that help you catch and analyze suspicious traffic." |
| Site-to-Site VPN | Establishes a VPN connection over the public internet between a customer gateway and a virtual private gateway ( AWS Side ).

 | Site-to-Site VPN: "Building a secure tunnel between your on-premises data center and the AWS cloud." |
| AWS VPN CloudHub | Establishes a hub-and-spoke VPN model to connect multiple sites. | AWS VPN CloudHub: "Connecting multiple sites to a central VPN hub in the cloud.” |

More concepts:

| AWS Service | Summary | Trick to Remember |
| --- | --- | --- |
| Direct Connect | Establishes a direct private connection to an AWS Direct Connect Location using a virtual private gateway on the VPC.

Resiliency:
Better: Multiple DX Connections
Cost-effective: DX + IPSec VPN with the same BGP prefix. DX preferred but switched if DX fails. | Direct Connect: "Directly connect to AWS through a private gateway." |
| Direct Connect Gateway | Connects multiple VPCs in different AWS regions through Direct Connect. ( But same AWS account ) | Direct Connect Gateway: "Gateway to connect multiple VPCs across regions through Direct Connect." |
| AWS PrivateLink | Enables private connectivity between service and customer VPCs without using VPC peering, public internet, NAT gateway, or route tables. Must be used with Network Load Balancer & ENI. | AWS PrivateLink: "Privately link services between VPCs without exposing them to the public internet." |
| ClassicLink | Privately connects EC2-Classic instances to a VPC. | ClassicLink: "Linking Classic instances privately to the modern VPC world." |
| Transit Gateway | Enables transitive peering connections for VPC, VPN, and Direct Connect. ( connect multiple VPCs )

IP Multicast supported ( not supported by any other AWS Service )

ECMR → Equal Cost Multi-Path Routing is enabled to scale throughput using multiple VPN tunnels | Transit Gateway: "A gateway for seamless peering connections across VPCs, VPNs, and Direct Connect." |
| Traffic Mirroring | Copies network traffic from ENIs for further analysis. | Traffic Mirroring: "Mirroring network traffic for detailed analysis and monitoring." |
| IPv6 | Cannot be disabled. IPv4 and IPv6 is dual-stack-mode | Both enabled |
| Egress-only Internet Gateway | Provides IPv6 egress traffic for internet connectivity.

Used for IPv6 only!
Route tables must be updated | Egress-only Internet Gateway: "Gateway to connect your VPC to the IPv6 world for outbound traffic." |

## AWS Disaster Recovery/ Data Migration

| Concept | Description |
| --- | --- |
| RPO | Recovery Point Objective ( Latest point that can be recovered ) |
| RTO | Recovery Time Objective (The fastest time when something can be recovered ) |
| RTO slower to faster
 | Backup and restore → Pilot Light → Warm Standby → Multi-Site
( faster is better ) |
| Backup and Restore | Snapshot and restore for example |
| Pilot Light | A small version of the app is always running in the cloud (the core of the app ) |
| Warm Standby | The full system is up and running but at a minimum size |
| Multi Site / Hot Site | Full Production Scale is running AWS and On-Premise |
| DMS ( Database Migration Service ) | Quickly and securely migrate databases to
AWS, resilient, self-healing
The source database remains available
during the migration
Zero Downtime
Continuous Replication with CDC ( Change Data Capture )

Both DBs must be up and running! |
| AWS Schema Conversion Tool (SCT) | Convert your Database’s Schema from one engine to another |
| AWS Backup | Fully managed service • Centrally manage and automate backups across AWS services |
| AWS Application Discovery Service | Plan migration projects by gathering information about on-premises data centers |
| AWS Application Migration Service (MGN) | Lift-and-shift (rehost) solution which simplifies migrating applications to AWS |

# Misc

| Service | Description |
| --- | --- |
| AWS Batch | AWS Batch supports multi-node parallel jobs, which enables you to run single jobs that span multiple EC2 instances. |
|  | Easily schedule jobs and launch EC2 instances accordingly. |
| AWS ParallelCluster | Open-source cluster management tool to deploy HPC on AWS. |
|  | Configure with text files. |
|  | Automate the creation of VPC, Subnet, cluster type, and instance types. |
|  | Ability to enable EFA on the cluster (improves network performance). |
| CloudFormation | Like terraform, manage Infra-as-Code |
| Amazon Simple Email Service (Amazon SES) | Fully managed service to send emails securely, globally, and at scale |
| Amazon Pinpoint | Scalable 2-way (outbound/inbound) marketing communications service |
| Systems Manager – SSM Session Manager | Allows you to start a secure shell on your EC2 and
on-premises servers

If there is SSM, it is managed instance. You can use RunCommand to configure them |
| Cost Explorer | Visualize, understand, and manage your AWS costs and usage over time |
| Amazon Elastic Transcoder | Elastic Transcoder is used to convert media files stored in S3 into media
files in the formats required by consumer playback devices (phones etc..) |
| Amazon AppFlow | SaaS ↔ AWS data flow |
| Well Architected Framework
6 pillers

Tool checks for these | 1) Operational Excellence
2) Security
3) Reliability
4) Performance Efficiency
5) Cost Optimization
6) Sustainability |
| Trusted Advisor | Analyzes for : Cost optimization • Performance • Security • Fault tolerance • Service limits

Keyword: real-time guidance |
| AWS resource access manager (RAM) | easily and securely share your resources with your AWS accounts.

VPC sharing possible |
| AWS SWF ( Simple Workflow Service )  | Used to create decoupled architecture |
| AWS Data Lifecycle Manager | Used to automate the creation, retention, and deletion of snapshots taken to back up your Amazon EBS volumes |
| AWS AppSync | Serverless GraphQL and Pub/Sub API service that simplifies building modern web and mobile applications

Note: different than AppFlow |
| AWS Wavelength | AWS Wavelength combines the high bandwidth and ultralow latency of 5G networks with AWS compute and storage services so that developers can innovate and build a new class of applications.

Wavelength Zones are AWS compute and storage services on the edge of a 5G network.

Keywords: 5G, single-digit millisecond latency |
| AWS Xray | Provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components ( like Jaeger ) |

| Type | Services |
| --- | --- |
| Global Services | IAM
Route 53
CloudFront
WAF |
| Serverless Services | Fargate based
Lambda
DynamoDB
API Gateway
S3
SNS
SQS
Step Functions
AWS Glue |
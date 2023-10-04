# To-do
+ Read AWS Q&A
+ Read Udemy slides
+ [Jayendra's blog](https://jayendrapatil.com/)
+ practice exams
   + John Bonso on Udemy (6)
   + apps(3)
# About the Exam
## Format and Logistics
+ valid for 2 years;
+ 130 minutes;
+ 65 multiple choice questions
+ identify key AWS features in the questions
   + look for key phrases like "whith the least operations"
# Domains
## Design Secure Architectures (30%)
+ secure application tier
+ secure data
  + in transit
    + SSL
    + VPN
    + Snowball
  + at rest
    + data on s3 is private by default and need credentials to access
+ networking infrastructure for VPC
  + subnets
  + security groups and NACL
  + IGW; NAT instance/ NAT gateway
  + bastion hosts
+ shared responsibility model
  + AWS responsibility: AZ, region, edge locations, EC2
+ Principle of least privilege
    
## Design Resilient Architecture (26%)
+ reliable and resiliant storage
   + EFS
   + EBS
   + S3
+ design decoupling mechanisms
   + SQS
   + load balancer
   + elastic IP: decoupe IP address from server
+ multi-tier architecture solutions
+ high availability and/or fault tolerant solutions
  + high availability: user can access service under any circumstance; can allow certain performance degrade
  + fault tolerance: user does not experience any issue; more strict requirement
  + RTO vs RPO:
    + **RTO** (Recovery Time Object): how much time an application can be down without causing significant demage to the business
    + **RPO** (Recovery Point Object): the amount of data that can be lost before significant harm to the business occurs   
### HA: Highly Available Architecture
+ always design for failure
+ use multiple AZs and regions wherever you can
+ multi-AZ vs. read repicas for RDS
+ scaling out vs. scaling up
    + scaling out: use auto scaling groups (add instances)
    + scaling up: increase resources inside EC2 instance (upgrade RAM or CPU)
+ HA Bastion Hosts
    + Option 1:Two EC2 instances, two AZs, use a **NLB** with static IP address and health checks
      + cannot use ALB, becasue it is layer 7 and we need layer 4;
    + Option 2: one host in a AZ behind an auto scaling group with health checks and a fixed EIP.
      + if the host fails, the health check will fail and the auto scaling group will automatically provision a new EC2 instance
      + not 100% fault tolerant; will have some downtime
      + lowest cost option
  
## Design High-Performing Architectures (24%)
### Define Performant Solutions
+ performant storage and database
  + EBS: different types
  + S3: host static files(instead of keeping on web server)
  + RDS vs. DynamoDB vs. Redshift
    + read replicas
+ apply caching
+ design solutions for elasticity and scalability
### High performing computing
+ data transfer: see [blog](https://jayendrapatil.com/aws-data-transfer-services/)
  + Offline transfer: snowcone, snowball, snowmobile
  + Online transfer
    + **DataSync**
      + between on-premises storage and s3 or EFS(Elastic File System)
      + ideal for one-time data migrations, recurring data processing workflows and automated replication for data protection and recovery  
    + **S3 Transfer Acceleration**
      + public internet transfer
      + ideal for recurring jobs that travel across the globe, eg. media uploads, backups, and local data processing tasks that are regularly sent to a central location
      
    + **VPN**
      + connect securely between data centers and AWS;
      + qucik to set up and cost-efficient
      + ideal for small data transfers and connectivity
      + **still use shared internet connection** 
    + **Direct Connect**
      + connect accelerately between data centers and AWS
      + provides reliable data transfer
      + ideal for regular large data transfer
      + needs time to setup, not a cost-efficient solution
      + can be secured using VPN over Direct connect  
+ cache
  + cloudFront
  + API Gateway
  + ElastiCache - Memcached and Redis
  + DynamoDB Accelerator (DAX)
+ compute and network
  + EC2 instances(GPU OR CPU optimized)
  + EC2 fleets (e.g. spot fleets)
  + placement groups (cluster placement groups for low latency)
  + enhanced networking (ENA, VF, EFA)
+ storage
  + instance-attached storage
    + EBS
    + instance store
  + network storage
    + S3: distributed object-based storage; not file system
    + EFS: scale IOPS based on total size, or use provisioned IOPS
    + FSx for Lustre: HPC-optimized distributed file system; millions of IOPS; backed by S3
  + Orchestration and automation
    + AWS Batch: run many batch computation jobs
    + AWS ParallelCluster
## Design Cost-Optimized Architectures(20%)
+ storage
+ compute
+ serverless architecture
+ CloudFront
  + no charge for data transfer between s3 and cloudfront

# Topic
## Security & Encryption
### KMS：Key Management Service   
+ encryption in AWS == KMS
+ IAM for authorization, audit KMS key usage using **CloudTrail**
+ KMS keys Types
   ![key types](https://github.com/MarkFu/AWS_SAA_study_material/blob/main/pic/CMKs.png)
+ CMK symmetry  
  ![symmetry](https://github.com/MarkFu/AWS_SAA_study_material/blob/main/pic/CMK_sym.png)
+ **DynamoDB Table**: client-side encryption to protect specific fields and guarantee only decrption if the client has access to an API key
+ **Global Aurora**:(AWS Encryption SDK) protect specific fields even from database admins
+ **S3 Objects**:
   +  SSE-S3 are replicates by default
   +  SSE-C(customer provided key) are never replicated
   +  SSE-KMS: adapt the key policy, IAM role to decrypt the source, key to encrypt for the target KMS key
+ AMI Sharing Process Encrypted via KMS:
### SSM Parameter Store
+ component of AWS System Manager(SSM)
+ serverless, **version tracking** of configurations and **secrets**
+ IAM(security), Amazon EventBridge(notifications), CloudFormation
### Secrets Manager
+ force **rotation of secrets** every X days
+ **automate** generation of secrets on rotation
+ secrets are encrypted using KMS, integrated with RDS
+ cases: disaster recovery strategies, multi-region apps, multi-region DB...
### Certificate Manager (ACM)
+ provision, manage, deploy TLS Certificates
+ free of charge for public TLS certificates
+ automatic TLS certificate renewal
+ can't use ACM with EC2
### Web Application Firewall (WAF)
+ protect web applications(layer 7) from common attack - SQL injection and cross-site scripting(XSS) 
+ layer7 IS HTTP ( vs Layer 4 is TCP/UDP) ---> no support for NLB(layer4)
+ we can use global accelerator for fixed IP and WAF on the ALB
### AWS Firewall Manager
+ Manage rules in all accounts of an AWS Organization
+ use cases:
   + define ACL rules in WAF
   + protect resources: WAF alone
   + WAF across accounts, automate protection: **Firewall Manager + WAF**
   + frequent DDoS attacks: advanced Shield
### GuardDuty
+ Intelligent Threat discovery to protect AWS Account
### Macie
+ use ML to protect and alert sensitive data, such as PII(personal idenytifiable information) 
## AWS Virtual Private Cloud - VPC
### 1. Subnets
+ 5 IP addresses: first 4 and last 1(broadcast address)
### 2. Internet Gateways -- IGW
+ need to edit route tables
+ public subnet
### 3. Bastion Hosts
### 4. NAT(Network Address Translation) Instance
+ EC2 in private subnets connect to the Internet
+ must be launched in a public subnet
+ **route tables** must be configured to route traffic from private subnets to the NAT instance
+ internet traffic bandwidth depends on EC2 instance type
+ Security groups & rules:
   + inbound：
      + **allow HTTP/HTTPS traffic coming from private subnets**
      + **allow SSH from home network**
   + outbound:
      + **allow HTTP/HTTPS traffic to the internet ** 
### 5. NAT Gateway
+ require an IGW (private subnet -> NATGW -> IGW)
+ NACL is **staetless**: the NACL outbound rules are going to be evaluated. And if they are not passing, then the request will not make it through.
+ **security group** is **stateful**: whatever is accepted in can go also out, no rules being evaluated;
   + inbound request: denied by security group, allowed in != allowed out;
   + outbound request: denied by NACL, allowed out == allowed in
### 6. VPC Endpoints(AWS PrivateLink)(Page 731)
+ Allows to connect to AWS services using a **private network** instead of using the public Internet
+ Remove the need of IGW, NATGW, ... TO AWS
+ Use cases: check DNS setting resolution in your VPC; check route tables.
+ Types:
   + interface endpoint: ENI(private Link, as entry point) + security group, charged
      + on-premises eg. site to site VPN, or Direct Connect --> Interface Endpoint --> PrivateLink --> S3; 
   + Gateway Endpoint: Gateway(as the target), only support S3/DynammoDB , free
      + iN-VPC Apps --> Gateway Endpoint --> S3;   
### 7. VPC Flow Logs
+ a VPC feature that captures information about IP traffic going into and from the interfaces:
   + VPC/Subnet/ENI level Flow logs
+ can go to S3, CloudWatch Logs, and Kinesis Data Firehose
+ analyze using Athena or CloudWatch Logs Insights
### 8. VPC Traffic Mirroring
+ a VPC feature used to copy network traffic from an Elastic Network Interface(ENI)
+ Use cases: content inspection, threat monitoring, troubleshooting ect. 
### 8. Site-to-Site VPN
+  setup a Customer Gateway on DC, a Virtual Private Gateway on VPC, and site-to-site VPN over **public Internet**
### 9. VPN CloudHub
+ communicate with multiple sites using AWS VPN, with or without a VPC
### 10. Direct Connect (DX)
+ dedicated **private** connection from a remote network to your VPC
+ need a **Virtual Private Gateway** on your VPC
+ access public resources(S3) and private(EC2) on same connection
### 11. Direct Connect Gateway
+ **one or more VPC in different regions(same account)** <-->Direct Connect Gateway <--> Direct Connect(With A Virtual Private Gateway)
+ Connection Types
   + Dedicated Connections: 1Gpbs, 10Gpbs, 100Gpbs
   + Hosted Connections: 50 Mbps, 500Mbps, to 10 Gbps
+ need often longer than 1 month to establish a new connection
+ backup Direct Connect connection(expensive),  
### 11.Transit Gateway
+ sharing cross-account using Resource Access Manager(RAM)
+ works with Direct Connect Gateway, VPN connections
+ supports **IP Multicast** (not supported by any other AWS service)
### 12.Egress-only Internet Gateway
+ Used for IPv6 only
+ must update the Route Tables
### 13. AWS Network Firewall
+ from Layer 3 to Layer 7 protection, protect entire Amazon VPC
+ uses the AWS **Gateway Load Balancer**


### CloudFront vs Global Accelerator
+ content delivery network (CDN) through edge locations or POP
+ DDoS protection (global), Shield, WAF
+ CloudFront vs S3 cross region replication
   + global edge network | setup for each region
   + files are catched for a TTL(maybe a day) | near real-time
   + great for **static** content that be available **everywhere** | read only and **dynamic** content that be available at low-latency in **few regions**
+  does NOT have the capability to route the traffic to the closest edge location via an **Anycast static IP Address**(page 342/864)
   + **Global Accelerator can** create Anycast IP and send traffic directly to Edge Locations
      + Consistent performance: internal AWS
      + Health checks: disaster recovery
      + Security: shield, only 2 external IP need to be whitelisted
+  **CloudFront vs Global Accelerator**
   + improve performance for both catcheable contenT such as images and videos | (GA): TCP or UDP    + dynamic content | HTTP use cases that require static IP addresses/ fast regional failover/ game, IoT, voice over IP



             

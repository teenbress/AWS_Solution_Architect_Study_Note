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
## Network





             

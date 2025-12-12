---
title: "AWS & cloud concepts in general"
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
metaLinks:
  alternates:
    - 
---


# **AWS & cloud concepts in general**

## **Core AWS Concepts**

* **Regions** = isolated geographic areas.
* **Availability Zones (AZs)** = physically separate datacenters inside a region.
* **Edge Locations** = global CDN points for CloudFront.
* **IAM** = users, roles, policies (JSON). Always assign **least privilege**. Prefer **IAM Roles** over access keys.
* **Organizations** = multi-account structure; service control policies (SCPs) to restrict accounts.

---

## **Networking**

* **VPC** = isolated virtual network.
* **Subnets**: public (route to IGW) / private (route via NAT or no internet).
* **IGW** = direct internet access.
* **NAT Gateway** = outbound internet for private subnets.
* **Route Tables** = control traffic.
* **Security Groups** = stateful firewall.
* **NACLs** = stateless firewall.
* **VPC Peering** = connect VPCs (no transitive routing).
* **Transit Gateway** = scalable hub for multi-VPC, hybrid networks.
* **PrivateLink** = private connectivity to services without exposing to internet.
* **Elastic Load Balancers (ELB)**: ALB (HTTP/HTTPS), NLB (TCP/UDP), CLB (legacy).
* **Global Accelerator** = Anycast routing for TCP/UDP acceleration.

---

## **Compute**

### **EC2**

* Instances with different families:

  * **t** (burst), **m** (general), **c** (compute), **r** (memory), **p/g** (GPU).
* Use **instance profiles** to access AWS APIs.
* Placement: **Spread**, **Cluster**, **Partition**.

### **Auto Scaling**

* **ASG** adds/removes EC2 based on metrics or schedules.
* **Launch Template** defines instance configuration.

### **Lambda**

* Serverless functions. Billing per ms.
* Integrates with API Gateway, SQS, SNS, Events.
* Cold starts: reduce via Provisioned Concurrency.

---

## **Containers**

### **ECS**

* Orchestrator, can run on EC2 or Fargate.
* Task Definition = container blueprint.

### **EKS**

* Managed Kubernetes.
* Worker nodes via EC2 or Fargate.
* Use **IRSA** for per-pod IAM permissions.

---

## **Storage & Databases**

### **S3**

* Object storage. 11 nines durability.
* Storage classes: Standard, IA, OneZone IA, Glacier, Deep Archive.
* Versioning + Lifecycle Rules.
* S3 Bucket Policies + IAM for access.
* **Static website hosting** supported.

### **EBS**

* Block storage for EC2. Types: gp3, io2, sc1.
* Snapshots stored in S3.

### **EFS**

* POSIX SMB-like file system, multi-AZ, scalable.

### **RDS**

* Managed SQL (PostgreSQL, MySQL, MariaDB, Oracle, MSSQL).
* Multi-AZ for HA.
* Read Replicas for scaling reads.
* Automatic backups + snapshots.

### **Aurora**

* Cloud-optimized, MySQL/PostgreSQL compatible.
* 6 copies across 3 AZs.
* Cheaper and faster than classic RDS.

### **DynamoDB**

* NoSQL.
* Strong or eventual consistency.
* On-Demand or Provisioned capacity.
* DynamoDB Streams for change events.

---

## **Messaging**

* **SQS** = message queue (standard / FIFO).
* **SNS** = pub/sub.
* **EventBridge** = event routing and scheduling.
* **Kinesis** = streaming data, real-time.

---

## **Security & Governance**

* **KMS** = key management and encryption.
* **Parameter Store / Secrets Manager** = store configs/secrets.
* **CloudTrail** = API audit logs.
* **Config** = tracking resource changes + compliance rules.
* **Shield** = DDoS protection.
* **WAF** = Web Application Firewall for ALB/API Gateway/CloudFront.
* **GuardDuty** = threat detection (ML-based).
* **Inspector** = vulnerability scanning (EC2, ECR, Lambda).

---

## **Monitoring & Observability**

* **CloudWatch Metrics** = performance data.
* **CloudWatch Logs** = log storage + log insights.
* **CloudWatch Alarms** = alert triggering.
* **X-Ray** = distributed tracing.

---

## **CI/CD**

* **CodeCommit** = Git repo.
* **CodeBuild** = build, tests.
* **CodeDeploy** = deploy to EC2/Lambda.
* **CodePipeline** = orchestrate CI/CD workflows.

(Most mid-level DevOps teams use GitHub/GitLab instead.)

---

## **General Cloud Concepts**

* **High Availability (HA)** = run across multiple AZs (sometimes regions).
* **Scalability** = horizontal (scale out) > vertical scaling.
* **Fault tolerance** = no single point of failure.
* **Disaster Recovery (DR)** = backups, snapshots, cross-region replication.
* **Design for failure** = assume components fail.
* **Shared Responsibility Model**:

  * Cloud: hardware, hypervisor, networking.
  * You: OS, containers, IAM, configs.
* **Infrastructure as Code (IaC)** = Terraform/CloudFormation/CDK.
* **Immutable infrastructure** = recreate instead of patch.

---

## **Cost Optimization**

* Use **Savings Plans** or **Reserved Instances** for predictable workloads.
* gp3 > gp2.
* Turn off unused EC2/EKS nodes.
* S3 lifecycle → Glacier for cold data.
* Use **Spot Instances** for stateless workers.

---

## **Architecture Patterns**

* **Multi-AZ** for HA.
* **Multi-Region** for DR or ultra-low latency.
* **Private subnets** for backend workloads.
* **Public subnets** only for load balancers and NATs.
* Use **VPC Endpoints** (PrivateLink) to avoid public S3/API calls.
* Use **ALB → Target Groups** pattern for microservices.



## 🚀 Amazon EC2 – BASICS
### 🧠 What Is EC2?
- Amazon EC2 (Elastic Compute Cloud) is a virtual server in the cloud.
- You choose:
  - Instance type (vCPU + memory)
  - Storage (EBS or instance store)
  - OS (Linux, Windows)
  - Network (VPC, subnets, IPs)
  - Pricing model (on-demand, spot, reserved, etc.)

### 🛠️ Key Concepts
| Concept              | Description                                                          |
| -------------------- | -------------------------------------------------------------------- |
| **AMI**              | Amazon Machine Image (pre-configured OS + software)                  |
| **Instance Type**    | Defines CPU, RAM, network bandwidth                                  |
| **User Data**        | Run a script on first boot (bash, PowerShell) – great for automation |
| **Security Group**   | Virtual firewall (stateful, allow rules only)                        |
| **Key Pair (SSH)**   | Required to connect via SSH (Linux) or RDP (Windows)                 |
| **Elastic IP**       | Static public IP that you can remap                                  |
| **IAM Role for EC2** | Grant EC2 permissions to call AWS services                           |
| **Placement Group**  | Control placement strategy (clustered, spread, partition)            |

### 🧪 Exam Triggers
| Scenario                                      | You Choose                    |
| --------------------------------------------- | ----------------------------- |
| Need to run a custom script at instance start | ✅ Use User Data               |
| Want EC2 to access S3 without keys            | ✅ Attach IAM Role             |
| Need to allow incoming HTTP traffic           | ✅ Security Group with port 80 |
| Want to SSH into a Linux EC2                  | ✅ Create Key Pair             |
| Need to scale web server                      | ✅ Use Auto Scaling Group      |

---

## 💾 EC2 INSTANCE STORAGE

## 🧱 EBS (Elastic Block Store)
- Persistent block storage for EC2 
- Survives instance stop/start (unless deleted)
- Snapshots for backup (stored in S3)
- Attached to one EC2 instance at a time 
- Supports encryption and resizing

🧠 Use EBS when you want data to persist after instance termination.

### 📦 EBS Volume Types – Quick Cheat Sheet
| Volume Type                        | Key Traits                                                      | Use Case                                 | Bootable? |
| ---------------------------------- | --------------------------------------------------------------- | ---------------------------------------- | --------- |
| `gp3` (General Purpose SSD)        | Default. Baseline 3,000 IOPS. Cheaper & more flexible than gp2. | General workloads, boot volumes          | ✅ Yes     |
| `gp2` (Legacy General Purpose SSD) | Baseline IOPS based on size.                                    | Older instances, general workloads       | ✅ Yes     |
| `io1/io2` (Provisioned IOPS SSD)   | High-performance, provisioned IOPS. `io2` = more durable.       | High-performance DBs (e.g., RDS, Oracle) | ✅ Yes     |
| `st1` (Throughput-optimized HDD)   | Low cost, high throughput. Not for small random I/O.            | Big data, streaming, logs                | ❌ No      |
| `sc1` (Cold HDD)                   | Cheapest. Lowest performance.                                   | Infrequent access, cold backups          | ❌ No      |
| `standard` (Magnetic - deprecated) | Legacy. Avoid in new projects.                                  | Legacy systems only                      | ✅ Yes     |

💡 Tips for the Exam
- gp3 > gp2: If cost-efficiency + performance = needed → think gp3.
- io1/io2: Pick when "high IOPS" or "critical DB" appears in the question.
- st1/sc1: Only for large, sequential workloads (e.g., logs, cold data).
- Only gp2/gp3/io1/io2 can be used for boot volumes.
- io2 > io1 in durability — if "durability" is mentioned, go io2.

## ⚡ Instance Store (Ephemeral)
- Physically attached to host (like NVMe SSDs)
- Non-persistent – data lost if instance stops/terminates 
- High IOPS, very fast 
- Cannot be resized or snapshotted

🧠 Use instance store for temporary data like caches, buffers, scratch space.

### 📦 EBS vs Instance Store
| Feature     | EBS                       | Instance Store               |
| ----------- | ------------------------- | ---------------------------- |
| Persistence | ✅ Survives stop/start     | ❌ Data lost on stop          |
| Speed       | Slower (network attached) | 🔥 Very fast (local to host) |
| Snapshots   | ✅ Supports snapshots      | ❌ No snapshot support        |
| Use case    | DB, app data              | Caches, temp files           |

--- 

## 🧰 High Availability & Scalability
### 🧬 High Availability (HA)
| Technique                    | Purpose                                                       |
| ---------------------------- | ------------------------------------------------------------- |
| **Multiple AZs**             | Deploy across different Availability Zones to survive outages |
| **Elastic Load Balancer**    | Distribute traffic across instances (multi-AZ)                |
| **Auto Scaling Group (ASG)** | Replace unhealthy instances automatically                     |
| **CloudWatch + Alarms**      | Monitor instance metrics and trigger recovery                 |


### 📈 Scalability
| Type                   | Description                                      |
| ---------------------- | ------------------------------------------------ |
| **Vertical scaling**   | Resize instance type (e.g. t3.small → m6i.large) |
| **Horizontal scaling** | Add more instances (ASG + ELB)                   |

🧠 Horizontal scaling = better fault tolerance.

🧠 Use launch templates/configs for consistent instance creation.

---

## 📌 EC2 Pricing Models
| Model                           | Use Case                                                         | Notes                                                                                                                                 |
| ------------------------------- |------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| **1. On-Demand**                | Short-term, spiky workloads                                      | - Pay per second<br>- No commitment<br>-Ex:dev,test env                                                                               |
| **2. Reserved Instances (RI)**  | Steady, predictable workloads over time, 24/7 backend service    | - 1 or 3-year term<br>- Save up to 72% vs On-Demand<br>- Standard RIs (fixed instance type), Convertible RIs (more flexibility)       |
| **3. Spot Instances**           | Flexible, fault-tolerant workloads (batch jobs, big data, CI/CD) | - Up to 90% cheaper<br>- Can be interrupted with 2-min warning                                                                        |
| **4. Savings Plans**            | Flexible alternative to RI (works accross lambda, EC2, fargate)  | - Commit to \$/hr for 1 or 3 years<br>- Applies across EC2, Fargate, Lambda<br>- Like an RI without strings attached (flexible infra) |
| **5. Dedicated Hosts**          | Regulatory/compliance needs, Bring Your Own License (BYOL)       | - Entire physical server allocated to you<br>- Useful for licensing and visibility                                                    |
| **6. Dedicated Instances**      | Isolation at hardware level, but not full control                | - Run on hardware isolated to a single customer<br>- Still share the underlying hardware control with AWS                             |
| **7. Capacity Reservations**    | Guarantee capacity for AZ in advance                             | - Pay even if not used<br>- Combine with RIs for discount                                                                             |

### 🧪 Exam Traps & Scenario Tips
- BYOL licensing? ➤ Choose Dedicated Hosts 
- Require hardware isolation but don’t care about licenses? ➤ Use Dedicated Instances 
- Need to ensure EC2 capacity during flash sales or launches? ➤ Use Capacity Reservations 
- Looking to reduce cost on flexible workloads? ➤ Use Spot Instances or Spot Fleets 
- Need long-term cost savings but unsure about instance family? ➤ Use Savings Plans (Compute)

### 🧠 Bonus Tips
| Key Phrase in Exam                 | Think Of                              |
|------------------------------------|---------------------------------------|
| “Cheapest EC2 option”              | ✅ Spot Instances                      |
| “Steady traffic, cost savings”     | ✅ Reserved Instances or Savings Plans |
| “Compliance / physical isolation”  | ✅ Dedicated Hosts                     |
| “Need capacity guarantee in an AZ” | ✅ Capacity Reservation                |
| “Scale flexibly with cost control” | ✅ Spot + On-Demand with Auto Scaling  |
| Dev/Test, short-term               | ✅ On-Demand                           |
| Steady usage (e.g., DB server)     | ✅ Reserved                            |
| Flexible, fault-tolerant batch     | ✅ Spot                                |
| Compliance / BYOL                  | ✅ Dedicated Host                      |
| Want savings but flexible infra    | ✅ Savings Plans                       |

### 🧠 Scenario based hints
| Scenario                                                     | Pricing Model     |
| ------------------------------------------------------------ | ----------------- |
| “Run an ML model at 2 AM”                                    | 🟩 Spot           |
| “Always-on microservice backend”                             | 🟩 Reserved       |
| “Dev team trying things for 2 weeks”                         | 🟩 On-Demand      |
| “Licensing + hardware isolation”                             | 🟩 Dedicated Host |
| “I want savings, but don't want to commit to instance types” | 🟩 Savings Plan   |


--- 

## 🔐 EC2 + IAM
Attach IAM Role to EC2 instance to grant permissions to AWS services (no access keys needed).
- Can be added/changed after launch.

🧪 Exam Example:
  - Your EC2 instance needs to read from DynamoDB and write to S3.
  
  - ✅ Answer: Attach IAM Role with necessary policies.

---
## 🧠 Common Exam Scenarios
| Scenario                                       | Solution                  |
| ---------------------------------------------- | ------------------------- |
| Run startup script when launching EC2          | User Data                 |
| Temporarily store logs for processing          | Instance Store            |
| Persist logs after reboot                      | EBS Volume                |
| Secure EC2 → S3 access without credentials     | IAM Role                  |
| Launch EC2 in multiple AZs                     | Auto Scaling Group        |
| Route traffic evenly to multiple EC2 instances | Elastic Load Balancer     |
| Want to reduce compute cost for batch jobs     | Spot Instances            |
| Need static IP for EC2 that won't change       | Elastic IP                |
| Recover instance if it fails                   | Auto Recovery or ASG      |
| Must scale app on high CPU usage               | ASG with CloudWatch Alarm |

### Quick notes:
- Instance classes: general / compute / memory / storage optimized
  - SG: applied on ENI (firewall), bound to a VPC (and region), can be attached to multiple instances (and vice-versa)
    - default SG: inbound from same SG allowed, outbound allowed
    - new SG: inbound blocked, outbound allowed
  - Instance profile -> to add roles:
    - `aws iam create-instance-profile`
    - `aws iam add-role-to-instance-profile`
    - `aws ec2 associate-iam-instance-profile`
  - Spot instances:
    - spot request -> one-time (launch once, done) / persistent (auto-relaunch after interrupt), ❗Cancel → then terminate manually
    - spot fleet -> manages multiple instance types / AZs, mix spot + On demand => launch template (not config)
    - strategies: lowestPrice, diversified, capacityOptimized
  - Elastic IP - static public IP, 💰Charged if: Unattached or multiple per instance.
  - Placement: cluster (fast, 1AZ) / spread (HA, max 7 per AZ) / partition (isolation, max 7 per AZ)
  - States: stop (EBS kept), terminated (EBS deleted), hibernate (max 60 days, EBS kept), standby (ASG only)
  - EC2 Nitro: newer, better networking, higher speed EBS
  - vCPU = 2 concurrent threads / CPU core -> 4CPU = 8VCPU
  - Billing:
    - on-demand instances in running / stopping state -> billed
    - burstable performance instances (T3, T3a, and T2 families) -> earn credit when not all CPU is consumed
  - System manager Run Command = remote shell for EC2s — securely and at scale
  - monitoring:
    - EC2 detailed monitoring - metrics each 1 minute
    - Cloudwatch:
      - Cloudwatch agent for on-prem / custom metrics (ex: RAM):
        - CloudWatchAgentServerPolicy for agents on EC2 (not on-prem)
        - on prem: no role -> access keys
  - instance metadata: public/private IP, instance ID, AMI ID, SG (http://169.254.169.254/latest/meta-data/)
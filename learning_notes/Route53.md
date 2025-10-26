### 📘 What is Route 53?
A highly available and scalable DNS web service by AWS.

| Function                | Description                                                           |
| ----------------------- | --------------------------------------------------------------------- |
| **DNS Service**         | Translates domain names to IP addresses                               |
| **Domain Registration** | You can buy/manage domains directly                                   |
| **Health Checks**       | Monitor endpoints to route traffic accordingly                        |
| **Traffic Routing**     | Advanced routing policies (e.g. latency-based, failover, geolocation) |


### 🔤 Core Concepts
| Term                         | Meaning                                              |
| ---------------------------- | ---------------------------------------------------- |
| **DNS (Domain Name System)** | Phonebook of the internet (e.g., `example.com` → IP) |
| **Hosted Zone**              | Container for records for a domain                   |
| **Record Set (DNS Record)**  | Maps domain to resource (A, AAAA, CNAME, etc.)       |
| **TTL (Time to Live)**       | Cache duration for DNS records                       |


### 📦 Record Types
| Record Type       | Description                                                               |
| ----------------- | ------------------------------------------------------------------------- |
| **A Record**      | Maps domain to IPv4 address                                               |
| **AAAA Record**   | Maps domain to IPv6 address                                               |
| **CNAME**         | Alias to **another domain name** (no root domain support)                 |
| **Alias**         | AWS-specific; maps domain to **AWS resources** (like ALB, S3, CloudFront) |
| **MX**            | Mail exchange                                                             |
| **TXT**           | Arbitrary text (SPF/DKIM/verification)                                    |
| **NS**            | Delegates subdomains to another name server                               |
| **SRV, PTR, SOA** | Other DNS standards (rarely tested in DVA-C02)                            |

🔁 Alias vs. CNAME
- Alias works at root domain (e.g., example.com → ALB)
- CNAME cannot be used for the root domain

### 📡 Routing Policies
| Policy                               | Description                                                | Use Case                     |
| ------------------------------------ | ---------------------------------------------------------- | ---------------------------- |
| **Simple**                           | 1 record per name                                          | Single IP/domain             |
| **Weighted**                         | Split traffic by %                                         | A/B testing, gradual rollout |
| **Latency-based**                    | Route to region with lowest latency                        | Global apps                  |
| **Failover**                         | Primary → Secondary based on health                        | High availability            |
| **Geolocation**                      | Route based on user’s location                             | Legal/geographic routing     |
| **Geoproximity (Traffic Flow only)** | Bias traffic based on location                             | Custom geographic control    |
| **Multivalue Answer**                | Return several healthy IPs (like poor man’s load balancer) | Simple failover/load sharing |

### ❤️ Health Checks
- Can monitor:
  - Public IPs, endpoints, ELBs, etc.
- Used with:
  - Failover 
  - Multivalue Answer
- Health checks support:
  - HTTP, HTTPS, TCP 
  - String matching in response body
- You can also monitor CloudWatch Alarms

✅ Tip: Use Health Check with Failover Routing to shift traffic if the primary is down.

---
### 🧠 Developer-Focused Scenarios
#### 🧪 Scenario: Route traffic based on user location
➡️ Use Geolocation Routing

#### 🧪 Scenario: Direct users to lowest latency AWS region
➡️ Use Latency-based Routing

#### 🧪 Scenario: Use root domain (example.com) with S3 static website
➡️ Use Alias record, not CNAME

#### 🧪 Scenario: Canary or blue/green deployment
➡️ Use Weighted Routing (e.g., 10% new version, 90% stable)

#### 🧪 Scenario: Route to healthy service only
➡️ Use Failover Routing with Health Checks

#### 🧪 Scenario: You need a simple public DNS for an EC2 or ELB
➡️ Use Simple Routing Policy

### ☁️ AWS Service Integrations
| AWS Service                   | Integration                                   |
| ----------------------------- | --------------------------------------------- |
| **S3 Static Website**         | Route 53 → Alias → S3                         |
| **CloudFront**                | Use **Alias** to point domain to distribution |
| **ALB/NLB**                   | Route 53 Alias → ALB/NLB                      |
| **API Gateway Custom Domain** | Route 53 → Alias → API Gateway                |
| **CloudWatch**                | Health checks can use CloudWatch Alarms       |
| **Global Accelerator**        | Also offers static IP, but not DNS-based      |

### 🧠 Memory Triggers
| Trigger Phrase              | Think Of                       |
| --------------------------- | ------------------------------ |
| “A/B testing”               | Weighted Routing               |
| “Failover if down”          | Health Check + Failover Policy |
| “Users near Germany”        | Geolocation                    |
| “Fastest response globally” | Latency-based                  |
| “Root domain to S3 or ALB”  | Alias record                   |
| “Simple public domain name” | Simple Routing                 |


### ❌ Common Exam Traps
❌ CNAME cannot be used for root domain

❌ Health checks do not work for private resources (e.g., private EC2)

❌ Alias ≠ CNAME, Alias is AWS-specific and free

❌ Latency-based ≠ geolocation — latency is performance, geo is location

❌ Forgetting to attach health checks to failover/multivalue records
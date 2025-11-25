### 📦 What is a VPC?
A Virtual Private Cloud is your own network within AWS.

Provides network isolation, custom IP ranges, subnets, routing, and security controls.

Each AWS Region has independent VPCs.

### 📐 VPC Basics
| Feature            | Notes                                                                |
| ------------------ |----------------------------------------------------------------------|
| **CIDR Block**     | Private IP range, e.g., `10.0.0.0/16`                                |
| **Subnets**        | Must be **non-overlapping**, and **subset of VPC CIDR**              |
| **Public Subnet**  | Connected to Internet Gateway (IGW) → resources can have public IPs. |
| **Private Subnet** | No IGW → no inbound internet. Outbound requires NAT Gateway          |
| **Reserved IPs**   | 5 IPs per subnet are reserved                                        |
| **Max Subnets**    | Soft limit = 200 (can increase)                                      |

👉 Think: Public = front door (web servers, ALB). Private = back room (databases, app servers).

### 🌍 Internet Access
| Component                  | Purpose                                                                                           |
| -------------------------- |---------------------------------------------------------------------------------------------------|
| **Internet Gateway (IGW)** | Enables internet access to public subnets                                                         |
| **NAT Gateway**            | Sits in a public subnet, has Elastic IP, lets private subnets go out to internet (outbound only). |
| **Egress-Only IGW**        | Same as NAT but only for IPv6                                                                     |

❗Exam trick:
- Private subnet + internet = NAT Gateway
  - Private servers can talk to the internet, but the internet cannot talk back.
  - For inbound internet access, you’d need a public subnet + IGW.
- Public subnet + internet = IGW + public IP (Elastic IP if you want static).

### 🔄 Routing
- Subnets are tied to Route Tables

- Routes define how traffic moves (e.g., local, IGW, NAT GW, VPC Peering)

- Main route table is the default for all subnets unless specified.

### 🔒 Security in VPC
- Security Groups (SGs) → stateful (if inbound allowed, outbound automatically allowed).
- NACLs → stateless (must allow both inbound + outbound explicitly).
- Default SG → allows nothing inbound, everything outbound.

👉 Exam trick: If you see “stateful vs stateless” → SG = stateful.<br>
✅ Use Security Groups for application-layer control.

### 🛠️ VPC Endpoints
Used when resources in private subnet need AWS services without internet.
- Gateway Endpoint → for S3, DynamoDB.
- Interface Endpoint (ENI) → for most other services (SSM, API Gateway, etc).

👉 Exam trick: Lambda in private subnet → needs S3 = Gateway Endpoint, NOT NAT.

### 🔗 VPC Connections
- VPC Peering → connects 2 VPCs privately. Must add routes both sides. NOT transitive.
- Transit Gateway → hub for connecting many VPCs/on-prem. Transitive supported.

👉 Exam trick: If 3+ VPCs need to talk → Transit Gateway, not peering.

### 📎 Elastic IP (EIP)
- Static IPv4 address.
- Used for: NAT Gateway, bastion hosts, EC2 needing fixed IP.
- You only pay if it’s not attached.

👉 Exam trick: If they say “static public IP required” → Elastic IP.

### 🧠 Memory Tips
| Keyword                                  | Think Of                                |
| ---------------------------------------- | --------------------------------------- |
| "No internet access from private subnet" | ❌ IGW, ✅ NAT Gateway or Endpoint        |
| "Lambda in VPC cannot access S3"         | Missing VPC **Gateway Endpoint**        |
| "High network traffic across VPCs"       | Prefer **Transit Gateway** over Peering |
| "Private network access to AWS service"  | Use **VPC Endpoint**                    |
| "Static IP"                              | Use **Elastic IP**                      |

### ❌ Common Exam Traps
- Assuming all subnets can access internet by default → ❌ Only public subnets with IGW can. 
- Forgetting to update route tables when adding NAT Gateway or VPC peering. 
- Trying to use NAT Gateway for IPv6 → ❌ Use Egress-Only Internet Gateway. 
- Confusing SGs (stateful) and NACLs (stateless).

### Quick notes
- REGIONAL, soft limit: 5 VPCs per region
- Subnets = IP sub-ranges bound to specific AZs.
  - 5 IPs reserved per subnet (first 4 + last 1).
  - Default VPC: only public subnets.
- Internet Gateway (IGW):
  - needed for public subnets to access internet:
    - update route table to allow requests to be routed to the IGW
  - attached to the VPC (not to the subnets)
- NAT Gateway:
  - deployed in public subnet, uses IGW behind the scenes
  - uses an Elastic IP -> static IP address for high availability
  - one per AZ for HA; private subnets route traffic to it.
- NACL:
  - Subnet-level firewall.
  - One NACL per subnet but a NACL can be attached to multiple subnets
  - Rules are stateless → both inbound & outbound must be allowed.
  - Evaluated by rule number (lowest wins).
  - Default NACL: allows all traffic.
- Security Groups are stateful, so allowing inbound traffic to the necessary ports enables the connection.
- Network ACLs are stateless, so you must allow both inbound and outbound traffic
- VPC endpoints (PrivateLink):
  - Gateway endpoint -> S3 and DynamoDB (add target in route table)
  - Interface endpoint (ENI) -> creates private IP per subnet, attach SG -> most AWS services, including S3
- VPC Flow Logs: info about the IP traffic, published to CloudWatch Logs / S3
- VPC peering: private connection, non-transitive, SG can be referenced across peered VPCs

### Exam-style scenarios
| 🧪 Scenario                                                                                      | ✅ Solution                                                                                                              |
|--------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| **Lambda in private subnet needs to call S3**                                                    | Use **Gateway VPC Endpoint** for S3 (cheaper, private).                                                                 |
| **Restrict S3 bucket from internet access**                                                      | Create **Gateway VPC Endpoint**, block S3 public access, and use bucket policy to allow only traffic from the endpoint. |
| **EC2 in private subnet needs outbound internet**                                                | Use **NAT Gateway** in public subnet, with Elastic IP (to have a fixed address).                                        |
| **EC2 in public subnet needs internet access**                                                   | Place in **public subnet**, attach **Elastic IP**, route via **Internet Gateway (IGW)**.                                |
| **Web tier (public), App tier (private)**                                                        | ALB in **public subnet**, EC2 app servers in **private subnet**, ALB routes to target group.                            |
| **EC2/Lambda in VPC needs private access to SSM, API Gateway, Secrets Manager**                  | Use **Interface Endpoint (ENI-based)**.                                                                                 |
| **Connect 2 VPCs privately (no internet)**                                                       | Use **VPC Peering** + add routes in both VPCs (remember: not transitive).                                               |
| **Connect many VPCs + on-prem (scalable)**                                                       | Use **Transit Gateway (TGW)** (supports transitive routing).                                                            |
| **EC2 in subnet needs a static IP**                                                              | Assign **Elastic IP** (EIP).                                                                                            |
| **Bastion Host** (special EC2 instance in a public subnet - lets you SSH in private subnet EC2s) | Place EC2 in **public subnet** with Elastic IP + SSH access, then access private subnet EC2s through it.                |
| **High availability NAT**                                                                        | Use **NAT Gateways in multiple AZs**.                                                                                   |
| **IPv6 private subnet outbound internet only**                                                   | Use **Egress-Only Internet Gateway**.                                                                                   |
| **Multiple AZ resilience for VPC Endpoints**                                                     | Deploy **endpoints in each AZ** used by private subnets.                                                                |
| **Application needs millions of cross-VPC connections**                                          | Use **Transit Gateway**, not VPC peering (scales better).                                                               |
| **EC2 can’t connect even though SG allows**                                                      | Check **NACL rules** (stateless: must allow inbound + outbound).                                                        |
| **Need to move virtual NIC between EC2s**                                                        | Use **Elastic Network Interface (ENI)** (can detach + reattach).                                                        |
| **Private subnet instances need S3 but not internet**                                            | **Gateway Endpoint for S3**, avoid NAT cost.                                                                            |
| **On-premises needs private connection to VPC**                                                  | Use **VPN over Direct Connect (DX)** or TGW for scaling.                                                                |
| **Lambda inside VPC can’t reach DynamoDB**                                                       | Add **Gateway Endpoint** for DynamoDB.                                                                                  |
| **Subnet IP exhaustion**                                                                         | Remember: **5 IPs are reserved per subnet**. Use larger CIDR block.                                                     |

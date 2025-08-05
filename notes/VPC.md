### 📦 What is a VPC?
A Virtual Private Cloud is your own network within AWS.

Provides network isolation, custom IP ranges, subnets, routing, and security controls.

Each AWS Region has independent VPCs.

### 📐 VPC Basics
| Feature            | Notes                                                   |
| ------------------ | ------------------------------------------------------- |
| **CIDR Block**     | Private IP range, e.g., `10.0.0.0/16`                   |
| **Subnets**        | Must be **non-overlapping**, and **subset of VPC CIDR** |
| **Public Subnet**  | Has **Route Table with Internet Gateway**               |
| **Private Subnet** | No direct internet access (unless via NAT Gateway)      |
| **Reserved IPs**   | 5 IPs per subnet are reserved                           |
| **Max Subnets**    | Soft limit = 200 (can increase)                         |

### 🌍 Internet Access
| Component                  | Purpose                                                             |
| -------------------------- | ------------------------------------------------------------------- |
| **Internet Gateway (IGW)** | Enables internet access to public subnets                           |
| **NAT Gateway**            | Allows **private subnets** to access the internet **outbound only** |
| **Egress-Only IGW**        | For **IPv6** outbound access only (no NAT for IPv6)                 |


❗ Common Trap: Private subnets + NAT Gateway = no inbound, only outbound

### 🔄 Routing
- Subnets are tied to Route Tables

- Routes define how traffic moves (e.g., local, IGW, NAT GW, VPC Peering)

- Main route table is the default for all subnets unless specified.

### 🔒 Security in VPC
| Control                                               | Description                                                               |
| ----------------------------------------------------- | ------------------------------------------------------------------------- |
| **Security Groups**                                   | **Stateful**: Allow rules only (inbound & outbound automatically tracked) |
| **Network ACLs (NACL)**                               | **Stateless**: Must explicitly allow both directions                      |
| **SGs** apply to EC2, Lambda (in VPC), RDS, ENI, etc. |                                                                           |
| **NACLs** apply at subnet level (rarely used by devs) |                                                                           |

✅ Use Security Groups for application-layer control.

### 🛠️ VPC Endpoints
- Allow private access to AWS services without internet or NAT.
- Two types:
  - Interface Endpoint (powered by ENI): For services like SSM, API Gateway 
  - Gateway Endpoint: Only for S3 and DynamoDB

🧠 Tip: If you see a scenario where Lambda in private subnet needs to call S3 or DynamoDB, use a Gateway VPC Endpoint.

### 🧱 Elastic Network Interface (ENI)
- A virtual network adapter attached to an EC2 or Lambda in VPC.
- ENIs have:
  - Private IP 
  - MAC address 
  - SGs 
  - Can be moved between EC2 instances.

### 🧭 VPC Peering
- Connects 2 VPCs privately (no IGW needed). 
- Must add routes in both VPCs. 
- Not transitive (VPC A ↔ VPC B, VPC B ↔ VPC C ≠ VPC A ↔ VPC C).

### 🔁 Transit Gateway (TGW)
- Central hub for connecting multiple VPCs and on-prem networks. 
- Transitive routing supported. 
- More scalable than peering.

### 📎 Elastic IP (EIP)
- Static IPv4 address. 
- Used for:
  - NAT Gateway 
  - Bastion hosts 
  - EC2s needing fixed IP 
- Only pay if not attached to a running resource.

### ☁️ Developer-Focused Scenarios

#### 🧪 Scenario: Lambda in Private Subnet needs to call S3
- ❌ NAT Gateway (expensive)

- ✅ Use Gateway VPC Endpoint for S3

#### 🧪 Scenario: Restrict access to S3 from public internet
- ✅ Create VPC Gateway Endpoint for S3

- ✅ Block S3 public access

- ✅ Restrict bucket policy to only allow VPC endpoint traffic

#### 🧪 Scenario: EC2 needs internet access
- ✅ Place in Public Subnet

- ✅ Attach Elastic IP

- ✅ Route through Internet Gateway

#### 🧪 Scenario: Application Tier in Private Subnet, Web Tier in Public Subnet
- ✅ ALB in public subnet

- ✅ EC2 App servers in private subnet

- ✅ Route ALB → EC2 via Target Group

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



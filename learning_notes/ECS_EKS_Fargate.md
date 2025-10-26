### 🔹 ECS (Elastic Container Service)
- Container Orchestration Service (AWS-native alternative to Kubernetes). 
- Launch Types:
  - EC2 Launch Type → you manage EC2 instances (cluster). 
    - More control, can use Spot instances. 
    - Must handle patching & scaling yourself. 
  - Fargate Launch Type → serverless. 
    - AWS runs infra, you define CPU & memory per task. 
    - No patching, pay per use, slightly more $$ than EC2.
- Integrations:
  - IAM roles per Task (fine-grained security). 
  - Load balancers: ALB, NLB supported. 
  - Storage: ECS tasks can mount EFS for persistent shared storage. 
  - CloudWatch Logs + Metrics by default. 
  - Auto Scaling supported (via Service Auto Scaling).
- Traps:
  - Exam loves: "Lambda too limited, need long-running processes → ECS". 
  - Don’t confuse ECS (orchestration) with ECR (registry). 
  - Fargate doesn’t allow privileged containers (low-level host access).

### 🔹 EKS (Elastic Kubernetes Service)
- Managed Kubernetes Control Plane (AWS runs masters, you run worker nodes).
- Use when:
  - Teams already use Kubernetes tooling (kubectl, Helm, etc). 
  - Portability across multi-cloud/hybrid is a requirement.
- Compute Options:
  - Managed node groups (AWS manages EC2 ASGs). 
  - Self-managed nodes (your EC2). 
  - Fargate (serverless pods).
- Networking/Security:
  - Uses IAM Roles for Service Accounts (IRSA) → attach IAM permissions per pod. 
  - Same VPC networking rules as ECS.
- Traps:
  - EKS is not the default exam answer unless the scenario explicitly says Kubernetes. 
  - More complex than ECS, higher learning curve.

### 🔹 Fargate
- Serverless compute engine for ECS & EKS.
- No need to manage EC2 clusters.
- You specify CPU, memory, networking, AWS provisions runtime.
- Good for spiky workloads and cost efficiency when idle.
- Traps:
  - Can’t use GPU workloads (ECS EC2 required). 
  - More expensive for steady, high-utilization workloads.

### 🔹 ECR (Elastic Container Registry)
- Private Docker image registry, AWS-managed.
- Store, version, and scan container images.
- Fully integrated with IAM for authentication.
- Image Scanning for vulnerabilities.
- Supports lifecycle policies for cleaning up old images.
- Traps:
  - Don’t confuse with ECS (execution) or EKS (Kubernetes). 
  - Exam likes: “Where do you store container images?” → ECR.

### 🔹 Networking & Security
- Where do containers run? 
  - ECS tasks / EKS pods run inside VPC subnets. 
  - They can be placed in public (internet access) or private subnets.
- Load Balancers:
  - ALB = Layer 7 routing (path-based, host-based). 
  - NLB = Layer 4, high throughput, TCP/UDP.
- IAM Roles:
  - ECS = per Task Role. 
  - EKS = per Pod Role via IRSA.
- Persistent Storage:
  - EFS = mountable by ECS/EKS for shared storage.

### 🔹 Scaling & Deployment Strategies
- ECS Scaling 
  - Service Auto Scaling (based on CloudWatch alarms). 
  - Can scale number of tasks.
- EKS Scaling 
  - Cluster Autoscaler (scale worker nodes). 
  - Horizontal Pod Autoscaler (scale pods).
- Deployment Strategies (CodeDeploy / ECS / Lambda)
  - ECS/EKS: Rolling updates (gradually replace old tasks), or Blue/Green (via CodeDeploy).
  - Lambda: Canary, Linear, All-at-once.
  - EC2/on-prem: In-place, Blue/Green.
- Traps:
  - If you see “containers + Blue/Green” → ECS/EKS with CodeDeploy.
  - If you see “Lambda + Canary” → choose Lambda-specific deployment.

### 🔹 Monitoring & Logging
- CloudWatch Logs: ECS tasks & EKS pods can ship logs.
- CloudWatch Metrics: ECS service/task metrics, EKS node metrics.
- X-Ray: Trace containerized apps. 
- CloudTrail: Who did what (API calls).

### 🔹 Common Exam Scenarios
- 🧪 Scenario: Want containers without managing servers  -> ✅ ECS on Fargate.
- 🧪 Scenario: Already invested in Kubernetes -> ✅ EKS.
- 🧪 Scenario: Store and version container images -> ✅ ECR.
- 🧪 Scenario: Need fine-grained IAM per container -> ✅ ECS Task Role / EKS IRSA.
- 🧪 Scenario: Containers need persistent, shared storage -> ✅ Use EFS.
- 🧪 Scenario: Long-running processes (not Lambda) -> ✅ ECS or EKS.
- 🧪 Scenario: Blue/Green deployments with containers -> ✅ ECS/EKS with CodeDeploy.


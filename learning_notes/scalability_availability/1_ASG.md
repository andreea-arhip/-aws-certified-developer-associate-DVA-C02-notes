## ⚙️ Auto Scaling (ASG – Auto Scaling Group)

### ✅ What is it?
Automatically adjusts EC2 instance count based on defined policies (e.g., CPU usage, requests per target, custom metrics).

### 🚀 Launch Template
- Required to create ASG. 
- Specifies AMI, instance type, key pair, SGs, IAM role, etc. 
- Launch Configurations are deprecated (don’t pick them!).

### 📈 Scaling Types
| Type           | Description                                    |
| -------------- | ---------------------------------------------- |
| **Manual**     | You set desired capacity manually              |
| **Dynamic**    | Based on alarms/metrics (e.g., CPU > 70%)      |
| **Predictive** | Uses ML to anticipate traffic (enterprise use) |
| **Scheduled**  | Scale at specific times (e.g., 9 AM weekdays)  |


### 🔄 Dynamic Scaling Policies
| Policy              | Description                                                      |
| ------------------- | ---------------------------------------------------------------- |
| **Target Tracking** | Keep metric at target (e.g., CPU = 50%)                          |
| **Step Scaling**    | Increase capacity gradually (e.g., +1 if CPU > 60%, +2 if > 80%) |
| **Simple Scaling**  | Add/remove fixed number of instances on alarm                    |

✅ Target Tracking is most recommended and most common in exam scenarios

### ⚠️ Gotchas
- ASG can span multiple AZs, but NOT regions. 
- You can attach Elastic Load Balancers (Classic/ALB/NLB) to ASG. 
- Health checks: use ELB or EC2 checks. 
- Cooldown period avoids rapid scale in/out.

### Quick notes
- Regional, supports multi-AZ, free (only pay for EC2, EBS etc)
- Scales EC2 instances horizontally (adds/removes instances).
- IAM Role attached to ASG → applied to all launched EC2s.
- If ASG is deleted → all instances are terminated.
- ELB marks unhealthy → ASG replaces automatically.
- Scaling policies:
    - Scheduled
    - Simple: If CPU > 90% → 10 instances
    - Step: +2 at 70%, -1 at 30%
    - Target: Keep CPU ~ 40%
    - Predictive (uses ML + history): auto-forecast
- Launch configuration -> does not support Spot instances, cannot be updated (only recreated)
- Launch template (newer) -> versioned, updatable (new version created), both On Demand & Spot
    - To change the launch template for an Auto Scaling group, you must create a launch template and then update your Auto Scaling group with it.
- Use User Data to bootstrap configuration (install agents, dependencies, etc.).
- Cooldown: 5mins, prevents overscaling
- Instance refresh: StartInstanceRefresh API:
    - rolling update using new Launch Template version -> no downtime
    - define minimum healthy %, warm-up time
- EC2 ASG works with: ALB, NLB, CLB
- Creating an ASG from an existing instance's launch template -> no AMI generated, needs to be created custom

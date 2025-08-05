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

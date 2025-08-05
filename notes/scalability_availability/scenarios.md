### 🧪 Scenario-Based Exam Triggers

#### ☁️ "Automatically scale the EC2 instances when traffic increases"
→ Use Auto Scaling Group with Target Tracking 

→ Attach to ALB or NLB

---

#### ⚠️ "Need to terminate unhealthy instances"
→ Auto Scaling + ELB Health Check

---

#### 🎯 "Route /api/* to one service and /admin/* to another"
→ ALB with path-based routing

---

#### 🔒 "Use Cognito to authenticate users via load balancer"
→ ALB + Cognito listener rule (Layer 7 only)

---

#### 📬 "Application requires static IP and TLS passthrough"
→ Use NLB, not ALB.

---

#### 🧠 "User session must always reach same backend"
→ Use Sticky Sessions (Cookies) on ALB or CLB

---

#### 💻 "App must detect client IP"
→ NLB preserves source IP

→ ALB uses X-Forwarded-For

---

### 🧠 Memory Tricks
- ALB = Application routing, Auth (Cognito), API Gateway-like behavior

- NLB = Network speed, No nonsense (TCP/UDP, fast, scalable)

- CLB = Clunky, Considered legacy

- ASG = Auto Server Generator

### ✅ Key Integration Tips
- ALB + Lambda = serverless routing!

- ASG + ALB = scalable and available compute

- ALB + ECS = microservice container routing

- ASG works only with EC2 instances

- NLB + PrivateLink = great for VPC-to-VPC secure services


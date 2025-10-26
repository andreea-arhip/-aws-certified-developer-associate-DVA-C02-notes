## 🧊 Elastic Load Balancers (ELB)
### ✅ Purpose:
Distribute incoming traffic across multiple targets (EC2, Lambda, containers, etc.)

### ⚖️ ELB Types
| Type                                | Use Case                     | Layer                            | Features                                     |
| ----------------------------------- | ---------------------------- |----------------------------------| -------------------------------------------- |
| **Classic ELB (CLB)**               | Legacy only                  | Layer 4 & 7                      | Not recommended anymore                      |
| **Application Load Balancer (ALB)** | HTTP(S), modern apps         | Layer 7 = HTTP/HTTPS, WebSockets | Path-based, host-based routing, web sockets  |
| **Network Load Balancer (NLB)**     | Extreme performance, TCP/UDP | Layer 4 = TCP/UDP                | High throughput, static IP, TLS passthrough  |
| **Gateway Load Balancer (GWLB)**    | 3rd-party virtual appliances | N/A                              | Advanced routing/filtering (e.g., firewalls) |


### 🧠 Exam Tips
- ALB:
  - Supports routing rules, target groups, listener rules 
  - Works with Lambda, EC2, ECS, Fargate 
  - Uses host-based and path-based routing:
    - /api/* → service A 
    - /admin/* → service B 
  - Can route to multiple target groups 
  - Supports WebSockets 
  - Supports redirects and fixed responses 
  - Integrates with WAF, Cognito for auth

- NLB:
  - Best for high-performance, low-latency TCP 
  - Supports static IPs, Elastic IPs 
  - Can preserve source IP (ALB cannot)
  - Can handle millions of requests/sec

### ✅ Traps & Gotchas (Exam Style)
- ALB can’t preserve source IP. NLB can. 
- NLB = super low latency, high throughput. ALB is smart but slower. 
- ALB integrates with Lambda, NLB does not. 
- Static IPs = only NLB (or Global Accelerator), not ALB. 
- WebSockets supported only on ALB, not NLB.

👉 So, if exam asks:
- “Need to route based on URL or host?” → ALB 
- “Need 1M requests/sec, low latency, static IP?” → NLB 
- “Need Lambda as backend?” → ALB 
- “Backend must know client IP?” → NLB


### 🧠 Load Balancer Concepts
| Concept                        | Description                                    |
| ------------------------------ | ---------------------------------------------- |
| **Listener**                   | Port/protocol (e.g., HTTP:80, HTTPS:443)       |
| **Target Group**               | Group of resources (EC2, Lambda, IPs)          |
| **Health Check**               | Determines if target is healthy                |
| **Sticky Sessions (ALB, CLB)** | Same user routed to same instance (via cookie) |
| **Cross-Zone Load Balancing**  | Evenly distributes traffic across AZs          |


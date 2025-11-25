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

### Quick notes:
- Regional, supports Multi-AZ
  - Distributes traffic across EC2s / containers / Lambdas.
  - Health checks on route + port (e.g. /health).
  - ❌ No weighted routing (use Route 53 for that).
  - errors:
    - 503 → no registered targets.
    - 504 → targets unreachable (SG/NACL issue).
  - ALB:
    - Layer 7, for microservices, containers, or path-based routing
    - headers added: X-Forwarded-For (client IP), X-Forwarded-Port, X-Forwarded-Proto.
    - Target groups: EC2, ECS tasks, Lambda, IPs (private only, no publicly routable IP addresses).
    - Routing rules: by Path, Host, Query String, Header, Source IP.
    - Dynamic Port Mapping → supports multiple containers on same EC2.
    - Security groups can be added to ALBs to filter requests
    - Supports OIDC / Cognito authentication natively:
      - The ALB handles all authentication before traffic reaches your app, so your existing code stays untouched.
      - Authentication occurs at HTTPS (port 443) — mandatory for secure login redirects.
    - SNI supported → multiple SSL certs.
    - Cross-zone load balancing - equal traffic across AZs -> always enabled for ALB
    - ALB access logs: optional (default = disabled), detailed info about requests, stored in S3
    - ALB request tracing: the LB adds a header with a trace identifier to track requests
    - ALB rule config:
      - Actions -> "Type": forward, redirect, fixed-response
      - Conditions -> "Field" + config object: "QueryStringConfig", "PathPatternConfig", "HostHeaderConfig"
      - Ex: `"QueryStringConfig"` -> `"Values": [ {"Key": "version", "Value": "v1"}, {"Value": "*example*"} ]`
    - SSL Termination (Offloading): Client  ---HTTPS--->  ALB  ---HTTP--->  EC2
      - The ALB handles decryption (offloads CPU work from EC2).
      - Requires uploading an SSL certificate to the ALB (from ACM).
      - The traffic inside the VPC (ALB → EC2) is usually plain HTTP — still secure since the VPC is private.
    - SSL Pass-Through: Client  ---HTTPS--->  ALB  ---HTTPS--->  EC2
      - Used only if you need end-to-end encryption (compliance or security policy).
      - EC2 must have the certificate and does the decryption itself. (heavier workload)
    - Multi-value headers for Lambda - applies for headers and query params
      - ?name=foo&name=bar ->> ‘name’: [‘foo’, ‘bar’]
  - Network Load Balancer (NLB)
    - Layer 4 (TCP/UDP), millions of requests/sec, ~100 ms latency.
    - Static IP per AZ (+ optional Elastic IP).
    - ❌ No SGs (targets must allow LB IPs).
    - ❌ No OIDC authentication — only forwards TCP traffic
    - Can target EC2, IPs, or ALB.
    - Preserves client’s original IP.
    - SNI supported (for TLS listeners).
  - Sticky sessions (CLB & ALB) - cookie types: app-generated (TTL), LB generated (AWSALB, AWSALBAPP, AWSALBTG)
  - SNI (Server name indication):
    - Only works for ALB & NLB + supported in CloudFront
    - extension to TLS - client tells which hostname is trying to reach — before the SSL handshake completes.
    - "Host multiple HTTPS websites on one ALB"
    - “Need multiple SSL certs on one listener”
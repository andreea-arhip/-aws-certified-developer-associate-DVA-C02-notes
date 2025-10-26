# AWS Certified Developer Associate — Edge, Networking, CDN, Route53 & Global Accelerator (Detailed Exam Scenarios)

## CloudFront & CDN

* **Detailed Exam-Oriented Scenarios & Patterns:**

    1. Static website → CloudFront + S3 origin, OAI for security. *Pattern:* Content delivery + security.
    2. Edge caching → set TTLs per path to reduce origin hits. *Pattern:* Performance optimization.
    3. Signed URLs/Cookies → private content distribution. *Pattern:* Controlled access.
    4. Custom error responses → redirect 404 to custom page. *Pattern:* Improved UX.
    5. Lambda@Edge → header modification, redirects, authentication. *Pattern:* Edge function customization.
    6. WAF integration → block malicious traffic. *Pattern:* Security at CDN edge.
    7. Multi-region origin → failover if primary origin fails. *Pattern:* High availability.
    8. Compression & Gzip → optimize bandwidth. *Pattern:* Cost & performance optimization.
    9. Enforce HTTPS only → TLS security. *Pattern:* Secure data transfer.
    10. Geo-restriction → block/allow content per region. *Pattern:* Compliance/licensing control.
* **Traps:** Expecting instant invalidation, ignoring cache behavior settings.

## Route 53

* **Detailed Exam-Oriented Scenarios & Patterns:**

    1. Simple routing → single resource. *Pattern:* Basic DNS routing.
    2. Failover routing → primary/secondary endpoint. *Pattern:* DR via DNS.
    3. Weighted routing → A/B testing traffic. *Pattern:* Experiment & traffic control.
    4. Latency-based routing → route to fastest region. *Pattern:* Improve end-user latency.
    5. Geo-location routing → content localization. *Pattern:* Regional content distribution.
    6. Alias vs CNAME → AWS resource mapping. *Pattern:* Correct routing configuration.
    7. Health checks → automated failover. *Pattern:* Monitoring + routing.
    8. Private hosted zones → VPC internal DNS names. *Pattern:* Internal-only service resolution.
    9. Multi-region DR → shift traffic to other region in outage. *Pattern:* DNS-based failover.
    10. Integration with CloudFront & S3 for static websites. *Pattern:* Full-stack serverless delivery.
* **Traps:** Misconfigured alias, missing health check, assuming latency-based routing works without global endpoints.

## Global Accelerator

* **Detailed Exam-Oriented Scenarios & Patterns:**

    1. Global static IPs → front app endpoints. *Pattern:* Firewall-friendly endpoints.
    2. Traffic steering → optimal AWS region. *Pattern:* Latency optimization.
    3. Integration with ALB/NLB → improve regional performance. *Pattern:* Multi-region app routing.
    4. Automatic failover between regions. *Pattern:* High availability.
    5. Latency-sensitive applications → choose GA over CloudFront. *Pattern:* Performance critical routing.
    6. Health checks → automatic endpoint monitoring. *Pattern:* Failover automation.
    7. Multi-region disaster recovery. *Pattern:* DR strategy.
    8. Static IPs for firewall whitelisting. *Pattern:* Enterprise security compliance.
    9. Edge location → AWS Anycast network. *Pattern:* Global low-latency routing.
    10. Integration with CloudFront → hybrid architecture for dynamic/static content. *Pattern:* Flexible CDN architecture.

* **Traps:** Confusing CloudFront and GA roles, forgetting static IP requirements.

* **Patterns & Best Practices:** CloudFront + WAF for security, Route 53 health checks for failover, Global Accelerator for latency-sensitive multi-region apps.

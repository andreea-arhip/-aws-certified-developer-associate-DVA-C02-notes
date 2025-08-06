### 🚀 What is CloudFront?
- Content Delivery Network (CDN) to cache and deliver content (static, dynamic, streaming) globally from edge locations.
- Speeds up delivery by reducing latency and offloading origin (like S3, EC2, ALB).
- Works with HTTP/HTTPS, supports TLS, and integrates with AWS Shield, WAF, Lambda@Edge

### 🗺️ Key Components
| Component             | Description                                                                       |
| --------------------- | --------------------------------------------------------------------------------- |
| **Origin**            | Where CloudFront pulls content from (e.g., S3, ALB, EC2, or external HTTP server) |
| **Edge Location**     | Global network of caching servers                                                 |
| **Distribution**      | A CloudFront configuration (links edge locations to origin)                       |
| **Cache Behavior**    | Rules for path patterns, methods, TTLs, headers, etc.                             |
| **TTL**               | Controls how long objects stay in cache (default: 24h)                            |
| **Signed URL/Cookie** | Restrict access to content (for private distributions)                            |

### ⚙️ How It Works
- User requests content via CloudFront URL.
- CloudFront checks cache in nearest edge location.
- If not cached (cache miss), CloudFront fetches from origin, then caches it.
- On next request, CloudFront serves from cache (cache hit).

### 🔐 Security & Access Control
| Feature                         | Use Case                                                                     |
| ------------------------------- | ---------------------------------------------------------------------------- |
| **Signed URLs / Cookies**       | Private access to specific content (e.g., paid videos)                       |
| **Origin Access Control (OAC)** | Restrict S3 bucket access to CloudFront only (preferred over deprecated OAI) |
| **Field-level encryption**      | Encrypt sensitive data (e.g., credit card)                                   |
| **HTTPS**                       | TLS encryption from user to edge, and optionally to origin                   |
| **WAF Integration**             | Block bad actors at edge before reaching origin                              |

### ⚡ Lambda@Edge
- Run Lambda functions at CloudFront edge locations.
- Use cases:
  - URL rewrites/redirection.
  - A/B testing. 
  - Authentication/authorization. 
  - Custom headers or cookies manipulation.
- Written in Node.js or Python.
- Can modify viewer request, origin request, origin response, or viewer response.

### 📌 Common Exam Scenarios
| Scenario                              | Solution                                                     |
| ------------------------------------- | ------------------------------------------------------------ |
| Deliver images/video fast globally    | Use CloudFront with **S3 or EC2** origin                     |
| Restrict S3 access to CloudFront only | Use **Origin Access Control (OAC)**                          |
| Dynamic behavior at edge              | Use **Lambda\@Edge**                                         |
| Access control on paid content        | Use **Signed URLs** or **Signed Cookies**                    |
| Reduce origin load                    | Use long **TTL** and caching                                 |
| Protect from malicious requests       | Attach **AWS WAF** to CloudFront                             |
| HTTPS only delivery                   | Enforce viewer protocol policy                               |
| Require custom domain                 | Use **Alternate Domain Names (CNAMEs)** + **SSL cert (ACM)** |

### 🧠 Tips & Tricks
- S3 + CloudFront: Great combo for static websites.
- CloudFront can cache dynamic content if headers allow it.
- Supports invalidations to force cache refresh (but costly for large numbers).
- Default TTL is 24h, but can be configured per path via cache behavior.
- Prefer OAC over old OAI for S3 access control.
- Free tier includes 1 TB/month CloudFront data transfer.

### 🔄 CloudFront vs S3 Static Website Hosting
| Feature             | S3 Static Website          | CloudFront                       |
| ------------------- | -------------------------- | -------------------------------- |
| **Global delivery** | ❌ (regional)               | ✅                                |
| **HTTPS**           | ❌ (unless with CloudFront) | ✅                                |
| **Custom caching**  | Limited                    | Advanced (TTL, cookies, headers) |
| **Access control**  | Basic (public/private)     | Advanced (signed URLs, WAF)      |

### 💡 Summary Table
| Use Case                    | CloudFront Feature                        |
| --------------------------- | ----------------------------------------- |
| Global low-latency delivery | Edge Locations                            |
| Restrict access to content  | Signed URLs/Cookies                       |
| Secure origin access        | Origin Access Control (OAC)               |
| Add logic at edge           | Lambda\@Edge                              |
| Protect app at edge         | WAF + CloudFront                          |
| Control TTL, headers        | Cache Behavior settings                   |
| Serve via custom domain     | Alternate Domain Names (CNAME) + ACM Cert |


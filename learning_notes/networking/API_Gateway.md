### 🚀 PURPOSE
Amazon API Gateway is a fully managed service to:
  - Create, publish, maintain, monitor, and secure REST, HTTP, and WebSocket APIs 
  - Connect frontend clients to backend services (e.g., Lambda, ECS, HTTP endpoints, etc.)
  - Provide features like authorization, rate limiting, caching, and monitoring

### ⚙️ API TYPES
| API Type      | Use Case                                               | Supports Lambda  |
| ------------- | ------------------------------------------------------ | ---------------- |
| **HTTP API**  | Lightweight, low-latency, simpler config, 70% cheaper  | ✅                |
| **REST API**  | Full-featured, supports API keys, usage plans, caching | ✅                |
| **WebSocket** | Real-time bidirectional communication                  | ❌ (not for REST) |

🧠 Exam Tip:
- Use HTTP API for simpler, cost-effective APIs. 
- Use REST API for features like API keys, request validation, throttling, usage plans. 
- WebSocket = real-time chat, multiplayer games, notifications.

### 🧠 MEMORY TIPS & EXAM SCENARIOS
| Keyword / Scenario                           | Think Of                          |
| -------------------------------------------- | --------------------------------- |
| "Expose Lambda over HTTPS"                   | API Gateway + Lambda              |
| "WebSocket / Chat / Live updates"            | WebSocket API                     |
| "Need API keys, request throttling, caching" | REST API                          |
| "Real-time push from client to server"       | WebSocket                         |
| "Simpler and cheaper API"                    | HTTP API                          |
| "Authorization at API level"                 | IAM / Cognito / Lambda Authorizer |

### 🔐 AUTHORIZATION OPTIONS
| Method                 | Use Case / Exam Tip                                        |
| ---------------------- | ---------------------------------------------------------- |
| **NONE**               | Public APIs                                                |
| **IAM**                | Use SigV4 auth (signed AWS credentials)                    |
| **Cognito User Pools** | Manage users & federate identities for login (JWT tokens)  |
| **Lambda Authorizer**  | Custom logic to validate tokens or headers (advanced auth) |

### 📊 THROTTLING, CACHING & API KEYS
🔸 Throttling
- Protect your backend from overload. 
- Two settings:
  - Rate limit: max requests per second (RPS)
  - Burst limit: short-term spike buffer 
- Configurable per stage or per usage plan

🔸 API Keys & Usage Plans
- REST API only
- Control API access and track usage by client (API Key = acts like an identifier (not authentication!) for each client)
- Pair with usage plans for setting:
  - throttling (requests per second)
  - burst limits → Short-term spike handling.
  - quotas (requests per day / week / month)

- ✅ Example Scenario (exam-style):
  - You have a public REST API with free and paid tiers.
    - Free tier: 1,000 requests/day, throttle at 5 req/sec.
    - Paid tier: 10,000 requests/day, throttle at 50 req/sec.
  - Solution: Create two usage plans, generate API keys for each tier, and link them to the corresponding plan.

🔸 Caching
- REST API only 
- Caches GET responses at API Gateway level (stage level (per method & URL))
- TTL (time-to-live) is configurable 
- Reduces calls to backend (e.g., Lambda, DynamoDB)
- Can be invalidated with client requests (if enabled).

### 🧱 INTEGRATION TYPES
| Integration Type       | Description / Example                               |
| ---------------------- | --------------------------------------------------- |
| **Lambda**             | Invoke function directly                            |
| **HTTP / HTTP\_PROXY** | Forward to any HTTP endpoint (e.g., EC2, SaaS, ALB) |
| **AWS Service**        | Call AWS services (e.g., S3, DynamoDB, SNS, etc.)   |
| **MOCK**               | Return fixed response (test/stub)                   |

🧠 Proxy integrations pass the full request to the backend (less config, more flexible).

### 🧩 DEPLOYMENTS & STAGES
- API Gateway uses stages to manage versions (e.g., dev, prod)
- Deployments are snapshots of your API config 
- Each stage can have:
  - Custom domain mappings 
  - Stage variables (e.g., $stageVariable)
  - Logging, throttling, cache settings

### 🔍 MONITORING & LOGGING
- Use CloudWatch Logs for request/response logging 
- Use CloudWatch Metrics for:
  - 4xx/5xx errors 
  - Latency 
  - Integration errors 
  - Count of requests

🧠 Logs are disabled by default — must be explicitly enabled per stage.

### 🧪 REQUEST/RESPONSE MAPPING (REST APIs)
- Mapping templates (Velocity Template Language - VTL)
- Transform requests before passing to backend 
- Useful for integrating with non-AWS systems 
- Customize headers, paths, query strings, and body

🧠 Example: Convert a REST call into a valid DynamoDB PutItem request.

### ⚠️ COMMON EXAM TRAPS
| Trick / Trap                         | Clarification / Fix                          |
| ------------------------------------ | -------------------------------------------- |
| Using REST API but can’t use caching | Make sure you enable cache in stage settings |
| API call unauthorized                | Check IAM role or Authorizer config          |
| Lambda returns error                 | Check integration request mapping or policy  |
| Cannot throttle requests             | Use usage plans + API keys (REST only)       |
| Trying to use API keys with HTTP API | Not supported — use **REST API** instead     |
| WebSocket confusion                  | Not used with Lambda REST endpoints          |

### 💡 DEVELOPER CONTEXT & COMMON COMBINATIONS
| Need                                  | Solution                                      |
| ------------------------------------- | --------------------------------------------- |
| Invoke Lambda from browser over HTTPS | API Gateway + Lambda + CORS                   |
| Authenticated API with user info      | API Gateway + Cognito or Lambda Authorizer    |
| Rate limit clients separately         | API Gateway + REST + Usage Plans + API Keys   |
| Connect frontend to backend securely  | API Gateway + VPC Link (for private services) |

### 📌 Quick notes
- API Gateway:
  - HTTPS only (TLS termination)
  - Swagger / OpenAPI config supported (import or export):
    - define, validate, import / export API schema (throw 400 error on validation)
  - integration: MOCK, AWS_PROXY, AWS, HTTP_PROXY (optional - add headers ex API key), HTTP
    - mapping templates: Content-type must be json/xml, uses VTL (Velocity Template Language)
  - endpoint types: edge-optimized (default - global via Cloudfront), regional, private
    - TLS termination: edge-optimized endpoint -> cert must be in us-east-1 / regional -> cert in same region
  - stage variables:
    - can be used to point to different Lambda aliases -> alias weights
    - can be changed without redeploying the API
    - passed to context of Lambda: ${stageVariables.variableName}
  - caching - at stage level, TTL 0-1h (default 300sec), expensive (use for PROD), can use encryption
    - Cache-Control: max-age=0 -> IAM policy OR Require authorization checkbox
  - usage plans:
    - Associate API stages and API keys with the usage plan using CreateUsagePlanKey API
    - Clients use API keys to access the APIs (passed in X-API-Key header)
  - logging - can be enabled at stage level (overrides API level)
    - 2 levels: execution (req, res) / access (who, how)
  - Metrics - at stage level:
    - CacheHitCount & CacheMissCount, 4XX / 5XX error counts
    - Count, Latency (client-API-backend), IntegrationLatency (API-backend)
  - 429 error -> throttling, set stage /methods limits, usage plans
  - CORS:
    - Browser sends a pre-flight OPTIONS request ==> Response must include → Access-Control-Allow-Origin, Access-Control-Allow-Methods, etc.
      - MaxAgeSeconds → how long the browser caches the pre-flight result.
    - 🧩 Proxy Integration: backend must return CORS headers.
    - 🧩 Non-Proxy Integration: API Gateway can inject headers automatically.
  - Lambda authorizer: token-based (JWT or OAUTH), request param-based (identity from headers, body, stageVariables, $context)
  - REST → full-featured, resource policies, API keys, ❌ no OIDC/OAuth2, 💰 expensive
  - HTTP → lightweight proxy (Lambda/HTTP), ✅ OIDC/OAuth2, ❌ API keys, 💸 cheap
  - WebSocket → 2-way persistent connection, real-time apps (chat, games)


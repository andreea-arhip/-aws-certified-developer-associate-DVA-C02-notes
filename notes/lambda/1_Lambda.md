### 🚀 1. OVERVIEW

| Feature               | Details                                                                                   |
| --------------------- | ----------------------------------------------------------------------------------------- |
| **What**              | Serverless compute: run backend code without provisioning servers                         |
| **Stateless**         | Each execution is independent                                                             |
| **Billing**           | Based on **invocations, duration, and memory**                                            |
| **Runtime Languages** | Node.js, Python, Java, Go, Ruby, .NET, Custom Runtime, Container Image                    |
| **Execution Model**   | Event-driven: reacts to triggers like API Gateway, S3, SQS, etc.                          |
| **Memory Device Tip** | Think: **FLEXX** – Function URL, Layers, Ephemeral `/tmp`, eXecution role, Timeout (900s) |

<br>

### ⚙️ 2. CONFIGURATION PROPERTIES

| Property                       | Details                                                                |
| ------------------------------ | ---------------------------------------------------------------------- |
| **Memory**                     | 128 MB to **10,240 MB** (10 GB); CPU increases with memory             |
| **Timeout**                    | Max: **15 minutes (900 sec)**                                          |
| **Ephemeral Storage (`/tmp`)** | Default 512 MB; configurable up to **10 GB**                           |
| **Environment Variables**      | Encrypted at rest (KMS optional)                                       |
| **Execution Role (IAM)**       | Required to access AWS resources (e.g., S3, DynamoDB, Secrets Manager) |
| **Architecture**               | x86\_64 (default) or **arm64** (cheaper, better cold starts)           |
| **Concurrency Limits**         | Default: 1000 per region                                               |

<br>

### 📦 3. DEPLOYMENT OPTIONS

| Type                | Max Size                    | Notes                                                        |
| ------------------- | --------------------------- | ------------------------------------------------------------ |
| **ZIP Upload**      | 50 MB direct, 250 MB via S3 | Standard packaging for small apps                            |
| **Container Image** | Up to 10 GB                 | Custom runtimes, large apps, compiled binaries               |
| **Lambda Layers**   | 50 MB per layer             | Max 5 per function; use to share dependencies across Lambdas |

<br>

### 🔁 4. VERSIONING & DEPLOYMENTS

| Feature              | Description                                                        |
| -------------------- | ------------------------------------------------------------------ |
| **Versions**         | Immutable snapshots (1, 2, 3, …)                                   |
| **Aliases**          | Pointers to versions (e.g., `dev`, `prod`)                         |
| **Traffic Shifting** | Supported via **CodeDeploy** using `AllAtOnce`, `Linear`, `Canary` |
| **Rollback Support** | Can trigger on CloudWatch alarms if new version fails              |

<br>

### 🔗 5. TRIGGERS & INTEGRATIONS

| Trigger Type             | Who initiates?        | Does Lambda retry on failure?    | Notes                                                          |
| ------------------------ | --------------------- | -------------------------------- | -------------------------------------------------------------- |
| **Asynchronous**         | Event source pushes   | ✅ Yes (with exponential backoff) | Good for decoupled systems; failure retries are built-in.      |
| **Synchronous**          | Caller invokes Lambda | ❌ Caller handles retry           | Example: API Gateway (waits for Lambda result).                |
| **Event Source Mapping** | Lambda **pulls**      | ✅ Yes (automatically)            | Used for pollable sources like SQS, DynamoDB Streams, Kinesis. |
---
| Trigger Source               | Invocation Type  | Notes                                                            |
| ---------------------------- | ---------------- | ---------------------------------------------------------------- |
| **API Gateway**              | Synchronous      | Use for RESTful web services                                     |
| **Function URL**             | Synchronous      | HTTPS endpoint, simpler than API Gateway (IAM or NONE auth only) |
| **S3**                       | Asynchronous     | Trigger on PUT, DELETE events                                    |
| **SNS / EventBridge**        | Asynchronous     | Push events to Lambda                                            |
| **SQS**                      | Event Source Map | Lambda polls; supports batching and retries                      |
| **DynamoDB Streams**         | Event Source Map | Trigger Lambda on item-level changes                             |
| **Kinesis**                  | Event Source Map | Similar to DynamoDB Streams, with record checkpointing           |
| **CloudWatch Events**        | Asynchronous     | For cron-style scheduled jobs or rule-based triggers             |
| **CodePipeline, CodeCommit** | Synchronous      | CI/CD integration                                                |

<br>

### 🧯 6. ERROR HANDLING & RETRIES
| Use Case                            | Solution                                                                            |
| ----------------------------------- | ----------------------------------------------------------------------------------- |
| **Async Invocation Failure**        | 2 retries by default; then can send to **DLQ (SQS/SNS)** or **Lambda Destinations** |
| **Event Source (SQS, Kinesis)**     | Retries automatically; behavior configurable via event source mapping               |
| **Synchronous (e.g., API Gateway)** | Caller is responsible for retry logic                                               |
| **Post-invocation Actions**         | Use **Lambda Destinations** to route success/failure events to another target       |

<br>

### 🌐 7. VPC INTEGRATION
| Feature             | Notes                                                                  |
| ------------------- | ---------------------------------------------------------------------- |
| **Required for**    | Accessing **private resources** like RDS, ElastiCache, or EFS          |
| **Subnets**         | Use **private subnets** (attach NAT Gateway if internet access needed) |
| **Security Groups** | Must be configured properly to allow outbound traffic                  |
| **Cold Starts**     | Higher latency for first execution in VPC (due to ENI provisioning)    |

<br>

### 📈 8. SCALING & CONCURRENCY
| Feature                     | Description                                                         |
| --------------------------- | ------------------------------------------------------------------- |
| **Default Concurrency**     | 1000 concurrent executions per region                               |
| **Burst Limits**            | Region-specific burst (e.g., 500+ concurrent for short bursts)      |
| **Reserved Concurrency**    | Limits function to fixed concurrent runs (also reserves that quota) |
| **Provisioned Concurrency** | Keeps functions pre-warmed (avoids cold starts, but costs more)     |

<br>

### 🔐 9. SECURITY BEST PRACTICES
| Feature                  | Tip                                                               |
| ------------------------ | ----------------------------------------------------------------- |
| **Execution Role (IAM)** | Follow **least privilege** principle                              |
| **KMS for Env Vars**     | Encrypt sensitive data in environment variables                   |
| **Access Secrets**       | Use **AWS Secrets Manager** or **SSM Parameter Store**            |
| **VPC Functions**        | Must assign subnets + security groups                             |
| **Function URLs**        | Use **IAM Auth** or make public if needed (careful with exposure) |

<br>

### ⚠️ 10. COMMON TRAPS & EXAM SCENARIOS
| Exam Clue / Trap                           | Think…                                               |
| ------------------------------------------ | ---------------------------------------------------- |
| “Serverless compute”                       | ✅ Lambda                                             |
| “Trigger on file upload to S3”             | ✅ S3 Event Notification → Lambda                     |
| “Must retry failed async executions”       | ✅ DLQ or Lambda Destinations                         |
| “Webhook or simple endpoint”               | ✅ Lambda Function URL or API Gateway                 |
| “Avoid cold start latency”                 | ✅ Use Provisioned Concurrency                        |
| “Run job every night at midnight”          | ✅ EventBridge rule with schedule + Lambda            |
| “Lambda needs internet in VPC”             | ✅ VPC + NAT Gateway                                  |
| “Lambda must access RDS”                   | ✅ Put Lambda inside VPC                              |
| “Long running job >15 mins”                | ❌ Lambda not suitable                                |
| “Share code across functions”              | ✅ Use Lambda Layers                                  |
| “User uploads file and trigger processing” | ✅ S3 trigger                                         |
| “Sensitive data in code”                   | ❌ Use Secrets Manager or SSM Parameter Store instead |
| “Rate-limit Lambda”                        | ✅ Use Reserved Concurrency                           |


### 🧠 What is SQS?
Amazon SQS is a fully managed, serverless message queuing service that enables decoupling of distributed components.

Unlike SNS (which is push-based), SQS is a pull-based system.

### 🧩 SQS Queue Types
| Queue Type         | Use Case                                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------------------ |
| **Standard Queue** | Default, nearly-unlimited throughput, best-effort ordering, at-least-once delivery                                 |
| **FIFO Queue**     | First-In-First-Out, **exactly-once processing**, guaranteed order, limited throughput (300–3000 tps with batching) |


### 🧃 Message Lifecycle
| Step                   | Detail                                                          |
| ---------------------- | --------------------------------------------------------------- |
| **SendMessage**        | Producers send messages to the queue                            |
| **ReceiveMessage**     | Consumers poll for messages (long or short polling)             |
| **Visibility Timeout** | Hides message after being read — prevents double processing     |
| **DeleteMessage**      | Must explicitly delete after processing to prevent reprocessing |


### ⚙️ Key Configuration Options
| Option                      | Default / Limit                      | Notes                                                              |
| --------------------------- | ------------------------------------ |--------------------------------------------------------------------|
| **Message retention**       | 4 days (min 1 min – max 14 days)     | How long SQS stores unconsumed messages                            |
| **Visibility timeout**      | Default 30 sec, max 12 hours         | Hide msg after retrieval, avoid duplication                        |
| **Max message size**        | 256 KB                               | Use S3 for larger payloads (store link in msg)                     |
| **Delivery delay**          | Up to 15 minutes (Delay Queue)       | Delay message delivery                                             |
| **Dead-letter queue (DLQ)** | For failed messages (redrive policy) | Messages can be moved after X total receives without being deleted |


### 🧠 Core Features
| Feature                    | Description                                        |
| -------------------------- | -------------------------------------------------- |
| **At-least-once delivery** | Message may be delivered multiple times            |
| **Decoupling**             | Enables components to communicate asynchronously   |
| **Durable**                | Messages persisted across AZs                      |
| **Long polling**           | Reduces cost and latency compared to short polling |
| **DLQ**                    | Handles poison messages (that keep failing)        |
| **Batched operations**     | Send, receive, or delete up to 10 messages at once |

### 📦 FIFO Queue Extras
| Feature               | Description                                                                      |
| --------------------- | -------------------------------------------------------------------------------- |
| **Message group ID**  | Maintains ordering for that group                                                |
| **Deduplication ID**  | Prevents duplicate messages (optional if content-based deduplication is enabled) |
| **Throughput limits** | 300 tps (default), 3000 tps with batching                                        |


### 🔗 Integrations
| Service            | Integration Role                                        |
| ------------------ | ------------------------------------------------------- |
| **SNS**            | SNS can push messages to SQS (for fan-out + durability) |
| **Lambda**         | Lambda polls SQS (event source mapping)                 |
| **EventBridge**    | Can route events to SQS                                 |
| **Step Functions** | Can push tasks to or pull from SQS queues               |
| **KMS**            | Encrypt SQS messages at rest                            |
| **CloudWatch**     | Monitor metrics like number of messages, DLQ usage      |


### 📖 Common Use Cases
| Scenario                            | Queue Type     | Notes                            |
| ----------------------------------- | -------------- | -------------------------------- |
| Decoupling microservices            | Standard Queue | High throughput, flexibility     |
| Order processing workflow           | FIFO Queue     | Guarantees order and uniqueness  |
| Retry failed transactions safely    | DLQ            | Redrive policy + troubleshooting |
| Batching write-intensive operations | SQS + Lambda   | Cost-efficient                   |
| Fan-out + durability                | SNS → SQS      | Classic pattern                  |


### 🔒 Security
| Feature          | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| **IAM policies** | Restrict access to SQS actions (SendMessage, ReceiveMessage) |
| **Queue policy** | Control who can send/read messages (cross-account allowed)   |
| **SSE**          | Server-Side Encryption with AWS KMS                          |


### 🧠 Memorization Tricks
- “SQS = Pull”, “SNS = Push” 
- “Order + exactly-once = FIFO” 
- “High throughput + best-effort order = Standard” 
- “Dead-letter = retry safety net” 
- “Visibility timeout ≠ message retention” 
- “Message too big? Store in S3, link in SQS”

### ⚠️ Tricky Questions & Gotchas
| Trap / Detail                                      | Watch Out!                                                                                                                                             |
| -------------------------------------------------- |--------------------------------------------------------------------------------------------------------------------------------------------------------|
| Forgetting to delete message                       | Message will reappear after visibility timeout                                                                                                         |
| FIFO requires Group ID                             | Always! No group = no ordering                                                                                                                         |
| Message duplication in Standard queues             | Expected — design for **idempotent consumers**                                                                                                         |
| Message deduplication in FIFO                      | Dedup ID = 5-minute deduplication window                                                                                                               |
| DLQ doesn't retry automatically                    | You must redrive messages from DLQ manually                                                                                                            |
| Lambda polling only works with SQS (not SNS alone) | SNS → SQS → Lambda for full decoupling   <br> This lets you buffer messages, handle retries, and avoid losing messages if Lambda is throttled or down. |

| Service Flow       | Invocation Type                | Notes                                       |
| ------------------ | ------------------------------ | ------------------------------------------- |
| SNS → Lambda       | Asynchronous Push              | Direct push, no polling                     |
| SQS → Lambda       | Polling (Event Source Mapping) | Lambda polls, supports batching and retries |
| SNS → SQS → Lambda | Combined                       | Decoupled, durable, reliable processing     |


### 🧪 Scenario-Based Questions (Think Like the Exam)
| Scenario                                                               | Likely Answer                          |
| ---------------------------------------------------------------------- | -------------------------------------- |
| Messages must be processed in order, no duplicates                     | FIFO Queue                             |
| Service needs to retry failed events without data loss                 | SQS with DLQ                           |
| Microservice produces a lot of data that needs asynchronous processing | Standard Queue                         |
| Use SNS to send to SQS and Lambda                                      | SNS → SQS (fan-out), then Lambda       |
| Lambda needs to process messages from queue                            | Lambda Event Source Mapping (SQS)      |
| Messages are being processed twice                                     | Check: delete + visibility timeout     |
| Queue is growing, but Lambda not scaling                               | Check batch size, timeout, concurrency |

### 📦 SQS + Lambda + DLQ Scenarios
| Scenario                                                                          | Partial Batch Response? | What Happens to Successful Messages?                                        | What Happens to Failed Messages?                                          | Which DLQ?                       |
| --------------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------- | -------------------------------- |
| **1. Batch fails (some messages fail)**                                           | ❌ No                    | **All messages stay in queue**, all `ReceiveCount++` (even successful ones) | All messages get same count. All can hit DLQ if count ≥ `maxReceiveCount` | **SQS DLQ**                      |
| **2. Batch fails (some messages fail)**                                           | ✅ Yes                   | Successful messages deleted immediately                                     | Only failed messages remain in queue, `ReceiveCount++` only for them      | **SQS DLQ** (per failed message) |
| **3. Message hits `maxReceiveCount`**                                             | Any                     | —                                                                           | Message moves to **SQS DLQ** automatically                                | **SQS DLQ**                      |
| **4. Lambda can’t be invoked at all** (bad permissions, throttling, service down) | Any                     | None are processed                                                          | Entire batch payload sent to **Lambda DLQ** after Lambda retry policy     | **Lambda DLQ**                   |
| **5. Lambda invoked but throws error for entire batch**                           | ❌ or ✅                  | —                                                                           | All messages stay in queue, `ReceiveCount++` for all                      | **SQS DLQ** if count exceeded    |
| **6. Successful processing**                                                      | Any                     | All messages deleted from queue                                             | —                                                                         | No DLQ used                      |

⚠️ Golden Rules to Remember for Exam:
- SQS DLQ = controlled by maxReceiveCount. Doesn’t care about Lambda internal success/fail, only about how many times the message was received without being deleted.
- Lambda DLQ = only for failed invocations. It’s about Lambda not even successfully processing (e.g., code error before processing, service down).
- Without Partial Batch Response: one bad apple spoils the whole batch — every message in that batch gets ReceiveCount++ and can be sent to DLQ together.
- With Partial Batch Response: only failed messages remain; successful ones are deleted immediately.
- Visibility Timeout just controls when the message reappears for retry — it doesn’t change DLQ logic.





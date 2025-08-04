### 📊 EventBridge vs SNS vs SQS – Comparison
| Feature / Use Case                   | 🟦 EventBridge                                   | 🟨 SNS (Simple Notification Service)             | 🟩 SQS (Simple Queue Service)                   |
| ------------------------------------ | ------------------------------------------------ | ------------------------------------------------ | ----------------------------------------------- |
| **Type**                             | Event Bus                                        | Pub/Sub messaging (push)                         | Message Queue (pull or Lambda)                  |
| **Fan-out supported?**               | ✅ Yes (multiple targets per rule)                | ✅ Yes (multiple subscribers)                     | ❌ No (1 consumer per message unless replicated) |
| **Push vs Pull**                     | Push                                             | Push                                             | Pull (or auto-trigger via Lambda)               |
| **Event filtering**                  | ✅ JSON pattern match                             | ✅ Filter policies on message attributes          | ❌ No filtering                                  |
| **Retention**                        | ❌ No (unless archive enabled)                    | ❌ No                                             | ✅ Yes (default 4 days, max 14)                  |
| **Message size limit**               | 256 KB                                           | 256 KB                                           | 256 KB                                          |
| **Supports DLQ (Dead Letter Queue)** | ❌ No native DLQ                                  | ❌ No (workaround via Lambda DLQ)                 | ✅ Yes                                           |
| **Message replay**                   | ✅ Yes (with archiving enabled)                   | ❌ No                                             | ❌ No (must re-send manually)                    |
| **Ordering guaranteed**              | ❌ No                                             | ❌ No                                             | ✅ With FIFO queues                              |
| **Exactly-once delivery**            | ❌ No                                             | ❌ No                                             | ✅ With FIFO queues                              |
| **Retries / Delay settings**         | ❌ No retry customization                         | ✅ Basic retry logic                              | ✅ Full retry/delay config                       |
| **Target types**                     | AWS services (Lambda, SQS, Step Functions, etc.) | HTTP(S), Email, Lambda, SQS                      | None (consumer pulls or is invoked)             |
| **Cross-account support**            | ✅ With permissions                               | ✅ With topic policy                              | ✅ With queue policy                             |
| **Schema registry**                  | ✅ Yes                                            | ❌ No                                             | ❌ No                                            |
| **Best for**                         | Event-driven microservices / SaaS / scheduling   | Fan-out notifications (SMS, email, Lambda, etc.) | Decoupling components with durable queues       |


### 🔑 When to Use Each
| Scenario                                                         | Best Service    | Reason                                                   |
| ---------------------------------------------------------------- | --------------- | -------------------------------------------------------- |
| Trigger Lambda every 5 mins                                      | **EventBridge** | Scheduled rule using `rate(5 minutes)`                   |
| Decouple microservices with retries and durability               | **SQS**         | Messages persist until processed, supports DLQ and retry |
| Send alerts via email/SMS/Lambda                                 | **SNS**         | Pub/Sub for notifications                                |
| Build event-driven app with multiple services reacting to events | **EventBridge** | JSON pattern filtering + multiple targets                |
| Want message ordering and deduplication                          | **SQS FIFO**    | FIFO queues offer exactly-once and ordering              |
| Integrate external SaaS apps (like Auth0, Zendesk) into AWS      | **EventBridge** | Partner event buses                                      |
| Need to store events for audit/debug later                       | **EventBridge** | Archive + replay functionality                           |


### 📌 Memory Tips
- EventBridge = "Event Router" (pattern-based, replay, serverless microservices)
- SNS = "Push Notifications" (fan-out to Lambda/email/SMS/SQS)
- SQS = "Pull Queue" (durable, retries, FIFO/LIFO, dead letter queue)


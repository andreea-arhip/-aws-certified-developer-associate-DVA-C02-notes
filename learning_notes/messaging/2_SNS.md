### 🧠 What is SNS?
Simple Notification Service (SNS) is a fully managed, serverless, push-based pub/sub messaging service.

It allows publishers to send messages to multiple subscribers asynchronously.

### 📌 Core Concepts
| Concept             | Details                                                                    |
| ------------------- | -------------------------------------------------------------------------- |
| **Topic**           | A logical access point for messages. Publishers send messages here.        |
| **Publisher**       | Pushes a message to a topic.                                               |
| **Subscriber**      | Receives messages sent to a topic. Can be Lambda, SQS, HTTP/S, email, etc. |
| **Pub/Sub**         | Publishers don’t know who the subscribers are — decoupled architecture.    |
| **Fan-out pattern** | One message to SNS can be delivered to multiple subscribers.               |


### 📦 Message Delivery
| Property               | Details                                                                 |
| ---------------------- | ----------------------------------------------------------------------- |
| **Delivery Type**      | Push-based                                                              |
| **Retries**            | Enabled (e.g., for HTTP/S endpoint, exponential backoff, DLQ supported) |
| **Message Size Limit** | Max 256 KB                                                              |
| **Durability**         | Messages are not persisted (ephemeral)                                  |
| **Security**           | IAM policies, access control via topic policy, encryption via SSE       |

### 🧩 Supported Protocols
| Protocol        | Notes                                       |
| --------------- | ------------------------------------------- |
| **Lambda**      | Auto-invokes function with payload          |
| **SQS**         | Push message to a queue                     |
| **HTTP/S**      | Push to webhook, with retry logic           |
| **Email**       | Sends plain text message                    |
| **SMS**         | Limited throughput; expensive               |
| **Mobile Push** | Via platform applications (e.g., APNs, FCM) |

### 🎯 Filtering Messages
SNS allows subscription filters using message attributes, so different subscribers only get messages matching certain attributes.

✅ Useful for targeting specific receivers

🔒 Reduces unnecessary traffic

### 🛡️ Security & Access
- IAM policies: Control which users/services can publish or subscribe 
- Topic Policies: Control access to the topic
- Encryption:
  - In-transit: HTTPS 
  - At-rest: SSE using KMS (managed or customer keys)

### 💡 Common Use Cases
| Use Case                        | Why SNS?                                       |
| ------------------------------- | ---------------------------------------------- |
| Alerting from CloudWatch Alarms | Push notifications to email, SMS, Lambda, etc. |
| Fan-out to multiple systems     | Push same message to SQS, Lambda, and email    |
| Mobile notifications            | Push to apps via FCM, APNs                     |
| Serverless workflows            | Trigger Lambda without polling                 |
| Application health events       | Real-time notifications                        |
| Messaging across microservices  | Easily decouples producers from consumers      |

### 🔗 Integrations with Other AWS Services
| Service                    | Role in Integration                         |
| -------------------------- | ------------------------------------------- |
| **Lambda**                 | SNS can trigger Lambda functions directly   |
| **SQS**                    | SNS can fan-out messages to multiple queues |
| **EventBridge**            | Can send SNS messages as a target           |
| **CloudWatch Alarms**      | Sends alerts via SNS                        |
| **Kinesis Firehose**       | SNS is not a source (use Kinesis instead)   |
| **Mobile Push (APNs/FCM)** | SNS acts as delivery manager                |


### ⚠️ Exam Tips & Tricky Questions
| Tip or Trap                                    | Explanation / What to Do                           |
| ---------------------------------------------- | -------------------------------------------------- |
| **SNS ≠ durable storage**                      | No persistence — it's not a queue.                 |
| **No pulling**                                 | SNS is push-based only                             |
| **SQS integration = pull model for consumers** | Use SQS if you want delayed/reliable delivery      |
| **Message filtering**                          | Be ready for questions using message attributes    |
| **Mobile push = SNS**                          | Always true — even if not explicitly stated        |
| **Fan-out pattern = SNS + SQS or Lambda**      | Classic integration model                          |
| **Direct subscriber failures**                 | SNS retries with exponential backoff (esp. HTTP/S) |
| **FIFO support**                               | Limited regions — prefer SQS FIFO for ordering     |
| **DLQ support only for certain endpoints**     | HTTP/S subscribers only                            |


### ⚙️ Configuration Properties
| Setting                   | Notes                                                     |
| ------------------------- | --------------------------------------------------------- |
| **Delivery Retry Policy** | Customize for HTTP/S subscribers (max attempts, interval) |
| **Access Control**        | Topic policy + IAM                                        |
| **Encryption at Rest**    | SSE using AWS KMS                                         |
| **Message Attributes**    | Key-value pairs for filtering                             |
| **Delivery Logs**         | Use CloudWatch Logs for troubleshooting                   |

### ✅ Filtering:
- Filter policies only apply to message attributes, not body.
- Use attributes for routing, e.g., "customerType": "premium".
- Message body = the actual message (e.g., "Order shipped").
```
"Message": "Order complete",
"MessageAttributes": {
  "priority": { "DataType": "String", "StringValue": "high" }
}
```
- Body: "Order complete" → What the message says.
- Attributes: "priority": "high" → Only this is used for filtering.

### 🧠 Memorization Tricks
- “Push = SNS, Pull = SQS” 
- “Fan-out = SNS + multiple SQS or Lambda” 
- “Message Filtering = SNS + message attributes” 
- “Mobile push notifications” = SNS (always)
- “Real-time alerts / notifications” = SNS
- "Decoupled event flow = SNS + SQS or Lambda"

### 🧪 Scenario-Based Practice (Think Like the Exam)
| Scenario Description                                                         | Likely Answer          |
| ---------------------------------------------------------------------------- | ---------------------- |
| You need to notify an email group, Lambda, and SQS when a new user registers | SNS fan-out            |
| You want to trigger Lambda on specific message types                         | SNS + filter policy    |
| Mobile push to app (FCM or APNs)                                             | SNS mobile push        |
| CloudWatch alarm triggers an action                                          | SNS                    |
| Retry logic with exponential backoff on webhook failure                      | SNS HTTP/S             |
| Filtering delivery by department                                             | SNS message attributes |
| You need guaranteed message persistence                                      | **Not SNS** → use SQS  |



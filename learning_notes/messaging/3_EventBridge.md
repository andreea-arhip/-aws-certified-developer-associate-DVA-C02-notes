### 🧠 What is EventBridge?
Amazon EventBridge is a serverless event bus that enables event-driven architecture by connecting services using events.

It routes JSON-formatted events from AWS services, your own apps, or SaaS apps to targets like Lambda, SQS, Step Functions, etc.

### 🚍 Core Concept: Event Bus
| Event Bus Type  | Use Case                                 |
| --------------- | ---------------------------------------- |
| **Default Bus** | Receives events from AWS services        |
| **Custom Bus**  | Receives events from custom applications |
| **Partner Bus** | Receives events from supported SaaS apps |

### 💡 How It Works (High-Level Flow)
Event Source → Event Bus → Event Rule → Target 
- Source: AWS service, app, SaaS 
- Bus: Routes events (default, custom, partner)
- Rule: Pattern match filters events 
- Target: Event is forwarded to 1+ AWS services

### 🧩 Supported Targets (not exhaustive)
- AWS Lambda 
- Step Functions 
- SQS 
- SNS 
- Kinesis Data Streams 
- ECS Tasks 
- EC2 (RunCommand)
- CodeBuild 
- API Gateway 
- EventBridge Pipes

### 🧠 Key Features
| Feature                 | Description                                                            |
| ----------------------- | ---------------------------------------------------------------------- |
| **Event filtering**     | Match JSON content via event pattern (not content-based like SQS FIFO) |
| **Event archiving**     | Retain event history (with replay)                                     |
| **Event replay**        | Reprocess historical events for debugging or recovery                  |
| **Schema Registry**     | Automatically discovers event schemas                                  |
| **Cross-account buses** | Send events across AWS accounts (via permissions)                      |
| **Pipes** (NEW)         | Connect sources to targets with optional filtering/transformation      |

### 🧾 Event Format
```
{
  "source": "aws.ec2",
  "detail-type": "EC2 Instance State-change Notification",
  "detail": {
    "instance-id": "i-1234567890abcdef0",
    "state": "running"
  }
}
```

### ⚙️ Event Rule Configuration
| Component    | Description                                     |
| ------------ | ----------------------------------------------- |
| **Pattern**  | Filter based on fields like source, detail-type |
| **Schedule** | Cron or rate-based (every X minutes/hours)      |
| **Target**   | One or more services triggered                  |

### Filtering
| 🔹 **Concept**                                        | 🔍 **Details**                                                                    |
| ----------------------------------------------------- | --------------------------------------------------------------------------------- |
| **Filtering happens at**                              | **Rule level** (in EventBridge, rules determine what events to match)             |
| **Filter logic based on**                             | **Event JSON content** (not just attributes like in SNS)                          |
| **Common fields to filter**                           | `source`, `detail-type`, `detail`, `account`, `region`                            |
| **Filtering uses**                                    | **Exact match**, **prefix**, **anything-but**, **numeric comparison**, **exists** |
| **Events must match all** filter conditions in a rule | ✅ (AND logic)                                                                     |

#### Example of filtering rule:
```
{
  "source": ["my.app"],
  "detail": {
    "price": [{ "numeric": [">", 100] }]
  }
}
```
➡ Matches events from my.app where price > 100.

### 🔒 Security
| Mechanism             | Purpose                                     |
| --------------------- | ------------------------------------------- |
| **IAM policies**      | Who can put events to a bus                 |
| **Resource policies** | Allow cross-account events                  |
| **KMS** (for targets) | Encrypt messages passed to targets like SQS |

### 📖 Common Use Cases
| Use Case                           | Notes                                                             |
| ---------------------------------- | ----------------------------------------------------------------- |
| Trigger Lambda on EC2 state change | Use `source = aws.ec2`, `detail-type = EC2 Instance State-change` |
| Scheduled events                   | `rate(1 hour)` or cron syntax                                     |
| Decouple microservices             | Producer app emits event → multiple consumers                     |
| Audit and replay events            | Enable event archiving and replay                                 |
| SaaS integration                   | Connect apps like Zendesk, Auth0 via partner event buses          |


### ⚠️ Tricky Questions & Exam Tips
| Trap / Gotcha                                        | Reminder                                                      |
| ---------------------------------------------------- |---------------------------------------------------------------|
| EventBridge **does not store events** by default     | Must enable archiving if you want retention                   |
| Filtering is **pattern-based** (not message content) | JSON pattern must match structure (even nesting fields)       |
| Events must be **JSON**                              | Binary payloads are not allowed                               |
| Cross-account?                                       | Must configure **resource policy** on target event bus        |
| Delay/Retry logic                                    | EventBridge **does not support retry config** like SQS/Lambda |
| Pipes = Source → Filter/Transform → Target           | EventBridge Pipes is newer alternative to writing custom code |


### 🔁 EventBridge vs SNS vs SQS (Quick Comparison)
| Feature/Need        | EventBridge        | SNS                        | SQS                  |
| ------------------- | ------------------ | -------------------------- | -------------------- |
| Event filtering     | JSON pattern       | Subscription filter policy | N/A                  |
| Event storage       | Optional (archive) | No                         | Yes                  |
| Push vs Pull        | Push               | Push                       | Pull                 |
| Fan-out             | Yes                | Yes                        | No                   |
| Order guarantee     | No                 | No                         | FIFO queue available |
| Retry/delay control | No built-in        | Some retry                 | Full control         |


### 📌 Memory Tricks
- “Bridge connects everything.” 
- “Pattern-based filtering” = EventBridge, not SQS. 
- “Event replay?” → Only EventBridge, using archive!
- “Scheduled Lambda?” → Use EventBridge rule (rate/cron). 
- “From app to Lambda via event” → EventBridge (custom bus)
- “SaaS trigger?” → EventBridge Partner Event Bus

### 🧪 Scenario-Based Tips (Like in the Exam)
| Scenario                                                        | Likely Answer                           |
| --------------------------------------------------------------- | --------------------------------------- |
| Trigger Lambda every 5 minutes                                  | EventBridge rule with `rate(5 minutes)` |
| EC2 instance changes state → notify team                        | EventBridge rule + SNS or Lambda        |
| Receive Auth0 login events into AWS                             | Use EventBridge Partner Event Bus       |
| Replay past events for testing                                  | Enable Event Archive and Replay         |
| Microservice emits events → multiple other services act on them | EventBridge fan-out to multiple targets |
| Developer needs schema for event structure                      | Use Schema Registry                     |
| Lambda should react to custom event payload                     | Custom event sent to Custom Bus         |


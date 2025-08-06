### 🚀 PURPOSE
Amazon DynamoDB is a fully managed, serverless, NoSQL key-value & document database that delivers:
- Single-digit millisecond latency
- Horizontal scaling
- Built-in high availability and replication

### ⚙️ TABLE BASICS
| Concept                | Description                                        |
| ---------------------- | -------------------------------------------------- |
| **Table**              | Collection of items (like a table in RDBMS)        |
| **Item**               | A single record (like a row)                       |
| **Attribute**          | A field (like a column)                            |
| **Primary Key**        | Required; uniquely identifies each item            |
| **Partition Key (PK)** | Determines partition where item is stored          |
| **Sort Key (SK)**      | Optional; enables range queries within a partition |
| **RCU / WCU**          | Read/Write Capacity Units (provisioned mode)       |

🧠 Exam Tip: Partition key = hash key, Sort key = range key

### 🧠 MEMORY TIPS & EXAM SCENARIOS
| If the exam says…                     | Think of…                              |
| ------------------------------------- | -------------------------------------- |
| “NoSQL”, “scalable”, “serverless”     | DynamoDB                               |
| “millisecond performance”             | DynamoDB                               |
| “millions of TPS” or “burst workload” | DynamoDB On-Demand or DAX              |
| “mobile backend”, “IoT”, “gaming”     | DynamoDB                               |
| “atomic counter”, “increment counter” | `UpdateItem` with `ADD` action         |
| “real-time leaderboards”              | Use **Sort Key** + `Query` with `DESC` |
| “event-driven” or “change capture”    | DynamoDB Streams + Lambda              |

### 🔐 SECURITY
✅ Integrated with IAM for fine-grained access control (via IAM policies)

✅ Encryption at rest with AWS KMS (enabled by default)

✅ VPC Endpoint (Gateway) for private access

✅ Supports resource policies for cross-account access

### ⚡ READ & WRITE MODES
| Mode            | Description                                                            |
| --------------- | ---------------------------------------------------------------------- |
| **Provisioned** | You define RCU/WCU manually (cost-efficient for predictable workloads) |
| **On-Demand**   | Pay-per-request (best for unpredictable workloads)                     |
🧠 Exam Tip: Use On-Demand when traffic is unknown or spiky.

### ✅ 1. CAPACITY UNIT DEFINITIONS
RCU (Read Capacity Unit):
- 1RCU = 1 strongly consistent read/sec for item up to 4 KB  --> ROUNDED_UP_SIZE / 4 * 1
- 1RCU = 2 eventually consistent reads/sec for item up to 4 KB --> ROUNDED_UP_SIZE / 4 * 0.5
- 2RCU = 1 transactional read --> ROUNDED_UP_SIZE / 4 * 2

WCU (Write Capacity Unit):
- 1WCU = 1 write/sec for item up to 1 KB --> ROUNDED_UP_SIZE / 1 * 1
- 2WCU = 1 transactional write --> ROUNDED_UP_SIZE / 1 * 2

🧠 DynamoDB rounds up:
- 6 KB read = 2 RCUs (8KB / 4KB)
- 1.5 KB write = 2 WCUs (2 KB / 1KB)

🧠 Writes are more expensive than reads.

| Operation                            | Size   | Consistency           | Capacity Units |
| ------------------------------------ | ------ | --------------------- | -------------- |
| Read 6 KB item (strong)              | 6 KB   | Strongly Consistent   | 2 RCUs         |
| Read 6 KB item (eventual)            | 6 KB   | Eventually Consistent | 1 RCU          |
| Write 0.5 KB item                    | 0.5 KB | N/A                   | 1 WCU          |
| Write 1.5 KB item                    | 1.5 KB | N/A                   | 2 WCUs         |
| Transactional write of 1 KB item     | 1 KB   | Transactional         | 2 WCUs         |
| 100 reads/sec of 4 KB items (strong) | 4 KB   | Strongly Consistent   | 100 RCUs       |

### 🔄 AUTOSCALING
- Target utilization: Defaults to 70% 
- Automatically adjusts RCUs/WCUs based on traffic 
- Must set min and max capacity bounds 
- Useful for fluctuating workloads 
- Can use CloudWatch alarms for scale-in/scale-out

🧠 If you see "unpredictable traffic", think: DynamoDB Autoscaling

### ⚡ BURST CAPACITY
- Available for short bursts in Provisioned mode 
- Unused throughput from previous minutes is stored in burst credits 
- Only applies to partition-level, not table-wide 
- Once burst credits are depleted, throttling occurs

🧠 If performance suddenly drops, check for partition burst limit reached



### 🔁 READ CONSISTENCY
| Type                      | Description                                |
| ------------------------- | ------------------------------------------ |
| **Eventually Consistent** | Default, low latency, eventual propagation |
| **Strongly Consistent**   | Optional, always returns latest data       |


### 📥 DATA ACCESS
| API              | Use For                                         |
| ---------------- | ----------------------------------------------- |
| `PutItem`        | Create or overwrite item                        |
| `UpdateItem`     | Update an existing item (atomic counters, etc.) |
| `DeleteItem`     | Delete an item                                  |
| `GetItem`        | Fetch a **single** item by primary key          |
| `Query`          | Fetch **multiple** items **by partition key**   |
| `Scan`           | Reads entire table (slow, costly, paginated)    |
| `BatchGetItem`   | Get multiple items (max 100)                    |
| `BatchWriteItem` | Put/delete multiple items (max 25)              |

🧠 Query = efficient (indexed), Scan = inefficient (full table)

### 🧪 FILTERING
- Use FilterExpression to filter after Query or Scan 
- Does not reduce RCU usage

### 🔧 INDEXES
| Index Type       | Use Case                                        |
| ---------------- | ----------------------------------------------- |
| **LSI (Local)**  | Same partition key, different sort key          |
| **GSI (Global)** | Different partition + sort keys (most flexible) |

#### Global Secondary Index (GSI) vs Local Secondary Index (LSI)

| Feature                       | GSI                      | LSI                                  |
| ----------------------------- | ------------------------ | ------------------------------------ |
| Alternate partition key       | Yes                      | No (shares base table partition key) |
| Alternate sort key            | Yes                      | Yes                                  |
| Provisioned capacity          | Separate from base table | Shared with base table               |
| Max per table                 | 20                       | 5                                    |
| Can add after table creation? | Yes                      | No (must define at table creation)   |


### 🧩 TRANSACTIONS
- Supports ACID transactions with TransactWriteItems and TransactGetItems 
- Up to 25 items or 4 MB per transaction
- Consume 2× read/write capacity units.
- Useful for financial, inventory, or atomic updates.

🧠 Use for multi-table/multi-item operations

### 🔄 STREAMS + LAMBDA (Event-Driven Design)
- Enable DynamoDB Streams to capture changes (insert, update, delete)
- Use Lambda trigger for near real-time processing
- Retention of 24h

🧠 Useful for event sourcing, analytics, replication

### 🔥 DAX (DynamoDB Accelerator)
| Feature         | Description                                      |
| --------------- | ------------------------------------------------ |
| In-memory cache | Managed cache for DynamoDB (microsecond latency) |
| Fully managed   | No code changes required                         |
| TTL-based       | Consistent read cache                            |

🧠 Use DAX if you see: "microsecond latency", "reduce read load"

### 🧹 TTL (Time to Live)
- Automatically delete expired items 
- Define TTL attribute with epoch time 
- Background job (not real-time) - takes up to 48h

🧠 Great for expiring sessions, logs, temp data

### 📊 MONITORING & BACKUPS
- CloudWatch metrics available (RCU, WCU, latency, errors)
- Point-in-time recovery (PITR): continuous backup up to 35 days
- On-demand backup/restore supported

### 🧱 PARTITIONING LOGIC
- Partition key determines how data is distributed
- Poor partitioning → hot partitions → throttling 
- Use high-cardinality keys for good distribution

🧠 Add randomness (e.g., userId#timestamp) to partition key if needed

### 🧩 Best Practices
- Avoid Scan in production; use Query instead. 
- Use ProjectionExpression to reduce RCU usage. 
- Use BatchGetItem/BatchWriteItem for bulk ops. 
- Implement exponential backoff on throttling errors.

### 🚨 COMMON EXAM TRAPS
| Trap / Confusion                         | Correct Answer / Reminder                             |
| ---------------------------------------- | ----------------------------------------------------- |
| Trying to query by an attribute only     | Only possible with GSI (not a key = no query)         |
| Need different sort key → after creation | Use GSI (LSI only at table creation)                  |
| Need unique value enforcement            | Not supported — enforce manually in app logic         |
| Need multi-item atomic write             | Use `TransactWriteItems`                              |
| Want TTL to delete immediately           | TTL is **eventual** (can take up to 48h)              |
| “Strongly consistent” + global           | DynamoDB Global Tables support strong consistency now |

### 📦 GLOBAL TABLES
- Multi-region replication 
- Eventually consistent by default 
- Use for disaster recovery, multi-region latency

🧠 Think “global active-active database”

### 📌 QUICK CONFIG CHECKLIST
✅ Proper key design (PK and SK as needed)?

✅ Read/write mode selected appropriately?

✅ Use GSI for alternate access patterns?

✅ Streams enabled for event-driven processing?

✅ TTL set for temp/expiring data?



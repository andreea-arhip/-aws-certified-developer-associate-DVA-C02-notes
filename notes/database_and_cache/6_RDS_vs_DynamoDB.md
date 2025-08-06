### ✅ When to Use Aurora / RDS (Relational Databases)
Use Case: You need structured, relational data with ACID transactions, joins, foreign keys, and SQL querying.

#### 🧠 Keywords/Scenarios:
- “Relational database”
- “Joins”, “foreign keys”, “normalized data”
- “SQL”, “PostgreSQL”, “MySQL”
- “Complex queries”, “multi-table relationships”
- “Reporting / analytics on relational data”
- “ACID compliance”

#### 📌 Choose RDS (non-Aurora) when:
- You need standard relational DB but not high performance.
- You’re using legacy applications that depend on specific engines (e.g., Oracle, SQL Server).
- You're okay with manual scaling, and performance is not critical.

#### 📌 Choose Aurora when:
- You need high performance and availability, but still relational.
- You want to scale read replicas easily.
- You want better performance than RDS, up to 5x MySQL.
- You want failover in <30 seconds.
- You want serverless for cost savings on intermittent workloads.
- You want multi-AZ and cross-region replication.

----

### ✅ When to Use DynamoDB (NoSQL)
Use Case: You need ultra-fast, highly scalable, low-latency key-value or document storage.

#### 🧠 Keywords/Scenarios:
- “Serverless”
- “Massive scale”, “high traffic”
- “Low latency”, “single-digit ms response time”
- “Key-value”, “document store”
- “NoSQL”
- “High throughput, unpredictable traffic”
- “IoT, gaming, mobile apps, session state”
- “Autoscaling read/write capacity”
- “TTL (Time to Live)”
- “Streams for change data capture”
- “Global tables” for multi-region writes

#### 📌 Choose DynamoDB when:
- You need millisecond latency at any scale.
- Your access patterns are predictable and simple (key-based).
- You want fully managed serverless.
- You want on-demand scaling or provisioned throughput.
- You need multi-region active-active setup.
- You want integrations with Lambda, EventBridge, Streams.
- You want cost-effective storage for large-scale apps.

---

### 🔁 Summary Comparison Table

| Feature                    | Aurora / RDS               | DynamoDB                          |
| -------------------------- | -------------------------- | --------------------------------- |
| Type                       | Relational (SQL)           | NoSQL (Key-Value / Document)      |
| Query Language             | SQL                        | PartiQL / Key-Value APIs          |
| Schema                     | Fixed schema               | Flexible schema                   |
| Scalability                | Vertical + Read Replicas   | Horizontal, seamless              |
| Performance                | High (Aurora)              | Ultra-low latency                 |
| Transactions               | Full ACID                  | ACID (with limitations)           |
| Joins                      | Yes                        | No                                |
| Use Cases                  | Complex data relationships | Simple queries, massive scale     |
| Serverless option          | Aurora Serverless v2       | DynamoDB is inherently serverless |
| Cost Model                 | Per hour + storage         | On-demand or provisioned R/W CU   |
| Multi-region active-active | Aurora Global Databases    | DynamoDB Global Tables            |
| TTL                        | ❌                          | ✅                                 |
| Streams (CDC)              | Aurora CDC → DMS           | ✅ DynamoDB Streams                |

---

### ❗Exam Trap Tips:
- If the question mentions Joins, always pick Aurora/RDS, not DynamoDB.
- If it says NoSQL, high throughput, low latency, think DynamoDB.
- If it mentions legacy DB engines (Oracle, SQL Server) → RDS.
- If the solution must be serverless and scale automatically → DynamoDB or Aurora Serverless depending on structure.



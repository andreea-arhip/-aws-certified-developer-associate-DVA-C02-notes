## 🧠 Comparison & Scenarios: RDS vs Aurora vs ElastiCache

| Feature / Property    | **RDS**                                 | **Aurora**                                                | **ElastiCache (Redis/Memcached)**                    |
| --------------------- | --------------------------------------- | --------------------------------------------------------- | ---------------------------------------------------- |
| **DB Type**           | Managed traditional RDBMS               | Cloud-native distributed DB (MySQL/PostgreSQL compatible) | In-memory key-value store                            |
| **Use Case**          | General-purpose relational DB           | High performance/scalability, low-latency reads           | Caching, session storage, pub/sub                    |
| **High Availability** | Multi-AZ standby (failover \~60s)       | Auto failover between reader nodes (<30s)                 | Redis supports replicas + failover                   |
| **Replication**       | Read Replicas (eventual consistency)    | Reader nodes + Global DB (low-latency global)             | Redis replica(s), Memcached doesn't support it       |
| **Scalability**       | Vertical (instance size), Read Replicas | Horizontal (up to 15 readers)                             | Horizontal sharding (manual in Redis)                |
| **Storage**           | EBS volumes (limited scaling)           | Auto-scaling to 128 TB                                    | RAM only (volatile unless Redis persistence enabled) |
| **Performance**       | Good                                    | Better (5x MySQL, 3x PostgreSQL)                          | Fastest (microsecond latency)                        |
| **Pricing**           | Pay per instance + storage              | Higher cost than RDS                                      | Low-cost, memory-based                               |
| **Persistence**       | Native DB engine                        | Built-in + Backtrack (MySQL only)                         | Only Redis supports snapshots/AOF                    |
| **Failover Type**     | Multi-AZ standby promotion              | Aurora Reader promotion                                   | Redis replica promotion                              |
| **IAM Auth**          | Supported                               | Supported                                                 | ❌ Not supported                                      |
| **VPC Access**        | Required                                | Required                                                  | Required                                             |
| **Global Options**    | Manual read replicas in other regions   | Aurora Global DB (1 primary, up to 5 secondaries)         | No built-in global replication                       |


### ✅ Must-Know Exam Scenarios

🔁 “The application needs to scale reads to thousands of clients”
- ✅ Aurora with Reader Endpoint 
- ✅ RDS with Read Replicas 
- ❌ Multi-AZ alone does not scale reads

---

🔁 “High performance, low-latency in-memory data access”
- ✅ ElastiCache (Redis for durability, Memcached for simplicity)

---

💥 “App requires automatic failover with minimal downtime”
- ✅ Aurora (auto-promotion of reader)
- ✅ RDS Multi-AZ (standby promotion)
- ✅ ElastiCache Redis with replica

---

🛡 “Secure DB with IAM authentication”
- ✅ Aurora and RDS support IAM-based DB logins 
- ❌ ElastiCache does not

---

🌍 “Deploy globally with low-latency read access”
- ✅ Aurora Global DB (replicates in <1s)
- ✅ RDS Read Replica in another region (manual)

--- 

🧪 “Rollback mistakes without restoring backup”
- ✅ Aurora Backtrack (MySQL only)

---

📈 “Application has spiky usage patterns, unpredictable load”
- ✅ Aurora Serverless v2 
- ❌ Not available for RDS

---

## ⚠️ Common Traps to Avoid
| Trap                                                   | Correction                                                |
| ------------------------------------------------------ | --------------------------------------------------------- |
| ❌ Multi-AZ provides read scalability                   | ✅ It provides HA only, not scaling                        |
| ❌ Snapshots are automatically cross-region             | ✅ Must **manually copy** snapshots between regions        |
| ❌ Read Replicas are automatically promoted on failure  | ✅ Only Multi-AZ or Aurora handle automatic failover       |
| ❌ ElastiCache can be accessed from the public internet | ✅ Must be in same VPC as client                           |
| ❌ Aurora = fully serverless                            | ✅ Only **Aurora Serverless v2** supports granular scaling |
| ❌ ElastiCache supports SQL-like querying               | ✅ It’s a key-value store, no SQL support                  |


## 🧠 Comparison & Scenarios: RDS vs Aurora
| Feature / Property       | **RDS**                                                                                               | **Aurora**                                                                                              |
| ------------------------ | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **DB Type**              | Managed traditional RDBMS                                                                             | Cloud-native distributed DB (MySQL/Postgres compatible)                                                 |
| **Use Case**             | General-purpose relational DB                                                                         | High performance + low-latency reads + scaling                                                          |
| **High Availability**    | Multi-AZ standby (failover \~60s)                                                                     | Auto failover → reader promoted to writer (<30s)                                                        |
| **Replication**          | Read Replicas (async, eventual consistency, cross-region possible)                                    | Built-in replication across 3 AZs + Global DB (low-latency global)                                      |
| **Scalability**          | Vertical (bigger instance) + Read Replicas                                                            | Horizontal (up to 15 readers, 1 writer)                                                                 |
| **Storage**              | EBS volumes (size scaling limited)                                                                    | Auto-scaling to 128 TB                                                                                  |
| **Performance**          | Good                                                                                                  | 5× MySQL, 3× Postgres                                                                                   |
| **Pricing**              | Cheaper 💰                                                                                            | More expensive 💰💰                                                                                     |
| **Persistence / Backup** | Snapshots + PITR                                                                                      | Snapshots + PITR + Backtrack (rewind, MySQL only)                                                       |
| **Failover Type**        | Multi-AZ standby promotion                                                                            | Reader promotion (auto)                                                                                 |
| **IAM Auth**             | ✅ Supported                                                                                           | ✅ Supported                                                                                             |
| **VPC Access**           | ✅ Required                                                                                            | ✅ Required                                                                                              |
| **Global Options**       | Manual read replicas in other regions                                                                 | Aurora Global DB (1 primary, up to 5 secondaries)                                                       |
| **Traps**                | ❌ Cannot promote Multi-AZ standby <br> ❌ Read Replicas ≠ backups <br> ❌ Snapshots are region-specific | ❌ No manual standby (cluster auto-handles) <br> ❌ Backtrack ≠ PITR <br> ❌ Only MySQL/Postgres supported |

#### High Availability
- ❓ App needs automatic failover if DB crashes.
  - → RDS Multi-AZ or Aurora cluster (NOT Read Replicas).

#### Scaling
- ❓ App is read-heavy (lots of SELECTs).
  - → RDS Read Replicas or Aurora Reader endpoint.


- ❓ App is write-heavy.
  - → Only Aurora writer node can accept writes (NOT read replicas).

#### Backups
- ❓ Company says: “We already have Read Replicas, so we don’t need backups.”
  - → WRONG ❌. Read Replicas ≠ backups. Snapshots needed.

#### Failover
- ❓ RDS Multi-AZ fails. Can I promote the standby as a new independent DB?
  - → ❌ No. Standby is only for HA failover.


- ❓ Aurora writer fails. What happens?
  - → ✅ Aurora auto-promotes a reader to be the new writer (<30s).

#### Global
- ❓ Need fast global reads with low latency.
  - → ✅ Aurora Global DB.
  - → ❌ RDS Global = only cross-region Read Replicas (slower).

#### Restores
- ❓ Can I copy snapshots across regions?
  - → ✅ Yes, but manually. Snapshots are region-specific by default.

#### Extra Aurora Trap
- ❓ What’s Backtrack?
  - → Time rewind for Aurora MySQL only. NOT the same as PITR.
  
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


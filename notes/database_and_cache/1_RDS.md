### ✅ Core Concepts
- Fully managed Relational DB service (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server)
- Supports Multi-AZ for HA, and Read Replicas for scaling (except SQL Server)
- Automated backups + manual snapshots 
- Point-in-time recovery (within retention period)
- No need to SSH into EC2 or manage OS/patches 
- IAM authentication supported 
- RDS Proxy for connection pooling and better scalability

### 🧠 Exam Tips & Traps
✅ Multi-AZ = HA, automatic failover, no scaling

✅ Read Replica = scaling, async replication, no failover

❌ Cannot promote a Multi-AZ standby

❌ Read Replicas are not backups

❌ Snapshots are region-specific unless copied

### 📌 Keywords & Scenario Mapping
| Keyword/Scenario                 | Think of                                     |
| -------------------------------- | -------------------------------------------- |
| “Failover”                       | ✅ Multi-AZ                                   |
| “Read-heavy workload”            | ✅ Read Replicas                              |
| “Instant recovery”               | ✅ Point-in-time recovery (automated backups) |
| “IAM-based DB access”            | ✅ IAM Authentication                         |
| “Many concurrent DB connections” | ✅ RDS Proxy                                  |
| “Encrypt at rest”                | ✅ KMS (enabled at creation time)             |
| “SQL Server license”             | ✅ License Included or BYOL options           |

### 💡 Integrations & Features
- Lambda / API Gateway: Can connect to RDS via RDS Proxy 
- CloudWatch: For metrics, alarms 
- SNS: For event notifications (e.g., backup completion)
- CloudTrail: Logs API activity

### 🧮 Defaults / Limits
- Max read replicas: 5 (can vary by engine)
- Backups retained: Default 7 days (up to 35)
- Port: Default is engine-specific (e.g., 3306 for MySQL)
- Multi-AZ failover time: Usually <1 minute


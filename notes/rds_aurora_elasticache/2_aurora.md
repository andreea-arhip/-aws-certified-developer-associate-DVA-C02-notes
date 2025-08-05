### ✅ Core Concepts
- Proprietary AWS engine (MySQL- and PostgreSQL-compatible)
- Cluster-based architecture:
  - 1 Writer 
  - Up to 15 Reader nodes 
  - 6 copies of data across 3 AZs (distributed storage layer)
- Supports Aurora Serverless v2 (on-demand auto-scaling)
- Backtrack: Rewind DB up to 72 hours (only Aurora MySQL)
- Supports Global Database (1 primary + 5 secondary regions)

### 🧠 Exam Tips & Traps
✅ Aurora automatically spreads data across AZs

✅ Reader endpoint = load-balanced read replicas

✅ Failover is automatic to a reader if writer fails

❌ No manual standby setup — cluster handles HA natively

❌ Backtrack ≠ Point-in-time recovery

### 📌 Keywords & Scenario Mapping
| Keyword/Scenario                    | Think of                 |
| ----------------------------------- | ------------------------ |
| “Millisecond failover”              | ✅ Aurora                 |
| “Instant scaling”                   | ✅ Aurora Serverless v2   |
| “Multiple readers”                  | ✅ Aurora Reader Endpoint |
| “Global multi-region DB”            | ✅ Aurora Global DB       |
| “Rollback without restoring backup” | ✅ Aurora Backtrack       |


### 💡 Integrations & Features
- RDS Proxy: Supported 
- IAM Authentication: Supported 
- Global Databases: Useful for low-latency cross-region apps 
- Monitoring: Enhanced Monitoring + CloudWatch

### 🧮 Defaults / Limits
- Max readers: 15 
- Storage: Auto-scales up to 128 TB 
- Backtrack: Up to 72 hours 
- Global DB lag: <1 second



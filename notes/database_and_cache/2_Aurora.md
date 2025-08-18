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
- Storage is automatically HA and replicated across AZs.
- You don’t set up Multi-AZ manually — Aurora does it by design.

✅ Reader endpoint = load-balanced read replicas
- No need to manage load balancing yourself.
- All reads go to the reader endpoint → evenly spread.

✅ Failover is automatic to a reader if writer fails
- Aurora does automatic failover between writer & readers.
- Much faster than RDS Multi-AZ failover.

❌ No manual standby setup
- Aurora = built-in HA.
- You don’t think about “standby vs active” → Aurora cluster takes care of it.

❌ Backtrack ≠ Point-in-time recovery
- Backtrack = a “rewind button” → jump back a few minutes/hours, without restoring a snapshot.
- Point-in-time recovery = full restore from logs / snapshots → creates a new DB instance.

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



### ✅ Core Concepts
- Fully managed in-memory data store 
- Engines: Redis (advanced features), Memcached (simple)
- Used for caching, session storage, leaderboards, etc. 
- Redis supports:
  - Persistence (AOF, snapshots)
  - Replication + automatic failover 
  - Pub/Sub, Streams
- Memcached:
  - Multi-threaded 
  - No persistence or failover

### 🧠 Exam Tips & Traps
✅ Redis = rich features, persistence, HA

✅ Memcached = simple, fast, no persistence or HA

❌ Redis supports 1 primary + N replicas (not multi-primary)

❌ Cannot directly access ElastiCache from public internet

### 📌 Keywords & Scenario Mapping
| Keyword/Scenario                   | Think of         |
| ---------------------------------- | ---------------- |
| “Low latency access”               | ✅ ElastiCache    |
| “Session store”                    | ✅ Redis          |
| “Ephemeral cache”                  | ✅ Memcached      |
| “In-memory store with replication” | ✅ Redis          |
| “Durable cache”                    | ✅ Redis with AOF |


### 💡 Integrations & Features
- Elastic Beanstalk: Can integrate with ElastiCache easily
- RDS/Redshift: Use ElastiCache to offload read pressure
- Lambda: Can call Redis if inside VPC

### 🧮 Defaults / Limits
- Redis max shard size: ~300 GB (varies)
- Encryption: At-rest and in-transit supported (Redis only)
- Backup: Redis supports it, Memcached does not


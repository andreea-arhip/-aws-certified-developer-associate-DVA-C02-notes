
## Comparison: Elasticache - Redis vs Memcached
| Feature / Property    | **Redis**                                                | **Memcached**                              |
| --------------------- | -------------------------------------------------------- | ------------------------------------------ |
| **DB Type**           | In-memory key-value store, supports complex types        | Simple in-memory key-value store           |
| **Use Case**          | Caching, session storage, pub/sub, ranking, leaderboards | Simple caching, multithreaded workloads    |
| **High Availability** | ✅ Supports replicas + automatic failover                 | ❌ No replica/failover support              |
| **Replication**       | ✅ Primary + replica(s)                                   | ❌ Not supported                            |
| **Scalability**       | Horizontal sharding (manual)                             | Horizontal sharding (built-in client-side) |
| **Storage**           | RAM only, with optional persistence (RDB / AOF)          | RAM only, no persistence                   |
| **Performance**       | Microsecond latency                                      | Microsecond latency                        |
| **Persistence**       | ✅ Snapshots & AOF supported                              | ❌ No persistence                           |
| **Failover Type**     | Replica promotion                                        | ❌ Not supported                            |
| **IAM Auth**          | ❌ Not supported                                          | ❌ Not supported                            |
| **VPC Access**        | ✅ Required                                               | ✅ Required                                 |
| **Global Options**    | ❌ No global replication                                  | ❌ No global replication                    |

#### Persistence
- ❓ App needs caching + persistence across restarts.
  - → ✅ Redis (snapshots/AOF).
  - → ❌ Memcached (no persistence).

#### HA / Failover
- ❓ What happens if a Redis primary fails?
  - → ✅ Replica is promoted.


- ❓ What happens if Memcached node fails? 
  - → ❌ Data lost, must repopulate cache from DB.

#### Use Case

- ❓ App needs leaderboards, pub/sub, or advanced data structures (sets, sorted sets).
  - → ✅ Redis.


- ❓ App just needs simple cache + very high throughput. 
  - → ✅ Memcached.

#### Scaling
- ❓ Need horizontal scaling with multiple nodes.
  - → Both support sharding.
  - → Redis = manual partitioning.
  - → Memcached = client-side auto-sharding.

#### IAM Auth
- ❓ Can we secure ElastiCache with IAM?
  - → ❌ No. Use VPC Security Groups instead.

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


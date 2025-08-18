### 🎯 What Is Amazon Kinesis?
Amazon Kinesis is a real-time data streaming service. It lets you ingest, buffer, and process real-time data (e.g. logs, metrics, video, clickstreams) at scale.

### 🧱 Kinesis Family of Services
| Service                        | Purpose                                                               |
| ------------------------------ | --------------------------------------------------------------------- |
| **Kinesis Data Streams (KDS)** | Real-time ingestion of data streams (e.g. logs, events, clickstreams) |
| **Kinesis Data Firehose**      | Load streaming data to AWS services (e.g. S3, Redshift, OpenSearch)   |
| **Kinesis Data Analytics**     | SQL queries on streaming data                                         |
| **Kinesis Video Streams**      | Stream & analyze video (e.g. for surveillance, ML inference)          |


### 🔵 Kinesis Data Streams (KDS)
| Term                | Description                                                           |
| ------------------- | --------------------------------------------------------------------- |
| **Shard**           | Base throughput unit: 1 MB/sec write, 2 MB/sec read, 1000 records/sec |
| **Producer**        | App or service that puts records into the stream                      |
| **Consumer**        | App or service that reads/processes records (e.g. Lambda, KCL, SDK)   |
| **Retention**       | 24 hours (default) – extendable to 365 days                           |
| **Partition Key**   | Determines which shard the record goes to                             |
| **Sequence Number** | Unique record ID per shard, enables ordering                          |


### ⚙️ Throughput
One shard supports:
- Write: 1 MB/sec or 1,000 records/sec
- Read: 2 MB/sec

To scale, add more shards (resharding).

### 📚 Data Ordering
Kinesis preserves ordering within a shard, based on Partition Key.

### 🔁 Record Processing Options
| Method                       | Notes                                           |
| ---------------------------- | ----------------------------------------------- |
| **SDK (GetRecords)**         | Manual pull with full control                   |
| **KCL (Kinesis Client Lib)** | Java-based consumer library w/ checkpoints      |
| **Lambda trigger**           | Auto-invokes Lambda (default 100 records/batch) |
| **Enhanced fan-out**         | Parallel consumers with dedicated throughput    |


### ✅ Kinesis vs SQS/SNS
| Feature                   | Kinesis                     | SQS                    | SNS    |
| ------------------------- | --------------------------- | ---------------------- | ------ |
| Real-time streaming       | ✅ Yes                       | ❌ No                   | ❌ No   |
| Ordering within partition | ✅ Yes                       | ✅ (FIFO only)          | ❌ No   |
| Replay support            | ✅ (within retention window) | ✅ (if not deleted)     | ❌ No   |
| Multiple consumers        | ✅ (via enhanced fan-out)    | ❌ (duplication needed) | ✅ Yes  |
| Message retention         | ✅ Up to 365 days            | ✅ Up to 14 days        | ❌ None |
| Push or Pull              | Pull (Lambda = push-ish)    | Pull (or Lambda)       | Push   |


---

### 🔥 Kinesis Data Firehose
Fully managed: No need to manage shards or consumers.

### 🧠 Use Case
Streaming data delivery → automatically loads into:
- S3 
- Redshift 
- OpenSearch
- (or to a custom HTTP endpoint)

### 💡 Features
| Feature                      | Notes                                                |
| ---------------------------- | ---------------------------------------------------- |
| **No shards to manage**      | Fully serverless                                     |
| **Data transformation**      | Use Lambda to modify records in transit              |
| **Compression & encryption** | Built-in gzip, KMS support                           |
| **Buffering settings**       | Buffer by time or size before sending to destination |


### 🧪 Exam Scenarios
| Scenario                                            | Kinesis Data Firehose Use? |
| --------------------------------------------------- | -------------------------- |
| Load logs into S3 in near real-time                 | ✅ Yes                      |
| Want to compress and transform data while streaming | ✅ Yes (Lambda transform)   |
| Need exactly-once replay and consumer coordination  | ❌ Use Kinesis Data Streams |

---

### 📈 Kinesis Data Analytics
Run SQL queries on real-time streams (Kinesis or Firehose)

| Feature                    | Notes                                           |
| -------------------------- | ----------------------------------------------- |
| SQL-based stream analytics | Use standard SQL to process JSON-formatted data |
| Input                      | Kinesis Data Stream or Firehose                 |
| Output                     | Firehose, Lambda, Streams                       |

### 🔒 Security & Permissions
| Component         | Notes                                       |
| ----------------- | ------------------------------------------- |
| **IAM Roles**     | Needed for producer & consumer apps         |
| **KMS**           | Encrypt data at rest                        |
| **VPC Endpoints** | Private connectivity to Kinesis             |
| **CloudWatch**    | Logs throughput, record age, write failures |


### 📖 Developer Exam Scenarios
| Scenario                                                             | Likely Answer                        |
| -------------------------------------------------------------------- | ------------------------------------ |
| Real-time processing of clickstream data                             | Kinesis Data Streams                 |
| Deliver transformed logs to S3 every 2 mins                          | Kinesis Firehose w/ Lambda transform |
| Query streaming sensor data using SQL                                | Kinesis Data Analytics               |
| Multiple microservices reading same data stream without interference | Kinesis + Enhanced Fan-Out           |
| Need ordering and replay for real-time stream                        | Kinesis Data Streams                 |
| Need replayability for stream errors                                 | Enable long retention on KDS         |
| Don’t want to manage infrastructure for streaming                    | Kinesis Firehose                     |


### ⚠️ Tricky Questions & Tips
| Trap / Mistake                          | Clarification                                                       |
| --------------------------------------- | ------------------------------------------------------------------- |
| Confusing Kinesis Firehose vs Streams   | Streams = real-time processing, Firehose = data delivery to storage |
| Assuming Firehose stores data           | No! Firehose buffers, transforms, then delivers                     |
| Thinking Kinesis always triggers Lambda | Only with proper event source mapping                               |
| Assuming auto-scaling of shards         | You must reshard KDS manually                                       |
| Believing Kinesis encrypts by default   | Must enable KMS manually                                            |


### 🧠 Quick Recall
- Streams = Real-time, multiple consumers 
- Firehose = Buffer → S3/Redshift/OpenSearch 
- Analytics = SQL on streaming data 
- Shards = Scaling units in KDS 
- Partition key = Ordering + distribution

### 💡 Mnemonics
| Mnemonic                          | Meaning                                  |
| --------------------------------- | ---------------------------------------- |
| **KDS** = **Keep Data Streaming** | Real-time events, multiple consumers     |
| **Firehose = Fire-and-Forget**    | Serverless delivery, no shard management |
| **Analytics = SQL for Streams**   | Query your stream data live              |



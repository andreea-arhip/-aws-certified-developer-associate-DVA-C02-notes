SQS
• Visibility timeout: default 30s, range 0–43,200s (12h)
• Delay seconds: default 0s, max 15min
• Long polling waitTime: 0 < waitTime ≤ 20s
• Message size: 1 byte – 256 KB
• Max number of messages to receive: 10
• Retention period: default 4 days, max 14 days
• FIFO throughput: 300 msg/s (without batching), 3,000 msg/s (with batching)

Kinesis Data Streams
• Retention: default 24h, max 365 days
• Record size: max 1 MB
• Shard capacity: 1 MB/s write, 2 MB/s read, 1,000 records/s write
• GetRecords: up to 10 MB or 10,000 records per call

EBS
• Max volume size: 16 TiB
• gp2: up to ~5.3 TiB, 16k IOPS cap
• gp3: 3,000 IOPS baseline, up to 16k configurable
• io1/io2: up to 50 IOPS/GiB, max 64k/256k
• Boot volumes: only SSD (gp2, gp3, io1, io2)

DynamoDB
• Item size: ≤ 400 KB
• Partition key length: ≤ 2048 bytes
• Sort key length: ≤ 1024 bytes
• RCU: 4 KB per strongly consistent read/sec
• WCU: 1 KB per write/sec
• Streams retention: 24h
• StreamViewType: KEYS_ONLY, NEW_IMAGE, OLD_IMAGE, NEW_AND_OLD_IMAGES
- BatchGetItem - can retrieve up to 16 MB of data, max 100 items
- TransactWriteItems - can write up to 4MB, max 25 distinct items

API Gateway
• Payload limit (REST): 10 MB
• Payload limit (HTTP/WebSocket): 128 KB
• Integration timeout: 29 seconds
• Caching TTL: default 300s, max 3600s
• TTL=0 disables caching
• Invalidate cache: Header Cache-Control: max-age=0

Lambda
• Max memory: 10,240 MB
• Timeout: default 3 sec, up to 15 minutes (900s)
• Environment variable size: 4 KB
• /tmp storage: default 512 MB, up to 10 GB configurable
• Deployment package size: 50 MB (upload), 250 MB (via S3)
• Unzipped code size (including layers): ≤ 250 MB
• Max layers: 5
• Concurrency limit: 1,000 (soft limit), but you can allocate max 900

STS
• Credential TTL: default 3600s (1h), range 900–3600s
• Federated user sessions can go up to 12h (rare case)

X-Ray
• Reservoir: first request each second
• Rate: 5% of additional requests
• Daemon port: UDP 2000
• Indexes up to 50 annotations per trace
• Trace segment size limit: 64 KB

Step Functions
• Standard workflow: max duration 1 year
• Express workflow: max duration 5 minutes
• Payload per state: 256 KB
• Max history size: 25,000 events

S3
• Single PUT upload: max 5 GB
• Multipart upload: max 5 TB
• Minimum part size: 5 MB
• Bucket limit per account: 100 (default)
• Object key length: ≤ 1024 bytes

CloudWatch
• Metric data resolution: 1s (high-res), 60s (standard)
• Max log event size: 256 KB
• PutMetricData: up to 20 metrics per call

SNS
• Message size: 256 KB
• HTTP/S delivery retries: up to 23 days total

CloudFormation
• Template size: 51,200 bytes (direct), 460,800 bytes (via S3)

EC2:
- Placement groups:
  - Spread - max 7 instances per AZ (ex 15 instances = 3 AZ)
  - partition - max 7 partitions per AZ
  - cluster
- Instance states: standby, stop, terminate, hibernate (max 60 days)
- vCPU = max concurrent threads per instance (1 CPU core ⇒ 2 threads(vCPU)
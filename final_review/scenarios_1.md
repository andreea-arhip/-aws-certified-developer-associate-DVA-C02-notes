# AWS Certified Developer Associate — Lambda, S3, API Gateway, SQS & SNS (Detailed Exam Scenarios)

## Lambda

* **Limits:** 15 min runtime, /tmp 512MB, deployment package 250MB, 1000 concurrent executions default, Provisioned Concurrency.
* **Detailed Exam-Oriented Scenarios & Patterns:**

  1. **S3 Upload Processing:** Image uploaded → Lambda resizes → output S3 → triggers SNS to notify user. *Pattern:* Event-driven serverless processing.
  2. **Spiky Traffic Handling:** S3 → SQS → Lambda batch processing. *Pattern:* Decoupled architecture for spiky workloads.
  3. **API Endpoint:** API Gateway → Lambda → DynamoDB write; Provisioned Concurrency for <100ms response. *Pattern:* Low-latency synchronous API.
  4. **Long Task Offload:** Task >15 min → ECS/Fargate. *Pattern:* Offload compute-intensive tasks.
  5. **VPC Access:** Lambda in VPC → RDS Proxy for connection pooling. *Pattern:* Serverless with relational DB.
  6. **Secrets Handling:** Lambda retrieves DB credentials from Secrets Manager. *Pattern:* Secure runtime secret access.
  7. **Kinesis Stream Consumer:** Lambda processes stream → DynamoDB aggregation. *Pattern:* Event streaming integration.
  8. **Temp vs Persistent Storage:** /tmp → ephemeral files; EFS → persistent storage. *Pattern:* Correct storage usage.
  9. **Dependency Management:** Large libraries → Lambda Layer/EFS. *Pattern:* Efficient packaging.
  10. **Async Error Handling:** Async execution → DLQ or Lambda Destinations. *Pattern:* Reliable async processing.

## S3

* **Limits:** Object up to 5TB, 100 buckets per account, multipart uploads >5GB.
* **Detailed Exam-Oriented Scenarios & Patterns:**

  1. Pre-signed URL → temporary external upload. *Pattern:* Secure temporary access.
  2. Static website → CloudFront + OAI. *Pattern:* Serverless hosting.
  3. Object lock → compliance/version retention. *Pattern:* Immutable storage.
  4. Lifecycle rules → Standard → IA → Glacier → Deep Archive. *Pattern:* Cost optimization.
  5. Event notifications → Lambda/SQS/SNS. *Pattern:* Event-driven architecture.
  6. Multipart uploads → handle large files. *Pattern:* Large file handling.
  7. Cross-account access → bucket policy + AssumeRole. *Pattern:* Secure multi-account sharing.
  8. VPC Endpoint → private S3 access. *Pattern:* Security/compliance.
  9. SSE-KMS encryption → sensitive data. *Pattern:* Data security.
  10. Cross-region replication → DR/geo-distribution. *Pattern:* High availability and DR.

## API Gateway

* **Limits:** 29s timeout, payload max 10MB.
* **Detailed Exam-Oriented Scenarios & Patterns:**

  1. REST API → Cognito auth → Lambda. *Pattern:* Secured serverless API.
  2. Long-running requests → async → SQS polling. *Pattern:* Handling execution time limits.
  3. Private API → VPC endpoint. *Pattern:* Internal access.
  4. Binary payloads → Lambda/S3. *Pattern:* File transfer.
  5. Rate limiting → usage plans. *Pattern:* Throttling to prevent abuse.
  6. Edge-optimized API → CloudFront integration. *Pattern:* Low-latency global API.
  7. Custom domain → ACM certificate. *Pattern:* Professional endpoint.
  8. Lambda proxy vs mapping templates → payload mapping. *Pattern:* Flexible integration.
  9. WebSocket API → real-time messaging. *Pattern:* Bi-directional communication.
  10. HTTP APIs → cost-efficient lightweight APIs. *Pattern:* Low-cost modern API.

## SQS & SNS

* **Limits:** SQS message 256KB, visibility timeout max 12h; SNS message 256KB.
* **Detailed Exam-Oriented Scenarios & Patterns:**

  1. Decouple web frontend → SQS → backend processing. *Pattern:* Loose coupling.
  2. FIFO queue + deduplication → order processing. *Pattern:* Ordered reliable processing.
  3. Fanout → SNS → multiple SQS/Lambda/email. *Pattern:* Broadcast pattern.
  4. DLQ → isolate poison messages. *Pattern:* Error handling.
  5. Large payload → store in S3, send pointer in message. *Pattern:* Large message workaround.
  6. Cross-account messaging → IAM/resource policies. *Pattern:* Secure multi-account integration.
  7. Mobile push via SNS. *Pattern:* End-user notification.
  8. Deduplication IDs for FIFO queues. *Pattern:* Prevent duplicate processing.
  9. Visibility timeout adjustment → processing buffer. *Pattern:* Reliable consumption.
  10. Long polling → reduce empty receives, cost-efficient. *Pattern:* Cost optimization.

---

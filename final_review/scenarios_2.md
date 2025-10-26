
# AWS Certified Developer Associate — DynamoDB, Kinesis & Step Functions (Detailed Exam Scenarios)

## DynamoDB

* **Limits:** 400KB/item, 20 GSIs, 5 LCIs, 40,000 RCU/WCU per table.
* **Detailed Exam-Oriented Scenarios & Patterns:**

    1. Single-table design → multiple entity types. *Pattern:* Efficient access patterns.
    2. Hot key mitigation → random suffix, multiple GSIs. *Pattern:* Load distribution.
    3. Streams → Lambda triggers. *Pattern:* Event-driven processing.
    4. TTL → auto-delete old items. *Pattern:* Data lifecycle.
    5. Global tables → multi-region writes & DR. *Pattern:* Global HA.
    6. On-demand mode → spiky workloads. *Pattern:* Cost-effective scaling.
    7. Projection expressions → limit returned attributes. *Pattern:* Read efficiency.
    8. Conditional writes → idempotent operations. *Pattern:* Safe updates.
    9. DAX → reduce read latency. *Pattern:* Caching for performance.
    10. BatchWrite/BatchGet → efficient bulk ops. *Pattern:* Cost and throughput optimization.

## Kinesis

* **Limits:** 1MB record, 1000 writes/sec per shard, 5 reads/sec per shard.
* **Detailed Exam-Oriented Scenarios & Patterns:**

    1. Stream logs → Kinesis → Lambda. *Pattern:* Event ingestion.
    2. Shard splitting → scaling. *Pattern:* Throughput management.
    3. Firehose → S3/Redshift/ElasticSearch. *Pattern:* Analytics pipeline.
    4. Retention period → default 24h, extend 7 days. *Pattern:* Data retention.
    5. Enhanced fan-out → multiple consumers. *Pattern:* Independent read streams.
    6. Lambda transformation → preprocess data. *Pattern:* Data pipeline integration.
    7. Partition keys → maintain ordering. *Pattern:* Deterministic data routing.
    8. Scale shards → high-volume streams. *Pattern:* Elastic scaling.
    9. DLQ → handle failed records. *Pattern:* Reliable ingestion.
    10. Cross-account access → IAM policies. *Pattern:* Secure multi-account streaming.

## Step Functions

* **Limits:** 25,000 state transitions/month free, 256KB input/output, 1-year max execution.
* **Detailed Exam-Oriented Scenarios & Patterns:**

    1. Orchestrate Lambda workflows → ETL jobs. *Pattern:* Serverless workflow.
    2. Choice state → route per data. *Pattern:* Conditional logic.
    3. Wait state → delays between tasks. *Pattern:* Time-based execution.
    4. Parallel state → concurrent Lambda executions. *Pattern:* Parallel processing.
    5. Express vs Standard → sync vs async. *Pattern:* Execution type selection.
    6. Error handling → Retry/Catch. *Pattern:* Reliable workflow.
    7. Human approval → Task Token. *Pattern:* Manual intervention.
    8. Integrations → Lambda, Batch, Glue, SNS. *Pattern:* Multi-service orchestration.
    9. Map state → iterate over items in parallel. *Pattern:* Batch processing.
    10. Long ETL jobs → Standard state → logging. *Pattern:* Observability for long-running jobs.

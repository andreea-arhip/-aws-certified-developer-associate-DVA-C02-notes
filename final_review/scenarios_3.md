# AWS Certified Developer Associate — RDS, Aurora, EFS & FSx (Detailed Exam Scenarios)

## RDS & Aurora

* **Limits:** Max IOPS varies by instance type, Aurora storage 128TB, max connections vary.
* **Detailed Exam-Oriented Scenarios & Patterns:**

    1. Multi-AZ deployment → automatic failover for HA. *Pattern:* High availability for critical workloads.
    2. Read replicas → scale read-heavy workloads. *Pattern:* Horizontal read scaling.
    3. Aurora Serverless → auto-scaling connections for unpredictable workloads. *Pattern:* Cost-efficient serverless relational DB.
    4. Cross-region read replicas → disaster recovery. *Pattern:* Multi-region resilience.
    5. RDS Proxy → Lambda → relational DB integration, prevents connection exhaustion. *Pattern:* Connection pooling for serverless apps.
    6. Database migrations → use DMS for zero-downtime migrations. *Pattern:* Minimal disruption migrations.
    7. Backups → automated/manual, point-in-time recovery. *Pattern:* Data durability and recovery.
    8. Encryption at rest (KMS) and in transit (SSL). *Pattern:* Security best practices.
    9. Parameter groups → custom configurations. *Pattern:* Tuned DB performance.
    10. Monitoring → CloudWatch metrics for CPU, connections, IOPS. *Pattern:* Proactive monitoring and alerting.
* **Traps:** Unlimited connections assumption, missing multi-AZ setup, wrong instance type selection.

## EFS & FSx

* **Limits:** EFS standard → unlimited storage, throughput modes: Bursting/Provisioned; FSx Windows → max 64TB.

* **Detailed Exam-Oriented Scenarios & Patterns:**

    1. Shared file storage → Lambda/ECS/EC2 applications. *Pattern:* Centralized file storage.
    2. POSIX compliance → Linux workloads using EFS. *Pattern:* Compatibility for Linux apps.
    3. FSx Windows → SMB access with Active Directory integration. *Pattern:* Windows-native workloads.
    4. Performance modes → general purpose vs max I/O. *Pattern:* Match throughput to workload.
    5. Backup & replication → EFS Backup plan or FSx snapshots. *Pattern:* Data protection.
    6. Encryption at rest & in transit. *Pattern:* Security best practices.
    7. VPC endpoints → private network access. *Pattern:* Secure access without public internet.
    8. Mount targets → multi-AZ for high availability. *Pattern:* HA configuration.
    9. Auto-scaling throughput → spiky workloads. *Pattern:* Elastic throughput.
    10. Integration with batch processing pipelines. *Pattern:* Scalable shared storage.

* **Traps:** Expecting low-latency like local FS, ignoring network/mount configs, misconfigured throughput mode.

* **Patterns & Best Practices:** Use RDS Proxy for serverless apps, multi-AZ and read replicas for HA and scaling, EFS for Linux shared storage, FSx for Windows workloads.

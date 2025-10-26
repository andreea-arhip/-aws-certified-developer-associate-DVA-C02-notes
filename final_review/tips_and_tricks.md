### 🔄 Service Limits & Gotchas (High-Yield Numbers)
AWS loves limits questions where one wrong number = wrong answer:
- Lambda → 15 minutes max runtime; /tmp storage 512 MB; max package ~250 MB.
- API Gateway → 29 sec timeout; payload max 10 MB.
- SQS → Max message size 256 KB; max delay 15 mins; visibility timeout up to 12h.
- SNS → Message size limit 256 KB.
- Kinesis → 1 MB/s write, 2 MB/s read per shard; max 1 MB record.
- DynamoDB → Strongly consistent read = 2x RCUs; Partition key design critical.
- Step Functions → Standard workflow max 1 year, Express workflow seconds to minutes.
- CloudFormation stack limit → 500 resources.
- CloudWatch Logs event → max 256 KB.

👉 Trick: If two answers both seem right, the one with the real AWS limit is correct.

### 🎭 Common Exam Patterns
- “Most secure way to…” → Always IAM roles, never static keys.
- “Scalable and cost-effective…” → Pick serverless/on-demand over provisioned/EC2.
- “High availability” → Multi-AZ.
- “Disaster recovery/global low latency” → Multi-region (Aurora Global, DynamoDB Global).
- “Temporary access to a resource” → STS or Pre-Signed URLs.
- “Audit and compliance” → CloudTrail + Config.
- “Workflow/orchestration” → Step Functions, not custom scripts.
- “Buffer/decouple” → SQS, not SNS/EventBridge.
- “Fanout/notify multiple targets” → SNS, not SQS.
- “Mobile/social login” → Cognito, not IAM users.

### 🧩 Service Integrations (These Come Up All the Time)
- S3 → Lambda (event-driven processing).
- SQS → Lambda (async job workers).
- SNS → Lambda + SQS + Email (fanout).
- EventBridge → Lambda / Step Functions (event-driven workflows).
- API Gateway → Lambda → DynamoDB (classic serverless backend).
- CodePipeline → CodeBuild → CodeDeploy (full CI/CD).
- CloudWatch Alarm → SNS → Lambda/Email (alerting).
- Kinesis → Lambda → S3/Redshift (analytics pipeline).
- CloudTrail → S3 → Athena (query API history).
- Secrets Manager → RDS / Lambda (secure credential rotation).

👉 If you see two services listed in a scenario and you know AWS has a built-in integration → that’s the right answer.

### 💡 Best Practice Reminders
- IAM → never use root account; rotate access keys; least privilege.
- API Gateway → throttle traffic with Usage Plans; integrate with Cognito for auth.
- Lambda → small, single-responsibility functions; avoid big monoliths.
- S3 → enable versioning; block public access unless explicitly required; use lifecycle rules.
- DynamoDB → design partitions carefully; enable AutoScaling or On-Demand.
- SQS → always handle duplicates; idempotent consumers.
- SNS → for pub/sub fanout, not for job queues.
- Step Functions → use retries instead of coding retry logic inside Lambda.
- CloudWatch → alarms for scaling and ops; X-Ray for tracing.
- CI/CD → automate with CodePipeline, never manual deploys.
- Encryption → KMS for most use cases; choose CMKs for compliance/audit needs.
- Secrets → store in Secrets Manager, never in environment variables or code.

### 📌 Extra Edge Topics (Often Forgotten but Appear on Exam)
- VPC Endpoints → Use for private S3/DynamoDB/API Gateway access. (Trap: NAT gateway)
- SAM & CDK → For serverless IaC; SAM has sam build/deploy, CDK uses real code.
- Elastic Beanstalk → Managed PaaS (not heavily tested, but know basics).
- CloudFormation StackSets → Deploy stacks across accounts/regions.
- CloudFront Signed URLs → Restrict CDN content access.
- S3 Object Lock (WORM) → Compliance immutability.
- Cognito Identity Pool vs User Pool → User Pool = auth (login); Identity Pool = AWS credentials.
- X-Ray vs CloudWatch → X-Ray = tracing; CloudWatch = metrics/logs.
- Cross-account access → IAM Role with trust + STS.
- Parameter Store tiers → Standard (free), Advanced (larger size, more features).

### ⚡ Exam Day Rapid Recall (Mental Shortcuts)
- Buffer → SQS.
- Fanout → SNS.
- Workflow → Step Functions.
- Temporary access → STS / Pre-Signed URL.
- Realtime stream → Kinesis.
- Analytics on logs → Athena (S3), CloudWatch Logs Insights (app logs).
- Trace request → X-Ray.
- API monitoring → CloudTrail.
- Static content → S3 + CloudFront.
- Relational DB → RDS / Aurora.
- NoSQL global scale → DynamoDB Global Tables.
- Mobile login → Cognito.
- Serverless Docker → ECS Fargate.
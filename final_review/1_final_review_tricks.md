🟣 AWS Lambda
- Invocations:
  - Async (S3/SNS/EventBridge) = automatic retries (2x) → failed events go to DLQ if configured ⭐
    - Use idempotent logic for all async integrations (SQS, S3, EventBridge).
    - “duplicate logs” or “same request ID twice” → async retry, not a bug.
    - DLQ: For async failures; supports SQS or SNS. 
    - Destinations: Can handle success or failure asynchronously → SQS, SNS, EventBridge, Lambda (used for routing, auditing)
  - Sync (API Gateway/ALB) = caller gets response; retries handled by caller
  - Stream (Kinesis/DynamoDB/SQS) = Lambda polls stream, retry until success or data expires.
- Use Provisioned Concurrency for cold-start-free performance.
- When Concurrency Limit Hits (Reserved concurrency)
  - SNS/S3/EventBridge: throttled → retried → DLQ if still failing. 
  - API Gateway/ALB: immediate 429/502. 
  - SQS / Streams: messages stay in source; retried later until expires.
- Missing CloudWatch logs for some async events → event failed and went to DLQ ⭐
  - Async invocations can silently fail and go to DLQ with no CloudWatch logs if the handler never executes
- High-res metrics (1s) + SNS alarm = instant Lambda failure notification (<30s).
- Environment variables belong in task definition (for ECS) or function config (for Lambda).
  - Encrypt env vars with KMS; use client-side encryption helpers if you need secrets hidden in console ⭐
- Lambda@Edge functions must be deployed in us-east-1.
- VPC access: Lambda must create ENIs in private subnets -> slower cold starts
  - For internet/S3 access → needs NAT Gateway or VPC endpoint.
    - The private subnet routes 0.0.0.0/0 → NAT Gateway → Internet Gateway → S3.
    - VPC Endpoint (BETTER) -> Adds a private route in your VPC route table directly to S3.
- Lambda needs S3 + KMS access → must allow both s3:GetObject and kms:Decrypt.
- Use AWS SDK (boto3) preinstalled in Lambda for S3 uploads — no code changes.

🟢 Amazon S3
- S3 event: event type = s3:ObjectCreated:Put => Lambda, SNS, SQS.
  - S3 event => Lambda is async → retried twice → can go to DLQ (if Lambda has one).
  - "notify application whenever an object is uploaded" => s3:ObjectCreated:*, not just Put.
- Pre-signed URLs = simple, secure direct uploads/downloads.
- Use Lifecycle rules for cost control (e.g., 30d → Glacier Deep Archive).
- Static site hosting: public access disabled → use CloudFront OAI.
- CRR & SRR = must enable versioning on both buckets + IAM role + KMS key permissions (if objects are encrypted)
  - Replication only applies to new objects after enabling it — existing ones aren’t copied automatically.
- Large uploads → Multipart Upload for reliability and resume support.
  - recommended for files > 100 MB (required for > 5 GB). Max 5TB
- Logging:
  - Server Access Logging: logs requests (must target a different bucket). 
  - CloudTrail Data Events: log object-level API calls (GetObject, PutObject). 
    - CloudTrail must have Data Events enabled to log object access (disabled by default).
  - CloudWatch Metrics: request counts, latency, errors. 
  - CloudWatch Alarms: can trigger on metrics or object-level changes via EventBridge
- Server access logs show who accessed; event notifications trigger actions.
- SSE-KMS – uses CMK; needs both s3:GetObject + kms:Decrypt.
  - If you encrypt with SSE-KMS and get access denied → missing kms:Decrypt permission.
- S3 is a regional service accessed over the public internet by default. 
  - Private access via:
    - VPC Gateway Endpoint (for S3 and DynamoDB only). 
    - Interface Endpoint (PrivateLink) for other services. 
    - Lambda in VPC → must use VPC endpoint or NAT Gateway to reach S3.
- 📦 Storage Management Tools 
  - S3 Inventory: daily/weekly CSV list of all objects and metadata. 
  - S3 Batch Operations: perform actions (copy, set ACL, tag) on large object sets. 
  - S3 Object Lock: WORM (write-once-read-many) for compliance — requires versioning. (“Legal hold” or “immutability”)
    - Need durable, immutable log storage -> S3 + Object Lock
  - S3 Glacier Restore: temporarily brings archived objects back for access.
- Need to block specific countries -> Use WAF + CloudFront, not S3

CloudFront
- Global CDN (Content Delivery Network)
- Origins: static (S3), dynamic (ALB/EC2), serverless (API Gateway / Lambda@Edge)
  - “Need to serve both static and dynamic content” → Use multiple cache behaviors.
- Also used to secure and accelerate access to private content (signed URLs, WAF, Shield).
- Cache behavior - Path-based rule (e.g., /images/* → long TTL, /api/* → short TTL).
  - Default TTL = 24 hours (unless overridden by headers).
  - Use Origin Shield for centralized caching layer (optional, advanced).
  - For dynamic content (e.g., API Gateway), caching can be disabled or short TTL.
    - “API responses must always be fresh” → Set Cache-Control: no-cache or short TTL.
- Security:
  - HTTPS (TLS) supported for both viewer and origin connections.
    - ✅ For low latency and SSL offload, always put CloudFront in front of API Gateway / ALB.
  - ACM Certificates used with CloudFront (must be in us-east-1 region).
  - OAI (Origin Access Identity) or OAC (Origin Access Control) restrict direct S3 access
    - “Static website hosting must stay private” → Use CloudFront + OAI/OAC + block S3 public access.
  - AWS WAF → attach to CloudFront (global) for filtering (SQLi, XSS, geo-blocking).
    - “Block users from specific countries” → Use WAF Geo-match rule at CloudFront.
    - ❌ S3 Bucket Policy can’t directly block countries — that’s CloudFront + WAF job.
  - AWS Shield Standard → DDoS protection (auto-enabled).
    - AWS Shield Advanced → optional, extra protection, cost.
- “Redirect user to another page when content is missing” → Custom Error Response -> Origin response
- “Must redirect based on country header” → Lambda@Edge (Viewer Request).
- “Need millisecond-latency lightweight rewrite” → CloudFront Function.
- Lambda@Edge functions must be deployed in us-east-1.
- “Need to give temporary access to private files” → CloudFront Signed URL/Cookie -> CloudFront verifies signature before serving the object.
  - Signed URL → one file per URL (video, image). 
  - Signed Cookie → multiple files (entire site section).
- “Need to see which URLs users request” → Enable standard logging to S3.

🟡 AWS WAF
- WAF = blocks malicious HTTP(S) traffic; can filter by IP, Geo, SQLi, XSS.
  - Behavior modes: Allow except blocked, Block except allowed, Count only. 
  - Geo match rule to block countries. 
  - Attach WAF Web ACL to CloudFront, ALB, API Gateway, AppSync.

🟠 AWS Certificate Manager (ACM) / Private CA 
- ACM = public certs (auto-renew if DNS validation CNAME still valid). 
  - Imported certs don’t auto-renew → reimport or request new ⭐
  - IoT devices can use ACM certificates for TLS.
- Private CA = internal certs, subordinate CAs, CloudTrail auditing ⭐

🟡 Amazon DynamoDB
- Read scaling: add Aurora Replicas or DAX (cache), not duplicate tables ⭐
- Monitor ConsumedRead/WriteCapacityUnits for cost efficiency.
- Hot partition = poor partition-key design → fix key, not capacity ⭐
- Eventually consistent = ½ RCU cost of strongly consistent reads.
- “Company policy: must encrypt before sending data” → DynamoDB Encryption Client, not SSE-KMS.
- Streams can trigger Lambda synchronously (Lambda polls the stream).
- “Need to restore to state 24 hours ago” → PITR.
- Developer added a DAX cluster, but performance didn’t improve -> ✅ Fix → Update app to use DAX SDK client.
- Global Tables (v2) -> use DynamoDB Streams to replicate data across regions
  - “Developer tried to add replica Region but received an error.” -> ✅ Root cause → DynamoDB Streams not enabled on source table.

🟡 AWS API Gateway
- Path /products + GET method → must create resource /products and method GET.
- Custom domain:
  - Must use ACM certificate in the same Region. 
  - For CloudFront-distributed APIs (like global REST APIs), certificate must be in us-east-1. 
  - Create Route 53 alias (A record) to the custom domain endpoint.
- REST API timeout = 29 seconds, longer → Lambda fails ⭐
  - 🧠 Trap: API Gateway → Lambda is synchronous -> no DLQ
- Security:
  - IAM authorization -> Uses SigV4 signed requests (typically for internal apps).
  - Cognito -> API Gateway authorizer verifies the ID token.
  - Use Lambda Authorizer (custom tokens) or Cognito Authorizer (OIDC/SAML).
- Stage settings: Enable Execution (integration/auth) + Access (who/what) logs for full visibility ⭐
- “Need separate environments with same API” → use stages + stage variables.
- VPC Link creates a private connection between API Gateway and your VPC’s private network.
  - “Private internal service on ALB” + “Expose via API Gateway securely” → VPC Link. 
  - Not to be confused with VPC Endpoint, which connects VPC → AWS service (not API Gateway).
- “Need to customize the 403 message returned to users” → use non-proxy integration + Gateway Response.
- WebSocket:
  - “Broadcast message to all users” → WebSocket API + connection IDs.
  - “Need to invoke different Lambdas based on JSON key ‘action’” → WebSocket API with $request.body.action.


🔵 Amazon RDS / Aurora
- RDS cannot scale automatically (must modify instance type). Only Aurora Auto Scaling scales readers automatically.
  - “Need relational DB, not NoSQL → RDS.”
  - “Need MySQL/PostgreSQL + auto scaling reads → Aurora.”
- max_connections parameter controls concurrent clients ⭐
- Aurora Auto Scaling = scales read replicas based on avg connections, not CPU ⭐
- Aurora supports cross-region read replicas and global database.
- RDS Proxy improves Lambda connection scalability.
- Can’t encrypt existing unencrypted DB → must create new from encrypted snapshot.
- IAM DB Auth (MySQL, Postgres) ≠ credentials — uses short-lived tokens (15 min).
- “Need to understand capacity usage” → use Performance Insights (query-level breakdowns) or CloudWatch metrics.
- “Lambda timeout calling RDS” -> Missing NAT Gateway or SG rule


🟠 Amazon ECS / Fargate
- Cluster
    └── Service (optional) - manages how many tasks run, keeps them safe, optional ALB, auto-scaling
    └── Task Definition (blueprint) - image, ports, env vars, CPU/mem
    └── 1+ Containers (actual app processes)
- Task Definition - Blueprint for containers (image, ports, env vars, CPU/mem).
  - Environment variables → define under containerDefinitions.environment in task definition, not service ⭐
  - “Run one-off background job” → ECS Run Task.
- Service - Runs & maintains N copies of a task, manages load balancing
    - “Need to ensure exactly 3 containers are always running” → ECS Service.
- To share data between containers → mount a shared volume in the same task definition
- “Mount volume across tasks” → use EFS volume.
- ECS Fargate + ALB = must assign awsvpc network mode for ENI per task ⭐
  - “Need unique IP per task” → awsvpc.
  - “Simplify security group assignment per container” → awsvpc.
  - “Reduce port conflicts” → awsvpc (since each task has unique IP).
- To avoid port conflicts, set containerPort = 80, hostPort = 0 (ECS auto-assigns host port).
  - “ECS + internal service DNS discovery” → Cloud Map. 
  - “Need sticky sessions or path-based routing” → ALB.
- EFS = shared, scalable, low-latency file system for Lambda/ECS/EC2.
- Use AWS Copilot to easily deploy ECS/Fargate microservices.
- “Evenly distribute across AZs” → placement = spread, field = attribute:ecs.availability-zone. 
- “Minimize unused CPU/memory” → placement = binpack.
- IAM:
  - “Task needs to access S3/DynamoDB” → Task role. 
  - “Task cannot pull image from ECR” → Execution role missing permission. 
  - “Grant least privilege to each task” → Use task role, not instance role.
- “Need per-container logs visible in CloudWatch” → Enable logging in task definition (logConfiguration).
  - Log drivers (awslogs) → send container logs → CloudWatch Logs.
  - Container Insights (CPU, memory, network metrics per task).
- “Need fully managed container hosting with automatic HTTPS” → App Runner.

ECR:
- Auth: aws ecr get-login-password | docker login --username AWS --password-stdin <account>.dkr.ecr.<region>.amazonaws.com
- Expired token → must re-auth every 12 hours. 
- “ImagePullBackOff” in ECS → missing ECR pull permission. 
- Permissions: IAM user/role needs ecr:GetAuthorizationToken, ecr:BatchCheckLayerAvailability, ecr:PutImage
- “Simplest way to pull image from ECR” → use aws ecr get-login-password then docker pull.


🧩 AWS CodeDeploy
- Lambda deployment configs: Canary10-5, Canary10-10, Linear10-1, Linear10-10, AllAtOnce
- ECS deployment configs: ECSCanary10-5, Linear10-3 
- EC2 types: AllAtOnce, HalfAtATime, OneAtATime.
- BeforeAllowTraffic / AfterAllowTraffic hooks = used for validation Lambdas. ⭐
- CodeCommit:
  - “CodeCommit via HTTPS requires” → Git credentials, not SSH or IAM password.
- CodeBuild:
  - cache dependencies - Use cache section (e.g., paths: /root/.m2/**/*)
  - Service Role → Allows CodeBuild to access S3, CodeCommit, ECR, etc.
  - CodeBuild sends logs to CloudWatch Logs and S3.
  - CodeBuild → ECR login: aws ecr get-login-password ⭐
- CodeDeploy:
  - ECS & Lambda do not support “in-place” deployments.
  - CodeDeploy integrates with CloudWatch Alarms for rollback triggers.
  - AppSpec file lives in S3 or GitHub and must match the right AppSpec schema for your platform (EC2, ECS, or Lambda).
  - EC2 / On prem -> IN PLACE / blue-green (new ASG, switch ELB):
    - Hooks for in-place: AppStop -> DownloadBundle -> BeforeInstall -> Install -> AfterInstall -> AppStart -> ValidateService
  - ECS Deployments (Blue/Green only) - Traffic shifting happens via ALB listeners.
    - Hooks: BeforeInstall -> AfterInstall -> AfterAllowTestTraffic -> BeforeAllowTraffic -> AfterAllowTraffic
    - Trap: ECS requires two ALB listeners: One for test traffic & One for production traffic
    - CodeDeployDefault.ECSAllAtOnce, CodeDeployDefault.ECSLinear10PercentEvery1Minutes, CodeDeployDefault.ECSCanary10Percent5Minutes
  - Lambda Deployments (Blue/Green only) - Lambda alias traffic shifting
    - Hooks: BeforeAllowTraffic -> AfterAllowTraffic
    - Need gradual traffic shifting” → Use Canary or Linear configs
- CodePipeline:
  - Manual approvals are per stage (not per action).
  - Pipeline IAM role must access all resources in all stages.
  - Artifacts move between stages using S3 buckets.
  - CodePipeline supports SNS notifications for stage success/failure.
  - CloudFormation used under the hood by CodePipeline actions.

AWS Copilot:
- High-level CLI for ECS + Fargate deployments.
- Handles ECS service creation, pipelines, and environment configs.
- “Simplest way to deploy containerized microservice with built-in pipeline” → AWS Copilot. 
- “Command-line tool to manage ECS CI/CD” → AWS Copilot (not CDK).

AWS Amplify (Front-End CI/CD):
- “Frontend web app, need CI/CD with minimal setup” → Amplify. 
- “Test new features without affecting production” → Connect new branch → deploy to separate subdomain (dev.appId.amplifyapp.com).
  - Do not test directly in production branch.
- Provides GraphQL (AppSync) + Auth (Cognito) integration.

🟣 AWS AppSync / GraphQL
- Use AppSync GraphQL API to aggregate multiple data sources (Lambda + DynamoDB) ⭐
- Enable API caching (full request) and encryption for HIPAA compliance ⭐
- GraphQL vs REST: GraphQL reduces number of API calls (1 endpoint, flexible queries).

Elastic Beanstalk:
- handles provisioning (EC2, ELB, ASG, RDS, CloudWatch)
- Configuration via Dockerrun.aws.json (for Docker) or Procfile.
- “Process background tasks using Beanstalk” → Worker tier + SQS.
- “Need Beanstalk in private subnets only” → Configure VPC in EB environment. 
- “ALB not needed” → Choose single-instance environment (no load balancer).
- Elastic Beanstalk CLI (EB CLI) → eb init, eb create, eb deploy.
- ❌ EB cannot deploy to ECS/Fargate.
- ✅ EB can deploy from ZIP, WAR, or Docker image.

🟣 AWS CloudFormation
- Failed delete (e.g., sg-11223344) → modify template to retain the resource, then delete stack ⭐
  - DeletionPolicy: Retain prevents accidental data loss.
- DependsOn = controls creation order.
- Stack creation fails if AMI not in region → copy AMI and update template ⭐
  - Stacks are region-specific -> AMI IDs differ between regions.
  - To deploy multi-region → use separate stacks (or StackSets).
- “Stack creation failed but you want to inspect created resources” → disable rollback.
  - By default, all stacks rollback on any single resource failure.
- “Prevent accidental update of RDS during stack update” → Stack Policy.
- “Need to create resource conditionally (dev/prod)” → !If with Conditions.
- “Reuse common infrastructure (VPC, security groups) across stacks” → Nested Stack.
- Templates ≤ 1 MB (direct upload), or up to 460,800 bytes (compressed) if uploaded directly; larger must go to S3.
- “Template syntax wrong, stack partially deployed” -> ❌ No, template is validated before deploy
- “Cross-account deploy” -> ✅ Use StackSets or CodePipeline role assumption
- aws cloudformation describe-stacks -> stack status

AWS SAM
- SAM = serverless (Lambda, API Gateway, DynamoDB, Step Functions), not EC2 or ALB.
- sam local invoke -> Run a single function locally using Docker. (ex Lambda)
- sam local start-api -> Run entire API locally (API Gateway + Lambda).
- sam deploy --guided -> Deploys CloudFormation stack interactively.
- sam sync -> Syncs live code changes without full redeploy.
- “Traffic shift 10% every 5 minutes” → CodeDeploy config in SAM.
- “Function needs S3 and DynamoDB access” → define Policies in SAM template: AWSLambdaBasicExecutionRole, AmazonDynamoDBFullAccess
- “Blue/Green Lambda deployments” → CodeDeploy config in SAM template.
  - ✅ Traffic shifting (Canary, Linear, AllAtOnce) → via CodeDeploy.
- “Infra-as-code for Lambda + API Gateway” -> SAM

AWS CDK:
- You write code → synthesize → CloudFormation template → deploy.
- cdk init app --language typescript 
- cdk synth       # Synthesizes CloudFormation template 
- cdk deploy      # Deploys template via CloudFormation 
- cdk diff        # Shows differences vs deployed stack 
- cdk destroy     # Deletes CloudFormation stack
- “Reusable infrastructure abstraction (e.g., a reusable Lambda construct)” → Construct.
  - | Level  | Example                                                    | Description                                      |
    | ------ |------------------------------------------------------------| ------------------------------------------------ |
    | **L1** | raw - `CfnBucket`                                          | Direct mapping to raw CloudFormation resources.  |
    | **L2** | high-level - `s3.Bucket(self, "MyBucket", versioned=True)` | Opinionated higher-level abstraction.            |
    | **L3** | patterns - `apigateway.LambdaRestApi(...)`                 | Prebuilt “patterns” combining multiple services. |
  - “Need to quickly create Lambda + API Gateway integration” → Use L3 construct (pattern).
- “Need to deploy different configs for environments” → use context or environment variables.
  - cdk deploy -c env=prod
- CI/CD integration → CodePipeline runs cdk synth + cdk deploy.
- Best practice: run cdk synth in CodeBuild, then deploy using CloudFormation action in CodePipeline.


🟢 AWS CloudWatch / X-Ray
- ConsumedRead/WriteCapacityUnits for DynamoDB cost tracking.
- Metrics (CPUUtilization = 70%) -> 1-minute intervals by default
  - Use high-res metrics (1s) for fast alarms (<30s reaction).
  - Each metric has a namespace (AWS/Lambda, AWS/EC2, Custom/MyApp). 
  - Dimensions = key/value pairs (e.g., InstanceId, FunctionName).
- Structure:
  - Log Group → e.g., /aws/lambda/myFunction
  - Log Stream → each instance/execution (like one EC2 or Lambda run)
  - Lambda automatically sends logs → CloudWatch Logs (via execution role policy: AWSLambdaBasicExecutionRole).
- Missing logs = check Lambda DLQ, not IAM permissions ⭐
- Cross-account dashboards = CloudWatch cross-region cross-account feature.
- ✅ “Need to analyze logs using SQL-like queries” → CloudWatch Logs Insights.
- “App logs contain metrics data for direct ingestion” → Embedded Metrics Format.

X-Ray
- X-Ray: Segment = service; Subsegment = inner operation; Trace = full request path.
  - - trace header (e.g., X-Amzn-Trace-Id) travels between services.
- To query X-Ray by user → use GetTraceSummaries with filter on user field ⭐
  - user field = special top-level property → query via GetTraceSummaries ⭐
- “Need to add debug info not searchable” → use Metadata (not indexed).
- ❌ X-Ray doesn’t automatically trace third-party APIs — you must wrap calls with the SDK.
  - “Need to capture outbound HTTP calls or custom segments” → use X-Ray SDK + patch_all().
- “Need to trace Lambda cold start time and downstream DynamoDB latency” → Enable X-Ray Active Tracing. 
- Daemon:
  - Needed on EC2, ECS, on-prem, etc. 
  - Not needed for Lambda or API Gateway, Beanstalk (native integration).
- AWSXRayDaemonWriteAccess → for services writing trace data. 
- AWSXRayReadOnlyAccess → for developers viewing trace console.
- “Trace only 10% of traffic to /api path” → custom sampling rule.

🟣 AWS CloudTrail
- Enable single multi-region trail to centralize logs ⭐
- Records API events as JSON objects -> Stored in S3 (default = per-region trail) - with SSE encryption.
- Events:
  - Management Events → control-plane actions (e.g., CreateBucket, RunInstances, PutRolePolicy)
  - Data Events → data-plane actions (e.g., GetObject, PutObject in S3) - Disabled by default (because they’re high volume). 
  - Insight Events → detect unusual API patterns automatically.
- “Trigger Lambda when IAM user creates a new security group” → CloudTrail → EventBridge rule → Lambda.

AWS Config — Resource Configuration & Compliance
- Continuously records configuration changes for supported AWS resources -> Stores snapshots in an S3 bucket.
- Can trigger:
  - Config Rules (AWS managed or custom Lambda-based). 
  - Remediation actions automatically when noncompliant.
- “Detect unencrypted S3 buckets” -> AWS Config managed rule: s3-bucket-server-side-encryption-enabled
- “View config history for a resource” -> Config timeline
- “Track IAM policy drift or SG changes” -> AWS Config
- “Custom logic for compliance” -> Custom Config rule (Lambda function)
- ❌ Config is regional; for multi-region, deploy rules in each region.

AWS Trusted Advisor — Account Optimization
- Provides real-time best practice checks across five key areas: cost, performance, security, fault tolerance, service limits
- “Account health check,” “Free cost and security recommendations.”
- ✅ Basic Support = limited checks (Service Limits + Security Groups only).
- ✅ Full checks require Business or Enterprise Support Plan.
- ❌ Trusted Advisor doesn’t generate alerts automatically — use EventBridge for notifications.
- ✅ “Best practice recommendations” → Trusted Advisor, not CloudWatch or Config.

IAM:
- AWS service managing resources (CodeDeploy, ECS, etc.) -> Service-linked role
- Cross-account access -> Role with trust policy allowing external principal
- “Short-lived credentials for mobile/web app” → Use STS AssumeRole or Cognito Identity Pool.
- SCPs don’t grant permissions — they only restrict:
  - “Organization-wide limit on IAM actions” - SCP in AWS Organizations
- Permissions boundary also doesn’t grant — it caps what’s possible (ex: Max allowed permissions for a role)
- “Detect public or cross-account resource access” → IAM Access Analyzer.
- “Troubleshoot denied access” - IAM Policy Simulator
- “Need to detect unused IAM permissions” → Access Advisor.
- “Need to audit account-wide IAM usage” → Credential Report.
- STS confirm identity: aws sts get-caller-identity

Cognito:
- “API Gateway authorizer uses Cognito tokens” → it validates ID token (not Access token). 
- ❌ ID token used for resource access → wrong → use Access token or Identity Pool credentials.
- “Need to check what user accessed the API” → extract from ID token claims.
  - “Must audit who accessed which resource” -> Use ID token claims or STS credentials in CloudTrail
- “Need fine-grained IAM access per user” → use Cognito Identity Pool ROLE MAPPING:.
  - “Allow each user to only access their own S3 folder” → use IAM condition key cognito:username.
  - “Restrict users based on group” → use cognito:groups in IAM policy.
- “Company uses on-prem SAML/OIDC/Fb,Google; app must use Cognito for access control” → Federate SAML → Identity Pool (then assign IAM roles).
- “Need to add custom claim to token” → Pre token generation trigger. 
- “Auto-confirm users from a certain domain” → Pre sign-up trigger.
- “User session expired after 1 hour; must not re-login every time” → use refresh token flow.

🟠 AWS KMS / Encryption
- Encrypt data via GenerateDataKey, not by passing data to KMS directly ⭐
- For many small objects, client-side encryption might be faster ⭐
- CMK types: AWS-managed (auto-rotated), Customer-managed (full control).
- S3 SSE-KMS requires both s3:GetObject + kms:Decrypt ⭐
- Use KMS ReEncrypt API to change CMK for existing ciphertext:
  - “Need new encryption without decrypting data first” → ReEncrypt.
- ❌ Cross-account access requires both KMS key policy + IAM allow.
- “Lambda fails to read encrypted env vars” → missing kms:Decrypt.

AWS Secrets Manager
- Audit via CloudTrail (every GetSecretValue logged).
- Versioned secrets → track updates.
- “Lambda retrieves encrypted credentials at runtime” → Secrets Manager + KMS.
  - ✅ Permissions must allow both secretsmanager:GetSecretValue + kms:Decrypt.

🟢 Networking
- Subnets + Routing 
  - Public subnet → has route to Internet Gateway. 
    - NAT Gateway → always deployed in a public subnet with an Elastic IP.
    - ❌ NAT Gateway in private subnet → no Internet.
    - “App must be reachable publicly” -> Deploy in public subnet with Elastic IP or behind ALB
  - Private subnet → no direct route to Internet; can access via NAT Gateway or VPC Endpoint.
    - ❌ Lambda in private subnet + no NAT Gateway or VPC Endpoint → can’t reach S3 or DynamoDB.
    - ✅ Gateway Endpoints (for S3/DynamoDB) → free and private alternative to NAT.
    - ✅ Interface Endpoint (PrivateLink) to connect to other AWS services (e.g., SNS, Secrets Manager)
      - Gateway Endpoints free, Interface Endpoints cost.
- Security control:
  - Security Groups (SGs) = STATEFUL (inbound auto-ALLOWED)
    - Evaluated before NACLs.
    - “Can’t SSH into EC2” -> Missing SG rule for inbound port 22
  - Network ACLs = STATELESS → must define both inbound/outbound.
    - “Need to explicitly deny IP ranges” → Use Network ACL (SGs can only allow).
- ENIs (Elastic Network Interfaces)
  - Virtual network adapters attached to EC2, ECS, Lambda-in-VPC, etc.
  - “Lambda in VPC timing out” → ENI must be created and have outbound route via NAT or Endpoint.
  - “Attach multiple IPs / SGs to instance” → Use multiple ENIs.
  - “ECS task with awsvpc mode, multiple SGs per container” -> Uses ENIs for each task
- Elastic IPs (EIP):
  - “Need a fixed public IP for EC2 /NAT Gateway / ALB accessible from Internet” → Elastic IP.
  - ❌ Elastic IPs aren’t automatically assigned in private subnets.
  - Elastic IP = static public address for EC2, best for DNS A record ⭐
- ❌ PrivateLink ≠ VPC Peering → PrivateLink connects services, Peering connects networks.
- ✅ VPCs are regional; subnets are AZ-specific.
- “Limit outbound Internet access” -> Egress-only Internet Gateway or restricted SG

Route 53:
- “How does Route 53 decide if an endpoint is unhealthy?” → Failure threshold (not latency).
  - “Failover routing between regions” -> Failover policy + health check
- “Team failed to configure CloudWatch alarms for health checks — how to monitor?” → View Route 53 dashboard.
- “Custom domain for CloudFront with HTTPS” → ACM certificate in us-east-1 + Alias record to CloudFront domain.
- “Custom domain for API Gateway” -> create ACM cert → import or request → add Route 53 Alias record
- “Split traffic 70/30 between endpoints” -> Weighted routing
- “Users should be routed to nearest region” -> Latency-based routing
- “Restrict by geography” -> Geolocation routing
- “Combine multiple healthy IPs” -> Multivalue answer routing
- ❌ CNAME at zone apex (root domain) → invalid → use Alias A record.
- ❌ Health checks on Alias record → not directly; must attach to target (like ALB).
- ❌ Private hosted zone → no Internet routing (internal only).
- ✅ DNS TTL (Time to Live) → affects caching & propagation, not routing speed.
- ✅ ACM + Route 53 integration simplifies domain validation (DNS validation auto-renews).
  - ❌ ACM-issued certs auto-renew only if DNS validation is still valid and domain used by AWS resource.

SNS:
- at-least once delivery, subscribers: SQS, lambda, HTTP(s) endpoint, Email/SMS/mobile
- Supports message filtering by attributes
- Can use FIFO topics for ordered, exactly-once delivery (only with FIFO SQS subscribers)
- ❌ SNS doesn’t queue messages — use SQS for decoupling.
- ✅ SNS + Lambda → async invocation. If function fails, retry with exponential backoff.
- supports SSE-KMS encryption

SQS:
- ✅ “Need to replay messages” → extend retention or use DLQ.
- SQS + Lambda Behavior 
  - Retries automatically (up to 6 hours visibility timeout). 
  - SQS + Lambda supports batchSize config.
  - If all retries fail → message sent to DLQ. 
  - Lambda scales concurrency with queue depth (up to concurrency limit).
- Enable long polling - ReceiveMessageWaitTimeSeconds.

EventBridge:
- EventBridge = “Smart event router” (rules + filtering + archive + replay).
- Central event bus + routing system — routes events from AWS services, SaaS apps, or custom apps to targets based on patterns.
  - EventBridge uses rules + patterns to match and route events.
  - “Need fine-grained filtering by content (JSON matching)” -> Use event pattern filters.
- EventBridge supports cross-account routing.
- “Need to integrate SaaS apps (e.g., Zendesk, Datadog)” -> EventBridge supports SaaS partners.
- “Need to replay old events or debug” -> Use Event Archive + Replay.
- “Need fan-out but with filtering/routing logic” -> EventBridge = “Smart SNS”.
- ✅ EventBridge retries async events automatically.
- ❌ Not for high-throughput streaming (use Kinesis instead).

Kinesis:
- “Need to load data continuously to S3, Redshift, or OpenSearch” - Kinesis Firehose auto delivers streams to destinations.
- “Need SQL-like real-time analytics on streams” - Kinesis Data Analytics runs SQL queries on streams.
- “Need to replay/reprocess data” - Kinesis Streams retain data for up to 365 days.
  - ❌ Firehose cannot reprocess old data — only Streams can.
- “Need multiple consumers per stream” - Kinesis supports multiple consumer apps per shard.
- ✅ Firehose = managed, near-real-time → no shard scaling needed.
- ✅ Kinesis = ordered (by partition key).

Amazon MQ:
- Managed ActiveMQ/RabbitMQ → for legacy apps using messaging protocols (JMS, AMQP, STOMP, MQTT).
- “Need to migrate on-prem message broker with minimal code change” -> Amazon MQ = drop-in replacement for ActiveMQ.
- “Need to preserve ordering, transactions, or message grouping” -> Traditional brokers handle this natively.
- ✅ MQ provides durable queues + pub/sub + topics within broker.

Step functions:
- serverless workflow orchestration -> full event history, error handling, visual execution
- States: TASK (sync), CHOICE, WAIT, PARALLEL, MAP, PASS (fwd data), SUCCEED / FAIL (end execution)
- State data flow:
    - InputPath (filter input) → Parameters (prepare) → ResultPath (merge result) → OutputPath (filter output).
- Error handling in State machines:
    - Built-in error codes: "ErrorEquals": States.ALL, States.Timeout, States.TaskFailed, States.Permissions.
    - Use Retry (with IntervalSeconds, MaxAttempts, BackoffRate) → then Catch.
    - ResultPath → pass error/output to next state.
- Wait for Task Token:
    - task sends TOKEN to worker -> worker calls back SendTaskSuccess / SendTaskFailure with token
    - "Resource": "arn:aws:states:::sqs:sendMessage.waitForTaskToken" -> tells Step Functions to wait
    - Push-based (external app calls back)
    - Pull-based (worker polls with GetActivityTask) -> use HeartBeats to keep task alive + TimeoutSeconds
- Execution types:
    - Standard workflow:
        - exactly once execution, up to 1 year
        - 90 day history -> send to Cloudwatch for longer
        - supports human approval steps
        - support all service integrations, activities, and design patterns
    - Express workflow < 5min
        - ASYNC: At-least-once, may retry -> background / messaging tasks
        - SYNC: at-most-once, no retries -> request-response (API Gateway, Lambda)
        - History in CloudWatch only.
        - do not support activities, job-run (.sync), and Callback patterns.

ElastiCache (Redis / Memcached)
- In-memory cache layer for low latency (< 1 ms). 
- Redis: persistence, replication, pub/sub, TTL, clustering. 
- Memcached: simple distributed cache (no persistence). 
- “Improve read performance / offload DB / session store” → Redis. 
- “Need simple scaling, no HA” → Memcached. 
- Lambda + Redis via VPC (requires ENI).

MemoryDB for Redis
- Redis-compatible durable, in-memory database (vs. ElastiCache Redis, which is cache-only).
- “Persistent Redis-compatible in-memory DB” → MemoryDB 
- “In-memory cache with no durability” → ElastiCache

Athena
- Serverless SQL on S3 (pay per query). 
- Uses Glue Data Catalog as schema store. 
- After adding new partitions → run MSCK REPAIR TABLE mytable; 
- Common errors: “partition missing” or “table not updated” → forgot repair. 
- “Query S3 CSV/JSON/Parquet with SQL” → Athena.

Glue 
- ETL (Extract-Transform-Load) + Data Catalog for Athena, Redshift, EMR. 
- Triggers jobs on schedule or on S3 events. 
- “Central schema metadata for Athena” → Glue Data Catalog. 
- “Need serverless ETL to clean S3 data before query” → Glue Job.

OpenSearch:
- Managed search & analytics engine— for full-text search, log analysis, and monitoring dashboards
- Uses OpenSearch Dashboards for visualizations (like Kibana).
- “Analyze and search application logs in real time” → OpenSearch 
- “Full-text search engine with analytics” → OpenSearch

Cloud9 
- Browser-based IDE; EC2-backed env auto-manages IAM creds & lifecycle. 
- “Least setup dev environment” → Cloud9 EC2 env.

🧰 AWS CloudShell
- Browser-based shell preconfigured with AWS CLI and IAM credentials.
- just terminal (no EC2 provisioning).
- “Run AWS CLI quickly without setup” → CloudShell 
- “Full development environment with code editing” → Cloud9

🤖 Amazon CodeGuru (Profiler + Reviewer)
- ML-based tool for automated code reviews and runtime performance profiling.
- “Automatically detect inefficient code or leaks” → CodeGuru Profiler. 
- “Static code review suggestions pre-deploy” → CodeGuru Reviewer.

🛠️ AWS Systems Manager (SSM)
- Unified operational suite for managing EC2, hybrid, and app config.
- Parameter Store: app configs + optional KMS encryption. 
- Session Manager: secure SSH-less shell to EC2. 
- Run Command: execute shell commands remotely. 
- Automation Documents: reusable operational workflows. 
- AppConfig: config deployment 
- Patch Manager: apply updates to EC2 fleets.

⚙️ AWS AppConfig (part of Systems Manager)
- Deploy new configurations (not code!) safely to apps.
- Supports canary or linear config rollouts.
- Configs stored in SSM Parameter Store, Secrets Manager, or S3.
- “Deploy app configs without redeploying code” → AppConfig. 
- “Roll out new app feature toggles gradually” → AppConfig, not CodeDeploy.


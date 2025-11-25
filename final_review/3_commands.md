# 🧠 AWS Developer Associate – Command & Code Cheat Sheet (Compact)

---

## 🚀 1. Serverless & Deployment (Lambda, SAM, API Gateway)

```bash
# Lambda deploy & update
aws lambda create-function --function-name MyFunc \
  --runtime python3.9 \
  --role arn:aws:iam::123456789012:role/lambda-role \
  --handler app.handler \
  --zip-file fileb://function.zip
  
aws lambda update-function-code --function-name MyFunc --zip-file fileb://function.zip
aws lambda invoke --function-name MyFunc output.json
```

**SAM workflow**

```bash
sam build
sam local start-api
sam deploy --guided
```

**SAM snippet**

```yaml
Resources:
  Fn:
    Type: AWS::Serverless::Function
    Properties:
      Handler: app.handler
      Runtime: python3.9
      Events:
        Api:
          Type: Api
          Properties:
            Path: /hello
            Method: get
```

**Tips:**

* Async = 2 retries, DLQ/Destination; Sync = caller handles retry.
* Stream (DynamoDB/Kinesis) retries until success (7 days).
* Attach VPC subnets + SGs for private resource access.

---

## ⚙️ 2. Storage & Data (S3, DynamoDB, RDS)

### **S3**

```bash
aws s3 cp file.txt s3://my-bucket/
aws s3 sync ./data s3://my-bucket/data
aws s3api put-bucket-versioning --bucket my-bucket --versioning-configuration Status=Enabled
```

**Tips:**

* Events → Lambda / SNS / SQS.
* Lifecycle rules = transitions + expiration.
* Pre-signed URLs = temporary access.

### **DynamoDB**

```bash
aws dynamodb create-table \
  --table-name Users \
  --attribute-definitions AttributeName=UserId,AttributeType=S \
  --key-schema AttributeName=UserId,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST

aws dynamodb put-item --table-name Users 
  --item '{"UserId":{"S":"123"},"Name":{"S":"Alice"}}'
  
aws dynamodb query 
  --table-name Orders 
  --key-condition-expression "CustomerId = :id" \
  --expression-attribute-values '{":id":{"S":"123"}}'
```

**Tips:**

* Query = partition key lookup; Scan = full table.
* LSI = same PK, diff sort key; GSI = diff PK.
* Streams → Lambda for change events.

### **RDS**

* Enforce SSL → via **RDS Parameter Group**.
* Connection strings use `?sslmode=require`.
* IAM DB Auth → short-lived token via AWS CLI.

---

## 🪴 3. Messaging & Eventing (SQS, SNS, EventBridge, Kinesis)

```bash
# SQS
aws sqs create-queue --queue-name MyQueue
aws sqs send-message --queue-url <url> --message-body "Hello"
aws sqs receive-message --queue-url <url>

# SNS
aws sns create-topic --name MyTopic
aws sns subscribe --topic-arn <arn> --protocol email --notification-endpoint me@example.com
```

**Patterns:**

* Fanout = SNS → SQS.
* FIFO queue = ordered + deduplicated.
* DLQ = handle failed processing.
* EventBridge = rule-based routing (SNS replacement).

---

## 🏧 4. Infrastructure as Code (CloudFormation, SAM, cfn tools)

```bash
aws cloudformation validate-template --template-body file://template.yaml
aws cloudformation deploy --template-file template.yaml --stack-name MyStack
```

**Helpers:**

* `cfn-init` → configure instance.
* `cfn-hup` → detect metadata changes, rerun init.
* `cfn-signal` → signal completion to CFN stack.

---

## 🔐 5. Secrets & Config (KMS, Secrets Manager, Parameter Store)

```bash
# KMS
aws kms encrypt --key-id alias/mykey --plaintext fileb://data.txt --output text --query CiphertextBlob

# Secrets Manager
aws secretsmanager create-secret --name MySecret --secret-string '{"password":"mypwd"}'
aws secretsmanager get-secret-value --secret-id MySecret
```

**Usage:**

* Secrets Manager = rotation + retrieval + encryption.
* Parameter Store = app config & secure strings.

---

## 🧮 6. CI/CD (CodeCommit, CodeBuild, CodeDeploy, CodePipeline)

```bash
# CodeCommit
git remote add origin https://git-codecommit.us-east-1.amazonaws.com/v1/repos/MyRepo
git push origin main
```

**Pipeline flow:**
CodeCommit → CodeBuild → CodeDeploy → (Lambda/EC2/EB)

**CodeDeploy Hooks:**
BeforeInstall → AfterInstall → ApplicationStart

**Deployment Strategies:**
All-at-once / Rolling / Blue-Green / Canary

---

## 🛸 7. Networking, Routing & Elastic Beanstalk

**Route 53 Routing Policies:**
Simple | Weighted | Latency | Failover | Geolocation | Geoproximity | Multi-Value

**Elastic Beanstalk Docker Modes:**

* Single container (EC2 + Dockerfile)
* Multi-container (ECS cluster + Dockerrun.aws.json)

**ECS Flow Logs:**
Enable `awsvpc` mode or Container ENI Flow Logs to capture only container traffic.

---

## 🧬 8. Security, IAM & Monitoring

```bash
# IAM role creation
aws iam create-role --role-name LambdaRole --assume-role-policy-document file://trust.json
aws iam attach-role-policy --role-name LambdaRole --policy-arn arn:aws:iam::aws:policy/AWSLambdaBasicExecutionRole
```

**CloudWatch:**

* Logs: Lambda & ECS output.
* Metrics: Custom app metrics.
* Alarms: Trigger SNS or Auto Scaling.

---

## 🔎 9. High-Yield Memory Hooks

| Concept                      | Quick Recall                                    |
| ---------------------------- | ----------------------------------------------- |
| Async Lambda                 | 2 retries → DLQ/Destination                     |
| Stream Lambda                | Retry until success (7 days)                    |
| API Gateway stages           | dev / test / prod                               |
| S3 Events                    | Trigger Lambda / SNS / SQS                      |
| Least Privilege              | Always narrow IAM roles                         |
| Infrastructure as Code order | SAM → CloudFormation → CDK                      |
| CodeDeploy Hooks             | BeforeInstall → AfterInstall → ApplicationStart |

## IAM:
- add role with instance profile to EC2 using CLI:
  - aws iam create-instance-profile --instance-profile-name EXAMPLEPROFILENAME 
  - aws iam add-role-to-instance-profile --instance-profile-name EXAMPLEPROFILENAME --role-name EXAMPLEROLENAME 
  - aws ec2 associate-iam-instance-profile --iam-instance-profile Name=EXAMPLEPROFILENAME --instance-id i-012345678910abcde
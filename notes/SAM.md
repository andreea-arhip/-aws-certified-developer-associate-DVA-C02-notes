### 🚀 What is SAM?
- Framework for developing serverless applications on AWS.
- Uses CloudFormation under the hood.
- Supports: Lambda, API Gateway, DynamoDB, S3, Step Functions, etc.
- Declarative: template.yaml file defines resources.

### 🧩 SAM CLI Features
| Command               | Purpose                                          |
| --------------------- | ------------------------------------------------ |
| `sam init`            | Scaffold a new SAM app                           |
| `sam build`           | Build dependencies (e.g., for Python, Node.js)   |
| `sam local invoke`    | Test Lambda locally                              |
| `sam local start-api` | Run local API Gateway                            |
| `sam deploy`          | Deploy to AWS                                    |
| `sam package`         | Create package for deploy (used before `deploy`) |

### 📦 Packaging & Deploying
- sam package -> uploads code to S3, outputs transformed template.
- sam deploy -> creates/updates stack from transformed template.
- sam deploy --guided -> prompts you for: 
  - Stack name 
  - Region 
  - Capabilities (IAM permissions)
  - Save settings in samconfig.toml

### 📝 template.yaml Structure
```
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Resources:
  MyFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: app.lambda_handler
      Runtime: python3.12
      Events:
        HelloAPI:
          Type: Api
          Properties:
            Path: /hello
            Method: get
```

### 🧠 Auto-Publish Alias & DeploymentPreference
```
AutoPublishAlias: live
DeploymentPreference:
Type: Canary10Percent10Minutes
Alarms:
- MyErrorAlarm
```
🧠 Enables safe Lambda deployments with traffic shifting using CodeDeploy.

### 🔄 Transform = AWS::Serverless-2016-10-31
- Special macro that expands simplified SAM syntax into full CloudFormation.
- Required at the top of your SAM template.

### 📚 Supported Resources
- AWS::Serverless::Function
- AWS::Serverless::Api
- AWS::Serverless::SimpleTable 
- AWS::Serverless::Application
- AWS::Serverless::LayerVersion
- Anything CloudFormation supports (via AWS:: syntax)

### 🔐 IAM in SAM
```
Policies:
- AmazonDynamoDBFullAccess
```

Or define inline IAM policy:
```
Policies:
- Statement:
    - Effect: Allow
      Action:
        - dynamodb:GetItem
          Resource: "*"
```

### 📎 SAM vs CloudFormation
| Feature            | SAM               | CloudFormation          |
| ------------------ | ----------------- | ----------------------- |
| Syntax             | Simplified        | Verbose                 |
| Transform          | `AWS::Serverless` | No transform            |
| Local Testing      | ✅ `sam local`     | ❌                       |
| CI/CD Integrations | ✅                 | ✅                       |
| Packaging          | Built-in          | Manual (with S3 upload) |

### 🔥 Exam Tips
✅ Use SAM when:
- You see serverless stack with Lambda, API Gateway, DynamoDB.
- You want safe deployments via CodeDeploy with traffic shifting.
- The question mentions sam deploy --guided, local testing, or aliasing.
- You want CI/CD built into your template (especially when paired with CodePipeline).

🧠 Remember:
- SAM is just a CloudFormation abstraction — everything still deploys via CloudFormation stack.
- Deployment preferences use CodeDeploy under the hood (same as Lambda deployment strategies).
- Can be used in conjunction with CDK, CodePipeline, CloudWatch alarms, etc.




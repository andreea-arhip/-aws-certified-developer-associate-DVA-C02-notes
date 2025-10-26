### 🟦 CloudFormation
📖 Think: The blueprint / architect’s plan.
- You write YAML/JSON templates → AWS creates resources. 
- Declarative: You say what you want (EC2, S3, RDS) → AWS builds it. 
- Supports parameters, outputs, nested stacks. 
- Infra as Code (IaC). 
- Used everywhere in AWS → exam loves it.

🔑 Exam keywords: “Infrastructure as code,” “template,” “JSON/YAML,” “repeatable deployments.”

### 🟩 AWS CDK (Cloud Development Kit)
📖 Think: Writing CloudFormation in real code.
- Instead of YAML/JSON → you use Python, Java, TypeScript, etc.
- CDK compiles down to CloudFormation under the hood.
- Better for developers (code reuse, loops, abstractions).
- Same power as CloudFormation, just developer-friendly.

🔑 Exam keywords: “Define infra in TypeScript/Python,” “synthesizes to CloudFormation.”

### 🟧 Elastic Beanstalk
📖 Think: Auto-DevOps team that sets up infra + deploys code.
- PaaS: You upload your app code → Beanstalk handles EC2, ASG, ELB, RDS setup.
- Supports Java, Node.js, Python, Go, etc.
- Lets you still customize underlying infra if needed.
- Good for “just run my app” scenarios.

🔑 Exam keywords: “Upload app, AWS handles infra,” “managed deployment,” “scales automatically.”

### 🟨 SAM (Serverless Application Model)

📖 Think: CloudFormation, but with serverless shorthand.
- Extension of CloudFormation → simplified for Lambda, API Gateway, DynamoDB, Step Functions.
- Provides CLI (sam build/deploy/local) to test serverless apps.
- Uses CodeDeploy under the hood for safe Lambda deployments (canary, linear).

🔑 Exam keywords: “Simplify Lambda + API Gateway deployment,” “CloudFormation extension,” “serverless shorthand.”

### 🧪 Quick Exam Scenarios
| Scenario                                                | Best Tool         |
| ------------------------------------------------------- | ----------------- |
| **Define reusable AWS infra with IaC**                  | CloudFormation    |
| **Want to use TypeScript/Python instead of YAML**       | CDK               |
| **Deploy serverless stack (Lambda + API Gateway) fast** | SAM               |
| **Just upload app code, AWS manages infra**             | Elastic Beanstalk |


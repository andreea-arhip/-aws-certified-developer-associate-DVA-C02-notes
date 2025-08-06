### 🧩 Core Services Overview
| Service                     | Description                                                |
| --------------------------- | ---------------------------------------------------------- |
| **CodeCommit**              | Fully managed **Git-based** source control                 |
| **CodeBuild**               | Builds & tests code in fully managed build servers         |
| **CodeDeploy**              | Automates application deployment (EC2, Lambda, ECS)        |
| **CodePipeline**            | Orchestrates CI/CD pipeline using other services           |
| **CodeArtifact** (optional) | Store software packages for builds (e.g., Maven, npm, pip) |

---

## 📦 CodeCommit
- Fully managed Git repository.
- Encrypted by default using KMS.
- Integrated with IAM for access control.
- Replaces GitHub/Bitbucket in AWS-native pipelines.

✅ Tips:
- Can trigger CodePipeline or CloudWatch Events on push.
- Use SSH, HTTPS, or credential helper for authentication.

---

## 🛠️ CodeBuild
- Compiles, tests, and packages code.
- Supports many languages: Java, Python, Node.js, Go, Docker, etc.
- Pay-as-you-go. Uses containers under the hood.

✅ Key Concepts:
- Uses buildspec.yml to define build phases:
```
version: 0.2
phases:
  install:
    commands:
      - echo Installing
  build:
    commands:
      - echo Building
artifacts:
  files:
    - target/myapp.jar
```
✅ Tips:
- Can pull dependencies from CodeArtifact.
- Output artifacts go to S3.
- Environment variables, compute types, and caching are customizable.

---

## 🚀 CodeDeploy
Automates deployment to:
- EC2 instances (via CodeDeploy Agent)
- ECS services
- Lambda functions

✅ Deployment Strategies:

| Type        | Target                       | Strategies Available |
| ----------- | ---------------------------- | -------------------- |
| EC2/on-prem | In-place, Blue/Green         |                      |
| Lambda      | Canary, Linear, All-at-once  |                      |
| ECS         | Rolling update or Blue/Green |                      |

✅ AppSpec File:
- Defines deployment structure:
```
version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/app
hooks:
  AfterInstall:
    - location: scripts/install_dependencies.sh
```
✅ Tips:
- Requires IAM Role (CodeDeploy to interact with EC2/ECS/Lambda).
- CodeDeploy Agent must be installed on EC2.
- Monitors health via CloudWatch Alarms, auto rollback on failure.

---

## 🔄 CodePipeline
- Orchestrates full CI/CD flow: Source → Build → Test → Deploy
- Connects CodeCommit, CodeBuild, CodeDeploy, S3, Lambda, etc.

✅ Stages: Source → Build → Test → Approve (optional) → Deploy

✅ Integrations:
- Triggers from Git push, CloudWatch Events, manual approvals.
- Artifacts are passed between stages via S3.

✅ Tips:
- Pipelines are declarative (YAML) or defined via console/CloudFormation/CDK.
- Supports manual approval gates before production.
- Use custom actions (e.g., invoke Lambda between steps).

---

### ✅ Common Exam Scenarios
| Scenario                                 | Best Tool                                             |
| ---------------------------------------- | ----------------------------------------------------- |
| Auto-deploy app when code pushed to repo | CodePipeline + CodeCommit                             |
| Run tests and package app                | CodeBuild                                             |
| Deploy Lambda with safe rollout          | CodeDeploy + Canary or Linear deployment              |
| Deploy to EC2 with rollback on failure   | CodeDeploy with CloudWatch alarms                     |
| CI/CD without managing servers           | CodePipeline + CodeBuild + CodeDeploy (fully managed) |
| Replace GitHub with AWS native           | CodeCommit                                            |
| Push Docker image to ECR and deploy      | CodeBuild + CodePipeline + ECS                        |
| Approval before production deploy        | Add Manual Approval in CodePipeline                   |

🧠 Extra Tips for the Exam:
- CodeDeploy does NOT provision infrastructure → must already exist (EC2, ECS, etc.).
- Blue/Green deployments = easiest rollback.
- Always look for AppSpec and buildspec files in scenarios.
- Use Amazon EventBridge to trigger pipelines from external events.
- CodePipeline and CodeBuild are serverless → no infra to manage.

---

### 🔐 IAM & Security
| Action                           | Role Required                                                                    |
| -------------------------------- | -------------------------------------------------------------------------------- |
| CodeBuild running build          | IAM role for CodeBuild                                                           |
| CodePipeline accessing resources | IAM role for pipeline                                                            |
| CodeDeploy accessing EC2         | IAM role for EC2 (instance profile) + CodeDeploy role                            |
| CodeCommit access                | IAM user/role with Git credentials                                               |
| Secure deployments               | Use **KMS encryption**, **CodePipeline artifact store**, and **CloudWatch logs** |


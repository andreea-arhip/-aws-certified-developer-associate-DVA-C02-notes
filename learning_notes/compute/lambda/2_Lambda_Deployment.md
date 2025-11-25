### Lambda Deployment Options
| Deployment Tool            | Strategy                                         | Notes                                                       |
| -------------------------- | ------------------------------------------------ | ----------------------------------------------------------- |
| **Manual / CLI / Console** | Immediate                                        | All traffic switches to new version                         |
| **CodeDeploy**             | ✅ Canary<br>✅ Linear<br>✅ All-at-once            | Safest way to deploy Lambda functions with traffic shifting |
| **SAM**                    | Uses CodeDeploy under the hood                   | Define strategy in `template.yaml`                          |
| **CDK**                    | Also supports traffic shifting with Lambda alias | Similar to SAM                                              |
| **CloudFormation**         | Use aliases & weights                            | Define deployment preference manually                       |

### 🧪 Deployment Strategies with CodeDeploy
| Strategy        | Description                                        |
| --------------- | -------------------------------------------------- |
| **All-at-once** | 100% of traffic shifted immediately                |
| **Canary**      | e.g., 10% for 10 mins → 100%                       |
| **Linear**      | e.g., 10% every 1 minute                           |
| **Rollback?**   | Yes, if failure detected via **CloudWatch alarms** |

🧠 How it works:
- CodeDeploy creates an alias for your Lambda function (e.g., prod alias).
- Shifts traffic between versions based on the chosen strategy.
- Optional: Define hooks (BeforeAllowTraffic, AfterAllowTraffic) to run validation scripts.

### 🔐 IAM Permissions Required
- CodeDeploy service role (to deploy the Lambda).
- Lambda execution role (to run the function).
- CloudWatch role (if alarms/metrics are used for rollback).

### 📝 Example in SAM Template
```
AutoPublishAlias: live
DeploymentPreference:
  Type: Canary10Percent10Minutes
  Alarms:
    - MyErrorAlarm
```

### ✅ Exam Tips
- "Safe rollout" → Use CodeDeploy with Canary or Linear.
- If rollback is mentioned, CloudWatch alarms + CodeDeploy must be part of the solution.
- Remember: CodeDeploy manages aliases & traffic shifting.
- No need to reinvent infra — CodeDeploy doesn’t create functions, it just deploys versions.

### Optional Tools Summary
| Tool                     | Built-in Deployment?           | Notes               |
| ------------------------ | ------------------------------ | ------------------- |
| **SAM**                  | Yes (with CodeDeploy)          | Template-defined    |
| **CDK**                  | Yes (with Lambda alias)        | Programmatic        |
| **Serverless Framework** | Yes (plugin-based)             | Not AWS-native      |
| **CloudFormation**       | Yes (manual setup)             | More complex        |
| **CodePipeline**         | Yes (combined with CodeDeploy) | For full CI/CD flow |

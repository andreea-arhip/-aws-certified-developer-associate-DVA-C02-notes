### 🧩 What Is Elastic Beanstalk?
- A Platform-as-a-Service (PaaS) that deploys web applications and backend services in supported languages/platforms without managing the infrastructure.
- Automatically handles provisioning, load balancing, auto scaling, monitoring, and deployment.

### 🛠️ Supported Platforms (as of 2025)
- Languages: Java, .NET, Node.js, Python, Ruby, Go, PHP
- Platforms: Docker, Tomcat, Nginx, Apache, IIS
- Custom platform: Build your own with Packer + Elastic Beanstalk CLI.

### ⚙️ How It Works
Upload code (ZIP, WAR, etc.) via Console, CLI, or CI/CD.

EB provisions and manages resources:
- ✅ EC2, 
- ✅ Auto Scaling Group, 
- ✅ Load Balancer, 
- ✅ RDS (optional), 
- ✅ CloudWatch, 
- ✅ S3 for deployment bundles.

Handles rolling updates, monitoring, and logs.

### 🔁 Deployment Strategies
| Strategy               | Description                                                                               |
| ---------------------- | ----------------------------------------------------------------------------------------- |
| **All at once**        | Fastest. Downtime likely.                                                                 |
| **Rolling**            | Update X instances at a time. No downtime (if enough capacity).                           |
| **Rolling with batch** | Fixed instance batch sizes.                                                               |
| **Immutable**          | Deploy to new set of instances → switch over. Safer.                                      |
| **Blue/Green**         | Create new environment → swap CNAME. Minimal risk. Manual step. ✅ Best for critical apps. |

### 📦 Application Versions & Environments
| Term                    | Description                                   |
| ----------------------- | --------------------------------------------- |
| **Application**         | Container for multiple environments/versions  |
| **Application version** | A deployable archive (S3-stored)              |
| **Environment**         | Deployment of a version (e.g., `dev`, `prod`) |
| **CNAME swap**          | Easy switch between environments (Blue/Green) |

### 🧠 Key Features
| Feature                                   | Purpose                                                                                         |
| ----------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Environment Configuration**             | Customize EC2 types, capacity, VPC, scaling, health checks                                      |
| **Environment Tier**                      | Web Server (default) or Worker Tier (for background jobs via SQS)                               |
| **Configuration Files (`.ebextensions`)** | YAML files to customize environment settings                                                    |
| **Saved Configurations**                  | Templates to reuse settings in other environments                                               |
| **Health Monitoring**                     | Basic + Enhanced (instance, deployment, app status)                                             |
| **Logs**                                  | Retrieved from EC2, can push to CloudWatch Logs                                                 |
| **Custom domain**                         | Use Route 53 + CloudFront if needed                                                             |
| **RDS integration**                       | Optional — but created **within the EB environment**, gets **deleted when env is terminated** ❗ |

### 📌 Common Exam Scenarios
| Scenario                                     | Solution                                 |
| -------------------------------------------- | ---------------------------------------- |
| You want a quick deployment of a Node.js app | Use Elastic Beanstalk                    |
| Want to test a new version without downtime  | Use **Blue/Green Deployment**            |
| Need a custom EC2 type or AMI                | Configure via `.ebextensions` or console |
| Background job queue                         | Use **Worker Tier** + **SQS**            |
| Want safe updates                            | Use **Immutable** deployment             |
| Need to retain RDS after deleting app        | **Create RDS outside EB environment** ❗  |
| CI/CD integration                            | Use EB CLI or SDK/CodePipeline           |

### 🔐 IAM & Security
- EC2 instances run with an Instance Profile (IAM role).
- Elastic Beanstalk uses a Service Role to manage AWS resources.
- RDS + EC2 must be in same VPC / security group if communicating.
- S3 bucket for app versions must allow Elastic Beanstalk access.

### 📤 EB vs CloudFormation vs CodeDeploy
| Service               | Use Case                                                             |
| --------------------- | -------------------------------------------------------------------- |
| **Elastic Beanstalk** | Easy full-stack app deployment, managed infra                        |
| **CloudFormation**    | Full infra as code, more control                                     |
| **CodeDeploy**        | Advanced deployment automation, custom control, no auto-provisioning |

### 🧠 Tips & Tricks
- EB does not abstract cost: You pay for all AWS resources used.
- For more control, consider CDK or CloudFormation.
- Environment variables, logs, scaling rules = all configurable in EB.
- Use S3 lifecycle policies to delete old application versions.

### ✅ Summary Table
| Feature           | Key Point                                    |
| ----------------- | -------------------------------------------- |
| Deployment        | Upload code → EB provisions infrastructure   |
| Deployment Types  | All-at-once, Rolling, Immutable, Blue/Green  |
| Worker Tier       | For background jobs using SQS                |
| Config            | Use `.ebextensions` for customization        |
| RDS Best Practice | Create **outside** EB if you want to persist |
| Rollback          | Easy revert to previous version              |
| Health            | Enhanced health checks = deeper metrics      |


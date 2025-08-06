### 🚀 What is CloudFormation?
- A service to provision AWS resources using code (YAML or JSON).
- Describes resources in a template.
- Uses a stack to create/update/delete the resources defined in the template.

### 🧱 Core Concepts
| Term                | Meaning                                                         |
| ------------------- | --------------------------------------------------------------- |
| **Template**        | YAML/JSON file describing AWS resources and their configuration |
| **Stack**           | A deployed instance of the template                             |
| **Change Set**      | Preview of what will change before updating a stack             |
| **Drift Detection** | Identifies manual changes made outside of CloudFormation        |
| **StackSet**        | Allows managing stacks across multiple accounts and regions     |

### 🧠 Exam Tips
- CloudFormation is declarative: you define the final state, and AWS figures out how to reach it.
- You can use Outputs to export values (e.g., VPC ID) to other stacks.
- CloudFormation integrates with IAM: define roles/policies for resources.
- Stack updates can fail due to resource replacement or dependency errors.
- If stack creation fails, resources are rolled back by default (unless disabled).
- Nested stacks help modularize templates.

### 🛠️ Key Template Sections (YAML)
```
AWSTemplateFormatVersion: '2010-09-09'
Description: "My App Infrastructure"
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-s3-bucket
Outputs:
  BucketName:
    Value: !Ref MyBucket
```

### ⚡ Shortcuts / Intrinsics Functions
| Function                        | Description                         |
| ------------------------------- | ----------------------------------- |
| `!Ref`                          | Get name/ID of a resource           |
| `!GetAtt`                       | Get an attribute (e.g., DNS of ELB) |
| `!Sub`                          | String interpolation                |
| `!Join`                         | Join strings                        |
| `!If`, `!Equals`, `!And`, `!Or` | Conditionals                        |
| `!ImportValue`                  | Import a value from another stack   |

### 🔐 Permissions & Security
- IAM roles can restrict what CloudFormation can provision.
- Use stack policies to protect critical resources from updates/deletion.
- Be cautious with templates that create IAM roles/policies, security groups, or public buckets.

### 🔄 Deployment Scenarios
| Use Case                          | CloudFormation Feature            |
| --------------------------------- | --------------------------------- |
| Create same infra in 3 accounts   | **StackSets**                     |
| Preview changes before deployment | **Change Sets**                   |
| Automatically delete on failure   | **Rollback on failure** (default) |
| Detect changes made outside CFN   | **Drift Detection**               |
| Separate templates for app & DB   | **Nested Stacks**                 |

### 🧠 Common Exam Scenarios
| Scenario                                 | Service Feature                  |
| ---------------------------------------- | -------------------------------- |
| Prevent updates to a DB                  | Use **Stack Policy**             |
| Automatically create a role with Lambda  | Define in **Resources** block    |
| Share VPC ID across stacks               | Use **Outputs** + `!ImportValue` |
| Deploy same infra in all regions         | **StackSets**                    |
| Revert stack creation on error           | Enable **rollback** (default)    |
| Detect manual changes to resource config | Use **Drift Detection**          |

### 🔄 CloudFormation vs Others
| Use Case                         | Better With                           |
| -------------------------------- | ------------------------------------- |
| Reproducible infra               | **CloudFormation**                    |
| Manual GUI-based deployment      | **Elastic Beanstalk**                 |
| Fast, code-based deploy + GitOps | **CDK** (Cloud Development Kit)       |
| Config only (no provisioning)    | **AWS Config**                        |
| App-level deployment + infra     | **CodePipeline** + **CloudFormation** |



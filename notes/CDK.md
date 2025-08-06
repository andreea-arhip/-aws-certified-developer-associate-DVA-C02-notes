### 💡 What is CDK?
- Infrastructure as Code (IaC) using general-purpose languages:
    - TypeScript, Python, Java, C#, Go
- Compiles to CloudFormation templates
- Helps you model and provision cloud resources with constructs

### ⚙️ Core Concepts
| Concept       | Description                                   |
| ------------- | --------------------------------------------- |
| **Construct** | Basic building block (like Lambda, S3 bucket) |
| **Stack**     | Collection of AWS resources as a unit         |
| **App**       | Root of the CDK application                   |
| **Stage**     | For multi-environment deployment (optional)   |
| **Synth**     | Converts code → CloudFormation                |
| **Deploy**    | Pushes stack to AWS                           |
| **Destroy**   | Deletes stack from AWS                        |

### 📦 Common Commands
```
cdk init app --language typescript
cdk synth        # generates CloudFormation template
cdk deploy       # deploys stack to AWS
cdk destroy      # removes stack
cdk diff         # shows what will change
```

### 🧱 Example (TypeScript)
```
import * as cdk from 'aws-cdk-lib';
import * as lambda from 'aws-cdk-lib/aws-lambda';

class MyStack extends cdk.Stack {
constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
super(scope, id, props);

    new lambda.Function(this, 'MyLambda', {
      runtime: lambda.Runtime.NODEJS_18_X,
      handler: 'index.handler',
      code: lambda.Code.fromAsset('lambda'),
    });
}
}

const app = new cdk.App();
new MyStack(app, 'MyStack');
```

### 🧠 CDK Advantages
| Feature          | CDK Strength                         |
| ---------------- | ------------------------------------ |
| **Reusability**  | Use loops, conditions, functions     |
| **Testability**  | Unit test infra code                 |
| **CI/CD**        | Integrates with pipelines            |
| **Productivity** | Less boilerplate than CloudFormation |

### 🔁 CDK vs CloudFormation vs SAM
| Feature       | CDK                   | SAM             | CloudFormation              |
| ------------- | --------------------- | --------------- | --------------------------- |
| Syntax        | Real code             | Simplified YAML | YAML/JSON                   |
| Compile → CFN | ✅                     | ✅               | N/A                         |
| Local testing | ❌                     | ✅ (sam local)   | ❌                           |
| Reusability   | ✅ (functions/classes) | ❌               | ❌                           |
| Deployment    | `cdk deploy`          | `sam deploy`    | `aws cloudformation deploy` |

### 📚 Libraries
- aws-cdk-lib: All core services
- constructs: Base class for building your own constructs
- Language-specific libraries installed via npm, pip, etc.

### 💥 Command: cdk bootstrap
- Sets up required resources (like an S3 bucket and roles)
- Must run once per environment before cdk deploy

### 🔐 Permissions (IAM)
- CDK automatically creates IAM roles and policies.
- Can still explicitly define roles/policies using code.

### 🧠 Exam Triggers
✅ Choose CDK if:
- You see TypeScript/Python/Java/C# syntax managing AWS resources.
- You want IaC with logic, like loops or conditions.
- You want to reuse infra as code modules (constructs).
- You see mentions of:
  - cdk deploy, cdk synth, constructs, cdk init, or CloudFormation stack from code.

❌ Avoid CDK if:
- The scenario demands local testing (Lambda) → SAM.
- The team wants declarative templates only → CloudFormation or SAM.

### 🛑 Limitations
- Needs bootstrapping
- Steeper learning curve than SAM for beginners
- No built-in local emulation for Lambda or API Gateway (unlike SAM)

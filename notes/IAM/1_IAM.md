### 🔹 CORE COMPONENTS
| Term          | Description                                                                      |
| ------------- | -------------------------------------------------------------------------------- |
| **User**      | Individual identity with credentials.                                            |
| **Group**     | Collection of users with shared permissions.                                     |
| **Role**      | Temporary access, no credentials, assumed by users/services.                     |
| **Policy**    | JSON doc that defines **permissions** (Allow/Deny) for **users, groups, roles**. |
| **Principal** | The entity (user, role, app) making a request.                                   |

🧠 Default is deny. You must explicitly allow actions in a policy.


### 🔑 IAM POLICIES
- Identity-Based: Attach to user, group, or role.
- Resource-Based: Used with services like S3, Lambda, SQS, etc.
- Managed Policies:
  - AWS-Managed (reusable)
  - Customer-Managed (custom)
- Inline Policies: One-off, tightly coupled with a single identity.

Policy Evaluation Logic (Explicit Deny overrides Allow):
- Start with default deny.
- Check for explicit deny → overrides everything.
- Check for explicit allow.
- Final result is allow/deny.

📌 Permission boundaries, session policies, and service control policies (SCPs) can limit what a role/user can do, even if granted permissions.

### 🧠 MEMORY & SCENARIO TIPS
| Scenario or Keyword                            | Think / Use                                     |
| ---------------------------------------------- | ----------------------------------------------- |
| “Temporary credentials”                        | STS + IAM Role                                  |
| “Cross-account access”                         | IAM Role with trust policy                      |
| “Application on EC2 accesses S3”               | EC2 Instance Profile + IAM Role                 |
| “Service accesses another AWS service”         | IAM Role with service trust (e.g., Lambda → S3) |
| “Human user needs AWS CLI access”              | IAM User with access keys                       |
| “Federated access via Google or AD”            | IAM Identity Provider                           |
| “Restrict what a user can do, even with Admin” | Permission Boundary                             |

### 🔒 IAM BEST PRACTICES (Exam-relevant)
- 🔒 MFA for all users (especially root).
- ❌ Do not use root account for daily operations.
- 🔑 Least Privilege – only grant needed permissions.
- 🔄 Use IAM Roles, not access keys for apps/EC2.
- 🛡️ Audit with IAM Access Analyzer, CloudTrail, or IAM Policy Simulator.


### ⏱️ TEMPORARY SECURITY CREDENTIALS (STS)
- AWS STS (Security Token Service) issues temporary tokens.
- Use cases:
  - Federation (AD, Cognito, SAML, etc.)
  - AssumeRole (cross-account access)
  - Mobile/Web apps (via Cognito)

🧠 Session duration:
- Can be configured (15 min – 12 hours depending on use case).
- Credentials include:
  - Access Key ID
  - Secret Access Key
  - Session Token

### ✅ IAM ROLES — DEVELOPER CONTEXT

| Use Case                   | How it works                                                         |
| -------------------------- | -------------------------------------------------------------------- |
| **EC2 Instance Role**      | Grants permissions via instance metadata endpoint `169.254.169.254`. |
| **Lambda Execution Role**  | Lambda assumes role to access AWS services.                          |
| **Cross-account Access**   | Role trust policy allows other account to assume it.                 |
| **Temporary Access**       | STS `AssumeRole` returns temporary credentials.                      |
| **Federated Access (SSO)** | IAM role mapped to external user identity.                           |

📌 No credentials stored. Secure and auditable.

📌 Trust policy defines who can assume the role.

### 👁️ POLICY STRUCTURE (JSON)
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow" | "Deny",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::mybucket/*"
    }
  ]
}
```
- Effect: Allow or Deny
- Action: API calls (e.g., s3:ListBucket)
- Resource: ARN of the resource
- Condition: Optional, adds constraints (e.g., IP, tag, MFA)

### 🔍 POLICY CONDITIONS
Add more control to permissions.

📌 Examples:
```
"Condition": {
  "IpAddress": {"aws:SourceIp": "203.0.113.0/24"},
  "Bool": {"aws:MultiFactorAuthPresent": "true"},
  "StringEquals": {"s3:x-amz-server-side-encryption": "AES256"}
}
```
🧠 Common exam condition keys:
- aws:SourceIp (IP restriction)
- aws:MultiFactorAuthPresent (MFA required)
- aws:username 
- aws:CurrentTime, aws:UserAgent

### 🔄 POLICY TYPES — COMPARISON
| Type                | Attached to                 | Reusable | Supports Conditions? |
| ------------------- | --------------------------- | -------- | -------------------- |
| Identity-based      | User, Group, Role           | Yes      | Yes                  |
| Resource-based      | Resource (S3, Lambda, etc.) | Yes      | Yes                  |
| Inline              | One identity only           | No       | Yes                  |
| SCP (Org-level)     | AWS Organizations           | Yes      | Yes                  |
| Permission Boundary | IAM entity (limits power)   | Yes      | Yes                  |
| Session Policy      | Temporary session tokens    | Yes      | Yes                  |

### 🧠 COMMON TRICK QUESTIONS
| Trick / Trap                            | Exam Tip                                                  |
| --------------------------------------- | --------------------------------------------------------- |
| User has admin policy but still blocked | Permission boundary or SCP in place                       |
| Lambda can't access S3                  | Check that the IAM **execution role** has `s3:*` perms    |
| EC2 instance needs S3 access            | Must attach **IAM Role** to EC2, not IAM User             |
| Root account use                        | Root is powerful — should be used **only in emergencies** |
| User can list but not write             | Likely missing `s3:PutObject` or wrong condition          |
| Policy allows but still denied          | Look for **explicit deny** somewhere else                 |
| Long-lived credentials in app           | Use **temporary creds (STS)** instead                     |
| Lambda reads from DynamoDB                    | **Execution Role**                                      |
| Cross-account access                          | **IAM Role + Trust Policy + STS AssumeRole**            |
| CLI user can't access S3                      | Check **user's IAM policy** and **credentials profile** |
| User gets too many permissions via AssumeRole | Use **Permissions Boundary** or **Session Policy**      |
| Need to limit max permissions of role         | **Permissions Boundary**                                |
| Sharing access to S3 bucket                   | **Resource-based policy**                               |

🧠 Always match use case → policy type → role setup → least privilege.

### 🔎 IAM & DEVELOPER SERVICES
- CodePipeline / CodeBuild: Use IAM Roles to define permissions. 
- Lambda: Execution Role controls AWS resource access. 
- Cognito: Federated identity pools issue temporary STS tokens. 
- S3 Pre-signed URLs: Work based on IAM permissions of signing entity.

### 🔐 IAM + Lambda
| Topic                     | Notes                                                                              |
| ------------------------- | ---------------------------------------------------------------------------------- |
| **Execution Role**        | Required for Lambda to access other AWS services.                                  |
| **Resource-based Policy** | Required if **other services** invoke the Lambda function (e.g., S3, EventBridge). |

🧠 Execution Role = what Lambda does, Resource-based Policy = who can invoke Lambda.


### 🧑‍💻 IAM + AWS CLI & SDK
| Concept                   | Description                                                            |
| ------------------------- | ---------------------------------------------------------------------- |
| **Authentication**        | CLI/SDK needs credentials via **access key + secret** or **IAM role**. |
| **Credentials File**      | Stored in `~/.aws/credentials`.                                        |
| **Profiles**              | Use named profiles: `aws configure --profile dev`.                     |
| **Temporary Credentials** | STS returns access key, secret key, and session token.                 |
| **Avoid hardcoding**      | Always prefer IAM roles or env variables over static credentials.      |
| **SDK Region**            | Must be specified (`AWS_REGION` or config file).                       |

🧠 Exam Tip: If the scenario involves automation tools, APIs, or CLI, expect a question about IAM permissions or credentials management.

### 🔧 HANDY AWS CLI COMMANDS
```
aws iam list-users
aws iam list-roles
aws sts assume-role --role-arn <ARN> --role-session-name session1
aws iam create-policy --policy-name <name> --policy-document file://policy.json
```



### ✅ QUICK RECAP (FOR LAST-MINUTE REVIEW)
| Topic              | Summary                                                |
| ------------------ | ------------------------------------------------------ |
| **IAM Users**      | Named entities with credentials                        |
| **IAM Roles**      | Assumed by users/services for temporary access         |
| **Policies**       | JSON docs to grant permissions                         |
| **Best Practices** | Least privilege, MFA, rotate creds, use roles          |
| **STS**            | Temp credentials (Cognito, AssumeRole, federation)     |
| **Trick Qs**       | Boundaries, SCPs, trust policies, deny overrides allow |






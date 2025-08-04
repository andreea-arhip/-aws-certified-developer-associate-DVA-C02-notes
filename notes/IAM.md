### 🔹 CORE COMPONENTS
| IAM Entity             | Description                                                                 |
| ---------------------- | --------------------------------------------------------------------------- |
| **Users**              | Individual accounts (human or app) with credentials                         |
| **Groups**             | Collections of users; attach policies to groups                             |
| **Roles**              | Temporary access via **assume role** (cross-account, services, federated)   |
| **Policies**           | JSON documents that define permissions (who can do what on which resources) |
| **Identity Providers** | Used for SSO (SAML 2.0, OIDC, Cognito, etc.)                                |

### 🔑 IAM POLICIES
- Identity-Based: Attach to user, group, or role.
- Resource-Based: Used with services like S3, Lambda, SQS, etc.
- Managed Policies:
  - AWS-Managed (reusable)
  - Customer-Managed (custom)
- Inline Policies: One-off, tightly coupled with a single identity.

Policy Evaluation Logic (Deny > Allow):
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
- Enable MFA for root and privileged users.
- Never use the root account for daily tasks.
- Use roles instead of users for apps or services.
- Use least privilege principle.
- Rotate credentials regularly.
- Use access advisor to clean up unused permissions.

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
- Used for assume role in trusted services or accounts.
- Common use cases:
  - Lambda assumes role to access S3
  - EC2 assumes role to access DynamoDB 
  - Cloud9 assumes role to deploy with CodeDeploy
- No credentials stored. Secure and auditable.

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

### 🔎 IAM & DEVELOPER SERVICES
- CodePipeline / CodeBuild: Use IAM Roles to define permissions. 
- Lambda: Execution Role controls AWS resource access. 
- Cognito: Federated identity pools issue temporary STS tokens. 
- S3 Pre-signed URLs: Work based on IAM permissions of signing entity.

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






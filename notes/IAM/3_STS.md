### 🧩 What is STS?
A global AWS service that issues temporary, limited-privilege credentials for AWS Identity Federation and IAM Role assumption.

### 📦 STS Core Use Cases
| Use Case                     | Example                                          |
| ---------------------------- | ------------------------------------------------ |
| 🧑‍💻 Assume IAM Role        | Lambda assumes a cross-account role to access S3 |
| 🧑‍🚀 Identity Federation    | Authenticate users via **Cognito**, SAML, OIDC   |
| 🧑‍🔧 Temporary Admin Access | Developer assumes `AdminRole` for 1 hour         |
| 🌍 Cross-Account Access      | App in Account A assumes role in Account B       |
| 🎭 Role Switching            | AWS Console / CLI role switch with STS           |

### 🔐 How STS Works (High-Level Flow)
1. Entity authenticates (IAM user, app, Cognito user, SAML/OIDC user).
2. STS issues temporary credentials (Access Key ID, Secret, Session Token).
3. Credentials last from 15 minutes to 12 hours (configurable).
4. Temporary credentials are used to call AWS services via SDK, CLI, or API.

### ⏱️ Types of Temporary Access
| Method                      | When to Use                           | Who Uses It       |
| --------------------------- | ------------------------------------- | ----------------- |
| `AssumeRole`                | Cross-account access, Lambda roles    | AWS services, IAM |
| `AssumeRoleWithWebIdentity` | Cognito, social login, OIDC           | Mobile/web apps   |
| `AssumeRoleWithSAML`        | Enterprise SSO integration            | SAML providers    |
| `GetSessionToken`           | MFA for IAM users                     | Console/CLI users |
| `GetFederationToken`        | Temporary limited access for IAM user | Less common now   |

🧠 Most exam-relevant: AssumeRole, AssumeRoleWithWebIdentity, AssumeRoleWithSAML

### 🧪 Exam Scenario Keywords
| If you see this...                             | Think...                                       |
| ---------------------------------------------- | ---------------------------------------------- |
| "Temporary credentials"                        | ✅ STS                                          |
| "Federated user needs access to S3/DynamoDB"   | ✅ STS via Identity Pool                        |
| "Mobile app authenticates via Facebook/Google" | ✅ STS via Cognito Identity Pool (Web Identity) |
| "App needs cross-account access"               | ✅ STS `AssumeRole`                             |
| "User authenticated via SAML"                  | ✅ STS `AssumeRoleWithSAML`                     |
| "Enable MFA for temporary access"              | ✅ STS `GetSessionToken`                        |

### 🔗 STS Integrations with Other Services
| Service                    | How it uses STS                                         |
| -------------------------- | ------------------------------------------------------- |
| **Cognito Identity Pools** | Uses STS to issue temporary AWS credentials after login |
| **IAM Roles**              | STS issues credentials when assuming a role             |
| **Lambda**                 | Uses STS under the hood when assuming execution role    |
| **Cross-account access**   | Always done via STS `AssumeRole`                        |

### 🔒 Security Tip
STS credentials include a session token, so SDKs/CLI tools must pass all 3 values:
Access Key + Secret Key + Session Token



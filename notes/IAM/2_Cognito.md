### 🧩 What is Cognito?
A user identity and authentication service to manage sign-up, sign-in, and access for web and mobile apps.

### 🏗️ Two Main Components
| Component          | Purpose                                               | Example Use                                           |
| ------------------ | ----------------------------------------------------- | ----------------------------------------------------- |
| **User Pools**     | **Authentication** – sign-up, sign-in, user directory | Login via username/password, MFA, social login        |
| **Identity Pools** | **Authorization** – get **temporary AWS credentials** | Access AWS resources (e.g., S3, DynamoDB) after login |

### 👥 Cognito User Pools – Deep Dive
| Feature                 | Description                                                                   |
| ----------------------- | ----------------------------------------------------------------------------- |
| Hosted UI               | Built-in login UI (customizable).                                             |
| Federation              | Supports **Social (Google, Facebook)** and **SAML** identity providers.       |
| MFA & Password Policies | Built-in support for **MFA**, password strength rules.                        |
| Triggers                | **Lambda functions** for custom logic (e.g., pre-sign-up, post-confirmation). |
| Tokens                  | Issues **JWT tokens**: ID token (user info), Access token, Refresh token.     |
| Groups                  | Organize users and manage permissions via **Cognito Groups**.                 |

🧠 JWT Tokens are NOT AWS credentials, only used for authentication.

### 🪪 Cognito Identity Pools – Deep Dive
| Feature                    | Description                                                                                               |
| -------------------------- | --------------------------------------------------------------------------------------------------------- |
| Authenticated Identities   | After logging in via **User Pool, SAML, or Social**, users can get **temporary IAM credentials** via STS. |
| Unauthenticated Identities | (Optional) allow **guest access** to AWS resources.                                                       |
| IAM Role Mapping           | Use token claims or groups to assign **different IAM roles** per user/group.                              |
| Federation Support         | Supports **Cognito User Pools**, **SAML**, **OIDC**, and **social identity providers**.                   |

🧠 Identity Pools = get AWS access, User Pools = log in and get user tokens.

### 🔐 Cognito Federated Identity Strategy
| Scenario                             | Service(s) Used                                    |
| ------------------------------------ | -------------------------------------------------- |
| Sign in users with email/password    | **Cognito User Pool**                              |
| Sign in with Google/Facebook         | **User Pool + Federation**                         |
| Access S3 after login                | **User Pool + Identity Pool + IAM Role**           |
| Allow guest (unauthenticated) access | **Identity Pool only**                             |
| Fine-grained AWS access by group     | Map **Cognito Group → IAM Role** via Identity Pool |

### 🔄 Cognito Integration with Other Services
| Use Case                      | Integration                              |
| ----------------------------- | ---------------------------------------- |
| Add login to web app          | **Cognito Hosted UI** or SDK integration |
| Protect API Gateway endpoints | Use **User Pool Authorizer**             |
| Custom logic during signup    | Use **Cognito Triggers + Lambda**        |
| Control AWS resource access   | Use **Identity Pool + IAM roles**        |

### 🧠 Exam Triggers & Tips
| Keyword in Question                   | Think...                        |
| ------------------------------------- | ------------------------------- |
| "Temporary AWS credentials"           | ✅ Identity Pool                 |
| "Access S3/DynamoDB after login"      | ✅ Identity Pool                 |
| "JWT token / ID token"                | ✅ User Pool                     |
| "Social login (Facebook, Google)"     | ✅ User Pool Federation          |
| "Lambda pre/post sign-up"             | ✅ User Pool Triggers            |
| "IAM roles mapped per group"          | ✅ Identity Pool Role Mapping    |
| "Anonymous guest access"              | ✅ Identity Pool Unauthenticated |
| "Protect API Gateway with user login" | ✅ User Pool Authorizer          |

### 🧪 Cognito vs IAM vs SSO – Know the Difference
| Feature                                 | Cognito               | IAM                    | AWS SSO |
| --------------------------------------- | --------------------- | ---------------------- | ------- |
| Users for your app                      | ✅                     | ❌                      | ❌       |
| Fine-grained access to AWS              | ✅ (via Identity Pool) | ✅                      | ✅       |
| Temporary AWS credentials               | ✅                     | ✅ (with STS)           | ✅       |
| Federation (Google, SAML, etc.)         | ✅                     | ✅ (complex setup)      | ✅       |
| MFA, password reset, email verification | ✅                     | ✅ (for console access) | ✅       |

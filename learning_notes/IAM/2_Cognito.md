### 🧩 What is Cognito?
A user identity and authentication service to manage sign-up, sign-in, and access for web and mobile apps.

### 🆚 Two Main Components - Cognito User Pools & Identity Pools

| Feature              | **User Pool**                                                   | **Identity Pool**                                                                                                         |
| -------------------- |-----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| **Purpose**          | Handles **AUTHENTICATION** (login/signup, password reset, MFA). | Handles **AUTHORIZATION** to AWS resources.                                                                               |
| **Who logs in?**     | Users of your app (email, phone, social, SAML, OIDC).           | Could be authenticated **via User Pool** OR **guest/unauthenticated** users.                                              |
| **What you get**     | A **JWT token** (ID & Access tokens) for your app.              | **Temporary AWS credentials** (STS) to access AWS services.                                                               |
| **When to use**      | When you need to manage users and their login flows.            | When you need those users to actually use AWS resources directly (S3, DynamoDB, etc.) without going through your backend. |
| **AWS service role** | Not directly linked to IAM roles.                               | Maps **user groups / auth state** to **IAM roles**.                                                                       |


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

### 🧩 How they work together
- User Pool = “Who are you?” → Authentication ✅
- Identity Pool = “What can you do in AWS?” → Authorization ✅
- In many apps:
  - User logs in via User Pool (gets JWT token). 
  - Token is passed to Identity Pool. 
  - Identity Pool exchanges it for temporary AWS creds (IAM role via STS).

---

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

### 🎯 Exam tips & scenarios
- User Pool only → If your app only needs authentication and talks to your backend (which then uses AWS resources).
- Identity Pool only → If you already have another auth system (Google, Facebook, SAML) and just want AWS credentials.
- Both → If you want Cognito login and direct AWS access from the client.
- Unauthenticated guests → Possible with Identity Pools (assign IAM role for unauthenticated).

### Identity Federation:
| Feature                       | Key Points                                                                                                                             | Exam Keyword → Answer                               |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| **SAML 2.0 Federation**       | Exchange workplace (corporate AD/SSO) credentials for **temporary AWS credentials** via STS. Works with **AWS Identity Center (SSO)**. | “Corporate login → AWS access” → **SAML + AWS SSO** |
| **AWS Identity Center (SSO)** | AWS’s main service for **workplace SAML federation**. Only supports **SAML 2.0**.                                                      | “Workplace + SAML 2.0” → **SSO**                    |
| **Amazon Cognito**            | For **non-workplace identities** (Google, Facebook, OAuth, social logins). Can also do **SAML 2.0** if AWS SSO isn’t in the answers.   | “Google login / OAuth” → **Cognito**                |
| **Exam Trick**                | If question mentions **SAML 2.0** but doesn’t list AWS SSO, the correct choice is **Cognito**. If **non-SAML** system → can’t use SSO. | Watch wording carefully!                            |

🎯 Memory Hooks
- Workplace login (AD, corporate SSO) → SAML → AWS SSO
- Google / OAuth / non-SAML → Cognito
- If SSO missing in answers but SAML is mentioned → Cognito


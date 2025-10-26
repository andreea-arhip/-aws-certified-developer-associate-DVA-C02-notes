## 🔑 KMS (Key Management Service)

- Symmetric Keys → AES-256 (default, encrypt/decrypt, SSE-KMS, EBS, RDS, S3, DynamoDB, Lambda env vars)
- Asymmetric Keys → For signing/verification, not general encryption
- CMKs (Customer Master Keys) →
  - AWS-managed (free, auto)
  - Customer-managed (you control, more flexibility, $1/month)
- Envelope Encryption → Data Key (used to encrypt data) encrypted by Master Key (performance + security)
- Key Policies → IAM must allow + KMS Key Policy must allow (dual permission model = exam trap)

| Feature                | Key Info                                                                                                                                                                                 | Exam Traps / Keywords                                                          |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **KMS Basics**         | Managed service to create and control **CMKs** (Customer Master Keys, now called KMS Keys). Handles encryption keys for AWS services & apps.                                             | “Managed keys”, “no need to store keys” → KMS                                  |
| **Types of Keys**      | **Customer-managed key (CMK)** = full control (policies, rotation, grants). <br> **AWS-managed key** = created automatically for you. <br> **AWS-owned key** = shared, you don’t see it. | “Fine-grained control” → customer CMK <br> “Default service key” → AWS-managed |
| **Encryption Types**   | **Envelope encryption**: Data encrypted with a **data key**; that key is encrypted by the **KMS key**.                                                                                   | “Large files” → use envelope encryption, not direct KMS                        |
| **Automatic Rotation** | Every 1 year for AWS-managed keys. <br> Customer-managed: optional enable (1 year).                                                                                                      | “Auto-rotated yearly” = AWS-managed                                            |
| **Cross-Region**       | Keys are **regional**. Cannot use same CMK in multiple regions.                                                                                                                          | “Encrypt in us-east-1, decrypt in eu-west-1” → ❌ not possible                  |
| **Grants**             | Temporary delegation for key usage without changing policy.                                                                                                                              | “Delegation to another AWS service” → use grants                               |
| **Integration**        | Used by S3, EBS, RDS, Lambda, Secrets Manager, etc.                                                                                                                                      | “Encrypt at rest” → always KMS                                                 |
| **SSE Options**        | - **SSE-S3** (AES-256, managed by S3) <br> - **SSE-KMS** (keys in KMS, logs in CloudTrail) <br> - **SSE-C** (customer provides key)                                                      | “Audit key usage” → SSE-KMS                                                    |
| **API Calls**          | Encrypt, Decrypt, GenerateDataKey.                                                                                                                                                       | “GenerateDataKey” → envelope encryption                                        |
| **CloudTrail**         | All KMS usage is logged in CloudTrail (who used the key, when).                                                                                                                          | “Audit access to encryption keys” → CloudTrail                                 |

### 🎯 Exam Scenarios to Remember
- Big file encryption? → Use KMS to generate data key (envelope encryption).
- Need audit of key usage? → Use SSE-KMS (CloudTrail logs every usage).
- Service encryption defaults? → S3, EBS, RDS can all use KMS CMKs.
- Cross-region decryption? → ❌ not supported (keys are regional).
- Delegate access temporarily? → Use Grants (not policies).

### 👉 Memory Hook:
Think of KMS as a locksmith:
- Locksmith (KMS key) doesn’t lock every door → it makes smaller keys (data keys) that do the job.
- The locksmith’s logbook = CloudTrail.
- You can ask the locksmith to give a friend a spare key for a while = Grants.
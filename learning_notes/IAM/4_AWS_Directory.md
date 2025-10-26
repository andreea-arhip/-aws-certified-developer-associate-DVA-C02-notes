## AWS Directory Services

| Service / Option             | What it is                                                                             | When to Use                                                                                                      | Example Exam Scenario                                                                             |
| ---------------------------- | -------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **AWS Managed Microsoft AD** | Full Microsoft Active Directory hosted by AWS (enterprise-grade).                      | If you need **real AD** in the cloud. Works with EC2, RDS (SQL Server), WorkSpaces, and integrates with on-prem. | “We want Windows EC2s and WorkSpaces in AWS to join a domain without running our own AD servers.” |
| **AD Connector**             | **Proxy/bridge** that forwards requests to your **on-prem AD**. No data stored in AWS. | If you already have **on-prem AD** and just want AWS resources to authenticate against it.                       | “We want AWS WorkSpaces users to log in with their existing corporate AD accounts.”               |
| **Simple AD**                | Low-cost, **Samba-based** directory. Lightweight alternative to Microsoft AD.          | For **small teams**, test/dev, or when you don’t need full AD features.                                          | “We want a cheap directory service to manage users/groups for dev environments.”                  |

### 🎯 Exam Pointers

- On-prem AD integration? → AD Connector.
- Enterprise-level, fully managed AD in AWS? → Managed Microsoft AD.
- Cheap, lightweight directory? → Simple AD (Samba).
- Windows EC2s auto-join a domain? → Managed Microsoft AD.
- RDS for SQL Server / WorkSpaces authentication? → Managed Microsoft AD.

### 👉 Think of it like car rentals:

- Managed Microsoft AD = luxury SUV (full-featured, expensive).
- AD Connector = Uber (uses your existing ride, just connects you).
- Simple AD = small city car (cheap, limited).
### 🧾 AWS CloudTrail – Who Did What?
| Feature                 | Description                                                   |
| ----------------------- | ------------------------------------------------------------- |
| **Event History**       | Record of every API call in your account (90-day default)     |
| **Trail**               | A configuration to send events to S3/CloudWatch               |
| **Logs**                | Include source IP, user identity, request params              |
| **Multi-Region Trails** | Capture across all regions (recommended)                      |
| **Data Events**         | Log S3 object/API-level access and Lambda invoke (extra cost) |

🧠 Exam Tips
- Use CloudTrail to audit, troubleshoot, or detect security issues.
- Data Events must be explicitly enabled (S3 access logs, Lambda calls).
- Events are eventually consistent (~15 mins delay).

### 🔍 CloudTrail Scenarios (Exam Focused)
| Scenario                             | Solution                       |
| ------------------------------------ | ------------------------------ |
| Find out who deleted an EC2 instance | CloudTrail Event Lookup        |
| Audit access to S3 bucket objects    | Enable **Data Events**         |
| Get alerts on unusual API activity   | CloudTrail + CloudWatch Alarms |
| Detect use of root account           | CloudTrail + SNS alert         |


### 🔄 Common Service Interactions
| Integration                | Use Case                                 |
| -------------------------- | ---------------------------------------- |
| CloudWatch + Lambda        | Alarm triggers automatic function        |
| CloudTrail + CloudWatch    | Detect root usage, send alert            |
| Config + SNS               | Alert when resource is non-compliant     |
| X-Ray + Lambda             | End-to-end latency trace                 |
| CloudWatch Logs + Insights | Troubleshoot log errors from Lambda, ECS |

### 🧠 Summary – When to Use What?
| Need                        | Use                          |
| --------------------------- | ---------------------------- |
| Real-time metrics & alerts  | **CloudWatch**               |
| Log query & dashboard       | **CloudWatch Logs/Insights** |
| API auditing (who did what) | **CloudTrail**               |
| Configuration compliance    | **AWS Config**               |
| Distributed tracing         | **AWS X-Ray**                |

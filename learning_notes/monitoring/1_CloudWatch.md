### 📊 Amazon CloudWatch – Core Monitoring Service
| Feature            | Description                                                   |
| ------------------ | ------------------------------------------------------------- |
| **Metrics**        | Data points for AWS resources (e.g., CPU, memory)             |
| **Custom Metrics** | You can push your own (e.g., app-level) metrics               |
| **Logs**           | Collect logs from EC2, Lambda, ECS, etc.                      |
| **Log Insights**   | Run queries on logs (great for debugging)                     |
| **Alarms**         | Trigger actions (SNS, Auto Scaling, etc.) based on thresholds |
| **Dashboards**     | Visualize metrics and alarms across regions                   |

🧠 Exam Tips
- CloudWatch does not store API activity logs (that’s CloudTrail).
- Use Logs + Metrics for real-time monitoring.
- Use Custom metrics for app-specific needs.

### 🧪 CloudWatch Exam Scenarios
| Scenario                               | Solution                                        |
| -------------------------------------- | ----------------------------------------------- |
| Alert when CPU > 80% for 5 mins        | CloudWatch Alarm                                |
| Monitor HTTP 500 errors in logs        | Log Insights                                    |
| Auto-restart EC2 if memory is too high | Custom metric + Alarm + Auto Recovery           |
| Schedule Lambda invocations            | **CloudWatch EventBridge Rule** (cron/interval) |


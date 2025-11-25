### 🧠  What Step Functions Are
- Think of Step Functions as a workflow manager for your AWS tasks.
- You design a state machine = a flowchart of steps.
- Each step can:
  - call a Lambda 
  - wait 
  - choose branches 
  - run tasks in parallel 
  - retry on errors 
  - catch errors and continue 
- Memory trick: ✨ Step Functions = “Lambda + Logic + Orchestration”.

### Two Step Functions Types (Important!)
- 1️⃣ Standard Workflows 
  - Long-running: up to 1 year 
  - Exactly-once execution 
  - Durable and highly reliable 
  - Higher cost 
  - 👉 Use for complex, long-running or business-critical workflows. 
- 2️⃣ Express Workflows 
  - High-throughput, short-lived 
  - Good for event-driven & streaming patterns 
  - Much cheaper 
  - Execution time: up to 5 minutes 
  - At-least-once semantics 
  - 👉 Use when you need speed + large volumes + cheap. 
- Exam Trick:
  - “Millions of events per second” → Express 
  - “Long process like approval workflow / ML training” → Standard

### Step Types (Must Know)
- ✅ Task State - Runs a unit of work (Lambda, ECS, DynamoDB, SNS, SQS, Glue…). 
- ✅ Choice State - If/Else logic. 
- ✅ Parallel State - Runs multiple branches at the same time. 
- ✅ Map State - Loop over a list; can run each iteration: sequentially or in parallel (Parallel Map)
- ✅ Wait State - Pause workflow (seconds or specific timestamp). 
- ✅ Pass State - Does nothing; passes input to output. 
- ❗ Catch/Retry - Defined at Task State level:
  - Retry → automatic retry with backoff 
  - Catch → handle failure & go to fallback state
- Exam trap: If you see “retry on Lambda throttling automatically”, that’s Step Functions Retry Policies.

### Input / Output Processing (Exam loves this)
- Every state can manipulate JSON using:
  - InputPath → what part of input to read 
  - ResultPath → where to inject result - merge result
  - OutputPath → what to pass forward 
- Exam trick: If seeing “remove unnecessary fields” → OutputPath

### Integrations (Super Important)
- AWS Step Functions integrates with over 220 AWS services without writing Lambda code.
- But for the exam, you need these:
  - 🔹 Direct Service Integrations (Preferred)
    - Run AWS APIs directly, no Lambda needed:
      - DynamoDB (PutItem, UpdateItem, Query)
      - SQS SendMessage 
      - SNS Publish 
      - Glue jobs 
      - ECS tasks (run/sync)
      - Batch jobs 
      - EventBridge 
      - API Gateway 
    - Exam keyword: “No need for Lambda → Step Functions direct integration”. 
  - 🔹 Lambda Integration - Used when:
    - transformation logic needed 
    - custom code needed

### Error Handling (MUST KNOW)
- Every state can define:
  - Retry 
    - retry on specific errors 
    - exponential backoff 
    - max attempts 
  - Catch - Catch errors and transition to fallback
- Exam trap: "If you need to rescue a failure and continue workflow → use Catch."

### Security
- Step Functions uses IAM Role to call other services.
- Workflow execution logs go to CloudWatch Logs. 
- State machine definitions can be encrypted with KMS.
- Exam key: “Step Functions needs permission to invoke Lambda” → Execution Role.

### Quick notes
- serverless workflow orchestration -> full event history, error handling, visual execution
- States: TASK (sync), CHOICE, WAIT, PARALLEL, MAP, PASS (fwd data), SUCCEED / FAIL (end execution)
- State data flow:
    - InputPath (filter input) → Parameters (prepare) → ResultPath (merge result) → OutputPath (filter output).
- Error handling in State machines:
    - Built-in error codes: "ErrorEquals": States.ALL, States.Timeout, States.TaskFailed, States.Permissions.
    - Use Retry (with IntervalSeconds, MaxAttempts, BackoffRate) → then Catch.
    - ResultPath → pass error/output to next state.
- Wait for Task Token:
    - task sends TOKEN to worker -> worker calls back SendTaskSuccess / SendTaskFailure with token
    - "Resource": "arn:aws:states:::sqs:sendMessage.waitForTaskToken" -> tells Step Functions to wait
    - Push-based (external app calls back)
    - Pull-based (worker polls with GetActivityTask) -> use HeartBeats to keep task alive + TimeoutSeconds
- Execution types:
    - Standard workflow:
        - exactly once execution, up to 1 year
        - 90 day history -> send to Cloudwatch for longer
        - supports human approval steps
        - support all service integrations, activities, and design patterns
    - Express workflow < 5min
        - ASYNC: At-least-once, may retry -> background / messaging tasks
        - SYNC: at-most-once, no retries -> request-response (API Gateway, Lambda)
        - History in CloudWatch only.
        - do not support activities, job-run (.sync), and Callback patterns.
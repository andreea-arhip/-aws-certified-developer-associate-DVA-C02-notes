## Lambda Execution Lifecycle

### 1️⃣ Init Phase (Cold Start)
- Happens when a new container (execution env) is created for the Lambda function.
- Steps:
  - Runtime initialization → Load the runtime (Node.js, Python, etc.). 
  - Global/static variable initialization → Your code outside the handler runs here. 
  - Init code can be reused for future invocations (good for DB connections, SDK clients).
- Adds to latency - Time spent here adds to cold start.
- 🧠 Put reusable setups (DB connections, SDK clients) outside handler.

### 2️⃣ Invoke Phase (Function handler execution)
- Lambda calls your handler function.
- Warm start → If environment is reused, only this phase runs.
- If async invocation (SNS, S3, EventBridge), Lambda queues and retries on failure.
- If poll-based (SQS, Kinesis), Lambda continuously polls and invokes handler when messages are found.

### 3️⃣ Freeze / Idle Phase
- After execution finishes, Lambda freezes the environment to save resources.
- Memory contents and global/static variables are preserved.
- No CPU is allocated during freeze.
- If invoked again before the environment is recycled → Warm start (faster, no re-init).

### 4️⃣ Reuse / Destroy
- AWS may reuse the same environment for multiple requests → keeps DB connections alive, caches warm.
- Environment is eventually destroyed (no guarantee on when).
- Code should not rely on warm state being present.

---

### 💡 Exam Tips

- Cold start: Occurs when Lambda needs a new execution environment (more common for rarely used functions, large packages, or VPC-configured Lambdas before SnapStart / Hyperplane ENIs).
- To reduce cold starts:
  - Keep package size small. 
  - Minimize init work. 
  - Use Provisioned Concurrency for predictable latency. 
- Global scope is key for reusing resources between invocations.

### ✅ Example Question:
"You have a Lambda that connects to an RDS database. You notice each invocation takes longer due to DB connection setup. How to fix?"

Answer: Initialize DB connection outside the handler so it’s created during Init Phase and reused during warm starts.


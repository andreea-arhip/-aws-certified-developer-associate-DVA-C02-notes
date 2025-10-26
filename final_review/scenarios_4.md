# AWS Certified Developer Associate — CI/CD, DevTools & IaC (Detailed Exam Scenarios)

## CodePipeline / CodeBuild / CodeDeploy

* **Detailed Exam-Oriented Scenarios & Patterns:**

  1. Blue/green Lambda deployment → safe release, rollback if failure. *Pattern:* Zero-downtime deployment.
  2. Canary deployment → partial traffic shift for testing. *Pattern:* Gradual release for risk mitigation.
  3. Cross-account deployment → role assumption. *Pattern:* Multi-account CI/CD.
  4. Pipeline triggers → GitHub push, CloudWatch Events. *Pattern:* Event-driven CI/CD.
  5. CodeBuild → build & test before deployment. *Pattern:* Continuous integration.
  6. Artifacts → S3 storage for deployment. *Pattern:* Centralized artifact repository.
  7. Manual approval step → prevent accidental promotion. *Pattern:* Controlled deployment.
  8. Rollback strategies → CloudFormation automated rollback. *Pattern:* Safe failure handling.
  9. Integration with SNS → notifications. *Pattern:* Monitoring CI/CD events.
  10. Multi-region deployment → HA & DR. *Pattern:* Global CI/CD strategies.
* **Traps:** Missing IAM roles, assuming rollback without configuration, exceeding parallel builds.

## SAM / CDK / CloudFormation

* **Detailed Exam-Oriented Scenarios & Patterns:**

  1. SAM → deploy serverless apps: Lambda + API Gateway + DynamoDB. *Pattern:* Serverless IaC.
  2. CDK → define infra as code, deploy via CloudFormation. *Pattern:* Code-driven infra.
  3. Nested stacks → modular infrastructure design. *Pattern:* Reusability & organization.
  4. Change sets → safe stack updates. *Pattern:* Preview updates before execution.
  5. Parameters → different environment configs. *Pattern:* Environment-specific deployments.
  6. Outputs → cross-stack references. *Pattern:* Inter-stack communication.
  7. Macros → dynamic template transformations. *Pattern:* Template flexibility.
  8. CI/CD integration → auto deployment. *Pattern:* Continuous deployment.
  9. Stack policies → protect resources during update. *Pattern:* Resource protection.
  10. Drift detection → detect manual changes. *Pattern:* Configuration integrity.

* **Traps:** Hardcoding values, ignoring dependencies, exceeding template limits.

* **Patterns & Best Practices:** Use IaC for serverless apps, separate dev/test/prod pipelines, minimal permissions testing first, automated rollback & notifications.

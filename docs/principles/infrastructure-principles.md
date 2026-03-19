# Infrastructure Principles

Version: v1.0

This document defines the organization's infrastructure standards and philosophy. It is input material for artifact generation within the Platform & Infrastructure Kit — not a governed artifact. Adapt this file to reflect your organization's actual infrastructure policies.

---

## 1. Infrastructure as Code

- All infrastructure must be defined in version-controlled, declarative configuration — not provisioned manually.
- Infrastructure definitions must be reproducible: applying the same configuration to an empty environment must produce an identical result.
- Infrastructure code must pass through the same review and approval process as application code.
- Drift between declared state and actual state must be detected and reconciled — manual modifications to running infrastructure must be back-ported or reverted.

## 2. Immutable Deployments

- Infrastructure components must be replaced, not patched in place — updates are delivered by deploying new instances and retiring old ones.
- In-place mutation creates configuration drift, makes rollback unreliable, and produces environments that cannot be reproduced from source.
- When immutable replacement is not feasible for a specific component, the exception must be documented with a justification and a compensating control (e.g., drift detection).
- Rollback must be achievable by redeploying the previous known-good version, not by reversing a sequence of mutations.

## 3. Environment Isolation

- Production environments must not share compute, storage, network, or identity infrastructure with non-production environments.
- Isolation prevents non-production activity (testing, development, experimentation) from affecting production availability, performance, or security.
- Access controls must enforce isolation boundaries — credentials that work in non-production must not work in production.
- When full physical isolation is not feasible, logical isolation with enforced resource quotas and separate access policies is the minimum acceptable standard.

## 4. Cost Visibility

- Every infrastructure resource must be attributed to a service, team, or cost center — unattributed resources are ungoverned spend.
- Cost attribution must be enforced through tagging, account structure, or resource naming conventions.
- Cost anomalies (unexpected increases or decreases) must be detected and investigated — they often indicate misconfiguration, scaling issues, or unused resources.
- Infrastructure decisions must include cost projections; capacity changes without cost impact analysis are incomplete.

## 5. Disaster Recovery Tested

- Disaster recovery plans must be validated through actual recovery exercises, not only documented as procedures.
- Recovery time objectives (RTO) and recovery point objectives (RPO) must be defined for every critical service and verified through testing.
- Recovery procedures that have never been exercised are aspirational — they are not reliable evidence that recovery will succeed.
- Recovery test results must be recorded, and gaps identified during testing must be remediated before the next planned exercise.

## 6. Least Privilege

- Infrastructure access must follow least-privilege by default — every identity receives only the permissions required for its function.
- Broad access (administrative, root, wildcard) must be the exception, not the baseline, and must require explicit justification.
- Access grants must be reviewed periodically — permissions that are no longer needed must be revoked.
- Service-to-service communication must be explicitly authorized; default-open network policies violate least privilege at the network layer.

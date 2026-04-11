# Infrastructure Specification

---

## 1. Document Control

| Field | Value |
|-------|-------|
| ISPEC ID | ISPEC-{PROJECT}-{NNN} |
| System Name | {system name} |
| Owner | {team or role responsible for infrastructure} |
| Version | v{N} |
| Status | Draft / Validated / Freeze Pending / Frozen |
| Scope | {which system(s) or service(s) this ISPEC governs} |
| PDR References | {list of frozen PDR IDs that inform this ISPEC} |
| Governance Model Version | 1.0 |
| Prompt Version | {prompt version} |
| Spec Version | {spec version} |
| Principles Version | {principles file versions} |

---

## 2. Compute Specification

| Field | Value |
|-------|-------|
| Compute Type | {e.g., EC2, ECS Fargate, Lambda, Kubernetes pods} |
| Instance/Container Size | {CPU: X vCPU, Memory: X GB} |
| Count (Minimum) | {N} |
| Count (Maximum) | {N} |
| Count (Desired) | {N} |
| Region | {e.g., us-east-1} |
| Availability Zones | {e.g., us-east-1a, us-east-1b, us-east-1c} |

**Additional Compute Resources (if applicable):**

| Resource | Type | Size | Purpose |
|----------|------|------|---------|
| {resource name} | {type} | {size} | {purpose} |

*If no additional compute resources, write: "No additional compute resources."*

---

## 3. Storage Specification

### Primary Storage

| Field | Value |
|-------|-------|
| Storage Type | {e.g., RDS PostgreSQL, S3, EBS, DynamoDB} |
| Size | {X GB/TB} |
| IOPS | {X IOPS or "Not applicable: {reason}"} |
| Data Classification | {PII / Financial / Logs / Application data / etc.} |

### Backup Policy

| Field | Value |
|-------|-------|
| Backup Frequency | {e.g., daily, hourly, continuous} |
| Retention Period | {e.g., 30 days, 90 days} |
| Backup Location | {e.g., cross-region S3 bucket, same-region snapshot} |

**Additional Storage (if applicable):**

| Storage Name | Type | Size | IOPS | Data Classification | Backup |
|-------------|------|------|------|--------------------|----|
| {name} | {type} | {size} | {IOPS} | {classification} | {backup summary} |

*If no additional storage, write: "No additional storage."*

---

## 4. Network Specification

### VPC / Network Design

| Field | Value |
|-------|-------|
| VPC CIDR | {e.g., 10.0.0.0/16} |
| Public Subnets | {CIDR ranges and AZs} |
| Private Subnets | {CIDR ranges and AZs} |

### Load Balancing

| Field | Value |
|-------|-------|
| Load Balancer Type | {e.g., ALB, NLB, API Gateway} |
| Health Check | {endpoint, interval, threshold} |
| Routing Rules | {path-based, host-based, etc.} |

### DNS

| Field | Value |
|-------|-------|
| Domain Name(s) | {domain names} |
| Record Type(s) | {A, CNAME, ALIAS, etc.} |
| TTL | {seconds} |

### Firewall / Security Groups

| Rule | Direction | Protocol | Port Range | Source/Destination | Purpose |
|------|-----------|----------|------------|--------------------|----|
| {rule name} | Ingress/Egress | {TCP/UDP/ICMP} | {port range} | {CIDR or security group} | {purpose} |

### Inter-Service Communication

{Method: service mesh / direct HTTP / message queue / gRPC / etc. Describe the communication pattern if multiple services exist.}

*If single service, write: "Single service — no inter-service communication."*

---

## 5. Scaling Rules

### Scale-Out Rules

| Metric | Threshold | Duration | Action |
|--------|-----------|----------|--------|
| {e.g., CPU utilization} | {e.g., > 70%} | {e.g., 5 minutes} | {e.g., add 2 instances} |

### Scale-In Rules

| Metric | Threshold | Duration | Action |
|--------|-----------|----------|--------|
| {e.g., CPU utilization} | {e.g., < 30%} | {e.g., 15 minutes} | {e.g., remove 1 instance} |

### Scaling Parameters

| Parameter | Value |
|-----------|-------|
| Minimum Instances | {N} |
| Maximum Instances | {N} |
| Cooldown Period (Scale-Out) | {seconds} |
| Cooldown Period (Scale-In) | {seconds} |

### Threshold Rationale

{Why were these specific thresholds chosen? Reference load testing data, historical patterns, or capacity planning analysis.}

---

## 6. Cost Estimate

| Category | Monthly Cost (Min Scale) | Monthly Cost (Max Scale) |
|----------|--------------------------|--------------------------|
| Compute | {$X} | {$X} |
| Storage | {$X} | {$X} |
| Networking | {$X} | {$X} |
| Other | {$X} | {$X} |
| **Total** | **{$X}** | **{$X}** |

**Assumptions:**

| Assumption | Value |
|------------|-------|
| Utilization Rate | {X%} |
| Data Transfer Volume | {X GB/month} |
| Request Volume | {X requests/month} |
| {other assumption} | {value} |

**Date of Estimate:** {YYYY-MM-DD}

---

## 7. Disaster Recovery

| Field | Value |
|-------|-------|
| RPO (Recovery Point Objective) | {e.g., 1 hour, 15 minutes, 0 (zero data loss)} |
| RTO (Recovery Time Objective) | {e.g., 4 hours, 30 minutes} |
| Failover Mechanism | {active-passive / active-active / cold standby / pilot light} |
| DR Testing Cadence | {e.g., quarterly, semi-annually} |

### Failover Procedure

{Description of the failover mechanism. How does traffic shift? What is automated vs. manual?}

### Backup Restoration

{Reference to or description of the backup restoration procedure. How long does restoration take? What is the tested restore time?}

---

## 8. Operational Notes

> **Advisory section** — Required by template structure; evaluated qualitatively. No hard gate applies. Validators note absence but do not block freeze.

**Known Limitations:**
- {limitation and planned resolution, if any}

**Planned Improvements:**
- {improvement and target timeline}

*If no notes, write: "No operational notes at this time."*

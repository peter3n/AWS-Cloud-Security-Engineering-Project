 # AWS Cloud Security Foundation

This system is built in AWS with Terraform and starts with seven components that piece together the cloud foundation for this project. The seven components are intentionally deployed in a weakened state for observation. The purpose is to understand the gaps and risks that could potentially be missed and deployed in a live production environment. The seven components consist of the following: VPC, KMS, IAM, S3 data bucket, Secrets Manager, CloudTrail, and CloudWatch. In the hardened environment, the VPC Endpoint SG only allows inbound traffic over HTTPS from 10.20.0.0/16 to future endpoints inside the VPC. IAM roles are scoped with policies that restrict overly permissive privileged actions. S3 buckets restrict access and protect stored data with SSE-KMS encryption. CloudTrail records activity and protects logs with SSE-KMS encryption. CloudWatch uses the flow logs to provide network visibility, combined with detection rules to identify unusual activity in future modules. Secrets Manager stores a generated encrypted secret that does not live on any source code. Customer-managed KMS encryption protects stolen data and automatic key rotation strengthens key management.

## Architecture Diagram

![AWS Cloud Security Foundation](diagrams/aws-cloud-foundation.png)

## Module 1 — Security Assessment & Hardening

### Security Assessment Results

The initial security assessment identified 14 findings across the AWS cloud foundation environment. After implementing and verifying the security hardening, the final assessment passed with zero findings.

| Assessment | Findings | Result |
|---|---:|---|
| Baseline | 14 | FAIL |
| Hardened | 0 | PASS |

[Baseline scan results](modules/01-cloud-foundation/evidence/baseline/baseline-scan.txt) | [Hardened scan results](modules/01-cloud-foundation/evidence/hardened/hardened-scan.txt)

### VPC — Security Hardening

**Security Group — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline VPC security group](modules/01-cloud-foundation/evidence/baseline/screenshots/vpc-inbound-rule.png) | ![Hardened VPC security group](modules/01-cloud-foundation/evidence/hardened/screenshots/vpc-sg-inbound-rule.png) |

**VPC Flow Logs — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline VPC Flow Logs](modules/01-cloud-foundation/evidence/baseline/screenshots/vpc-flow-logs.png) | ![Hardened VPC Flow Logs](modules/01-cloud-foundation/evidence/hardened/screenshots/vpc-flow-logs.png) |

VPC flow logs are configured for CloudWatch, adding network telemetry from the VPC. The SG has been tightened up with an inbound rule that is configured to only allow HTTPS traffic from the VPC CIDR 10.20.0.0/16. Only traffic already inside the VPC can reach future endpoints.

### IAM — Security Hardening

**Workload Role Permissions — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline IAM workload role](modules/01-cloud-foundation/evidence/baseline/screenshots/iam-workload.png) | ![Hardened IAM workload grant](modules/01-cloud-foundation/evidence/hardened/screenshots/iam-workload-grant.png) |

**Flow Logs Role — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline IAM Flow Logs role](modules/01-cloud-foundation/evidence/baseline/screenshots/iam-flowlogs.png) | ![Hardened IAM Flow Logs role](modules/01-cloud-foundation/evidence/hardened/screenshots/iam-flowlogs-deny-destructive-policy.png) |

**Workload Role — Destructive-Action Deny Policy**

![Workload role destructive-action Deny](modules/01-cloud-foundation/evidence/hardened/screenshots/iam-workload-deny-destructive.png)

IAM workload and flowlogs role destructive-action deny policy is set to all resources "*". Destructive actions like the deletion of a bucket or secret are not authorized. Workload grant permissions have been scoped down across S3, Secrets Manager, KMS, and CloudWatch Logs to specific resources. A compromised role with scoped permissions would limit what an attacker could do.

### S3 — Security Hardening

**Data Bucket Versioning — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline data bucket versioning](modules/01-cloud-foundation/evidence/baseline/screenshots/s3-data-bucket-versioning.png) | ![Hardened data bucket versioning](modules/01-cloud-foundation/evidence/hardened/screenshots/s3-data-bucket-versioning.png) |

**Data Bucket TLS-Only Policy — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline data bucket policy](modules/01-cloud-foundation/evidence/baseline/screenshots/s3-data-bucket-policy.png) | ![Hardened data bucket policy](modules/01-cloud-foundation/evidence/hardened/screenshots/s3-data-bucket-policy.png) |

**CloudTrail Bucket Versioning — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline CloudTrail bucket versioning](modules/01-cloud-foundation/evidence/baseline/screenshots/s3-cloudtrail-bucket-versioning.png) | ![Hardened CloudTrail bucket versioning](modules/01-cloud-foundation/evidence/hardened/screenshots/s3-cloudtrail-bucket-versioning.png) |

**CloudTrail Bucket TLS-Only Policy — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline CloudTrail bucket policy](modules/01-cloud-foundation/evidence/baseline/screenshots/s3-cloudtrail-bucket-policy.png) | ![Hardened CloudTrail bucket policy](modules/01-cloud-foundation/evidence/hardened/screenshots/s3-cloudtrail-policy.png) |

**Data Bucket SSE-KMS Default Encryption**

![Hardened S3 data bucket SSE-KMS encryption](modules/01-cloud-foundation/evidence/hardened/screenshots/s3-sse-kms.png)

Overwritten and intentionally or accidentally deleted data in the S3 data and CloudTrail buckets can now be recovered because bucket versioning is enabled. The TLS-only bucket policy denies unencrypted HTTP traffic, protecting all S3 data for both buckets during transit. SSE-KMS encryption is the new default encryption for the data bucket, adding key-access controls and KMS key-usage auditing for S3 data.

### KMS — Security Hardening

**Automatic Key Rotation — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline KMS key rotation](modules/01-cloud-foundation/evidence/baseline/screenshots/kms-key-rotation.png) | ![Hardened KMS key rotation](modules/01-cloud-foundation/evidence/hardened/screenshots/kms-key-rotation.png) |

**Hardened KMS Key Policies**

![Hardened KMS key policies](modules/01-cloud-foundation/evidence/hardened/screenshots/kms-key-policies.png)

KMS automatic key rotation is now enabled and the rotation period is 365 days, reducing long-term risks. Workload role gets a key policy grant for workload use. CloudTrail gets a key policy grant that's required for encrypted audit log delivery.

### CloudTrail — Security Hardening

**Trail Configuration — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline CloudTrail configuration](modules/01-cloud-foundation/evidence/baseline/screenshots/cloudtrail-general.png) | ![Hardened CloudTrail configuration](modules/01-cloud-foundation/evidence/hardened/screenshots/cloudtrail-general-details.png) |

**S3 Object-Level Data Events — Before vs. After**

| Baseline | Hardened |
|---|---|
| ![Baseline CloudTrail data events](modules/01-cloud-foundation/evidence/baseline/screenshots/cloudtrail-data-events.png) | ![Hardened CloudTrail data events](modules/01-cloud-foundation/evidence/hardened/screenshots/cloudtrail-data-events.png) |

All four CloudTrail security features are enabled now. Multi-region trail logging captures audit events in all AWS regions. Audit log integrity can be verified to detect tampering. Trail logs are SSE-KMS encrypted, adding key access controls and KMS key-usage auditing. S3 object-level data read and write events are now being recorded, adding activity visibility.

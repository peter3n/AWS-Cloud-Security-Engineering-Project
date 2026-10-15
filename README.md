 # AWS Cloud Security Foundation

This system is built in AWS with Terraform and starts with seven components that piece together the cloud foundation for this project. The seven components are intentionally deployed in a weakened state for observation. The purpose is to understand the gaps and risks that could potentially be missed and deployed in a live production environment. The seven components consist of the following: VPC, KMS, IAM, S3 data bucket, Secrets Manager, CloudTrail, and CloudWatch. In the hardened environment, the VPC Endpoint SG only allows inbound traffic over HTTPS from 10.20.0.0/16 to future endpoints inside the VPC. IAM roles are scoped with policies that restrict overly permissive privileged actions. S3 buckets restrict access and protect stored data with SSE-KMS encryption. CloudTrail records activity and protects logs with SSE-KMS encryption. CloudWatch uses the flow logs to provide network visibility, combined with detection rules to identify unusual activity in future modules. Secrets Manager stores a generated encrypted secret that does not live on any source code. Customer-managed KMS encryption protects stolen data and automatic key rotation strengthens key management.

## Architecture Diagram

![AWS Cloud Security Foundation](diagrams/aws-cloud-foundation.png)

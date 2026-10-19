# Hardened IAM Review
IAM workload and flowlogs role destructive-action deny policy is set to all resources "*". Destructive actions like the deletion of a bucket or secret are not authorized. Workload grant permissions have been scoped down across S3, Secrets Manager, KMS, and CloudWatch Logs to specific resources. A compromised role with scoped permissions would limit what an attacker could do.

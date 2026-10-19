# Baseline IAM Review
The IAM role workload is set with wildcard allow permissions across S3, secretsmanager, KMS, bedrock, and logs. There is no deny destructive policy set for either workload or the flowlogs role. A compromised app with a role that has overly broad permissions like workload without explicit deny policies could lead to the deletion and modification of important resources.

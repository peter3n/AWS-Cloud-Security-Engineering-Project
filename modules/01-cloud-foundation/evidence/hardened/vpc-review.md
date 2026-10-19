# Hardened VPC Review
VPC flow logs are configured for CloudWatch, adding network telemetry from the VPC. The SG has been tightened up with an inbound rule that is configured to only allow HTTPS traffic from the VPC CIDR 10.20.0.0/16. Only traffic already inside the VPC can reach future endpoints.

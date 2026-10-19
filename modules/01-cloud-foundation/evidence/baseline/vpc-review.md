# Baseline VPC Review
There's a security group configured with an inbound rule open to any HTTPS traffic from any IP. If public network connectivity exists, future endpoints connected to this SG would be subject to outside network reachability. VPC flow logs are not configured so any network-flow telemetry within the VPC would not be recorded.

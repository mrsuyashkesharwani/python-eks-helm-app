# DESIGN.md

## Observability Strategy for Python EKS App

This document outlines a production-grade observability strategy for our containerized Python application deployed on Amazon EKS using AWS-native tooling.

## Metrics Strategy

Tool: Amazon CloudWatch Container Insights

Why:

* Fully integrated with EKS and other AWS services
* Requires minimal setup with aws-for-fluent-bit or CloudWatch Agent

Top 5 Key Metrics:

1. Pod CPU and Memory Usage

   * Alert if CPU usage exceeds 80% for more than 5 minutes

2. Application HTTP Request Latency

   * Alert if the 95th percentile latency exceeds 1 second for 5 minutes

3. HTTP Error Rates (4xx and 5xx)

   * Alert if the error rate is higher than 5% of total requests

4. Pod Restarts

   * Alert if the restart count exceeds 3 within 10 minutes

5. Replica Count vs Desired Count

   * Alert if under-replicated for more than 2 minutes

## Logging Strategy

Tool: Amazon CloudWatch Logs

Flow:

1. Use CloudWatch Agent or aws-for-fluent-bit DaemonSet to collect logs from all pods.
2. Logs are automatically pushed to CloudWatch log groups, separated by namespace or pod name.
3. Use CloudWatch Log Insights for log filtering and searching.

Important Log Fields:

* timestamp
* pod\_name
* container\_name
* namespace
* log\_level

## Security Considerations

* Restrict CloudWatch log access using IAM policies
* Enable encryption at rest and in transit
* Limit log retention according to compliance and cost management policies

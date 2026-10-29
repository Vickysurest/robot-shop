# Payment Error Budget Policy

## Service and objective

- **Service:** `payment` — checkout depends on it.
- **SLI:** Share of time that at least one payment pod is healthy.
- **SLO:** 99.5% availability over 30 days.

## Error budget

Thirty days is 43,200 minutes. At 99.5% availability, the allowed downtime is:

```text
43,200 × (1 − 0.995) = 216 minutes
| SLO | Allowed downtime in 30 days |
|---|---:|
| 99% | 432 minutes |
| 99.5% | 216 minutes |
| 99.9% | 43.2 minutes |

Measurement
Prometheus tracks available payment replicas with kube_deployment_status_replicas_available in the robot-shop namespace. PaymentUnavailable fires when the payment deployment has zero available replicas for more than one minute and routes to Slack.
Error budget policy
Review the remaining budget after incidents. If the budget is nearly exhausted, prioritize reliability work and avoid risky payment changes until availability improves. Record outages and budget impact in the incident postmortem.
Lab measurement limitation
The lab Prometheus configuration retains data for 6 hours, so it cannot calculate a complete rolling 30-day availability SLI. The 216-minute budget is the 30-day target allowance; use recorded incident durations for this lab’s estimate.
# Incident Drill Postmortem: Payment Unavailable

## Summary

During a planned incident drill, the payment deployment was scaled to zero, making checkout unavailable. Payment was scaled back up, and the resolved alert arrived in Slack.

## Impact

The payment service had no available replicas during the drill, so customers could not complete checkout. We use the resolved notification time as the conservative recovery confirmation: 31 minutes 44 seconds elapsed from drill start to that confirmation.

## Timeline (UTC)

**October 29, 2026**

- 15:10:36 — Payment outage drill started.
- 15:11:46 — The alert condition was visible in Prometheus.
- 15:38:04 — Slack outage alert arrived in `#philanthropy`.
- 15:40:16 — Recovery was initiated by scaling payment back up.
- 15:42:20 — Slack resolved alert arrived; this is the recorded recovery confirmation.

## Root cause

This was a planned drill, not an unplanned production failure.

1. Why could customers not check out? Payment had no available replicas.
2. Why were there no available replicas? The deployment was intentionally scaled to zero for the drill.
3. Why did scaling to zero affect checkout? The application requires the payment service to complete checkout.
4. Why was the issue detected? Prometheus monitors payment's available replicas and raised `PaymentUnavailable`.
5. Why was notification delayed? The Slack webhook configuration had been malformed earlier; replacing the webhook secret restored delivery.

## What went well, what went badly, where we got lucky

- Went well: Payment recovered after scaling it back up, and the resolved notification reached Slack.
- Went badly: The Slack alert arrived substantially later than the Prometheus alert condition appeared.
- Got lucky: This was a controlled drill, so the outage did not affect real customers.

## Action items

| Type | Action | Owner | Due date |
|---|---|---|---|
| Detect | Measure time from Prometheus firing to Slack delivery during a follow-up test. | Victor Osundele | October 30, 2026 |
| Prevent | Keep the Slack webhook in a Kubernetes Secret and avoid copying it into chat or repository files. | Project owner | October 30, 2026 |
| Mitigate | Keep the payment recovery steps in this runbook and verify the rollout after scaling up. | Project owner | October 30, 2026 |

## Error budget consumed

Using the conservative interval from drill start to resolved Slack confirmation:

- Elapsed time: 31 minutes 44 seconds = 31.73 minutes
- Monthly budget: 216 minutes
- Calculation: 31.73 ÷ 216 × 100 = **14.69%**
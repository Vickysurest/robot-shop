# Runbook: Robot Shop payment unavailable

## Alert

Payment has had no available replicas for at least two minutes.

## Check the deployment

```bash
kubectl -n robot-shop get deployment payment
kubectl -n robot-shop get pods -l service=payment
kubectl -n robot-shop describe deployment payment
kubectl -n robot-shop get events --sort-by=.lastTimestamp
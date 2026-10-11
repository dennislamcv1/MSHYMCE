# Operational Runbook — Edge Inference Pipeline

Audience: distribution-center operations staff (no raw kubectl). Platform-team-only steps are marked **[PLATFORM]**.

## SLA / Target Reference (fill before use)

| Metric | Target |
|---|---|
| Rollout success rate (fleet-wide) | TODO % |
| Sync window after reconnection | TODO minutes for buffer to fully drain |
| MTTR — single-site incident | TODO minutes |
| MTTR — fleet-wide incident | TODO hours |
| Max concurrent site incidents before escalation | TODO |
| Inference latency p95 threshold | < 200 ms |

## 1. Health Check (run first, every time)

- [ ] Open telemetry dashboard → site status panel.
- [ ] Confirm inference latency p95 < 200 ms. If above, go to §3.
- [ ] Confirm buffer utilization < 80% of 5 GB reserved. If above, go to §3.
- [ ] Confirm last successful sync timestamp < TODO minutes ago.

## 2. Trigger a Rollout

- [ ] Confirm change is merged to the GitOps release branch (Flux v2 AKS extension reconciles automatically — do not push manually to the cluster).
- [ ] Watch rollout status on the dashboard (Canary: Flagger analysis panel / Blue-Green: traffic-weight panel).
- [ ] Verify success criteria: TODO (e.g., error rate < X% over Y minutes).

## 3. Troubleshoot Common Failures

| Symptom | Likely cause | Operator action |
|---|---|---|
| Latency p95 above threshold | Device resource contention | Restart inference module via operator dashboard action (not kubectl) |
| Buffer near capacity | Extended outage beyond 72h | Escalate to platform team **[PLATFORM]** |
| Sync not resuming after reconnect | Idempotency key collision / route misconfig | Escalate to platform team **[PLATFORM]** |
| Rollout stuck in Canary analysis | Metric threshold breach | Automatic rollback triggers; confirm via dashboard, no operator action needed |

## 4. Rollback

- [ ] Identify last known-good release tag in GitOps release history.
- [ ] Trigger rollback via git revert on the release branch (Flux reconciles automatically) — operator-facing action, no kubectl.
- [ ] Confirm health check (§1) passes post-rollback.
- [ ] If rollback does not resolve within TODO minutes, escalate **[PLATFORM]**.

## 5. Escalation Path

1. Operator → Site Lead (immediate, if health check fails twice in a row)
2. Site Lead → Platform Team on-call (if §3/§4 doesn't resolve within MTTR target)
3. Platform Team → raw kubectl / cluster-level diagnostics (platform-only, never operator-facing)

## 6. [PLATFORM] Diagnostic Commands (not for operator use)

```bash
# TODO — platform-team-only kubectl/az diagnostics
kubectl get pods -n edge-inference
az iot hub device-identity connection-string show --device-id TODO
```

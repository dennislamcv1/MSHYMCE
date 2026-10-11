# Scenario Data Package — Edge Inference Pipeline (Package Damage Detection)

## 1. Site Hardware Profile

| Parameter | Value |
|---|---|
| Sites | 50 distribution centers |
| Edge compute | Ruggedized industrial PC, 4-core x86_64, 2 GB RAM, 32 GB eMMC storage |
| GPU/NPU | None (CPU-only inference — informs runtime choice) |
| OS | Linux (Ubuntu Server 22.04 LTS or Azure Linux 3.0) |
| Camera feeds per site | 4–8 fixed cameras on conveyor lines |
| Local network | Wired LAN to edge device; site-to-cloud uplink separate (see §2) |
| Storage partition reserved for buffer | 8 GB dedicated volume, separate from OS/module storage |

## 2. Network Profile

| Parameter | Value |
|---|---|
| Primary uplink | MPLS/wired, ~10 Mbps guaranteed |
| Backup uplink | LTE cellular failover |
| Average outage duration | 45 minutes |
| Worst-case outage duration (99th percentile) | 4 hours |
| Extended outage ceiling (design target) | 72 hours (storm/carrier-outage scenarios) |
| Outage frequency | ~2–3 events/site/month during peak season |

## 3. Model Specs

| Parameter | Value |
|---|---|
| Task | Binary + multi-class package damage detection (object detection) |
| Candidate architectures | YOLO-family detector, exported to ONNX (fp16) or LiteRT (int8 quantized) |
| Model size (ONNX fp16) | ~25 MB |
| Model size (LiteRT int8) | ~7 MB |
| Target inference latency (p95) | < 200 ms per frame on target hardware |
| Accuracy baseline (pre-deployment eval) | mAP@0.5 ≥ 0.88 on holdout set |

## 4. Volume Estimates

| Parameter | Value |
|---|---|
| Packages/site/day (peak season) | ~8,000 |
| Inference events/site/day | ~8,000 (1 per package pass) |
| Avg payload per event (image + result JSON) | ~50 KB |
| Daily data volume/site | ~400 MB |
| 72-hour buffer requirement/site | ~1.2 GB (with 4x safety margin → 5 GB minimum reserved) |
| Fleet-wide daily sync volume (50 sites, all online) | ~20 GB/day |

## 5. Timeline Constraint

- Peak season start: 8 weeks from design kickoff.
- Zero-downtime update requirement applies from week 6 onward (freeze window during peak).

# Plan

## Pitch
Don't build a better detector. Cut what a human must review. Headline number: **review-queue size at fixed recall**.

## Problem
At 1 anomaly per 1,000 events, a 99%-accurate detector floods analysts with false positives. Guard stops looking. Real threat missed.

## Solution
Triage layer on top of pretrained YOLO. Embed crops → compare to "normal" bank → rank by novelty × persistence × zone sensitivity. Dismissals mute look-alikes via vector match. No retraining.

## Build (5 steps)
1. **Dilute data** — Anomalies (UCSD Ped2, CUHK Avenue, ShanghaiTech) mixed into normal footage NOT in the normal bank. Target 1:1,000+ base rate. Measures real flooding, not claimed.
2. **Naive baseline** — Pretrained YOLO alerts on every detection. Log alerts/hr. This is the flood.
3. **Triage** — Track objects. Embed crops (CLIP/DINOv2) + speed + direction. Store normal embeddings in FAISS. Novelty = distance to nearest normal. Merge track repeats. Rank by `novelty × persistence × zone`.
4. **Feedback loop** — One-tap dismiss → embedding to suppression bank. Look-alikes muted ("matches N dismissed"). No retraining. Simulated analyst dismisses first 100, log alerts/hr falling.
5. **Dashboard** — Left: naive flood. Right: triage queue (clip + "why flagged" line from features, not LLM). Live counters + audit log.

## Report 3 Numbers
- Alerts/hr: naive vs triage
- Event recall within fixed review budget (top 30 alerts/hr)
- Analyst-minutes per true event (assume 30s/alert)

## Pitch Delivery
- Open with base-rate math, then the numbers.
- Adopter: ISR/control-room. Shadow mode on archived footage beside analysts — less approval than live ops.
- All offline, local models. Public benchmark. Feedback loop = how real labels accumulate.
- Drone/satellite angle: same loop on LEVIR-CD/OSCD image tiles. Suppress benign change (shadows, seasons).

## Hard Rules
- No "accuracy" claims.
- No training big models.
- No live inference on stage.
- Precompute detections — demo cannot fail.
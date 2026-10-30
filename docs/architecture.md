# Architecture

```text
Normal footage ──> detections / crops ──> embeddings ──> normal FAISS index
Evaluation footage ──> saved YOLO detections ──> naive alert queue
                              │
                              └──> tracking ──> crops + motion features
                                                   │
Normal index ──────────────────────────────────────┤
                                                   v
                                    novelty + persistence + zone
                                                   │
                                     merge and rank track alerts
                                                   │
Dismissals ──> suppression bank ──> similarity match / audit log
                                                   │
                                      triage queue and dashboard
```

All processing is offline. Detections are saved before the demo; pretrained models run locally and are not retrained. Embedding model, tracker, feature normalization, zone definitions, and suppression threshold are implementation decisions to validate.

## Component boundaries

- **Ingestion:** read footage and annotations, prepare the normal bank and evaluation stream, and preserve split provenance.
- **Baseline:** turn saved YOLO detections into individual alerts.
- **Tracking:** link detections over time and derive persistence, speed, and direction.
- **Embeddings:** embed crops and query the normal FAISS index.
- **Triage:** merge repeated track alerts, combine scoring features, and rank the queue.
- **Feedback:** store dismissals, identify similar alerts, and record suppression decisions.
- **Dashboard:** display both queues, clips, explanations derived from features, counters, and the audit log.

The evaluation code should consume saved queue and annotation records, rather than depend on dashboard state. Record IDs and timestamps must make each displayed alert traceable to its detections, track, source footage, and evaluation event where applicable.

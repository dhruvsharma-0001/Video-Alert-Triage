# Implementation phases

These phases turn the plan into a reproducible offline comparison. Dataset selection, model choices, and scoring parameters remain open until validated against the available footage and hardware.

## 1. Data and evaluation setup

- Select and document benchmark footage, event annotations, and dataset access requirements.
- Define separate footage for the normal bank and evaluation; construct a diluted evaluation stream targeting at least 1 anomaly per 1,000 events.
- Define what counts as an event and how detections match annotated anomalies.
- Record stream duration, base rate, splits, and a reproducible sampling seed.

**Done when:** the evaluation stream and normal-bank inputs can be reproduced, and their separation and base rate can be checked.

## 2. Naive baseline

- Run pretrained YOLO offline and save detections and timestamps.
- Create an alert for every detection and measure alerts per hour and event recall.

**Done when:** saved detections can reproduce the baseline queue and its metrics without live inference.

## 3. Triage pipeline

- Associate detections into tracks and extract crop embeddings and motion features.
- Build a FAISS index from normal-bank embeddings and calculate nearest-normal novelty.
- Merge repeated track alerts and rank candidates using novelty, persistence, and zone sensitivity.
- Store the features needed to explain each ranking.

**Done when:** a reproducible ranked queue and explanations can be generated from precomputed inputs.

## 4. Feedback simulation

- Save dismissed embeddings in a suppression bank and match future look-alikes.
- Simulate dismissals of the first 100 eligible alerts, recording suppression decisions and queue volume over time.
- Preserve an audit record of dismissals and matches.

**Done when:** the simulation can be replayed and its effect on both workload and recall measured.

## 5. Dashboard and demo

- Show the naive queue beside the triage queue, with clips, feature-based explanations, counters, and an audit log.
- Use precomputed artifacts for the demo; keep models and data local.
- Present the three planned workload and recall measures and their assumptions.

**Done when:** the comparison can be demonstrated offline without live inference and the displayed numbers can be traced to evaluation artifacts.

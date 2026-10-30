# Two-person build plan

This plan divides the offline alert-triage prototype between the **AI owner** (you) and the **backend owner** (one person). Both work toward the same outcome: a reproducible comparison of the naive alert queue and the triage queue, including workload and event recall.

## Responsibilities

| Area | AI owner | Backend owner |
| --- | --- | --- |
| Data and evaluation | Select benchmark footage and annotations, define event matching and splits, and prepare a reproducible evaluation stream. | Define storage and artifact organization; load and serve footage, annotations, and generated results. |
| Detection and baseline | Run pretrained YOLO offline; produce detections and define the naive alert generation rules. | Ingest saved detections and expose the naive queue and footage to the dashboard. |
| Tracking and triage | Choose and run tracking and embeddings; build the normal index, novelty scoring, feature combination, and track merging. | Build the pipeline interface and persist and serve ranked alerts, scores, and explanations. |
| Feedback | Define embedding similarity and suppression rules; simulate dismissals and measure their effect on recall. | Implement dismissal actions, suppression records, and the audit log; connect them to the AI similarity workflow. |
| Metrics and demo | Compute and validate the evaluation metrics and document assumptions. | Display queues, clips, counters, and audit records using precomputed artifacts; package the offline demo. |

Each owner is responsible for checking their own output. Agree on formats and acceptance criteria together before implementation. The backend owner also owns the dashboard integration in this initial two-person split; keep the interface minimal.

## Shared handoff contract

Start with simple local files and version the schema before choosing a database or API. Agree on IDs, timestamp units, paths, and missing-value handling. The following fields are a starting point, not a final schema:

- **Footage and events:** `video_id`, source path, duration, split, and annotation event ID and time range.
- **Detection:** `detection_id`, `video_id`, timestamp or frame, bounding box, class, and confidence.
- **Track:** `track_id`, source detection IDs, start and end times, and motion features.
- **Alert:** `alert_id`, source video and time range, detection or track IDs, queue type, rank, score components, explanation, and clip reference.
- **Feedback:** dismissal ID, alert ID, time or simulation step, matched dismissal IDs, similarity, suppression decision, and reason.
- **Evaluation run:** run ID, input and model versions, configuration, seed, metrics, and output artifact paths.

The AI owner supplies sample artifacts and documents how to regenerate them. The backend owner validates and consumes those artifacts, then reports schema or integration issues. Keep raw embeddings and index storage behind a documented interface rather than requiring the dashboard to understand them.

## Build order and handoffs

### 1. Agree on data and a thin end-to-end example

**AI owner:** Pick an initial public benchmark and inspect its annotations and access terms. Define the event unit, separate normal-bank and evaluation footage, and create a small sample with known anomalies. Document the sampling seed and the actual base rate; expand toward the 1:1,000 target only when enough normal data is available.

**Backend owner:** Set up the project runtime and local artifact directories. Load a sample video and annotation record and serve or display a clip from its time range.

**Together:** Agree on the handoff contract, event matching rules, and what constitutes one alert. The first handoff is a sample video, annotations, and a manifest that the backend can load.

**Done when:** both can reproduce the same sample and trace an annotation to the correct footage.

### 2. Produce the naive queue

**AI owner:** Run pretrained YOLO offline on the sample and export timestamped detections. Define one alert per detection and calculate initial alerts per hour and event recall.

**Backend owner:** Import the detection records and show the naive queue with clip references. Preserve source IDs so displayed alerts can be traced back to detections.

**Done when:** the dashboard count matches the exported queue and the baseline can be regenerated without live inference.

### 3. Build and integrate triage

**AI owner:** Track detections, extract crop embeddings and motion features, build the normal FAISS index, and score novelty, persistence, and zones. Merge repeated track alerts and export a ranked queue with feature-derived explanations. Document model versions, feature scaling, and parameters.

**Backend owner:** Import and display the triage queue alongside the baseline. Provide clip playback and expose score components and explanations for each alert.

**Done when:** a saved run reproduces the ranking and every displayed triage alert traces to its track and source footage.

### 4. Add feedback and evaluation

**AI owner:** Define and implement the dismissal similarity rule and simulated analyst policy. Run the first 100 eligible dismissals where available, track suppressed matches, and evaluate workload and event recall before and after feedback.

**Backend owner:** Add dismissal controls and persist the dismissal and suppression audit trail. Connect the controls to the documented similarity interface or load a precomputed simulation for the demo.

**Together:** Verify that a dismissal, its matches, and their effects on the queue can be replayed. Decide how suppressed alerts count toward the review budget before reporting results.

**Done when:** the feedback history and metric changes are reproducible, including any lost anomaly events.

### 5. Prepare the offline demo

**AI owner:** Run the full evaluation and supply results for alerts per hour, event recall at 30 alerts per hour, analyst-minutes per true event, and queue size at a shared recall target. Include definitions, counts, and limitations.

**Backend owner:** Package the dashboard and precomputed data for offline use. Show both queues, clips, explanations, counters, and the audit log; verify the displayed values against the evaluation artifacts.

**Together:** Rehearse using the saved artifacts and check that the comparison and assumptions are clear. Do not use live inference on stage or claim detector accuracy.

## Immediate tasks

1. Together, choose the first dataset and agree on the event and alert definitions.
2. AI owner: create a small manifest, annotation sample, and saved YOLO detection example.
3. Backend owner: set up a local app that reads those sample records and displays a video clip and naive alert list.
4. Together, verify IDs, timestamps, and counts before extending the pipeline.

See [phases](phases.md) for milestone criteria and [evaluation](evaluation.md) for the measurement decisions that must be settled before results are reported.

# Video alert triage

An offline prototype for reducing the number of video alerts a human must review while retaining anomalous events. The system compares a naive YOLO detection queue with a ranked, deduplicated queue built from novelty, persistence, and zone sensitivity.

The prototype is organized around precomputed detections and local models. Its primary measure is review-queue size at a fixed recall; evaluation also reports event recall within a fixed review budget.

## Project map

- `docs/` — product plan, phased roadmap, architecture, and evaluation protocol.
- `src/` — placeholders for the processing pipeline and dashboard.
- `data/` — guidance for local datasets and generated artifacts.
- `scripts/` — entry point for future preparation and evaluation scripts.
- `tests/` — place for pipeline and evaluation tests.

Start with [the plan](docs/plan.md), then follow [the phases](docs/phases.md). This is an initial scaffold; the pipeline and dashboard are not implemented yet.

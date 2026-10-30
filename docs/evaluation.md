# Evaluation

Compare the naive and triage queues on the same held-out evaluation stream. Keep normal-bank footage separate from evaluation footage and document how anomalies and normal footage were mixed. The plan targets a base rate of at least 1 anomaly per 1,000 events; report the realized rate and the definition of an event.

## Reported measures

- **Alerts per hour:** queue volume divided by footage duration, for the naive and triage queues. Report before and after feedback when applicable.
- **Event recall at a fixed review budget:** fraction of annotated anomaly events represented in the top 30 alerts per hour. Define how alerts are selected within each hour and matched to events before running the comparison.
- **Analyst-minutes per true event:** reviewed alerts multiplied by 30 seconds, divided by the number of distinct true events found. State the review budget and handling of runs with no true events found.
- **Queue size at fixed recall:** compare the number of alerts that must be reviewed to reach the same event recall. State the recall target and report when either method cannot reach it.

Report event-level recall alongside suppression effects so reduced alert volume cannot hide missed events. Include the stream duration, counts, assumptions, sampling seed, and matching rules with results. Do not substitute detector accuracy for these measures.

## Decisions to settle before evaluation

- Dataset and annotation licenses, splits, and the unit used to calculate the base rate.
- Event matching rules, including temporal overlap and multiple alerts for one event.
- Normal-bank construction, embedding model, feature scaling, and zone definitions.
- Review ordering, ties, and how the per-hour budget applies to partial hours.
- Dismissal simulation policy, similarity threshold, and whether suppressed alerts count against the review budget.

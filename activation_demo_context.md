# Activation Demo Context

Use this file as Git-connected context for the demo agent. It replaces the old Snowflake/schema context for the simplified two-source version.

## Demo objective

A Growth PM reports that activation rate dropped last week. The agent should investigate the drop using exactly two runnable data sources:

1. `activation_cohort_export.csv` — cohort export from the PM.
2. `activation_preprocessing.py` — preprocessing logic pushed from VS Code into the shared Deepnote workspace.

This markdown file is business context, not a third data source.

## Company activation definition

A workspace is considered **activated within 7 days** when all four events are true for the signup cohort:

- `invited_teammate_7d = 1`
- `connected_data_source_7d = 1`
- `ran_first_query_7d = 1`
- `onboarding_completed_7d = 1`

The CSV includes `activated_7d`, but the agent should recompute activation from the four milestone fields above and use that recomputed value as the source of truth.

## Analysis requirements

When asked to investigate an activation drop, the agent should:

1. Compare current week `2026-06-15` to `2026-06-21` against prior week `2026-06-08` to `2026-06-14`.
2. Break down activation by `experiment_arm` and `acquisition_channel`.
3. Identify the segments with the largest percentage-point decline.
4. Produce three hypotheses grounded in the observed movement.
5. Build a Plotly dashboard with filters for `experiment_arm` and date range.

## Expected storyline in demo data

The synthetic data intentionally contains a last-week activation decline. The drop is strongest for:

- `guided_onboarding` experiment arm
- `Paid Search` acquisition channel
- Users with slower `time_to_first_query_minutes`

Good hypotheses should mention onboarding friction, lower-quality paid traffic, and delayed first-query completion.

## Safe demo prompt

```text
Investigate the activation rate drop last week. Use the cohort CSV and the preprocessing file pushed from VS Code. Use our activation definition from the context file. Break down by experiment arm and acquisition channel. Hypothesize the top 3 causes.
```

## Dashboard prompt

```text
Build a Plotly dashboard with filters for experiment arm and date range. Include activation rate trend, activation rate by acquisition channel, and a table of the biggest week-over-week declines.
```

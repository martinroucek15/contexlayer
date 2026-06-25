# Activation Definition

This document is the business context for the activation investigation demo.

## Business definition

A new user is considered **activated** when they complete all of the following onboarding milestones inside the activation window:

1. Connect a data source
2. Run their first query
3. Complete onboarding
4. Invite a teammate

The default activation window is **7 days** after signup.

During the demo, the engineer changes the activation logic from:

```python
ACTIVATION_WINDOW_DAYS = 7
```

to:

```python
ACTIVATION_WINDOW_DAYS = 14
```

This represents the team testing whether more users should count as activated when the onboarding journey takes longer than one week.

## KPI

The primary KPI is:

**Activation rate = activated users / total new users**

## Required breakdowns

When investigating an activation drop, always break the result down by:

- Signup week
- Experiment arm
- Acquisition channel

## Expected investigation

The analysis should identify:

- Where activation dropped week over week
- Whether the drop is concentrated in a specific experiment arm
- Whether the drop is concentrated in a specific acquisition channel
- Which onboarding milestone appears to be the biggest source of friction

## Demo prompt for Deepnote AI

Investigate why activation rate dropped last week.

Use:
- the uploaded `activation_cohort_export.csv`
- the activation logic in the notebook
- this Git Markdown context about our activation definition

Break the result down by experiment arm and acquisition channel.
Create a Plotly visualization.
Give me the top 3 likely causes.

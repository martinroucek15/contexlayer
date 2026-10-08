# Ember Realms — New Player Retention: Context Layer

> Read this before writing SQL. It explains what the table means, how the business defines retention, and the game's release calendar. Answers are for **non-technical staff**: plain language, a chart, and the definition used.

## 1. Business context

**Ember Realms** is a free-to-play fantasy RPG on iOS and Android. The studio's #1 health metric is **new player retention**: of the players who install the game, how many come back the next day, after a week, and after a month. A drop in retention means acquisition money is being wasted and usually signals a product problem (a bad update, a confusing tutorial, a technical issue).

## 2. Table

`<YOUR_GCP_PROJECT>.ember_realms.new_player_retention` (BigQuery Standard SQL)

**Grain: one row per player (41,004 rows).** Covers installs from **2026-01-05 to 2026-06-28**; activity is tracked through **2026-07-31**, so every row has mature D1, D7 and D30 values.

| Column | Type | Meaning |
|---|---|---|
| `player_id` | STRING | Unique player ID |
| `install_date` | DATE | UTC date the player first opened the game (the **cohort date**) |
| `install_week` | DATE | Monday of the install week, for weekly trends |
| `country` | STRING | ISO-2 code: US, DE, GB, FR, BR, IN, JP, KR, MX, CZ, PL, CA, AU |
| `platform` | STRING | `iOS` or `Android` |
| `device_tier` | STRING | Phone performance class: `low`, `mid`, `high` (iOS has no `low`) |
| `acquisition_channel` | STRING | Where the player came from: `organic`, `google_ads`, `meta`, `tiktok`, `apple_search_ads` |
| `app_version_at_install` | STRING | Game build the player installed (see release calendar) |
| `tutorial_completed` | BOOLEAN | Finished the onboarding tutorial |
| `day0_sessions` | INTEGER | Play sessions on install day |
| `day0_minutes_played` | FLOAT | Minutes played on install day |
| `day0_crashes` | INTEGER | Sessions that crashed on install day |
| `first_session_crashed` | BOOLEAN | The very first session ended in a crash |
| `returned_d1` | BOOLEAN | Played on the day after install (Day 1) |
| `returned_d7` | BOOLEAN | Played exactly 7 days after install |
| `returned_d30` | BOOLEAN | Played exactly 30 days after install |
| `active_days_first_30` | INTEGER | Number of distinct days played in days 0–29 (1 = only played on install day) |

## 3. Metric definitions

| Business term | Definition | SQL |
|---|---|---|
| Installs / new players | Number of rows | `COUNT(*)` |
| **D1 retention** | Share of installs who played on Day 1 | `AVG(IF(returned_d1,1,0))` |
| **D7 / D30 retention** | Same for Day 7 / Day 30 | `AVG(IF(returned_d7,1,0))` |
| Churn | 1 − retention | |
| Tutorial completion rate | `AVG(IF(tutorial_completed,1,0))` | |
| First-session crash rate | `AVG(IF(first_session_crashed,1,0))` | |
| Engagement | Average `active_days_first_30` | |

**Synonyms:** "come back", "stick around", "still playing" → retention. "Next-day" → D1. "Weekly" → D7. "Monthly" → D30. "Lost players" / "drop-off" → churn. "New players", "downloads" → installs. "Update", "patch", "build" → `app_version_at_install`. "Low-end phones" → `device_tier = 'low'`.

If someone says "retention" without a day, use **D1** for trend questions and **D7** for comparisons, and say which you used.

## 4. Release calendar (use to annotate charts)

| Version | Release date | Notes |
|---|---|---|
| 2.1.0 | 2026-01-05 | Baseline build |
| 2.2.0 | 2026-02-10 | Guild chat, new hero |
| 2.3.0 | 2026-03-17 | Guild Raids feature, balance pass |
| 2.4.0 | 2026-04-14 | New tutorial art, upgraded rendering engine, Arena season 2 |
| 2.4.1 | 2026-05-05 | Hotfix: stability improvements |
| 2.5.0 | 2026-06-09 | Summer Festival content, new map region |

Marketing note: a large TikTok campaign ran **2026-03-02 to 2026-03-29**, which raises TikTok's share of installs in March.

## 5. Rules

1. Retention is a **rate**: always show installs next to it. Flag groups with fewer than 200 installs as low confidence.
2. When a trend dips or spikes, **check the release calendar** and test whether the change is concentrated in a segment (platform, device tier, channel, country, app version).
3. When comparing over time, note that the **mix of channels changes** (e.g. the March TikTok push). Check whether a dip remains within each segment before blaming the product.
4. Use `install_week` for trend charts; daily data is noisy.
5. Dates are UTC.

## 6. Answer style

- One-sentence answer first, in plain English.
- Then 2–4 key numbers with the definition and date range.
- Suggest one chart: **line** for trends (with release markers), **bar** for comparing groups, **heatmap** for week × segment.
- Put SQL at the end for analysts.

## 7. Verified query pattern

```sql
SELECT install_week,
       COUNT(*)                               AS installs,
       ROUND(AVG(IF(returned_d1, 1, 0)), 3)   AS d1_retention,
       ROUND(AVG(IF(returned_d7, 1, 0)), 3)   AS d7_retention
FROM `ember_realms.new_player_retention`
GROUP BY install_week
ORDER BY install_week;
```

# Objective 2 implementation summary

The historical engagement state is observed at the current session's start. The enriched analysis reproduced all supplied reference counts and both output tables passed full-file round-trip checks.

## A. Existing implementation

Sections 1–9 retain Objective 1's episode and interaction stream, the original session keys, observed event counts and navigation, three original time measures, conservative same-app merging, conflict detection, both window frequencies and expanding personal P75/P90 baselines. Assignment uses a strict comparison and requires 30 earlier starts. Percentiles become available after ten.

## B. New measurements

| Measurement | Inputs and definition |
|---|---|
| Recorded engagement | Union of positive reconstructed start–end intervals. Window histories use strictly earlier completions and clip the left edge. |
| App composition | Exact app name mapped to one primary group, an alternate and an uncertainty note. Visits count positive unflagged bouts. Duration covers all positive intervals with an explicit ambiguous bucket. Shares retain unknowns in their denominators. |
| Bout navigation | App identity, start/end times, ties, earlier apps and conflict flags determine supported adjacent transitions and revisits. Short concentration is the fraction of eligible visits under 15 seconds. Switching rate divides supported transitions by union minutes. |
| Historical coverage | Earlier record timestamps, distinct observed dates, preceding starts, elapsed time, available window span, contributing bouts and insufficient-history flags. |
| Characterization | Matched-window sensitivities, app-mapping substitution, disjoint observed weeks, pooled state distributions and within-participant comparisons. |

The 87-name [versioned mapping](<../../Notebooks/metadata/lsapp_app_categories_v1.csv>) covers the entire input. Three app identities have an unresolved primary category. The lookup explains every assignment. Named Snapchat visit counts and attributed seconds permit the alternative mapping for each row.

## C. Reference results

| Reference measure | Reproduced |
|---|---|
| events | 899,349 |
| episodes | 585,520 |
| interactions | 313,829 |
| bouts | 334,613 |
| short bouts | 131,808 |
| unflagged short bouts | 118,849 |
| sessions | 70,979 |
| participants | 291 |
| eligible starts | 63,054 |
| eligible participants | 240 |

| P75 state | Starts |
|---|---|
| neither_elevated | 42,372 |
| session_only | 6,849 |
| short_bout_only | 10,324 |
| both_elevated | 3,509 |
| insufficient_history | 7,925 |

Every label count matches. No threshold or reference definition changed.

## D. Final datasets and missingness

| Dataset | Files | Rows | Columns | Columns with missing values |
|---|---|---|---|---|
| completed_sessions | [CSV](<../../Datasets/Objective_2/completed_sessions.csv>) | 70,979 | 90 | 41 |
| decision_points | [CSV](<../../Datasets/Objective_2/decision_points.csv>) | 70,979 | 148 | 59 |

The [data dictionary](<../../Datasets/Objective_2/schema.csv>) lists every column, dtype, unit, meaning, missingness rule and missing count. CSV blanks represent missing values. Both tables use one row per user–session pair. All current-session outcomes are confined to the completed table.

- Completed sessions have 12,282 missing union durations from interaction-only sessions and 2,904 measured zero durations.
- The decision table has 291 missing gaps and 2,832 missing P75 session baselines. 13,964 starts have zero recorded union duration in the preceding hour, so their duration shares are undefined. The 60-minute dominant category is unresolved at 21,770 starts.
- Raw telemetry and the prepared workbook remain the project inputs. Input workbook SHA-256 is `d2cb2bd812ebaf913c3458846bb9b7fbea7fbbb732c75d8cacadb3bba868b4c5`. Lookup SHA-256 is `1833a56c19795e114090d01c7376adbe7129a2c9e4998ab9b05ac8c67f298777`.

## E. Validation and findings

The interval union removes 134,056 duplicate seconds from episode sums. Conservative attribution places 2,410,404 covered seconds in the ambiguous category. The event sequence contains 234,479 name transitions; the bout sequence supports 87,753 transitions and 41,403 revisits.

All category counts and shares reconcile, all historical unions respect their window length, and each short-bout total matches the original. The notebook tests clipped and tied intervals, changes and additions to synthetic future events, and one-second occupancy at 26 real decisions in both windows. Source sessions were strictly separated for every user. Full CSV read-back restored both schemas and values.

| Alternative | All starts changed | Jointly eligible changed | Mean user change, all starts |
|---|---|---|---|
| P90 | 18.76% | 21.12% | 11.59% |
| 30-minute window | 24.61% | 27.70% | 15.15% |
| short <10 s | 4.84% | 5.44% | 2.95% |
| short <30 s | 5.21% | 5.86% | 3.29% |
| 10 prior starts | 7.18% | 0.00% | 21.91% |
| 20 prior starts | 3.46% | 0.00% | 9.40% |
| 50 prior starts | 6.25% | 0.00% | 13.86% |
| include conflicted bouts | 2.47% | 2.78% | 1.27% |

The [sensitivity summary](<../../Datasets/Objective_2/state_sensitivity.csv>) and [per-user results](<../../Datasets/Objective_2/state_sensitivity_by_user.csv>) give all denominators. Warm-up changes alter eligibility. Among starts eligible under both rules, warm-up changes leave state assignments intact. Moving Snapchat to messaging changes the 60-minute dominant category at 404 starts (0.57%). The primary state is independent of app coding. See the [mapping comparison](<../../Datasets/Objective_2/mapping_sensitivity.csv>).

Mean recorded minutes in the preceding hour are 4.92 for neither elevated, 5.86 for session only, 13.12 for short bouts only and 7.94 for both. For the 219 people observed in both short-bout-only and neither-elevated states, the mean within-person difference is 6.37 minutes. The [figure](<../../Datasets/Objective_2/state_profiles.png>), [pooled distributions](<../../Datasets/Objective_2/state_profiles_pooled.csv>) and [paired contrasts](<../../Datasets/Objective_2/state_profiles_paired.csv>) include category mix, fragmentation, revisit and clock descriptors.

Only 42 users supply two adjacent fully observed-date weeks. The median absolute difference is 3.07 starts per observed day; median category total-variation distance is 0.122. The [weekly pairs](<../../Datasets/Objective_2/weekly_stability.csv>) describe this selected subset.

## F. Limitations

Recorded intervals measure app engagement. LSApp lacks screen state, notification delivery, motives, within-app activity, questionnaire scores and clinical outcomes. Clock time is used as recorded. A date with a record establishes partial observation; silent days and data gaps have unknown coverage. Conservative ambiguity handling can understate attributed category exposure and supported navigation. Dominance abstains for ties, unresolved winners and empty windows. Ratios have different availability by state. The thresholds, windows and warm-up are operational choices. Between-state differences remain descriptive.

## G. Objective 3 handoff

Use `decision_points` and its `decision_timestamp` to initialize observable history. Retain the two defining counts, P75/P90 references, state, recorded minutes, category proportions, transition/revisit measures, calendar context and all coverage/quality indicators. Preserve warm-up and unavailable ratios as explicit conditions. Completed-session fields can support retrospective calibration. Psychological states, intervention responses and rewards require separate modeling and evidence.

## Reproduction

Run the [guided notebook](<../../Notebooks/Research_Project_Objective_2_Guided.ipynb>) top-to-bottom from the project root or Notebooks directory. Read the [local methods memo](<objective_2_lsapp_behavioural_indicators.pdf>) for the measurement rationale and source studies. The [pre-extension backup](<../../Notebooks/Backups/Research_Project_Objective_2_Guided_pre_extension.ipynb>) preserves the prior notebook. This run used Python 3.13.5, pandas 2.3.3, NumPy 2.5.5, openpyxl 3.1.5, Matplotlib 3.11.2. No random sampling is used.

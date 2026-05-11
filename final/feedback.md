I was really happy with the conceptual pivot you made in this project after your initial report. Moving from "predict outcomes for inspectors" to "predict inspection likelihood for restaurant owners" was the right reframe, and you articulate the stakeholder rationale clearly. The recall-priority framing — "a missed warning is worse than a false alarm" — follows correctly from that pivot and is the right metric to lean on. I also appreciated that you responded to feedback by adding geographic and socioeconomic features.

Unfortunately, final report doesn't actually pull the work together to support the pivot, and the analyses that would have validated the contribution don't appear in the supporting notebooks either. I've added details below:

## What's missing

You claim socioeconomic features and geography are the most significant improvement over the baseline, but the report contains no analysis of them. I checked the work folder to see whether the analyses were buried in notebooks rather than written up, but I couldn't find anything.  So, it's not just that the report is incomplete (it seems to be, since it is without any supporting visualizations), but the empirical work doesn't actually seem to be in place. 

## On the union training strategy

The "never-inspected restaurant" problem is real, and I appreciate that you attempted to grapple with it. But the union training approach — adding zeroed placeholder rows for test-set restaurants to the training data — doesn't really solve the problem. It conflates "never inspected" with "missing features," and it teaches the model to predict y=0 when features are all zeros, which is just what you'd get from an intercept anyway. It also raises a leakage concern: the test restaurants now appear in training, even if with zeroed features.

The right approach for the never-inspected problem is harder. A bootstrap analysis on the trained model — where you simulate hypothetical restaurants by sampling feature distributions for a given borough / cuisine / socioeconomic profile and getting predicted inspection probabilities with uncertainty — would have been a much better way to reason about how a never-seen restaurant in (say) the South Bronx might fare under your model. That's not something we covered in class, so it's a reasonable thing to have missed, but it would have been the more principled answer to the question the union strategy was trying to address.

## Strengths

- **The pivot is the strongest part of the project.** Restaurant owners as the stakeholder, framed as advance-warning rather than enforcement support, is more compelling than the original framing and you responded to feedback by reorienting the whole project.
- **Recall-priority justification is well-articulated.** The reasoning "a missed warning is worse than a false alarm" follows correctly from the stakeholder choice.
- **Class imbalance handled deliberately.** Balanced logistic regression, with the tradeoff acknowledged (recall up, precision down).
- **Geographic and socioeconomic features added per midterm feedback.** Even if the analysis is missing, the feature integration itself was a responsive move.
- **Honest about limitations.** Never-inspected blind spot, time-since-open proxy, borough-level vs. tract-level socioeconomic granularity are all acknowledged.
- **Temporal filtering decision is defensible.** Excluding pre-2022 data to avoid COVID-era disruption is the right call and you justify it.

## Weaknesses

- **No visualizations in the report despite referencing notebook 07.** The report's own Supporting Files section claims notebook 07 contains "Final charts and visualizations used in this report" — but the report has none. 
- **No analysis of the contribution you claim is the main improvement.** Socioeconomic and geographic features are mentioned as the most significant addition, but neither the report nor supporting notebooks show an ablation, a coefficient comparison, a partial dependence plot, or any quantification of their impact. 
- **No baseline comparisons.** A "predict inspection if days_since_last > 80" rule would have established how much ML actually adds over a one-feature heuristic. Without this, the 70% recall result has no anchor.
- **Target variable has mechanical structure that isn't probed.** NYC inspections operate on a roughly annual cadence, so "will this restaurant be inspected in the next 90 days?" is largely a function of `days_since_last_inspection` — which is a feature. The model may be mostly learning a simple time-since-last rule.
- **`historical_avg_score` has a circularity issue.** Expanding-window averages give more inspected restaurants more reliable averages, so the feature partly encodes "how many inspections you've had" — mechanically related to "will you be inspected?"
- **Model comparison is unfair.** Logistic Regression has 26% recall, Balanced LR has 70%, Random Forest has 38%. But only LR got `class_weight='balanced'`. A balanced, tuned Random Forest could plausibly match or beat the balanced LR — you didn't try it.
- **No threshold or PR curve analysis.** The 0.5 default threshold is arbitrary. With recall 0.70 / precision 0.44, deployed restaurant owners would see 56% of "high risk" alerts not result in inspections — and quickly learn to ignore the tool. A PR curve would have let you (and them) reason about the tradeoff.
- **No subgroup analysis.** Does the model work equally well across boroughs, cuisines, restaurant sizes? Bootstrap CIs on per-segment recall would have been informative — and would have directly addressed whether the socioeconomic features are doing meaningful work.
- **The union training approach doesn't really solve the never-inspected problem** (see discussion above).

## Summary

So, to reiterate, I'm really happy with the pivot and the recall framing follows it cleanly. What's missing is the empirical follow-through to make the pivot land — particularly around the geographic and socioeconomic features, which are claimed as the central contribution but aren't actually analyzed. The analyses don't need to be elaborate: with-vs-without ablation, a coefficient table, a partial dependence plot for income or poverty rate, a per-borough recall breakdown. Any one of these would have transformed the project from "we added these features" to "we showed what these features do." That gap is the main reason the score is where it is. Solid conceptual work; the empirical case needs more.

**Score: 25/30**


---

## Final Project Grade
| Assessment Item | Jessica Aimunmondion | Joel Barnes | Mian Hamid | Zeek Moore |
|---|---|---|---|---|
| **Proposal (5 pts)** | 5 | 5 | 5 | 5 |
| **Midterm Report (10 pts)** | 9 | 9 | 9 | 9 |
| **Final Presentation (5 pts)** | 5 | 5 | 5 | 5 |
| **Final Report (30 pts)** | 25 | 25 | 25 | 25 |
| **Weekly Updates (30 pts)** | 30 | 25 | 28 | 30 |
| **Total (80 pts)** | **74** | **69** | **72** | **74** |

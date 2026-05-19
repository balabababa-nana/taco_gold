# Main Rerun Result Memo

## 1. Scope

This memo records the final outcome of the `main_rerun` mainline rerun through `Stage 2`.

Frozen scope for this rerun:

- new `primary holdout = 2025-07-15 ~ 2025-10-25`
- `Stage 1` rebuilt with `5-fold segmented / gap-aware split`
- `Stage 1 final variant` reselected from outer-CV
- `Stage 2` daily table rebuilt from rerun `Stage 1` outputs
- `Stage 2 final model` reselected under corrected protocol
- no ex-ante `robustness` inside the frozen mainline selection path
- no legacy summary rewrite

All rerun outputs were isolated under `main_rerun/`.

## 2. Important Method Corrections

Two material corrections were made during the rerun:

1. `Stage 1` final variant selection was corrected from legacy `Brier -> ECE -> PR-AUC` wording to:
   - calibrated variants only
   - `PR-AUC` first
   - `Brier / ECE` as tie-break

2. `Stage 2` final model selection was corrected so that:
   - outer-CV selection compares only `two_stage_v2_core` across `ridge / random_forest / xgboost_regressor`
   - `macro_only` is not part of final-model outer selection
   - `macro_only_ridge`, `macro_only_random_forest`, and `random_walk_zero` are holdout benchmarks only

This second correction is the key reason the final `Stage 2` conclusion differs from the earlier intermediate reading.

## 3. Stage 1 Final Outcome

Final selected `Stage 1` variant:

- `linear_svm__sigmoid`

Selection rule:

- `calibrated_only__pr_auc_first__brier_ece_tiebreak`

Outer-CV summary for the selected variant:

- `PR-AUC = 0.119414`
- `Brier = 0.056294`
- `ECE = 0.056772`
- `ROC-AUC = 0.632799`

Primary holdout summary for the selected variant:

- `PR-AUC = 0.153295`
- `Brier = 0.045794`
- `ECE = 0.018409`
- `ROC-AUC = 0.571429`

Reference artifacts:

- `main_rerun/artifacts/stage1/stage1_v3_selected_variants.csv`
- `main_rerun/artifacts/stage1/stage1_v3_outer_summary_metrics.csv`
- `main_rerun/artifacts/stage1/stage1_v3_primary_holdout_metrics_selected.csv`

## 4. Stage 2 Final Outcome

### 4.1 Final selection rule

`Stage 2 final model` is selected by outer-CV only.

Candidate pool:

- variant: `linear_svm__sigmoid`
- spec: `two_stage_v2_core`
- model:
  - `ridge`
  - `random_forest`
  - `xgboost_regressor`

Evaluation priority:

- this project is treated primarily as a continuous-value regression task
- therefore `RMSE / OOS R2` are the main comparison metrics
- `MAE` and `sign_accuracy` are supporting reads, not the primary selection target

### 4.2 Selected final model

Final selected `Stage 2` model:

- `linear_svm__sigmoid + random_forest + two_stage_v2_core`

Outer-CV metrics of selected final model:

- `RMSE = 0.009845`
- `MAE = 0.007236`
- `R2 = -0.019075`
- `OOS R2 = -0.013328`
- `sign_accuracy = 0.513932`

Primary holdout metrics of selected final model:

- `RMSE = 0.014453`
- `MAE = 0.009367`
- `R2 = -0.015483`
- `OOS R2 = 0.007664`
- `sign_accuracy = 0.648649`

Reference artifacts:

- `main_rerun/artifacts/stage2/final_compare/stage2_nested_cv_model_compare_outer_summary_metrics.csv`
- `main_rerun/artifacts/stage2/final_compare/stage2_nested_cv_model_compare_selected_model.csv`
- `main_rerun/artifacts/stage2/final_compare/stage2_nested_cv_model_compare_holdout_metrics.csv`

## 5. Holdout Benchmark Compare

After final model selection, the selected model was compared on holdout against:

- `Random Walk (Zero)`
- `Ridge + macro_only`
- `RandomForest + macro_only`

Holdout comparison against the selected final model:

- vs `RandomForest + macro_only`
  - selected model `RMSE` better by `0.000017`
  - selected model `OOS R2` better by `0.002368`
  - selected model `sign_accuracy` better by `1.35` percentage points
- vs `Ridge + macro_only`
  - selected model `RMSE` better by `0.000157`
  - selected model `OOS R2` better by `0.021626`
  - selected model `sign_accuracy` better by `6.76` percentage points
- vs `Random Walk (Zero)`
  - selected model `RMSE` better by `0.000153`
  - selected model `OOS R2` better by `0.021070`
  - selected model `sign_accuracy` better by `64.86` percentage points

Reference artifacts:

- `main_rerun/artifacts/stage2/final_compare/stage2_nested_cv_model_compare_holdout_benchmark_metrics.csv`
- `main_rerun/artifacts/stage2/final_compare/stage2_nested_cv_model_compare_holdout_compare.csv`

## 6. Single-Stage Ablation Refresh

A rerun-aligned richer `single-stage` ablation was executed after the mainline `Stage 2` result was frozen.

Setup:

- single-stage spec: `single_stage_daily_raw_rich`
- learner: `RandomForestRegressor`
- holdout window aligned to this rerun: `2025-07-15 ~ 2025-10-25`
- richer single-stage daily table rebuilt from the rerun-aligned `Stage 2` daily base
- benchmarks reused from this rerun:
  - `two-stage final`
  - `RandomForest + macro_only`
  - `Ridge + macro_only`
  - `Random Walk (Zero)`

Outer-CV summary of richer single-stage RF:

- `RMSE = 0.007904`
- `MAE = 0.005856`
- `R2 = -0.043460`
- `sign_accuracy = 0.586667`

Primary holdout summary of richer single-stage RF:

- `RMSE = 0.014530`
- `MAE = 0.009345`
- `R2 = -0.026350`
- `sign_accuracy = 0.621622`

Holdout reading:

- vs `two-stage final`
  - richer single-stage RF `RMSE` is worse by `0.000077`
  - `sign_accuracy` is lower by `2.70` percentage points
- vs `RandomForest + macro_only`
  - richer single-stage RF `RMSE` is worse by `0.000060`
- vs `Ridge + macro_only`
  - richer single-stage RF `RMSE` is better by `0.000080`
- vs `Random Walk (Zero)`
  - richer single-stage RF `RMSE` is better by `0.000076`

Conclusion:

- richer `single-stage` did not overturn the mainline conclusion
- on the aligned holdout, it did not beat the selected `two-stage final`
- on the aligned holdout, it also did not beat `RandomForest + macro_only`

Reference artifacts:

- `main_rerun/artifacts/ablation/one_stage_daily_raw_rich_modeling/outer_summary_metrics.csv`
- `main_rerun/artifacts/ablation/one_stage_daily_raw_rich_modeling/holdout_metrics.csv`
- `main_rerun/artifacts/ablation/one_stage_daily_raw_rich_modeling/refreshed_one_stage_vs_two_stage_table.csv`
- `main_rerun/artifacts/ablation/one_stage_daily_raw_rich_modeling/refreshed_one_stage_vs_two_stage_summary.md`

## 7. Targeted Follow-up Check

A separate follow-up run was executed only as a diagnostic to test whether `two_stage_v2_core` had been underestimated because of a too-coarse search grid.

What changed in that follow-up:

- `ridge`: stronger regularization range
- `random_forest`: shallower trees and more trees
- `xgboost_regressor`: smaller learning rate, shallower trees, larger `min_child_weight`

Conclusion of the follow-up:

- it did not overturn the mainline conclusion
- it did not show a stable, cross-model reason to reject the selected final model
- it remains diagnostic evidence, not the official mainline result

Reference artifacts:

- `main_rerun/artifacts/stage2/final_compare_targeted_followup/`

## 8. Dense Export Sensitivity V2

A post-mainline robustness test was executed to stress the key `single-stage vs two-stage` project comparison by perturbing only the `two-stage` training-side signal-export mechanism.

Frozen setup of this robustness test:

- `Stage 1 winner` still fixed to `linear_svm__sigmoid`
- holdout still fixed to `2025-07-15 ~ 2025-10-25`
- `single-stage` not rerun or redefined
- `Stage 2` compare rule unchanged
- only the legal `Stage 1 OOF -> Stage 2` export coverage was changed

What changed in `v2`:

- `MIN_INITIAL_TRAIN_POSITIVES = 4`
- `LATER_SEGMENT_MIN_PREFIX_DAYS = 5`

Coverage effect of `v2`:

- `event-level OOF coverage`: `36.7% -> 78.8%`
- `positive-event OOF coverage`: `56.4% -> 90.9%`
- `positive target-day coverage`: `14 / 22 -> 18 / 22`

Selected `v2` two-stage model:

- `linear_svm__sigmoid + random_forest + two_stage_v2_core`

Outer-CV reading vs mainline two-stage:

- `RMSE`: worse by `0.000006`
- `MAE`: better by `0.000001`
- `OOS R2`: worse by `0.001212`

Primary holdout reading vs mainline two-stage:

- `RMSE`: worse by `0.000009`
- `MAE`: worse by `0.000003`
- `OOS R2`: worse by `0.001225`
- `sign_accuracy`: lower by `6.76` percentage points

Primary holdout reading vs benchmark models:

- vs `RandomForest + macro_only`
  - `RMSE` better by `0.000008`
  - `OOS R2` better by `0.001143`
  - `sign_accuracy` lower by `5.41` percentage points
- vs `Ridge + macro_only`
  - `RMSE` better by `0.000148`
  - `OOS R2` better by `0.020401`
  - `sign_accuracy` unchanged
- vs `Random Walk (Zero)`
  - `RMSE` better by `0.000144`
  - `OOS R2` better by `0.019845`

Robustness reading:

- this is a meaningful robustness test for the project-level `single-stage vs two-stage` comparison because it materially improves the train-side `two-stage` signal coverage without changing the holdout, the `Stage 1 winner`, or the final compare rule
- even after that targeted perturbation, the main project conclusion was not overturned
- in the same robustness run, the dense-export `two-stage` model also remained ahead of both `macro_only` benchmarks on holdout `RMSE / OOS R2`
- therefore, the mainline `single-stage vs two-stage` comparison appears robust to this specific “OOF coverage too sparse” objection

Reference artifacts:

- `main_rerun/artifacts/dense_export_sensitivity/stage1/stage1_v3_dense_export_coverage_summary.csv`
- `main_rerun/artifacts/dense_export_sensitivity/stage2/final_compare/stage2_nested_cv_model_compare_selected_model.csv`
- `main_rerun/artifacts/dense_export_sensitivity/stage2/final_compare/stage2_nested_cv_model_compare_holdout_metrics.csv`

## 9. Operational Notes

- `Stage 1` and `Stage 2` rerun notebooks were generated through isolated wrappers under `main_rerun/py/`
- new rerun notebooks and artifacts did not overwrite legacy `stage1/` or `stage2/` result directories
- the effective holdout daily end date in the rebuilt `Stage 2` table is `2025-10-24`, which is the last trading day inside the frozen holdout window ending `2025-10-25`

## 10. Bottom Line

The final mainline rerun conclusion is:

- `Stage 1 final variant = linear_svm__sigmoid`
- `Stage 2 final model = random_forest + two_stage_v2_core`

Under the corrected `Stage 2` procedure, the selected two-stage final model beats all three designated holdout benchmarks:

- `random_walk_zero`
- `macro_only_ridge`
- `macro_only_random_forest`

The refreshed richer `single-stage` ablation does not overturn that conclusion.

The post-mainline `dense export sensitivity v2` robustness test also does not overturn that conclusion, while still leaving the perturbed `two-stage` model ahead of the `macro_only` benchmarks on holdout `RMSE / OOS R2`.

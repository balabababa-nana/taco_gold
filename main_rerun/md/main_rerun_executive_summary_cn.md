# Main Rerun Executive Summary

## 1. 最终结论

本次 `main_rerun` 到 `Stage 2` 为止的正式主结论是：

- `Stage 1 final variant = linear_svm__sigmoid`
- `Stage 2 final model = random_forest + two_stage_v2_core`

在修正后的 `Stage 2` 选择规则下，最终选出的 two-stage 主模型在 `primary holdout` 上优于以下 3 个 benchmark：

- `random_walk_zero`
- `macro_only_ridge`
- `macro_only_random_forest`

## 2. 本次最重要的方法修正

### 2.1 Stage 1

- `primary holdout` 冻结为 `2025-07-15 ~ 2025-10-25`
- `Stage 1` 改为 `5-fold segmented / gap-aware split`
- `Stage 1 final variant` 按 outer-CV 选择
- `Stage 1` 正式选择规则修正为：
  - 只在 calibrated variants 中比较
  - `PR-AUC` 第一优先
  - `Brier / ECE` 作为 tie-break

### 2.2 Stage 2

- `Stage 2 final model` 的 outer 选择只比较：
  - `ridge + two_stage_v2_core`
  - `random_forest + two_stage_v2_core`
  - `xgboost_regressor + two_stage_v2_core`
- `macro_only` 不再混入 final-model outer 选择
- `macro_only_ridge`、`macro_only_random_forest`、`random_walk_zero` 只在 holdout 上做 benchmark compare

## 3. Stage 1 结果

正式选中的 `Stage 1` variant：

- `linear_svm__sigmoid`

outer-CV 关键指标：

- `PR-AUC = 0.119414`
- `Brier = 0.056294`
- `ECE = 0.056772`

primary holdout 关键指标：

- `PR-AUC = 0.153295`
- `Brier = 0.045794`
- `ECE = 0.018409`
- `ROC-AUC = 0.571429`

## 4. Stage 2 结果

正式选中的 `Stage 2 final model`：

- `linear_svm__sigmoid + random_forest + two_stage_v2_core`

评价口径：

- 本项目主任务按连续值回归处理
- 因此以 `RMSE / OOS R2` 为主
- `MAE` 和 `sign_accuracy` 只作辅助读数

outer-CV 关键指标：

- `RMSE = 0.009845`
- `MAE = 0.007236`
- `OOS R2 = -0.013328`
- `sign_accuracy = 0.513932`

primary holdout 关键指标：

- `RMSE = 0.014453`
- `MAE = 0.009367`
- `OOS R2 = 0.007664`
- `sign_accuracy = 0.648649`

## 5. Holdout Benchmark Compare

以最终选中的 `random_forest + two_stage_v2_core` 为基准：

- 对 `RandomForest + macro_only`
  - `RMSE` 更好 `0.000017`
  - `OOS R2` 更好 `0.002368`
  - `sign_accuracy` 更高 `1.35` 个百分点

- 对 `Ridge + macro_only`
  - `RMSE` 更好 `0.000157`
  - `OOS R2` 更好 `0.021626`
  - `sign_accuracy` 更高 `6.76` 个百分点

- 对 `Random Walk (Zero)`
  - `RMSE` 更好 `0.000153`
  - `OOS R2` 更好 `0.021070`
  - `sign_accuracy` 更高 `64.86` 个百分点

## 6. 一句话总结

这次 `main_rerun` 在修正 `Stage 1 split`、`Stage 1 selection rule` 和 `Stage 2 final-model selection logic` 之后，得到的正式主链结果是：

- 上游 `P_taco` 主版本选为 `linear_svm__sigmoid`
- 下游 two-stage 主模型选为 `random_forest + two_stage_v2_core`
- 该主模型在新的 primary holdout 上优于既定 benchmark 集合

补充一句：

- rerun 对齐后的 richer `single-stage RF` ablation 没有推翻这个结论；它在 holdout 上既没有超过 `two-stage final`，也没有超过 `RandomForest + macro_only`

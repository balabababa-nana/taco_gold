# Main Rerun Stage 2 Final Model SHAP Summary

## Final model
- `variant_key = linear_svm__sigmoid`
- `model_name = random_forest`
- `spec_name = two_stage_v2_core`
- `best_params_json = {"model__max_depth": 3, "model__max_features": 0.5, "model__min_samples_leaf": 5, "model__n_estimators": 200}`

## Sample
- `train_pool rows = 1130`
- `primary_holdout rows = 74`
- `feature count = 11`

## Holdout metrics
- `RMSE = 0.014453`
- `MAE = 0.009367`
- `OOS R2 = 0.007664`
- `sign_accuracy = 0.648649`

## Top Mean |SHAP| Features
- `vix_ma_5d_lag1`: `mean|SHAP| = 0.000128`, `mean SHAP = -0.000128`
- `p_taco_top1`: `mean|SHAP| = 0.000096`, `mean SHAP = -0.000081`
- `vix_change_5d_lag1`: `mean|SHAP| = 0.000084`, `mean SHAP = 0.000068`
- `p_taco_any_decay_2d`: `mean|SHAP| = 0.000064`, `mean SHAP = 0.000033`
- `spx_ret_1d_lag1`: `mean|SHAP| = 0.000042`, `mean SHAP = 0.000025`
- `gold_spx_corr_20d_lag1`: `mean|SHAP| = 0.000035`, `mean SHAP = -0.000009`
- `dxy_ret_1d_lag1`: `mean|SHAP| = 0.000032`, `mean SHAP = 0.000021`
- `real_yield_change_5d_lag1`: `mean|SHAP| = 0.000029`, `mean SHAP = 0.000001`
- `spx_drawdown_5d_lag1`: `mean|SHAP| = 0.000028`, `mean SHAP = -0.000022`
- `p_taco_any`: `mean|SHAP| = 0.000008`, `mean SHAP = 0.000000`

## Artifacts
- `summary_plot = main_rerun/artifacts/stage2/final_compare/shap/stage2_final_selected_model_holdout_shap_summary.png`
- `shap_importance = main_rerun/artifacts/stage2/final_compare/shap/stage2_final_selected_model_shap_importance.csv`
- `shap_values = main_rerun/artifacts/stage2/final_compare/shap/stage2_final_selected_model_holdout_shap_values.csv`
- `holdout_predictions = main_rerun/artifacts/stage2/final_compare/shap/stage2_final_selected_model_holdout_predictions.csv`

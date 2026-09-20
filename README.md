# Predicting Traffic Accident Severity (KNIME)

Group project for *Big Data and Databases*. It predicts whether a US traffic accident is **severe** and identifies the factors that drive severity.

## Data
- US Accidents dataset, reduced sample of **500,000 records and 46 variables** (location, time, weather, road infrastructure).
- Target: `Severity_bin` = 1 if Severity ∈ {3, 4}. Severity here measures the accident's impact on traffic, not injuries.

## Data preparation
- Dropped IDs, address fields, duplicate coordinates and free-text columns.
- Imputed missing weather values with the median and added a `Precipitation_missing` flag.
- Grouped raw weather descriptions into `Weather_Group` (CLEAR, CLOUDY, RAIN, SNOW, FOG, OTHER).
- Extracted hour, day of week and month from `Start_Time`, plus `Rush_Hour` and `Weekend` flags.
- Stratified 80/20 train/test split with a fixed seed.

## Models
| Model | Setup | Test AUC |
|---|---|---|
| Random Forest (basic) | Full feature set | **0.867** |
| Random Forest (balanced) | Row sampling + SMOTE | 0.833 |
| Random Forest (reduced) | Correlated variables removed | 0.766 |
| Gradient Boosting (H2O) | Grid search (70 trees, depth 15, lr 0.15), Youden threshold | **0.870** |
| MLP (Texas / New York) | State-specific, downsampled, 5-fold CV over 9 architectures, K-means spatial clusters as features | 0.686 / 0.693 |

## Key findings
- **State** is the strongest predictor in every tree model, so severity is mainly regional and structural.
- Distance and wind direction rank high, but they act as proxies for road type and regional climate rather than as direct causes.
- Removing correlated predictors hurts performance: tree ensembles use that information and are robust to multicollinearity.
- State-level MLPs behave differently in highway-dominated Texas than in dense urban New York. This supports **state-specific** safety policies and models instead of a

# Audit and refactor

Removes the hard-coded row deletion, arbitrary ordinal category encoding, test-set scaler fitting, and MAE classification scoring. Missing predictors are imputed within folds. CustomerID is excluded; use --group-column CustomerID if customers have repeated records. Confirm predictors are available before churn occurs.

Original algorithms and historical outputs remain in legacy/. No production dataset was available during refactoring; new real-data metrics remain unmeasured. See README.md for the reproducible evaluation protocol.

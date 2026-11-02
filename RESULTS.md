# Real-data evaluation — 2026-11-02

Input: `Churn/customer_churn_dataset-training-master.csv`. Dataset SHA-256: `a41be7b96fb85110521bf03d1530879e9ca94b9f5de19866757f6d184300fff7`.

Model selection used only training-fold CV. One final holdout was reserved before preprocessing. The full CV leaderboard, dependency versions, seed, feature schema and split sizes are in [experiments/report.json](experiments/report.json).

- Model: **RandomForestClassifier**
- Training rows: **352665**; holdout rows: **88167**
- Selection metric: `f1_macro`; selected CV score: **0.998961**
- Dummy baseline CV score on the same folds: **0.361882**
- Five CV folds; seed 42; tuning: **False**
- Identical raw predictor rows grouped: **False**

| Metric | Holdout value |
|---|---:|
| accuracy | 0.999285 |
| balanced_accuracy | 0.999361 |
| f1_macro | 0.999272 |
| roc_auc | 0.999999 |
| average_precision | 0.999999 |

Accuracy 95% Wilson interval: [0.9991, 0.9994]. This treats holdout observations as independent; grouped duplicates reduce the effective sample size, so the interval is descriptive rather than a complete uncertainty estimate.


## Dataset-specific interpretation

One row with missing Churn is removed. CustomerID is excluded from predictors. The supplied separate test dataset is evaluated only after model selection, with an exact-predictor overlap audit. Differences between internal and separate-test performance must be reported together. Dataset generation/provenance and the timing of support/payment variables remain unverified.

## Reproduce

Activate an environment with the project's requirements, change to the repository root, and run:

```cmd
python -m mlproject train --data "D:\data\Churn\customer_churn_dataset-training-master.csv" --output artifacts --folds 5 --seed 42
```

Results were obtained with the dependency versions recorded in the JSON report. Different versions can change results slightly. Negative MAE selection scores are higher-is-better. CV scores are selection estimates and optimistic; the holdout is the final internal result. Hyperparameter tuning is enabled on the smaller datasets and diabetes; for credit, soccer, student and churn the default bounded candidate comparison is used. Scores are not directly comparable across datasets or tasks.

Do not tune against the now-observed holdout or claim improvement over the legacy notebooks: leakage, split policies and preprocessing differ. Raw data, row-level predictions and trained pickle artifacts are not included in this evidence commit.

## Supplied independent test file

Rows: **64374**; exact predictor overlaps with the original training file: **0**. No external-test rows were used for fitting or model selection.

| Metric | Separate test |
|---|---:|
| accuracy | 0.504023 |
| balanced_accuracy | 0.528756 |
| f1_macro | 0.383531 |
| roc_auc | 0.621270 |
| average_precision | 0.545050 |

See experiments/external-test.json. This is a separate supplied file, not independently verified provenance. A large internal/external gap signals limited generalization; it must not be hidden by reporting the internal holdout alone.

**Generalization finding:** internal accuracy is approximately 99.93%, while supplied external-test accuracy is 50.40% (below its majority-class baseline of 52.63%). The model identifies only 5.88% of external non-churn cases. In the training file, 100% of monthly-contract records have Churn=1; in the external file this rate is approximately 51.61%. For Support Calls > 4, the corresponding rates are approximately 99.08% and 60.90%. These aggregate audits demonstrate different feature/label relationships. They do not establish dataset provenance or a causal explanation. Treat this model as an educational result with poor separate-file generalization; obtain representative development data and reserve a fresh final test before further tuning.

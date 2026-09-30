---
layout: page
title: Predicting Which Interconnection Requests Reach Operation
description: Led a machine learning project predicting which generation projects in CAISO's interconnection queue withdraw and which reach operation.
img: assets/img/projects/card_queue_pipeline.png
og_image: /assets/img/projects/card_queue_pipeline.png
importance: 3
category: grid & financial modeling
---

_Project lead, with Claire Liu, Ke Hu, and Sindre Iversen Carlsen · Mentored by Linda Wright, Lead Interconnection Specialist, CAISO · ER 131 (Data, Environment and Society) class project, UC Berkeley, 2023_

<div class="card mt-3 mb-4">
  <div class="card-body">
    <p class="text-muted mb-2" style="font-size: 0.8rem; letter-spacing: 0.08em; text-transform: uppercase">At a glance</p>
    <table class="table table-sm mb-0">
      <tr><th scope="row" style="width: 7rem; border-top: none">Role</th><td style="border-top: none">Project lead: scoped the project, sourced all datasets, framed the prediction questions, and built all models for the main question</td></tr>
      <tr><th scope="row" style="width: 7rem; border-top: none">Methods</th><td style="border-top: none">Decision tree, random forest, and logistic regression; cross-validated grid search; SMOTE rebalancing; ROC and confusion-matrix evaluation</td></tr>
      <tr><th scope="row" style="width: 7rem; border-top: none">Tools</th><td style="border-top: none">Python (pandas, scikit-learn, imbalanced-learn)</td></tr>
      <tr><th scope="row" style="width: 7rem; border-top: none">Skills</th><td style="border-top: none">Interconnection process, classification, imbalanced data, feature engineering, data leakage checks</td></tr>
      <tr><th scope="row" style="width: 7rem; border-top: none">Available on request</th><td style="border-top: none">Final report notebook</td></tr>
    </table>
  </div>
</div>

## The question

Two out of three requests in CAISO's generation interconnection queue withdraw before connecting to the grid. Each withdrawal can trigger restudies for the projects behind it, slowing down the ones that are viable. We asked whether public data can predict which requests will reach commercial operation, so grid operators can prioritize viable projects and plan transmission around them.

<div class="row justify-content-sm-center">
  <div class="col-sm-12 mt-3 mt-md-0">
    {% include figure.liquid zoomable=true loading="eager" path="assets/img/projects/card_queue_pipeline.png" title="Queue outcomes and modeling pipeline" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Where queue requests end up, and the five steps from raw queue data to a model chosen for the planning decision.
</div>

## What I did

1. **Led the project.** Proposed the motivation, found all six datasets, framed the three prediction questions, divided the work, and wrote most of the background and data sections of the final report.
2. **Built the dataset.** Merged CAISO queue records with state generation and consumption data and CAISO's network upgrade reimbursement rates, and caught a field that leaked the outcome (see the code below).
3. **Built and compared the models.** Trained and tuned decision tree, random forest, and logistic regression classifiers for the main question: will a request withdraw or reach operation?
4. **Tackled the class imbalance.** Only about 1 in 6 decided requests reached operation, so the first models never predicted a completion. I rebalanced the training data with SMOTE and chose the final model on how many completions it found.

## What it shows

<div class="row justify-content-sm-center">
  <div class="col-sm-12 mt-3 mt-md-0">
    {% include figure.liquid zoomable=true loading="lazy" path="assets/img/projects/queue_model_results.png" title="Test-set confusion matrices" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Held-out test set of 159 decided requests, 27 of which reached operation.
</div>

- **Accuracy is the wrong yardstick.** With so few completions, betting that every project withdraws looks accurate but tells a planner nothing. Rebalancing with SMOTE is what let the models find completions at all.

<div class="row justify-content-sm-center">
  <div class="col-sm-12 mt-3 mt-md-0">
    {% include figure.liquid zoomable=true loading="lazy" path="assets/img/projects/queue_tradeoffs.png" title="Model trade-offs and top predictors" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Recall and precision for completed projects on the test set, and the strongest predictors in the decision tree.
</div>

- **Match the model to the decision.** No single model wins. The choice depends on whether a missed viable project or a wasted study costs the grid operator more.
- **There is signal to build on.** Project size, local energy demand, and the proposed timeline carry the most information, a starting point for a screening tool built on data known at filing.

_We completed this project in 2023 using the CAISO queue data available at the time. Queue rules have changed since, including FERC Order 2023 and CAISO's interconnection process enhancements, so the results describe the queue as it was then._

## Code highlights

Excerpts from my part of the team's final notebook, lightly trimmed for readability.

**Preventing leakage and building features.** Withdrawn projects keep their original online date, so that field would reveal the outcome:

```python
# 'Current On-line Date' equals the proposed date for every withdrawn project,
# so it leaks the outcome and is dropped
df["Duration between Queue & Online"] = (
    df["Proposed Online Year"] - df["Year of Queue"])
df = df.drop(columns=["Current On-line Date", "Year of Queue",
                      "Proposed Online Year",
                      "Interconnection Agreement Status"])
df["Application Status"] = df["Application Status"].map(
    {"COMPLETED": 1, "WITHDRAWN": 0})
```

**Tuning with cross-validated grid search:**

```python
param_grid = {"n_estimators": [100, 200],
              "max_depth": [10, 20, None],
              "min_samples_split": [2, 5],
              "min_samples_leaf": [1, 2],
              "max_features": ["sqrt", "log2", None]}
grid_search = GridSearchCV(RandomForestClassifier(random_state=2023),
                           param_grid, cv=5, n_jobs=-1)
grid_search.fit(X_train_smote, y_train_smote)
```

**Rebalancing only the training data with SMOTE,** then judging on the untouched test set:

```python
smote = SMOTE(random_state=42)
X_train_smote, y_train_smote = smote.fit_resample(X_train_smaller, y_train_smaller)

y_pred = best_lr_smote.predict(X_test)
print(classification_report(y_test, y_pred))  # completed-project recall: 0.48
print(confusion_matrix(y_test, y_pred))
```

## What I'd do differently

- **Check every field for leakage.** Proposed online dates can also be revised after a project withdraws. I would keep only information known when the request was filed.
- **Tune for the question that matters.** Grid search optimized overall accuracy. Tuning on recall or F1 for completed projects would fit the goal better.
- **Model time to operation, not just the outcome.** A survival model would use still-active requests too, and answer how long a request is likely to take.

_The final report notebook is available to employers on request. [Contact me](mailto:yliang@hks.harvard.edu)._

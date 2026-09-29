---
layout: page
title: Predicting Which Interconnection Requests Reach Operation
description: Led a machine learning project predicting which generation projects in CAISO's interconnection queue withdraw and which reach operation.
img: assets/img/projects/queue_results.png
og_image: /assets/img/projects/queue_results.png
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
    {% include figure.liquid loading="eager" path="assets/img/projects/queue_requests_by_year.png" title="Interconnection requests by year" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Interconnection requests entering the CAISO queue each year, from the CAISO-controlled grid generation queue.
</div>

## What I did

1. **Led the project.** Proposed the motivation, found all six datasets, framed the three prediction questions, divided the work, and wrote most of the background and data sections of the final report.
2. **Built the dataset.** Merged CAISO queue records (size, technology, fuel, utility, county, dates) with state generation and consumption data and CAISO's network upgrade reimbursement rates, and dropped a field that leaked the outcome.
3. **Built and compared the models.** Trained decision tree, random forest, and logistic regression classifiers for the main question (withdraw or complete), tuned each with cross-validated grid search, and compared them on accuracy, ROC curves, and confusion matrices.
4. **Tackled the class imbalance.** Only about 1 in 6 decided requests reached operation, so the first models never predicted a completion. I rebalanced the training data with SMOTE and chose the final model on its ability to find completions.

## What it shows

<div class="row justify-content-sm-center">
  <div class="col-sm-12 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/projects/queue_results.png" title="Test-set confusion matrices" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Confusion matrices on the held-out test set of 159 requests.
</div>

- **Accuracy alone misleads.** The tuned random forest scored 83% accuracy while identifying none of the 27 projects that reached operation.
- **The right model depends on the decision.** With SMOTE, logistic regression found 13 of 27 completions (recall 0.48) at the cost of more false alarms (precision 0.22). The random forest was more precise but found only 4.
- **There is signal to work with.** On the validation set, the random forest separated completions from withdrawals with an ROC AUC of 0.74.

<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/projects/queue_roc.png" title="ROC curve" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  ROC curves on the validation set, before rebalancing.
</div>

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

_We completed this project in 2023 as a four-person course project, using the CAISO queue data available at the time. Queue rules and data have changed since, including FERC Order 2023 and CAISO's interconnection process enhancements, so the results describe the queue as it was then._

## What I'd do differently

- **Check every field for leakage.** Proposed online dates can also be revised after a project withdraws. I would keep only information known when the request was filed.
- **Tune for the question that matters.** Grid search optimized overall accuracy. Tuning on recall or F1 for completed projects would fit the goal better.
- **Model time to operation, not just the outcome.** A survival model would use still-active requests too, and answer how long a request is likely to take.

_The final report notebook is available to employers on request. [Contact me](mailto:yliang@hks.harvard.edu)._

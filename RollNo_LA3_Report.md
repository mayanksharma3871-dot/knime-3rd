# Lab Assessment 3: Exploratory Data Analysis and Workflow Automation using KNIME

**Course:** Data Analytics & Mining Lab  
**Assessment:** Lab Assessment 3 (20 Marks)  
**Dataset:** Titanic Passenger Dataset (891 rows × 12 columns)  
**Workflow Name:** `RollNo_LA3`  

---

## 1. Executive Summary & Workflow Architecture

This laboratory analytical system demonstrates end-to-end data processing, exploratory visual analysis, machine learning classification, and automated reporting built inside **KNIME Analytics Platform**. The workflow is structured into four distinct, annotated functional sections:

1. **Prepare:** Ingestion of raw passenger data, handling missing values (median/mode imputation), column exclusion, categorical labeling, row filtering, and aggregation.
2. **Explore:** Summary statistics, categorical color attribution, distributions (Histogram & Box Plot), bivariate relationships (Scatter Plot), and linear correlation analysis.
3. **Model:** Feature filtering, 70/30 stratified partitioning, Gini-based Decision Tree induction, test set scoring, decision rule extraction, and pruning comparison.
4. **Report & Automate:** Modular componentization (`Prepare Data`), automated multi-sheet Excel export (`LA3_Report.xlsx`), test prediction export (`Predictions.csv`), and one-click execution pipeline.

---

## 2. Checkpoints & Visual Evidence

### Checkpoint 1: Data Preparation
> **Screenshot 1 Caption:** *Execution of data preparation pipeline showing zero missing values, an 891 × 10 prepared table, 216 first-class records, and GroupBy survival rates of 74.2% (female) and 18.9% (male).*

* **Missing Value Analysis:**
  * Raw Data: `Age` had 177 missing entries; `Cabin` had 687 missing entries (77.1% sparse); `Embarked` had 2 missing entries.
  * Imputation & Cleaning: Dropped uninformative columns (`Name`, `Ticket`, `Cabin`). Imputed missing `Age` using median (**28.0**) and missing `Embarked` using mode (**'S'**).
* **Target Label Transformation:** Rule Engine created `SurvivedLabel` mapping `1 -> "Yes"` and `0 -> "No"`.
* **Row Filter Verification:** Filtering by `Pclass = 1` yielded exactly **216 rows**.
* **GroupBy Aggregation Verification:**
  * **Female:** 314 total passengers, 233 survived (**74.20% survival rate**).
  * **Male:** 577 total passengers, 109 survived (**18.89% survival rate**).

---

### Checkpoint 2: Exploratory Data Analysis (EDA)
> **Screenshot 2 Caption:** *Exploratory charts and linear correlation matrix confirming mean Age ~29.4, mean Fare ~32.2, female survival dominance (0.74 vs 0.19), and primary correlations with Pclass (-0.34) and Fare (+0.26).*

* **Descriptive Statistics:**
  * `Age`: Mean = **29.36 years**, Std Dev = **13.02**, Min = **0.42**, Max = **80.00**.
  * `Fare`: Mean = **£32.20**, Std Dev = **49.69**, Min = **£0.00**, Max = **£512.33**.
* **Visual Insights:**
  * **Color Manager:** Applied Green for `Yes` (Survived) and Red for `No` (Deceased).
  * **Bar Chart:** Female average survival is **0.74**, compared to **0.19** for males.
  * **Histogram:** Age distribution exhibits a single peak around 20–30 years with a positive skew towards elderly passengers.
  * **Box Plot:** Fare distribution reveals extreme upper outliers above £200–£500 representing luxury first-class suites.
  * **Linear Correlation with Survived:**
    * `Pclass`: **-0.338 (-0.34)** (strongest negative correlation: higher socio-economic status improved survival).
    * `Fare`: **+0.257 (+0.26)** (strongest positive correlation: higher ticket price correlated with survival).
    * `Age`: **-0.065**, `SibSp`: **-0.035**, `Parch`: **+0.082**.

---

### Checkpoint 3: Decision Tree Classification
> **Screenshot 3 Caption:** *Decision Tree model view and Scorer confusion matrix confirming root split on Sex, test accuracy of ~76.9%, and equivalent accuracy with reduced complexity under min 20 records/node.*

* **Partitioning Configuration:** 70% Training (623 rows), 30% Testing (268 rows), Stratified on `SurvivedLabel`, Random Seed: `42`.
* **Learner Configuration:** Quality Measure = `Gini Index`, Minimum records per node = `5`.
* **Performance Evaluation:**
  * **Test Accuracy:** **76.87%** (within the expected 75%–85% rubric range).
  * **Confusion Matrix:**
    * True Negatives (No): **137** | False Positives (Yes): **28**
    * False Negatives (No): **34**  | True Positives (Yes): **69**
  * **Sensitivity / Recall (Yes):** 66.99% | **Specificity (No):** 83.03% | **Precision (Yes):** 71.13%
* **Simpler Tree Comparison (min 20 records per node):**
  * Tree size decreased substantially from **133 nodes** down to **47 nodes**.
  * Generalization accuracy remained steady at **76.87%**, demonstrating that pruning eliminated overfit branches without penalizing predictive performance.

---

### Checkpoint 4: Automation & One-Click Export
> **Screenshot 4 Caption:** *Automated workflow showing Prepare Data component, all nodes executed in green state, and successful generation of LA3_Report.xlsx (3 sheets) and Predictions.csv (268 rows).*

* **Componentization:** Encapsulated `Column Filter`, `Missing Value`, and `Rule Engine` into the reusable **Prepare Data** component.
* **Excel Writer Output (`LA3_Report.xlsx`):**
  1. Sheet `Prepared_Data`: 891 rows × 10 columns of cleaned dataset.
  2. Sheet `Survival_by_Sex`: Summary metrics grouped by passenger gender.
  3. Sheet `Tree_Accuracy`: Complete Scorer metrics table and confusion matrix counts.
* **CSV Writer Output (`Predictions.csv`):** 268 test records with predicted class and confidence values.
* **One-Click Re-run:** Resetting and pressing `Shift + F7` executes the entire pipeline in under 3 seconds with zero manual intervention.

---

## 3. Decision Tree Rules (IF-THEN Formulation)

Examining the Decision Tree hierarchy reveals the following primary rules:

* **Rule 1 (Female Survival Rule):**
  $$\text{IF } \text{Sex} = \text{"female"} \text{ AND } \text{Pclass} \in \{1, 2\} \implies \text{SurvivedLabel} = \text{"Yes"}$$
  * *Interpretation:* Over 94% of female passengers travelling in 1st or 2nd class survived the disaster, highlighting the strict prioritization of women in higher-tier accommodations.

* **Rule 2 (Adult Male Mortality Rule):**
  $$\text{IF } \text{Sex} = \text{"male"} \text{ AND } \text{Age} \ge 9.5 \implies \text{SurvivedLabel} = \text{"No"}$$
  * *Interpretation:* Adult males experienced an overwhelming mortality rate (~83% non-survival) due to the strict enforcement of the "women and children first" maritime evacuation protocol.

---

## 4. Key Analytical Insights

1. **Gender Was the Single Strongest Determinant of Survival:**
   The statistical analysis and Decision Tree root split unanimously prove that gender dictated passenger outcomes. Females had a **74.2%** survival rate compared to only **18.9%** for males, confirming the historical protocol governing lifeboat boarding.

2. **Socio-Economic Class Drastically Stratified Mortality:**
   `Pclass` had the strongest negative linear correlation with survival (**-0.34**), while `Fare` had the strongest positive correlation (**+0.26**). First-class passengers enjoyed closer physical proximity to the boat deck and preferential evacuation, whereas third-class passengers faced structural barriers and delayed notifications.

3. **Pruning Prevents Overfitting While Preserving Accuracy:**
   Increasing the minimum records per node threshold from 5 to 20 reduced tree complexity by over **64%** (from 133 to 47 nodes) without sacrificing test accuracy (**76.87%**). This highlights the importance of regularization in decision trees to ensure reliable generalization on unseen data.

---

## 5. Workflow Node Annotations Reference

| Node Name | Node Type | Annotation / Purpose |
| :--- | :--- | :--- |
| **CSV Reader** | IO / Read | Reads `titanic.csv` with header (891 rows × 12 cols). |
| **Data Explorer** | Visual / Explore | Evaluates column quality, identifying 177 missing Age, 687 Cabin, 2 Embarked. |
| **Column Filter** | Manipulation | Removes uninformative text columns (`Name`, `Ticket`, `Cabin`). |
| **Missing Value** | Preprocessing | Imputes missing `Age` with Median (28) and `Embarked` with Mode ('S'). |
| **Rule Engine** | Manipulation | Adds binary label `SurvivedLabel` (`1 -> Yes`, otherwise `No`). |
| **Row Filter** | Filter | Filters for 1st-class passengers (`Pclass = 1`), yielding exactly 216 rows. |
| **GroupBy** | Aggregation | Groups by `Sex`, aggregating mean of `Survived` and passenger count. |
| **Statistics** | Visual / Stats | Computes mean, min, max, std dev for numerical columns (`Age`, `Fare`). |
| **Color Manager** | Visual / Style | Maps outcome classes: `Yes` = Green, `No` = Red across all downstream charts. |
| **Bar Chart** | Visualization | Plots average survival rate partitioned by passenger `Sex`. |
| **Histogram** | Visualization | Displays the age frequency distribution. |
| **Box Plot** | Visualization | Evaluates fare distribution and detects upper extreme outliers. |
| **Scatter Plot** | Visualization | Plots `Age` vs. `Fare`, color-coded by `SurvivedLabel`. |
| **Linear Correlation** | Statistics | Measures Pearson correlation coefficient between numeric predictors and Survived. |
| **Partitioning** | Modeling / Split | Performs 70/30 stratified train/test split on `SurvivedLabel` with seed 42. |
| **Decision Tree Learner** | Mining / Train | Trains tree model using Gini index with minimum 5 records per node. |
| **Decision Tree Predictor**| Mining / Predict | Applies trained tree model to the 30% unseen test partition. |
| **Scorer** | Evaluation | Generates confusion matrix and calculates accuracy and error metrics. |
| **Decision Tree View** | Visualization | Renders graphical tree structure to inspect hierarchical split rules. |
| **Excel Writer** | IO / Export | Exports `LA3_Report.xlsx` with sheets: `Prepared_Data`, `Survival_by_Sex`, `Tree_Accuracy`. |
| **CSV Writer** | IO / Export | Exports test set predictions table to `Predictions.csv`. |

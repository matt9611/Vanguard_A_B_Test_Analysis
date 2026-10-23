# Vanguard A/B Test Analysis

## Project Overview

This project evaluates whether Vanguard’s redesigned digital process (**Test**) performs better than the existing experience (**Control**).

The analysis combines client demographic data, experiment assignment data, and web interaction logs to assess:

- Whether the Test and Control groups are comparable
- Whether the new design improves completion and funnel progression
- Whether users complete the process faster
- Whether the redesign changes error and backtracking behaviour
- Whether users require fewer visits to complete the process
- Whether the observed differences are statistically significant

The analysis was completed in Python. The processed datasets were exported for visualization in Tableau.

## Team

- Amanda
- Matteo
- Tina

## Data

The notebook uses four source files:

| File | Purpose |
| :--- | :--- |
| `df_final_demo.csv` | Client demographic and account information |
| `df_final_web_data_pt_1.csv` | First part of the digital interaction logs |
| `df_final_web_data_pt_2.csv` | Second part of the digital interaction logs |
| `df_final_experiment_clients.csv` | Experiment assignment: Test, Control, or no assigned variation |

### Main fields used

- `client_id`
- `visitor_id`
- `visit_id`
- `process_step`
- `date_time`
- `Variation`
- `age`
- `gender`
- `balance`
- `tenure_years`

The digital journey contains the following steps:

`start → step_1 → step_2 → step_3 → confirm`

## Data Preparation

The main cleaning and preparation steps were:

1. Renamed demographic columns to make them easier to interpret.
2. Combined gender value `X` with unidentified gender `U`.
3. Concatenated the two web data files.
4. Removed **10,764 exact duplicate web events**.
5. Removed experiment roster records without a Test or Control assignment.
6. Created age groups:
   - Under 30
   - 30–39
   - 40–49
   - 50–59
   - 60+
7. Created tenure groups.
8. Merged demographic, experiment, and web interaction data.
9. Converted `date_time` to a datetime field and ordered events chronologically within each visit.
10. Created additional variables for completion, step duration, backward navigation, funnel progression, and visit behaviour.

The final experiment roster contains:

- **Control:** 23,532 clients
- **Test:** 26,968 clients
- **Total:** 50,500 clients

Although the two groups are not equal in size, their demographic distributions are very similar, supporting comparison between the two variants.

## KPIs

The experiment was evaluated using six main areas:

1. **Completion rate**
2. **Time spent on each step**
3. **Error rate and backtracking**
4. **Total completion time**
5. **Funnel progression and drop-off**
6. **Number of visits before completion**

Statistical hypothesis tests were used to evaluate whether observed differences between Test and Control were likely to be meaningful rather than random variation.

## Key Results

### 1. Completion Rate

| Variation | Completion rate |
| :--- | ---: |
| Control | **65.59%** |
| Test | **69.29%** |

The Test variant improved completion by approximately **3.71 percentage points**, equivalent to a **5.65% relative improvement** over Control.

The result is statistically significant (`p < 0.05`).

Vanguard’s cost-effectiveness requirement was a **5% relative improvement** in completion rate. The minimum required Test completion rate was **68.87%**, while the observed Test rate was **69.29%**.

**Result:** The Test design exceeds the 5% completion-rate threshold.

### 2. Time Spent on Each Step

Average time for valid forward transitions:

| Process step | Control | Test |
| :--- | ---: | ---: |
| Start | 37.64 sec | **31.02 sec** |
| Step 1 | **33.82 sec** | 37.10 sec |
| Step 2 | 87.02 sec | **86.49 sec** |
| Step 3 | 127.15 sec | **104.83 sec** |

The largest practical improvement occurs at **Step 3**, where Test users move forward approximately **22 seconds faster on average**.

The analysis finds statistically significant evidence of lower Test times at Start, Step 2, and Step 3 under the one-sided tests used in the notebook. Step 1 does not show an improvement for Test.

> Step-duration calculations include only expected forward transitions. Repeated or backward transitions are excluded from this KPI.

### 3. Error Rate

An error is defined as a **backward movement in the process**, for example:

- `step_1 → start`
- `step_2 → step_1`
- `step_3 → step_2`
- `confirm → step_3`

| Variation | Clients with an error |
| :--- | ---: |
| Control | **26.50%** |
| Test | **33.71%** |

The Test design has an error rate approximately **7.2 percentage points higher** than Control.

A two-proportion statistical test shows that this increase is highly statistically significant.

The largest numbers of backward events occur around:

- **Step 3**
- **Step 1**
- **Step 2**

**Result:** Error rate is the main weakness of the redesigned experience.

### 4. Total Completion Time

Among visits containing both a Start and Confirm event:

| Variation | Average completion time |
| :--- | ---: |
| Control | 6.51 min |
| Test | **6.24 min** |

The Test experience is approximately **0.27 minutes (16 seconds) faster on average**.

The difference is statistically significant (`p ≈ 0.00034`).

**Result:** Users who complete the process do so faster with the Test design.

### 5. Funnel Progression

Percentage of clients reaching each stage relative to users who started:

| Step | Control | Test |
| :--- | ---: | ---: |
| Start | 100.00% | 100.00% |
| Step 1 | 86.13% | **90.96%** |
| Step 2 | 79.71% | **83.43%** |
| Step 3 | 74.46% | **78.27%** |
| Confirm | 65.97% | **70.04%** |

The Test group maintains a higher share of users at every stage of the journey.

Hypothesis testing in the notebook also finds significantly higher Test progression at Steps 1, 2, and 3.

**Result:** The redesigned interface reduces drop-off throughout the funnel rather than only improving the final confirmation stage.

### 6. Visits Before Completion

Among visitors who eventually completed:

- **Control mean visits:** 1.260
- **Test mean visits:** 1.274

The hypothesis test does **not** provide evidence that Test users complete the process in fewer visits (`p ≈ 0.972` for the one-sided test used).

At the same time, the Test group has a larger share of one-visit achievers as a proportion of all visitors because its overall completion rate is higher.

**Result:** The redesign improves completion, but the data does not support the claim that completers need fewer visits.

## Experiment Evaluation

### What improved with Test?

- Higher overall completion rate
- Completion uplift exceeds the 5% cost-effectiveness threshold
- Better funnel progression at every stage
- Lower overall completion time
- Faster progression at Start and especially Step 3
- More users successfully completing the journey overall

### What became worse?

- Significantly higher client error and backtracking rate
- Step 1 is not faster in the Test experience
- No evidence that Test users require fewer visits to complete

## Recommendation

### Proceed with the Test design, but address the error-rate issue before a full rollout.

The redesigned experience produces a clear improvement in the main business outcome: **more users complete the process**, and they complete it **faster overall**. The completion uplift also exceeds Vanguard’s 5% cost-effectiveness threshold.

However, the Test variant produces substantially more backward navigation. A full rollout should therefore be accompanied by further investigation and optimization.

Recommended next steps:

1. **Investigate the higher Test error rate**, particularly around Step 1 and Step 3.
2. **Preserve the improvements at Step 3**, where the largest time saving is observed.
3. **Review Step 1 usability**, since Test users do not progress through this step faster.
4. **Use a phased rollout** while monitoring completion, drop-off, completion time, and error rate.
5. **Run a follow-up A/B test** after addressing the main sources of backward navigation.
6. Treat **error rate as a guardrail KPI**: higher conversion should not come at the cost of excessive user friction.

## Statistical Methods

The notebook uses several statistical tests, including:

- Independent-sample t-tests
- One-sided hypothesis tests for directional KPI comparisons
- Two-proportion z-test for error rates
- Chi-square tests for error distribution

A significance threshold of **α = 0.05** is used throughout the analysis.

## Tools and Libraries

### Python

- `pandas`
- `numpy`
- `scipy`
- `statsmodels`
- `matplotlib`
- `seaborn`

### Visualization

- **Tableau**
- Matplotlib
- Seaborn

## Generated Analysis Files

The notebook exports several intermediate datasets for further analysis and Tableau visualization, including:

- `exp_client_data.csv`
- `roster_web_data.csv`
- `completed_visits.csv`
- `valid_step_durations.csv`
- `df_roster_web_data.csv`
- `df_roster_sorted.csv`
- `df_confirm.csv`
- `visitor_summary2.csv`

## How to Run

1. Place the four source CSV files in the same directory as the notebook.
2. Install the required Python packages.
3. Open `project2 v5 19Aug.ipynb` in Jupyter Notebook or JupyterLab.
4. Run the notebook cells from top to bottom.
5. Use the exported CSV files for the Tableau dashboards.

Install the required packages with:

```bash
pip install pandas numpy scipy statsmodels matplotlib seaborn jupyter
```

## Important Methodological Notes

- Test and Control have different sample sizes, so demographic comparisons should focus on **proportions rather than raw counts**.
- A small number of demographic records contain missing values.
- Funnel progression means a client **reached a step at least once**; it does not guarantee a perfectly linear journey through every previous step.
- Step-time calculations include only valid forward transitions.
- Backward navigation is used as the operational definition of an error.
- Completion-rate and funnel-confirm percentages differ slightly because they use different denominators: total experiment clients vs. clients observed at the Start step.
- Statistical significance should be interpreted together with the practical size and business relevance of each effect.

## Final Conclusion

The A/B test provides strong evidence that the redesigned experience improves the core customer journey.

The **Test** variant increases completion, exceeds the required cost-effectiveness threshold, improves funnel progression, and reduces total completion time. Its main drawback is a **significant increase in backward navigation and errors**.

Overall, the results support moving forward with the redesign, provided the higher error rate is investigated and monitored during a phased rollout.

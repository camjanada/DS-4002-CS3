# Relationship between Movie Review Complexity and Polarization

**Research question:** Is a more complex movie review associated with a more polarizing review?

Fit a multiple linear regression to test whether review length (word count) and readability (Flesch-Kincaid grade level) are associated with review polarization, controlling for top-critic status. Polarization is how far a review's normalized score (0–1) falls from the midpoint of 0.5.

---

## Repository contents

| File | What it is |
|---|---|
| `ds_4002_project_1.ipynb` | Main analysis notebook: feature creation, exploratory analysis, regression model and model testing |
| `rotten_tomatoes_data_with_vars.csv.zip` | Cleaned dataset (zipped): 120,622 Rotten Tomatoes critic reviews from 2021-01-01 onward |
| `README.md` | This file |

The data is zipped because the unzipped CSV (about 47 MB) is over GitHub's 25 MB browser upload limit.

---

## Data

**Source:** [Massive Rotten Tomatoes Movies & Reviews](https://www.kaggle.com/datasets/andrezaza/clapper-massive-rotten-tomatoes-movies-and-reviews) by andrezaza on Kaggle, scraped from Rotten Tomatoes in April 2023.
**License:** CC0: Public Domain.

---

## How to run

### Option A: Run the analysis (recommended)

Uses the cleaned data already in this repo.

1. Download `rotten_tomatoes_data_with_vars.csv.zip` from this repo. **Do not unzip it.**
2. Open `ds_4002_project_1.ipynb` in [Google Colab](https://colab.research.google.com/) (*File → Upload notebook*).
3. Choose *Runtime → Run all*.
4. When Section 1 opens a file picker, upload `rotten_tomatoes_data_with_vars.csv.zip`.
5. Wait for it to finish. The grade-level step in Section 2 takes a minute or two. When prompted, allow Colab to download files; you will get `rotten_tomatoes_data_with_vars.csv` and `OUTPUT.zip`.

The notebook installs `textstat` itself. Everything else it uses (pandas, numpy, matplotlib, seaborn, statsmodels, scikit-learn) comes with Colab.

## Notebook structure

| Section | What it does |
|---|---|
| 1. Load the cleaned data | Uploads and reads the zipped dataset |
| 2. Create the model variables | Builds `normalized_score`, `polarization`, `word_count` and `grade_level` |
| 3. Exploratory data analysis | Summary statistics and distribution plots; saves the data with the new variables |
| 4. Model development | 80/20 train/test split, multiple linear regression with HC3 robust standard errors, assumption checks |
| 5. Model testing | Applies the model to the held-out 20%, compares it with a training-mean baseline and checks the plan's success criteria |
| 6. Export all outputs | Saves every table and graph into `OUTPUT.zip` |

**Model:**
`polarization = b0 + b1·log(1 + word_count) + b2·grade_level + b3·is_top_critic`

Word count and grade level are winsorized at the 1st and 99th percentiles before fitting, to limit the influence of extreme values.

---

## Outputs

All tables and graphs are collected in `OUTPUT.zip`, which downloads at the end of the notebook.

### Tables (CSV)

| File | Section | Contents |
|---|---|---|
| `summary_statistics.csv` | 3 | Count, mean, SD, min, quartiles and max of normalized score, polarization, word count and grade level |
| `coefficient_table.csv` | 4 | Each coefficient with its HC3 robust SE, t-value, p-value, 95% CI and standardized beta |
| `performance_table.csv` | 4 | Held-out MAE, RMSE and R² for the model vs. the training-mean baseline |
| `vif_table.csv` | 4 | Variance inflation factors (multicollinearity check) |
| `diagnostic_table.csv` | 4 | Breusch-Pagan, Jarque-Bera, Ramsey RESET and Durbin-Watson tests |
| `performance.csv` | 5 | R², MAE, RMSE and ±0.5 accuracy for model and baseline, on training and held-out data |
| `generalization.csv` | 5 | Training vs. held-out R², MAE and RMSE, with the difference |
| `criteria.csv` | 5 | YES/NO for each success criterion in the analysis plan |
| `margin_table.csv` | 5 | Share of held-out predictions within ±0.05, ±0.10, ±0.25 and ±0.5 |

### Graphs (PNG)

| File | Section | Contents |
|---|---|---|
| `eda_distributions.png` | 3 | Histograms of word count and polarization |
| `eda_polarization_by_length.png` | 3 | Average polarization at each review length |
| `eda_grade_level.png` | 3 | Histogram of Flesch-Kincaid grade level |
| `model_diagnostics.png` | 4 | Residuals vs. fitted, normal Q-Q plot, observed vs. predicted, coefficients with 95% CIs |
| `model_testing.png` | 5 | Held-out residuals, observed vs. predicted, MAE/RMSE vs. baseline, R² vs. the 0.20 goal |

### Data file

| File | Section | Contents |
|---|---|---|
| `rotten_tomatoes_data_with_vars.csv` | 3 | The cleaned data plus the four new variables (downloaded separately, not in `OUTPUT.zip`) |

---

## Results summary

| Measure | Result |
|---|---|
| Reviews modeled | 120,622 (96,497 training / 24,125 held-out) |
| Held-out R² | 0.002 (goal was > 0.20, **not met**) |
| Held-out MAE | 0.1114 (baseline 0.1117) |
| Held-out RMSE | 0.1321 (baseline 0.1322) |
| Overall F-test | p < 0.001 |

All three predictors are statistically significant (p < 0.05), and the model does slightly better than the baseline on new data. However, the effects are very small: review length, readability and critic status explain only about 0.2–0.3% of the variation in polarization. Longer reviews are slightly *less* polarized, and harder-to-read reviews are slightly *more* polarized. With this many reviews, even tiny effects become statistically significant, so the results should be read in terms of effect size. All findings are associative, not causal.

---

## Limitations

- Rotten Tomatoes critics do not represent all moviegoers, so the findings may not generalize to all reviewers.
- Word count and Flesch-Kincaid grade level do not fully capture a review's complexity or quality, and many reviews are short excerpts.
- Critics use different scoring systems (out of 4, 5 or 10, letter grades), so the standardized scores involve judgment calls about how to convert them.

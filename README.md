# Diamond Price Exploratory Data Analysis

> An R-based exploratory data analysis project investigating the characteristics associated with diamond prices and preparing candidate predictors for regression modelling.

[![R](https://img.shields.io/badge/R-4.x-276DC3?logo=r&logoColor=white)](https://www.r-project.org/)
[![R Markdown](https://img.shields.io/badge/R%20Markdown-analysis-1f425f)](https://rmarkdown.rstudio.com/)
[![Dataset](https://img.shields.io/badge/Dataset-53%2C940%20diamonds-6c757d)](#dataset)
[![Status](https://img.shields.io/badge/Status-EDA%20complete-success)](#project-status)

## Project Overview

Diamond prices vary substantially even among stones with similar physical characteristics. This project uses exploratory data analysis and statistical screening to understand which diamond attributes are most strongly associated with **price**.

The analysis was completed in R and combines:

- Data-quality assessment
- Duplicate detection and removal
- Missing-value checks
- IQR-based outlier treatment
- Distribution analysis
- Continuous-variable relationship analysis
- Pearson correlation analysis
- Categorical-variable comparison
- One-way ANOVA
- Exploratory predictor screening for a future regression model

The project is intentionally positioned as an **EDA and feature-screening study**, rather than a completed price-prediction model.

## Business / Analytical Question

**Which physical and quality-related characteristics provide the strongest evidence for explaining variation in diamond prices, and which variables should be carried forward into regression modelling?**

## Dataset

The dataset contains **53,940 diamonds and 10 variables**, matching the widely used `ggplot2::diamonds` structure. It contains price, diamond weight, quality grades, and physical dimensions. citeturn1search1turn1search5

| Variable | Type | Description |
|---|---|---|
| `price` | Numeric | Diamond price in US dollars |
| `carat` | Numeric | Diamond weight in carats |
| `cut` | Categorical | Cut quality: Fair, Good, Very Good, Premium, Ideal |
| `color` | Categorical | Colour grade from D (best) to J (worst) |
| `clarity` | Categorical | Clarity grade from I1 (lowest) to IF (highest) |
| `depth` | Numeric | Total depth percentage |
| `table` | Numeric | Width of the top relative to the widest point |
| `x` | Numeric | Length in millimetres |
| `y` | Numeric | Width in millimetres |
| `z` | Numeric | Depth in millimetres |

## Analytical Workflow

```text
Raw dataset
    ↓
Data quality checks
    ↓
Duplicate removal
    ↓
Outlier treatment for x, y and z
    ↓
Extreme z-value filtering
    ↓
Distribution analysis
    ↓
Continuous-variable relationship analysis
    ↓
Pearson correlation screening
    ↓
Categorical price comparisons
    ↓
One-way ANOVA
    ↓
Candidate predictor set
    ↓
Future regression modelling
```

## Key Findings

### 1. Data quality

- The original dataset contains **53,940 observations and 10 variables**.
- There are **146 exact duplicate rows**.
- No missing values are present in the original dataset.
- The raw data contains zero-dimensional records: **20 observations have z = 0**, with additional zero values in x and y. These values require domain-aware treatment because physical dimensions of zero are not meaningful for a diamond.
- After removing duplicates, the working dataset contains **53,794 observations**.
- The existing analysis applies IQR-based boundary replacement to `x`, `y`, and `z), affecting **31, 28, and 48 observations**, respectively.
- The subsequent `z > 2.06` and `z < 6.5` filter leaves **53,771 observations**, removing 23 additional records from the post-duplicate dataset.

### 2. Price distribution

Diamond price is strongly right-skewed.

- Mean price: approximately **$3,932.80**
- Median price: **$2,401**
- Minimum: **$326**
- Maximum: **$18,823**

The difference between the mean and median indicates that a relatively small number of expensive diamonds pull the mean upward.

### 3. Carat is the strongest individual numeric predictor

Pearson correlations with price show a very strong positive association between diamond weight and price:

| Variable | Correlation with price |
|---|---:|
| `carat` | **0.9215** |
| `x` | **0.8871** |
| `y` | **0.8886** |
| `z` | **0.8824** |
| `table` | 0.1267 |
| `depth` | -0.0111 |

The original, unfiltered dataset gives almost identical headline correlations: `carat` ≈ **0.9216**, `x` ≈ **0.8844**, `y` ≈ **0.8654**, `z` ≈ **0.8612**, `table` ≈ **0.1271**, and `depth` ≈ **-0.0106**. citeturn1search3turn1search9

**Interpretation:** larger/heavier diamonds tend to command substantially higher prices. However, correlation is not causation and does not by itself establish a predictive model.

### 4. The physical-dimension variables are highly collinear

After the cleaning/filtering steps, the correlations among the main size variables are extremely high:

- `carat`–`x`: **0.9774**
- `carat`–`y`: **0.9764**
- `carat`–`z`: **0.9761**
- `x`–`y`: **0.9985**
- `x`–`z`: **0.9915**
- `y`–`z`: **0.9912**

This is an important modelling consideration. Although `x`, `y`, and `z` are individually strong price predictors, including all of them alongside `carat` in a linear regression can introduce severe multicollinearity.

**Improvement to the original methodology:** correlation with the target should not be the only feature-selection rule. A future regression stage should diagnose multicollinearity using tools such as VIF and compare alternative feature specifications.

### 5. Diamond quality categories are associated with price

The categorical analysis indicates statistically significant differences in mean price across all three quality dimensions:

| Predictor | ANOVA F-statistic | p-value |
|---|---:|---:|
| `cut` | **172.09** | < 0.001 |
| `color` | **286.38** | < 0.001 |
| `clarity` | **212.41** | < 0.001 |

These results provide strong evidence that mean diamond price is not the same across the categories of cut, colour, or clarity.

For example, after the cleaning/filtering workflow:

- Median price ranges from about **$1,813 for Ideal cut** to **$3,282 for Fair cut**.
- Median price increases from about **$1,842 for D colour** to **$4,234.50 for J colour**.
- Median price varies substantially across clarity grades, from about **$1,080 for IF** to **$4,071.50 for SI2**.

These patterns should **not** be interpreted as “lower quality causes higher prices.” The dataset contains strong interactions and confounding relationships among carat, quality grades, and physical dimensions. A multivariable model is required before making stronger claims.

### 6. Variables selected for the original screening objective

Using the project's original screening logic:

- Numeric variables with |r| > 0.5: **carat, x, y, z**
- Categorical variables with significant ANOVA results: **cut, color, clarity**

This produces the candidate set:

```text
carat + x + y + z + cut + color + clarity
```

The variables `depth` and `table` are not selected by the original correlation threshold.

**Important:** this is an exploratory candidate set, not a final statistically optimal regression specification.

## Methodological Improvements

The original analysis provides a useful foundation, but a portfolio-grade workflow should make several distinctions explicit:

1. **Correlation is screening, not proof of predictive importance.** Pearson correlation measures linear association with price; it does not account for interactions, nonlinear effects, or confounding.
2. **ANOVA tests group mean differences.** It is more accurate to describe the categorical results as evidence of differences in mean price rather than “correlation.”
3. **Regression assumptions should be assessed separately.** Normality of the raw price variable is not a requirement for ordinary least squares. Linearity, independence, homoscedasticity, influential observations, and residual behaviour are more relevant.
4. **Multicollinearity must be considered.** `carat`, `x`, `y`, and `z` contain highly overlapping information.
5. **Outlier rules should be domain-aware.** The IQR treatment is useful for exploratory robustness, but physical impossibilities and genuine high-value diamonds should not automatically be treated as errors.
6. **A final modelling stage should compare alternatives.** For example, a model using `carat` plus quality variables could be compared with a model using engineered physical size measures.

## Recommended Next Step

The natural continuation of this project is a **diamond-price regression study**:

- Encode categorical predictors appropriately
- Diagnose multicollinearity with VIF
- Engineer interpretable size features where useful
- Split data into training and test sets
- Fit multiple linear regression and nonlinear/ensemble models
- Evaluate with **RMSE, MAE and R²**
- Validate residual assumptions
- Compare model performance
- Interpret the most important predictors
- Optionally deploy the final model as a small web application

## Tools & Technologies

- **R**
- **R Markdown**
- **readxl** — Excel import
- **dplyr** — data manipulation
- **tidyr** — data preparation
- **ggplot2** — data visualization
- **corrplot** — correlation visualization
- **gridExtra** — plot arrangement

## Repository Structure

```text
Diamonds-dataset-analysis/
├── DiamondPricesData.xlsx
├── Exploratory_Data_Analysis_2.Rmd
├── DATA_DICTIONARY.md
├── LICENSE
├── README.md
└── .gitignore
```

## Reproducibility

The analysis notebook is preserved in its original form in `Exploratory_Data_Analysis_2.Rmd`.

For a cleaner reproduction workflow, place the repository on your local machine and ensure the Excel file is available in the project directory. The current R Markdown contains a machine-specific working-directory setting; this README intentionally documents that limitation rather than modifying the original notebook.

## Limitations

- This project is exploratory and does not yet present a validated price-prediction model.
- Observational associations should not be interpreted as causal effects.
- The categorical variables are ordinal in nature, but their relationship with price is not necessarily linear.
- Several physical-size variables are highly collinear.
- The current outlier treatment and z-value filter should be validated against domain knowledge before being used in a production model.
- The exact R Markdown workflow is retained unchanged for academic traceability.

## Project Status

**EDA complete — regression modelling is the next phase.**

---

### Author

**Timothy Mugisha**  
Data Science & Analytics Student | ML Enthusiast | BI Developer

- GitHub: [@timomugishan8-ai](https://github.com/timomugishan8-ai)
- LinkedIn: [Timothy Mugisha](https://www.linkedin.com/in/timothy-mugisha-39ba2a2b/)

If this project is useful, feel free to explore the repository and connect.

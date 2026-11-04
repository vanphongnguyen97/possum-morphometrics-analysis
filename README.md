# 🐾 Mountain Brushtail Possum Morphometrics — EDA, Hypothesis Testing & Regression

A statistical analysis of body measurements from **104 mountain brushtail possums** captured at seven sites across Australia, built in Python and Jupyter. The project covers the full workflow: cleaning a messy real-world dataset, exploratory data analysis, two hypothesis tests, and four linear regression models used for **statistical inference** (not prediction).

> Completed as Assessment 2 for **BUSA8000 – Techniques in Business Analytics** at Macquarie University (Macquarie Business School), 2025.

---

## 📌 Project Overview

| | |
|---|---|
| **Goal** | Understand how possum body measurements relate to each other and differ across sex and population, to support Australian wildlife conservation efforts |
| **Data** | `possum.csv`, 104 rows × 14 columns |
| **Tools** | Python, Jupyter, pandas, NumPy, Matplotlib, Seaborn, SciPy, statsmodels |
| **Methods** | Data cleaning, median imputation, IQR outlier treatment, Welch's t-test, OLS regression, VIF multicollinearity check |

---

## 📂 Repository Structure

```
.
├── possum_analysis.ipynb   # Main notebook: cleaning, EDA, hypothesis tests, regressions
├── possum.csv              # Raw dataset (as provided, with data quality issues)
└── README.md
```

---

## 🗂️ Dataset

Each row is one possum. Measurements are in millimetres except where noted.

| Column | Description |
|---|---|
| `case` | Observation ID |
| `site` | Trapping site (1–7) |
| `Pop` | Population: `Vic` (Victoria) or `other` (New South Wales / Queensland) |
| `gender` | `m` / `f` |
| `age` | Age in years |
| `hdlngth` | Head length |
| `skullw` | Skull width |
| `totlngth` | Total length (cm) |
| `taill` | Tail length (cm) |
| `footlgth` | Foot length |
| `earconch` | Ear conch length |
| `eye` | Distance from medial canthus to lateral canthus of right eye |
| `chest` | Chest girth (cm) |
| `belly` | Belly girth (cm) |

*Source: Lindenmayer, D. B., et al. (1995). Morphological variation among populations of the mountain brushtail possum. Australian Journal of Zoology, 43, 449–458. Distributed in the R package `DAAG`. The version used here was deliberately modified with errors for the data-cleaning exercise.*

---

## 🔍 Methodology

### 1. Data Loading & Inspection
- Inspected data types, summary statistics and unique values of every column.
- Found numeric columns stored as text (`age`, `skullw`, `footlgth`, `belly`), placeholder strings for missing data, comma decimal separators, and impossible negative values (e.g. `taill = -22`).

### 2. Data Cleaning
- Replaced placeholder strings (`?`, `n/a`, `--`, etc.) with `NaN`.
- Converted `,` decimals to `.` and cast columns to numeric.
- Treated negative measurements as missing.

### 3. Missing Values
- Filled the missing `case`, `site` and `Pop` by inspecting the surrounding sequence (case 95 → site 7 → `other`).
- Compared medians by `Pop × gender` groups; differences were small, so numeric gaps were filled with the **overall column median**.

### 4. Outlier Treatment
- Detected outliers with the **1.5 × IQR rule** and replaced them with the column median.
- Re-checked iteratively until no outliers remained (`hdlngth`, `skullw` needed extra passes).
- Verified with before/after boxplots, histograms and skewness (all |skew| < 0.6 after cleaning).

### 5. Hypothesis Testing (Welch's two-sample t-test, α = 0.05)

| # | Hypothesis | t | p-value | Decision |
|---|---|---|---|---|
| H1 | Head length differs between males and females | 1.70 | 0.092 | Fail to reject H₀ |
| H2 | Skull width differs between Victorian and other populations | 0.28 | 0.783 | Fail to reject H₀ |

### 6. Regression Analysis (OLS, statsmodels)

**Simple linear regressions**

| Model | Equation | R² | p (slope) |
|---|---|---|---|
| Head length ~ Chest | `hdlngth = 80.40 + 0.468 × chest` | 0.162 | < 0.001 |
| Tail length ~ Total length | `taill = 20.88 + 0.186 × totlngth` | 0.235 | < 0.001 |
| Ear conch ~ Foot length | `earconch = −1.69 + 0.728 × footlgth` | **0.595** | < 0.001 |

**Multiple linear regression — Chest girth**

- **Predictors:** age, head length, skull width, total length, tail length, foot length, ear conch, eye, belly, population (dummy), gender (dummy).
- Categorical variables were one-hot encoded, and multicollinearity was checked with a correlation heatmap and **VIF** (all predictors < 7.5).
- **R² = 0.503, Adj. R² = 0.444, F = 8.48 (p < 0.001)**
- Significant predictors: **belly girth** (β = 0.205, p = 0.005) and **skull width** (β = 0.207, p = 0.046).

---

## 💡 Key Findings

- **No significant sex or population differences** were found in head length or skull width at the 5% level.
- **Foot length is a strong indicator of ear length**: it explains about 60% of the variation.
- Head length, tail length and total length are positively related but only modestly (R² of 16–24%).
- **Chest girth is best explained by belly girth and skull width.** Together, the 11 predictors explain about half of the variation in chest size.

---

## ▶️ How to Run

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>

# 2. Install dependencies
pip install numpy pandas matplotlib seaborn scipy statsmodels jupyterlab

# 3. Launch Jupyter and open the notebook
jupyter lab possum_analysis.ipynb
```

Keep `possum.csv` in the same folder as the notebook.

---

## 👤 Author

**Van Phong Nguyen**
Master's student, Macquarie University
📧 phongnguyenvan97dn@gmail.com

---

*This repository is shared for portfolio purposes. If you are a current BUSA8000 student, please follow Macquarie University's academic integrity policy.*

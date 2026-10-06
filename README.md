<div align="center">

# 🚢 Titanic Passenger Analytics Dashboard

**Auditing messy passenger data, engineering useful features, and exploring it all in an interactive Python dashboard**

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Cleaning-150458?logo=pandas&logoColor=white)
![Plotly](https://img.shields.io/badge/Plotly-Interactive%20Charts-3F4F75?logo=plotly&logoColor=white)
![Dash](https://img.shields.io/badge/Dash-Interactive%20Dashboard-008DE4?logo=plotly&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

</div>

---

## 📌 Table of Contents
- [Project Overview](#-project-overview)
- [Important Data Warning](#%EF%B8%8F-important-data-warning)
- [Business Questions & Answers](#-business-questions--answers)
- [Dataset](#-dataset)
- [Tech Stack](#-tech-stack)
- [Workflow](#-workflow)
- [Data Quality Audit](#-data-quality-audit)
- [Feature Engineering](#-feature-engineering)
- [Dashboard](#-dashboard)
- [Key Insights](#-key-insights)
- [Recommendations & Next Steps](#-recommendations--next-steps)
- [Repository Structure](#-repository-structure)
- [How to Run](#-how-to-run)
- [Author](#-author)

---

## 🎯 Project Overview

This project takes a raw passenger manifest (**418 Titanic passengers**) through a complete analytics pipeline, entirely in **Python**:

**audit → clean → engineer features → compute KPIs → explore visually → interactive dashboard**

Instead of cleaning blindly, every data issue goes through a documented **issue → evidence → decision → action** log, so each cleaning choice is justified. The result is a filterable **Dash dashboard** with KPI cards and tabbed charts, plus a set of business-style findings about class, fares, family travel, and data quality.

## ⚠️ Important Data Warning

The `Survived` column in this file is **not reliable for analysis**. In this dataset, **all 152 women are marked as survivors and all 266 men as non-survivors** (100% vs 0%). Real survival outcomes were never that clean, which indicates the label follows a simple sex-based rule rather than true records.

For that reason, this project:
- flags the issue in the audit log,
- shows a warning banner in the dashboard, and
- **does not draw any survival conclusions**.

## ❓ Business Questions & Answers

| # | Question | Answer |
|---|---|---|
| 1 | **Who were the passengers?** | 418 passengers: **63.6% male, 36.4% female**, average age **29.4** (median 26) |
| 2 | **What was the class mix?** | **3rd class 52.2%** (218), 1st class 25.6% (107), 2nd class 22.2% (93) |
| 3 | **How did fares differ by class?** | Median fare: about **$60 in 1st class**, **$15.75 in 2nd**, and about **$7.90 in 3rd**. The maximum fare was **$512.33** |
| 4 | **Where did passengers board, and who boarded where?** | **Cherbourg:** 54.9% were 1st class (56 of 102). **Southampton:** supplied **65.1% of all 3rd-class passengers** (142 of 218). **Queenstown:** almost entirely 3rd class (41 of 46) |
| 5 | **Did people travel alone?** | Yes, mostly: **60.5% traveled solo**. Average family size was 1.84, and the largest family on board was **11** |
| 6 | **How do titles relate to age?** | Strongly. Median age: **Master 7**, **Miss 22**, **Mr 26**, **Mrs 35.5**, Officer/Other 44 (after imputation) |
| 7 | **How complete is the cabin data?** | Only **21.8%** of passengers have a cabin. Coverage depends on class: **1st ≈ 75%, 2nd ≈ 8%, 3rd ≈ 2%** |
| 8 | **Were tickets shared?** | Yes. **97 passengers (23.2%)** traveled on shared tickets, in groups of 2 to 5, so raw fares can represent more than one person |
| 9 | **Can we analyze survival?** | **No.** The `Survived` column is 100% female / 0% male, so it is not trustworthy (see warning above) |
| 10 | **How much data had to be filled in?** | **86 ages (20.6%)** were imputed. 1 missing fare was left as is, and cabin gaps were kept as an "Unknown" deck |

## 🗂 Dataset

| Property | Details |
|---|---|
| **File** | `File 3.csv` (Titanic passenger list, IDs 892 to 1309) |
| **Rows × Columns** | 418 × 12 raw |
| **Fields** | `PassengerId`, `Survived`, `Pclass`, `Name`, `Sex`, `Age`, `SibSp`, `Parch`, `Ticket`, `Fare`, `Cabin`, `Embarked` |
| **Missing values** | `Age` 86, `Cabin` 327 (78.2%), `Fare` 1 |
| **Duplicates** | 0 |

## 🛠 Tech Stack

| Purpose | Tools |
|---|---|
| Language | Python |
| Data cleaning | Pandas, NumPy, `re` (regex) |
| Visualization | Plotly Express |
| Dashboard | Dash (`dcc`, `html`, callbacks) |
| Environment | Jupyter Notebook |

## 🔄 Workflow

```
Raw Data → Audit → Cleaning → Feature Engineering → KPIs → EDA Charts → Dash Dashboard → Insights
```

1. **Exploration:** checked structure, nulls, duplicates, and expected category values
2. **Audit log:** documented 12 data issues with evidence, decision, and action
3. **Cleaning:** stripped whitespace, normalized titles, fixed data types
4. **Imputation:** filled missing ages with a hierarchical median
5. **Feature engineering:** built title, deck, ticket, and family features
6. **KPIs and EDA:** computed headline metrics and explored distributions, correlations, and class/port mix
7. **Dashboard:** built an interactive Dash app with filters and tabs
8. **Insights:** summarized business findings and data-quality warnings

## 🧹 Data Quality Audit

| Issue | Evidence | Decision & Action | Status |
|---|---|---|---|
| **Missing Age** | 86 rows | Impute with median by **Title × Pclass**, then Title, then global median. Add an `Age_Imputed` flag | ✅ Done |
| **Missing Cabin** | 78.2% missing | Don't impute. Create `HasCabin`, `CabinCount`, and `Deck` (with an "Unknown" category) | ✅ Done |
| **Whitespace** | Stray spaces in text | Strip all text columns | ✅ Done |
| **Mixed ticket formats** | Tickets contain letters | Extract `TicketPrefix` | ✅ Done |
| **Shared tickets** | Several passengers per ticket | Derive `TicketGroupSize` | ✅ Done |
| **Multi-value cabins** | Some rows list several cabins | Derive `CabinCount` | ✅ Done |
| **Wrong data types** | Categories stored as int/object | Cast to `category` | ✅ Done |
| **Age under 1** | 5 infant rows | Keep exact fractional ages | ✅ Done |
| **Suspicious `Survived`** | 100% female / 0% male | Flag it, add a warning banner, avoid causal claims | ✅ Done |
| **Fare outliers** | Max fare $512.33 | Keep 1st-class fares, use log scale in charts | ✅ Done |
| **Missing Fare** | 1 row | Planned: impute with the median fare of Pclass 3 and port S | ⏳ Not yet applied |
| **Zero fares** | 2 rows | Planned: keep and add an `IsZeroFare` flag | ⏳ Not yet applied |
| **Fare per person** | Shared tickets share one fare | Planned: `FarePerPerson = Fare / TicketGroupSize` | ⏳ Not yet applied |

**Data validation checks passed:** all `Master` title holders are male with a maximum age of 14.5, and `Embarked` contains only the expected values (`C`, `Q`, `S`).

## 🧩 Feature Engineering

| New Column | Logic |
|---|---|
| `Title` | Extracted from `Name`. Rare titles grouped into *Officer/Other*, and Ms/Mlle/Mme normalized to Miss/Mrs |
| `HasCabin`, `CabinCount`, `Deck` | Parsed from `Cabin` (unknown deck labeled "Unknown") |
| `TicketPrefix` | Letter prefix of the ticket, or `NUMERIC` |
| `TicketGroupSize` | Passengers sharing the same ticket |
| `FamilySize` | `SibSp + Parch + 1` |
| `IsAlone` | `1` when `FamilySize == 1` |
| `Age_Imputed` | `1` for rows whose age was estimated |



**Features:**
- 🎛 **Filters:** passenger class, sex, and port of embarkation (multi-select)
- 📊 **KPI cards** that update with the filters: total passengers, average age, median fare
- 🗂 **Three tabs:**
  - *Overview & Class:* class mix by embarkation port
  - *Demographics:* age distribution by title
  - *Fares & Grouping:* fare distribution by class (log scale)
- ⚠️ **Data-quality warning banner** about the `Survived` column

**Headline KPIs (full dataset):**

| KPI | Value |
|---|---|
| Total Passengers | 418 |
| Average / Median Age | 29.4 / 26 |
| Median Fare | $14.45 |
| % Male / % Female | 63.6% / 36.4% |
| % Solo Travelers | 60.5% |
| Average Family Size | 1.84 |
| % With Cabin Info | 21.8% |
| % Age Imputed | 20.6% |

## 💡 Key Insights

1. **🎟 Third class dominated volume, first class dominated price.** 3rd class was **52.2% of passengers** with a median fare near **$7.90**, versus a median of **$60** in 1st class. A small group of high-fare passengers carried most of the ticket value.

2. **🗄 Missing cabin data is structural, not random.** About **75% of 1st-class** passengers have a cabin recorded versus roughly **2% of 3rd-class** ones. "Missing cabin" is therefore a strong proxy for ticket tier, so it was kept as an `Unknown` deck instead of being deleted or imputed.

3. **👥 Titles predict age well.** Median ages are Master 7, Miss 22, Mr 26, and Mrs 35.5. A single global average would have distorted ages, which is why imputation used **Title × Class** medians.

4. **🎫 Raw fares often cover several people.** 23.2% of passengers shared a ticket (groups of 2 to 5), so a fare per person is the fairer price measure than the raw ticket fare, which reached $512.33.

5. **🌍 Each port served a different market.** Cherbourg skewed to 1st class (54.9%), Southampton supplied about two-thirds of all 3rd-class passengers, and Queenstown was almost entirely 3rd class.

6. **🧍 Most people traveled alone.** 60.5% were solo travelers, and the biggest family group on board had 11 members.

7. **⚠️ The survival label is unreliable.** All women are marked as survivors and all men as non-survivors, so it should not be used for modeling or conclusions without a verified source.

## ✅ Recommendations & Next Steps

- **Verify the source of `Survived`** before any survival analysis or machine-learning model, since the current label looks rule-generated
- **Finish the planned fixes:** impute the single missing fare, add `IsZeroFare`, and compute `FarePerPerson`
- **Use per-person fares** for any pricing or willingness-to-pay analysis
- **Keep `Deck = Unknown` as a category** rather than imputing cabins
- **Extend the dashboard** with an age slider and a title filter, and move it into a standalone `app.py` for easier deployment

## 📁 Repository Structure

```
titanic-passenger-analytics-dashboard/
│
├── Data_Files/
│   ├── Raw_Files/
│   │   └── File 3.csv              # Raw passenger data
│   └── Cleaned_Files/
│       └── cleaned_data.csv        # Cleaned + engineered dataset
│
├── Task5.ipynb                     # Audit, cleaning, KPIs, EDA & dashboard
├── images/
│   └── dashboard.png               # Dashboard screenshot
└── README.md
```

> Adjust the tree to match your actual repository layout.

## 🚀 How to Run

```bash
# 1. Clone the repository
git clone https://github.com/kirlosmagdy/titanic-passenger-analytics-dashboard.git
cd titanic-passenger-analytics-dashboard

# 2. Install dependencies
pip install pandas numpy plotly dash jupyter

# 3. Open the notebook
jupyter notebook Task5.ipynb
```

Run the cells in order. The final cell launches the dashboard inline at `http://127.0.0.1:8050`.

> ⚠️ Update the file paths in the notebook (`pd.read_csv(...)` and `df.to_csv(...)`) to match your local folders.

## 👤 Author

**Kirolos Magdy**: Data Engineer | Analytics Engineer
Faculty of Computers and Data Science, Alexandria University

[![GitHub](https://img.shields.io/badge/GitHub-kirlosmagdy-181717?logo=github)](https://github.com/kirlosmagdy)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Kirolos%20Magdy-0A66C2?logo=linkedin)](https://linkedin.com/in/kirolos-magdy1/)

---

<div align="center">

⭐ If you found this project useful, consider giving it a star!

</div>

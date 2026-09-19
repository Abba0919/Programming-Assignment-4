# Programming-Assignment-4

## Llorin, Merwin Abba B. 

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Dataset Description](#-dataset-description)
- [Output Summary](#-output-summary)
- [Problem Specifications & Solutions](#-problem-specifications--solutions)
  - [A. Visayas Communication DataFrame](#a-visayas-communication-dataframe)
  - [B. Visayas Female DataFrame](#b-visayas-female-dataframe)
  - [C. Category-Average Visualization](#c-category-average-visualization)
- [Results & Interpretation](#-results--interpretation)
- [Project File Structure](#-project-file-structure)
- [Prerequisites & Requirements](#-prerequisites--requirements)
- [Edge Cases Handled](#-edge-cases-handled)

---

## 🔍 Overview

This project contains the Python solution for **Experiment 4: Data Wrangling and Data Visualization**
using the ECE Board Exam 2 dataset. The tasks demonstrate fundamental data analysis concepts including:

- Boolean filtering with multiple explicit conditions (`&` operator)
- Column selection and DataFrame construction
- Derived columns using row-wise aggregation (`.mean(axis=1)`)
- Grouped summary statistics (`groupby()`)
- Multi-panel bar charts with Matplotlib (`plt.subplots()`)

All tables and plotted values are computed directly from the dataset. No row, category mean, or bar
height is typed manually.

---

## 📊 Dataset Description

The dataset `board2.csv` contains **30 records** of ECE board exam takers.

| Column | Type | Description |
|---|---|---|
| `Name` | `str` | Student identifier (S1–S30) |
| `Gender` | `str` | `Male`, `Female` |
| `Track` | `str` | `Instrumentation`, `Communication`, `Microelectronics` |
| `Hometown` | `str` | `Luzon`, `Visayas`, `Mindanao` |
| `Math` | `int` | Mathematics subject score |
| `Electronics` | `int` | Electronics subject score |
| `GEAS` | `int` | GEAS subject score |
| `Communication` | `int` | Communication subject score |
| `Average` | `float` | **Derived** — mean of the four subject scores |

> **Note:** The raw file does not contain an `Average` column, so it is computed as the mean of
> `Math`, `Electronics`, `GEAS`, and `Communication`. It is added to a copy named `board` so that
> the original DataFrame `df` stays unchanged, as required by the handout.

---

## ⚙️ Output Summary

| Object | Type | Key Logic |
|---|---|---|
| `df` | `DataFrame` | Original dataset, never modified |
| `board` | `DataFrame` | Copy of `df` with the derived `Average` column |
| `VisComm` | `DataFrame` | Filters `Hometown == "Visayas"` **and** `Track == "Communication"`, then selects 5 columns |
| `VisFemale` | `DataFrame` | Filters `Hometown == "Visayas"` **and** `Gender == "Female"`, then selects 5 columns |
| `VisFemale_60` | `DataFrame` | Subset of `VisFemale` where `Average >= 60`, stored under a new name |
| `mean_by_track` | `Series` | `board.groupby("Track")["Average"].mean()` |
| `mean_by_gender` | `Series` | `board.groupby("Gender")["Average"].mean()` |
| `mean_by_hometown` | `Series` | `board.groupby("Hometown")["Average"].mean()` |

---

## 🧩 Problem Specifications & Solutions

### A. Visayas Communication DataFrame

**Specification:** Create a DataFrame named `VisComm` containing students whose `Hometown` is
Visayas and whose `Track` is Communication, keeping only `Name`, `Gender`, `Math`, `Electronics`,
and `Average`. Display it together with its number of rows.

**Solution:**

```python
VisComm = board[(board["Hometown"] == "Visayas") & (board["Track"] == "Communication")][
    ["Name", "Gender", "Math", "Electronics", "Average"]
]

print("Number of rows in VisComm:", VisComm.shape[0])
```

**Key logic:** Both conditions are written explicitly inside one boolean mask joined by `&`, and the
column list is applied only *after* the filtering, so the selection never happens on unfiltered data.

**Result:** 5 rows.

---

### B. Visayas Female DataFrame

**Specification:** Create `VisFemale` containing students whose `Hometown` is Visayas and whose
`Gender` is Female, keeping only `Name`, `Track`, `GEAS`, `Electronics`, and `Average`. Then display
only the rows whose `Average` is at least 60, without overwriting `VisFemale`.

**Solution:**

```python
VisFemale = board[(board["Hometown"] == "Visayas") & (board["Gender"] == "Female")][
    ["Name", "Track", "GEAS", "Electronics", "Average"]
]

# Second filter saved to a new name - VisFemale stays as it is
VisFemale_60 = VisFemale[VisFemale["Average"] >= 60]
```

**Key logic:** The `>= 60` filter returns a new object assigned to `VisFemale_60`. A short print
statement compares both row counts to prove the original was preserved.

**Result:** `VisFemale` has 6 rows; 4 of them have `Average >= 60`.

---

### C. Category-Average Visualization

**Specification:** For `Track`, `Gender`, and `Hometown`, compute the mean of `Average` per category,
display the three summary tables, plot them as three bar charts inside one figure, and write three
interpretation statements.

**Solution:**

```python
mean_by_track = board.groupby("Track")["Average"].mean()
mean_by_gender = board.groupby("Gender")["Average"].mean()
mean_by_hometown = board.groupby("Hometown")["Average"].mean()

fig, axes = plt.subplots(1, 3, figsize=(15, 5))

for ax, (data, feature, title) in zip(axes, summaries):
    ax.bar(data.index, data.values, color="steelblue", edgecolor="black")
    ax.set_title(title)
    ax.set_xlabel(feature)
    ax.set_ylabel("Mean Average Score")
    ax.set_ylim(0, 100)          # consistent scale for all three charts
```

**Key logic:** `groupby()` produces the category means; a single `for` loop draws the three subplots
so the styling stays identical. All three axes share the `0–100` score scale, every axis is labeled,
x-tick labels are rotated for readability, and the numeric value is printed above each bar.

---

## 📈 Results & Interpretation

**Mean `Average` per category**

| Track | Mean | | Gender | Mean | | Hometown | Mean |
|---|---|---|---|---|---|---|---|
| Communication | 67.97 | | Female | 66.62 | | Luzon | 68.08 |
| Instrumentation | 65.22 | | Male | 67.18 | | Mindanao | 66.68 |
| Microelectronics | 67.50 | | | | | Visayas | 65.75 |

**Interpretation statements**

1. Among the three tracks, **Communication** has the highest sample mean Average (67.97), followed by
   Microelectronics (67.50) and Instrumentation (65.22).
2. Between the two genders, **Male** students have the higher sample mean Average (67.18) compared to
   Female students (66.62).
3. Among the three hometowns, **Luzon** has the highest sample mean Average (68.08), ahead of
   Mindanao (66.68) and Visayas (65.75).

> These are sample means of the 30 records in this dataset only. The gaps between groups are small
> relative to the spread of the individual scores, and a difference in group means does not by itself
> establish that Track, Gender, or Hometown causes a higher board exam score.

---

## 📁 Project File Structure

```
ECE2112---EXPERIMENT-4/
│
├── ECE2112_Experiment4.ipynb   # Main notebook (all cells executed)
├── board2.csv                  # ECE Board Exam 2 dataset
└── README.md                   # This file
```

---

## 🧾 Prerequisites & Requirements

| Requirement | Version used |
|---|---|
| Python | 3.10 or higher |
| pandas | 2.0+ |
| matplotlib | 3.7+ |
| Jupyter Notebook / JupyterLab | any recent version |

Install the libraries with:

```bash
pip install pandas matplotlib notebook
```

---

### Using Jupyter Notebook

1. Place `ECE2112_Experiment4.ipynb` and `board2.csv` **inside the same folder**.
2. Open the notebook in Jupyter Notebook or JupyterLab.
3. Select **Kernel → Restart Kernel and Run All Cells**.
4. The notebook runs from beginning to end without errors and reproduces every table and figure.

---

## ⚠️ Edge Cases Handled

- **Missing `Average` column** — the raw dataset stores only the four subject scores, so `Average` is
  derived instead of assumed to exist.
- **Original DataFrame preserved** — `df` is never modified; all derived work happens on `board`.
- **`VisFemale` not overwritten** — the `Average >= 60` filter is assigned to `VisFemale_60`, and a
  printed row-count comparison confirms both objects still exist.
- **Ambiguous label `Communication`** — it appears both as a value of `Track` and as a subject column.
  Each is referenced through the correct column name so the filters never mix them up.
- **Consistent plot scale** — all three subplots use `set_ylim(0, 100)` so the bar heights stay
  comparable across features instead of auto-scaling per chart.
- **Readable category labels** — x-tick labels are rotated by 20° so long names such as
  `Microelectronics` do not overlap.
- **Relative file path** — `pd.read_csv("board2.csv")` works on any machine as long as the dataset sits
  beside the notebook, so no absolute path needs editing.

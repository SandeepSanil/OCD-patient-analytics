# OCD Patient Analytics — Power BI Dashboard

An interactive Power BI dashboard analyzing a synthetic OCD (Obsessive-Compulsive Disorder) patient dataset, covering demographics, clinical severity (Y-BOCS scores), obsession/compulsion patterns, comorbidities, and treatment.

## 📊 Overview

This project cleans a raw OCD patient dataset and turns it into a 4-page interactive Power BI report with KPI cards, trend charts, comparison visuals, and cross-filtering slicers.

**Pages:**
1. **Overview** — headline KPIs, diagnosis trend over time, severity by obsession type, family history split
2. **Demographics** — patient distribution by age group, gender, ethnicity, marital status, education level
3. **Clinical Details** — obsession/compulsion type breakdown, average Y-BOCS scores by age group, medication and prior diagnosis distribution
4. **Insights** — comorbidity patterns (depression/anxiety) by obsession type, severity trends by age group

## 🗂️ Dataset

- **Source file:** `ocd_patient_dataset.csv`
- **Size:** 1,500 patient records
- **Key fields:** Age, Gender, Ethnicity, Marital Status, Education Level, OCD Diagnosis Date, Duration of Symptoms, Obsession Type, Compulsion Type, Y-BOCS Score (Obsessions/Compulsions), Depression Diagnosis, Anxiety Diagnosis, Family History of OCD, Medications, Previous Diagnoses

> Note: This is a synthetic/educational dataset used for dashboarding practice, not real patient data.

## 🧹 Data Cleaning

Performed before loading into the model:

| Issue | Fix |
|---|---|
| `Patient ID` reused across different patients (not a valid key) | Added a true unique `Row ID`; original field kept as `Patient Ref ID` |
| Nulls in `Previous Diagnoses` / `Medications` | Filled with `"None"` |
| `OCD Diagnosis Date` as text | Converted to Date type; added `Diagnosis Year` and `Diagnosis Month` |
| No age brackets | Added `Age Group` (18–25, 26–35, 36–45, 46–55, 56–65, 66–75) |
| No combined severity score | Added `Y-BOCS Total Score` (Obsessions + Compulsions, 0–80) and `Severity Level` (Mild → Extreme) |

Cleaning was done both as a pre-processed CSV and replicated in Power Query (M) so every transformation step is visible inside the `.pbix`.

## 📐 Key Measures (DAX)

```DAX
Total Patients = COUNTROWS('ocd_patient_dataset')

Avg Age = AVERAGE('ocd_patient_dataset'[Age])

Avg YBOCS Total = AVERAGE('ocd_patient_dataset'[Y-BOCS Total Score])

Pct Family History = 
DIVIDE(
    CALCULATE(COUNTROWS('ocd_patient_dataset'), 'ocd_patient_dataset'[Family History of OCD] = "Yes"),
    [Total Patients]
)

Pct Comorbid = 
DIVIDE(
    CALCULATE(
        COUNTROWS('ocd_patient_dataset'),
        'ocd_patient_dataset'[Depression Diagnosis] = "Yes" || 'ocd_patient_dataset'[Anxiety Diagnosis] = "Yes"
    ),
    [Total Patients]
)
```

## 📈 Visuals

**Overview**
- 5 KPI cards: Total Patients, Average Age, Avg Y-BOCS Total, % Family History, % Comorbid
- Line chart: Patients diagnosed per year
- Line + stacked column: Diagnoses by obsession type with average severity trend
- Clustered bar: Compulsion type by gender
- Donut: Family history of OCD

**Demographics**
- Bar charts: patient count by age group, ethnicity, marital status, education level
- Donut: gender split

**Clinical Details**
- Bar charts: patients by obsession type, patients by compulsion type
- Clustered column: average Y-BOCS (obsession vs. compulsion) by age group
- Donut: medication type distribution
- Bar chart: previous diagnoses distribution

**Insights**
- 100% stacked bars: depression/anxiety diagnosis rate by obsession type
- Line chart: average Y-BOCS total by age group

All pages share **Gender**, **Age Group**, and **Ethnicity** slicers with synced cross-filtering.

## 🛠️ Tools Used

- **Power BI Desktop** — data modeling, DAX measures, report design
- **Power Query (M)** — data cleaning and transformation
- **Python (pandas)** — initial dataset inspection and pre-cleaning

## 🚀 How to Use

1. Clone this repo
2. Open `ocd.pbix` in Power BI Desktop
3. If prompted, point the data source to your local copy of `ocd_patient_dataset_cleaned.csv`
4. Refresh the data (Home → Refresh)

## 📌 Notes / Limitations

- Dataset is synthetic and for learning/demo purposes only — not suitable for clinical or research conclusions
- `Patient ID` in the source file is not a reliable unique key; use `Row ID` for any row-level counts


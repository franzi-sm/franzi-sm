# Power BI Management Reporting — KMU Demo

> End-to-end business intelligence project for owner-led SMEs (KMU/Mittelstand): 
> from raw monthly Excel exports to a governed Power BI data model and an 
> interactive management reporting dashboard with plan/actual, EBIT, and 
> regional KPI insights.

---

## Project Overview

This project covers the full analytics pipeline for SME management reporting — 
data ingestion from Excel, dynamic transformation logic, dimensional data 
modelling in Power BI, and a multi-page interactive report built for 
board-level review.

**Data period:** synthetic monthly data, DACH region  
**Data source:** synthetic dataset (4 business segments × 5 regions), built to 
mirror a typical KMU ERP/Excel export

---

## Tech Stack

`Power BI` · `Power Query (M)` · `DAX` · `Star Schema Modelling`

---

## Pipeline

### 1. Extract
- Monthly Excel workbook (one sheet per period) hosted on SharePoint/OneDrive
- A single centralized connection query (`fx_Workbook`) so every downstream 
  query reads from one point of truth

### 2. Transform (Power Query / M)
- Pattern-based sheet detection (`fnIstMonatsblatt`) — identifies valid 
  monthly-data sheets by name pattern instead of a hardcoded year, so new 
  months/years are picked up automatically on refresh
- Per-sheet reshaping (`fnTransformBlatt`) — derives the reporting date from 
  the sheet name and reshapes each sheet into the standard fact-row layout
- Fact table kept clean: keys and KPI values only, no descriptive text columns

### 3. Load & Model (Power BI)
- Star-schema dimensional data model
- Fact table: `Fakt_Umsatz` (revenue, plan, EBIT, order intake, headcount costs)
- Dimension tables: `Dim_Datum` (daily grain, for correct `DATEADD` time 
  intelligence), `Dim_Regionen` (with lat/long for map visuals)

---

## Dashboard Pages

| Page | Key Metrics |
|------|-------------|
| Management Report | Umsatz vs. Plan/LM · EBIT & EBIT-Marge · regional map · order intake trend · headcount cost comparison |
| Quartalsreport | Condensed quarterly KPI view for board-level review |

---

## Key Findings & Technical Highlights

- A daily-grain date dimension is required for `DATEADD`-based time 
  intelligence to return correct, non-blank results — monthly grain breaks it
- An earlier concatenated text key (`MonatJahrKey`) caused duplicate-key 
  relationship errors once multiple rows per month existed; fixed by joining 
  on a proper `date` column instead
- Dynamic display measures auto-switch between K€ and Mio.€ formatting based 
  on magnitude, so KPI cards stay legible across scales
- Conditional arrow/colour indicators (`SWITCH(TRUE(), ...)` + `UNICHAR`) 
  flag over-/under-plan performance at a glance without manual formatting

---

## Repository Structure
powerbi-kmu-management-reporting/  
├── pbix/  
│   └── Management-Report-Demo.pbix   # Power BI demo file  
├── LICENSE  
└── README.md  

---

## Data Disclaimer

All data used in this project is synthetic and was generated for 
demonstration purposes only. It does not represent any real company, client, 
or business figures.

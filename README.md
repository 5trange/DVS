# VAHAN RTO Vehicle Registration Analytics (DVS Assignment)

An interactive, executive-level Streamlit analytics dashboard and data pipeline built for the **Data Visualization and Storytelling (DVS)** assignment on Indian VAHAN RTO vehicle registration data (2018–2024).

---

## ⚡ Quick Start for Evaluators & Professors

Run the dashboard in **1 command** from the project directory:

### Option A: One-Click Launch Scripts (Recommended)
* **macOS / Linux:**
  ```bash
  ./run.sh
  ```
* **Windows:**
  Double-click `run.bat` or run:
  ```cmd
  run.bat
  ```

### Option B: Manual Launch (Standard)
```bash
# 1. Activate environment
source venv/bin/activate    # On macOS/Linux
# venv\Scripts\activate     # On Windows

# 2. Install dependencies (if needed)
pip install -r requirements.txt

# 3. Launch dashboard
streamlit run app.py
```
The interactive dashboard will open automatically in your browser at **`http://localhost:8501`**.

---

## 📁 Repository Structure

```text
DVS/
├── README.md                              # This project overview & run instructions
├── run.sh                                 # One-click launcher for macOS/Linux
├── run.bat                                # One-click launcher for Windows
├── requirements.txt                        # Required Python packages
├── pyproject.toml                          # Python project configuration
├── .gitignore                              # Git ignore configuration
├── app.py                                 # Main Streamlit Dashboard Application
├── data/
│   ├── VAHAN_Dataset.xlsx                 # Consolidated Excel workbook (cleansed + raw)
│   ├── raw/
│   │   └── India_VAHAN_Dataset.csv        # Original uncleaned raw dataset
│   └── processed/
│       ├── VAHAN_Dataset_Completely_Cleaned.csv     # Fully cleaned dataset
│       └── VAHAN_Dataset_Fully_Corrected_Issues.csv # Main dataset powering the dashboard
├── notebooks/
│   ├── 01_Data_Cleaning_Exploration.ipynb  # Exploratory data analysis & anomaly cleaning
│   └── 02_Metrics_Calculation.ipynb       # CFAR & Herfindahl FMI calculation routines
└── docs/
    ├── Assignment_Specification.md         # Full assignment task requirements
    ├── DVS_Assignment_Citations.md         # Academic design rationale & visualization citations
    ├── VAHAN_RTO_Registration_Analytics_Report.docx # Executive whitepaper report
    └── Data_Visualization_and_Storytelling_Course_Ref.pdf # Course reference material
```

---

## 📊 Core Business Metrics & Features

### Key Metrics
1. **Clean Fuel Adoption Rate (CFAR %):** Percentage of registered vehicles using clean powertrains (**Electric**, **CNG**, **Hybrid**).
2. **Fuel Mix Index (FMI):** Herfindahl-Hirschman Diversity score ($1 - \sum s_i^2$) measuring powertrain diversification across states and OEMs (0.0 to ~0.75+).
3. **Fleet Compliance Share (%):** Percentage of vehicles satisfying compound compliance rules (**BS6/ZEV** emission norm AND vehicle age $\le$ 15 years).

### Dashboard Layout (3 Interactive Tabs)
- **Tab 1: 📊 Macro Fuel Transition:** Macro adoption trends, CFAR regional rankings, market share trajectory over time, annual volume bar chart, CFAR category bullet charts, and net basis-point shifts.
- **Tab 2: 🏎️ OEM & Powertrain Strategy:** OEM performance metrics, interactive 4-quadrant scatter plot (CFAR vs. FMI diversity), year-over-year OEM market share trends, stacked OEM fuel mix comparison, and engine capacity (CC) distributions.
- **Tab 3: 🚨 Regulatory & Data Quality Audit:** Fleet scrappage risk heatmap (vehicle age vs. emission norm), non-compliant vehicle risk table with CSV export, data hygiene score %, and RTO error rate audits.

---

## 📑 Submission Package & References

- Refer to `docs/VAHAN_RTO_Registration_Analytics_Report.docx` for the whitepaper report.
- Refer to `docs/DVS_Assignment_Citations.md` for design choices justification (Gestalt principles, color palette, zero baselines).
- Refer to `data/VAHAN_Dataset.xlsx` for the underlying Excel file containing both raw and processed sheets.

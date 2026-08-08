# VAHAN RTO Vehicle Registration Analytics (DVS Assignment)

An interactive, executive-level Streamlit analytics dashboard and data pipeline built for the **Data Visualization and Storytelling (DVS)** assignment on Indian VAHAN RTO vehicle registration data (2018–2024).

---

## 📁 Repository Structure

```text
DVS/
├── README.md                              # This comprehensive project documentation
├── requirements.txt                        # Required Python packages
├── pyproject.toml                          # Python project configuration
├── .gitignore                              # Git ignore configuration
├── app.py                                 # Main Streamlit Dashboard Application
├── data/
│   ├── raw/
│   │   └── India_VAHAN_Dataset.csv        # Original uncleaned raw dataset
│   └── processed/
│       ├── VAHAN_Dataset_Completely_Cleaned.csv     # Fully cleaned dataset
│       └── VAHAN_Dataset_Fully_Corrected_Issues.csv # Main dataset with corrected EV & compliance flags
├── notebooks/
│   ├── 01_Data_Cleaning_Exploration.ipynb  # Exploratory data analysis & anomaly cleaning
│   └── 02_Metrics_Calculation.ipynb       # CFAR & FMI calculation routines
└── docs/
    ├── Assignment_Specification.md         # Full assignment task requirements
    ├── DVS_Assignment_Citations.md         # Academic design rationale & visualization citations
    ├── VAHAN_RTO_Registration_Analytics_Report.docx # Report template for submission
    └── Data_Visualization_and_Storytelling_Course_Ref.pdf # Course reference material
```

---

## 🚀 Getting Started & How to Run

### 1. Environment Setup

A virtual environment (`venv`) is included in the workspace. Activate it and install dependencies:

```bash
# Activate virtual environment
source venv/bin/activate

# Install dependencies (if needed)
pip install -r requirements.txt
```

### 2. Launch the Streamlit Dashboard

Run the main application from the root directory:

```bash
streamlit run app.py
```

The interactive dashboard will open automatically in your browser at `http://localhost:8501`.

---

## 📊 Core Business Metrics & Features

### Key Metrics
1. **Clean Fuel Adoption Rate (CFAR %):** Percentage of registered vehicles using clean powertrains (**Electric**, **CNG**, **Hybrid**).
2. **Fuel Mix Index (FMI):** Herfindahl-Hirschman Diversity score ($1 - \sum s_i^2$) measuring powertrain diversification across states and OEMs.
3. **Fleet Compliance Share (%):** Percentage of vehicles satisfying compound compliance rules (**BS6/ZEV** emission norm AND vehicle age $\le$ 15 years).

### Dashboard Layout (3 Interactive Tabs)
- **Tab 1: 📊 Macro Fuel Transition:** Macro adoption trends, CFAR regional rankings, market share trajectory over time, and net basis-point shifts.
- **Tab 2: 🏎️ OEM & Powertrain Strategy:** OEM performance metrics, interactive 4-quadrant scatter plot (CFAR vs. FMI), stacked OEM fuel mix comparison, and engine capacity (CC) distributions.
- **Tab 3: 🚨 Regulatory & Data Quality Audit:** Fleet scrappage risk heatmap (vehicle age vs. emission norm), non-compliant vehicle risk table with CSV export, data hygiene score %, and RTO error rate audits.

---

## 📑 Submission Documents

- Refer to `docs/Assignment_Specification.md` for task details.
- Refer to `docs/DVS_Assignment_Citations.md` for justification of design choices (Gestalt principles, color palette, zero baselines).
- Use `docs/VAHAN_RTO_Registration_Analytics_Report.docx` as the final whitepaper report template for your submission.

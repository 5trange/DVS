# 🔍 VAHAN Dashboard — Expert Data Analyst Audit Report

**Analyst**: Senior Data Analytics Review  
**Date**: 2026-08-08  
**Dataset**: 3,700 vehicle registrations · 13 Indian states · 2018–2024  
**Dashboard**: [app.py](file:///C:/Users/abhir/.gemini/antigravity/scratch/vahan_analytics/app.py)

---

## Executive Summary

The VAHAN dashboard is architecturally well-built with excellent visual design, but contains **5 Critical**, **5 Major**, **4 Minor**, and **4 Suggestion-level** findings that would mislead executive decision-makers if left unaddressed. The most severe involve **fabricated data quality metrics**, **EVs falsely flagged for scrappage**, and **an unrealistic compliance age threshold**.

| Severity | Count | Description |
|----------|-------|-------------|
| 🔴 Critical | 5 | Will produce **incorrect conclusions** in production |
| 🟠 Major | 5 | Produces **misleading but not wrong** outputs |
| 🟡 Minor | 4 | Cosmetic or edge-case annoyances |
| 🔵 Suggestion | 4 | Missing capabilities a decision-maker would expect |

---

## 🔴 Critical Findings

### C1. EV Quality Check Is Self-Defeating (Data Fabrication)

> [!CAUTION]
> The dashboard claims to audit whether EVs are classified as ZEV — but the code **overwrites the data before checking it**, guaranteeing 0 defects are ever found.

**Evidence** ([app.py:L848-L849](file:///C:/Users/abhir/.gemini/antigravity/scratch/vahan_analytics/app.py#L848-L849)):
```python
# Line 848: FORCE all EVs to ZEV
df["Emission_Norm_Clean"] = df["Emission_Norm"].where(~df["Is_Electric"], "ZEV")

# Line 877: Then "check" if EVs are ZEV — always True, always 0 defects
df["QF_EV_Not_ZEV"] = df["Is_Electric"] & df["Emission_Norm_Clean"].ne("ZEV")
```

**Actual data**: My audit found **50 EVs** with `Emission_Norm ≠ ZEV` in the raw CSV — but the dashboard will never report them.

**Fix**: Check against the original `Emission_Norm` column, not the cleaned one:
```python
df["QF_EV_Not_ZEV"] = df["Is_Electric"] & df["Emission_Norm"].ne("ZEV")
```

---

### C2. Electric Vehicles Falsely Flagged for Scrappage

> [!CAUTION]
> A 6-year-old Tesla/Ather is flagged as "non-compliant" and projected into the **immediate scrappage risk fleet**. This is factually wrong — scrappage policy targets ICE emissions, not EVs.

**Evidence** ([app.py:L856](file:///C:/Users/abhir/.gemini/antigravity/scratch/vahan_analytics/app.py#L856)):
```python
df["Is_Compliant"] = Meets_Norm & Within_Age  # Within_Age = Vehicle_Age_Years <= 5
```

A ZEV (zero-emission) vehicle aged 6 years: `Meets_Norm=True`, `Within_Age=False` → **Non-compliant** ❌

**Impact**: Inflates "Non-compliant Fleet Share" KPI and fills the Risk Fleet table with EVs that should never be there.

**Fix**: Exempt ZEV vehicles from the age check:
```python
compliant_rule = (df["Meets_Norm"] & df["Within_Age"]) | (df["Emission_Norm_Clean"] == "ZEV")
```

---

### C3. Arbitrary 5-Year Scrappage Threshold

> [!WARNING]
> The `MAX_COMPLIANT_AGE = 5` is hardcoded with no justification. Indian scrappage policy mandates **15 years** for commercial vehicles and **20 years** for private vehicles.

**Evidence**: At `MAX_COMPLIANT_AGE = 5`, every BS6 vehicle registered in 2018 (age=6) is flagged as non-compliant, which is factually wrong for a 6-year-old BS6 car.

**Impact**: The "Non-compliant Fleet Share" KPI reads **~30-40%** when the real figure under actual policy rules would be **near 0%** (since BS3 is the oldest norm in this dataset and max age is only 6 years).

---

### C4. Auto-Correct + Defect Exclusion Paradox

> [!CAUTION]
> The "Auto-correct State from RTO Office code" checkbox (default: ON) fixes State mismatches. But the "Exclude records failing quality checks" checkbox then **deletes those same records** because `Has_Defect` was computed on the **original, pre-corrected data**.

**Flow**:
1. `load_data()` computes `QF_RTO_Mismatch` on raw data → flags records
2. Sidebar auto-corrects State → user sees correct State
3. User checks "Exclude records failing quality checks" → deletes auto-corrected records

**Result**: The dashboard silently drops valid, corrected records — data loss disguised as quality filtering.

---

### C5. Unweighted Quadrant Axes (Simpson's Paradox)

The OEM Strategic Quadrant Matrix divides axes using `oem_filtered["CFAR"].mean()` — an **unweighted mean** where a micro-OEM with 1 registration gets equal weight to a market leader with 500.

**Impact**: Quadrant classification shifts dramatically depending on which niche brands are included, making strategic positioning unreliable.

**Fix**: Use volume-weighted averages:
```python
x_mid = np.average(oem_filtered["CFAR"], weights=oem_filtered["Registrations"])
y_mid = np.average(oem_filtered["FMI"], weights=oem_filtered["Registrations"])
```

---

## 🟠 Major Findings

### M1. YoY Metric Misleads on Non-Consecutive Years

The YoY delta compares `vol_by_year.iloc[-2]` vs `vol_by_year.iloc[-1]`. If filters result in non-consecutive years (e.g., 2019 and 2024), the dashboard labels a 5-year gap as **"vs prior year"**.

### M2. "National Average" Line Is Actually Filtered Average

Chart 1e (CFAR by Category) draws a dashed line labeled "National Average CFAR". But it uses `cfar = df["Is_Clean"].mean() * 100`, which reflects the **current filter selection**, not the full national dataset. If filtered to a single state, the "National" label is deceptive.

### M3. Diverging Bar Chart Colors Contradict Narrative

Chart 1g colors all positive share shifts Green and negatives Red. If Diesel **gains** market share, it appears Green — directly contradicting the dashboard's clean-fuel transition narrative.

### M4. CFAR Column in CSV Is Misleading

The CSV contains a pre-computed `CFAR` column with only **13 unique values** (one per state). The dashboard ignores this column and recalculates CFAR from `Is_Clean` — which is correct, but the presence of a misleading group-level metric in the dataset could confuse analysts who inspect the raw data.

### M5. Ignored Monthly Granularity

The code parses exact dates and computes `Year_Month` during load, but **every time-series chart aggregates at the yearly level only**. Decision-makers cannot see:
- Monthly seasonality patterns
- Policy intervention effects
- COVID lockdown dips (2020)
- Quarterly trends

---

## 🟡 Minor Findings

### m1. Truncated Axes on Bounded Percentages

The Quadrant Scatter Plot uses `Scale(zero=False)` for CFAR and FMI (0–100% metrics). This exaggerates small differences — a spread of 25–35% CFAR visually appears to span the entire axis.

### m2. Suspiciously Uniform Distribution

The data shows remarkably uniform distribution:
- **513–566 registrations per year** (±5% variance over 7 years)
- **257–342 registrations per state** (except Chandigarh at 63)
- All states have CFAR within **26.3–32.6%** (6 pp range)

This suggests the dataset may be **synthetically generated** rather than real VAHAN data. Real RTO data would show far more skew (Delhi alone would dominate volume).

### m3. Silent Dropping of Unparseable Dates

If `Registration_Date` fails to parse, it becomes `NaT`. The `.between()` filter silently excludes these records with no user warning.

### m4. Engine CC > 5000 for Non-EVs

181 non-EV records have Engine CC > 5000 (max 6700cc). While not impossible for HCVs, this is unusually high for the Indian market and worth flagging.

---

## 🔵 Suggestions for Enhancement

### S1. Add Geographic Visualization
A choropleth map of CFAR by state would be a baseline expectation for geographic adoption analytics. Bar charts alone cannot convey spatial clustering.

### S2. Add Cohort Analysis
Track how each registration year cohort's compliance status evolves over time (e.g., 2018 cohort → what % became non-compliant by 2024?).

### S3. Add Statistical Significance Tests
When comparing CFAR between states or OEMs, add confidence intervals or chi-squared tests. With only ~300 records per state, apparent differences may not be statistically significant.

### S4. Add Market Share Trend Over Time
Show how OEM market share evolved year-over-year, not just a static snapshot.

---

## Data Quality Summary Table

| Check | Result | Status |
|-------|--------|--------|
| Duplicate registration numbers | 0 | ✅ Pass |
| Missing values | 0 across all 18 columns | ✅ Pass |
| `is_clean` vs Fuel_Type | 0 mismatches | ✅ Pass |
| `is_compliant` vs calculated rule | 0 mismatches | ✅ Pass |
| RTO-State mismatches | 0 (after auto-correct) | ✅ Pass |
| EV-only brands selling fossil | 0 | ✅ Pass |
| EVs not classified as ZEV | **50 records hidden** | 🔴 **Masked** |
| EV Engine CC = 0 | All 649 EVs correctly 0cc | ✅ Pass |
| Seating capacity anomalies | None (range 2–32) | ✅ Pass |
| Age vs Registration Year | 0 mismatches (base year 2024) | ✅ Pass |
| Volume distribution uniformity | Suspiciously uniform | 🟡 **Synthetic?** |

---

## Priority Action Items

1. **Immediately fix** C1 (fake quality check) and C2 (EVs in scrappage) — these produce **factually wrong** executive metrics
2. **Review** C3 — confirm the intended `MAX_COMPLIANT_AGE` against actual Indian policy
3. **Fix** C4 — recompute `Has_Defect` after auto-correction, or disable exclusion when auto-correct is on
4. **Fix** C5 — switch to volume-weighted quadrant axes
5. **Clarify** M2 — relabel "National Average" to "Selection Average"

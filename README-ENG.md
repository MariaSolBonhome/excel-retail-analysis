# Maven Roasters — Operational Analysis

> También disponible en [español](README-ESP.md)

---

## Business Overview

Three locations. Similar volumes. Similar revenue. Same business? No.

An analysis of 149,116 transactions from Maven Roasters NYC's first half of 2023 reveals that the three locations have structurally different operational profiles. Lower Manhattan collapses after noon. Astoria holds steady traffic all day. Hell's Kitchen sustains footfall until 8PM. The standardized menu works. The standardized operation doesn't.

**Core insight:** The differences between locations aren't about *what* customers buy — they're about *when* they buy. That has direct implications for staffing, hours, and inventory, without needing to redesign the menu.

---

## Technical Overview

End-to-end descriptive analysis in Excel on a 149,116-transaction dataset from a fictional NYC coffee chain (Kaggle). The project covers data cleaning, feature engineering, multidimensional analysis with PivotTables and PivotCharts, and an executive dashboard with dynamic KPIs and location-based filtering.

**Stack:** Excel — Structured Tables · PivotTables · PivotCharts · Slicers · Formulas

---

## Dataset

| Field | Detail |
|---|---|
| Source | Kaggle — Coffee Shop Sales |
| Company | Maven Roasters (fictional NYC coffee chain) |
| Locations | Astoria · Hell's Kitchen · Lower Manhattan |
| Period | January–June 2023 (6 months) |
| Transactions | 149,116 |
| Data quality | No nulls · No duplicates · Minimal cleaning required |

---

## File Structure

| Sheet | Content |
|---|---|
| `raw_data` | Unmodified raw data — auditable source of truth |
| `clean_data` | Clean data + 7 calculated columns (Table `tbl_clean`) |
| `horarios` | Q1 — Peak and off-peak hours by location |
| `perfiles_sucursal` | Q2 — Product mix by location |
| `volumen_vs_ingresos` | Q3 — Volume vs. revenue by product |
| `productos_zombies` | Q4 — Low-performing products |
| `evolucion_mensual` | Q5 — Business evolution over the semester |
| `mix_horario` | Q6 — Product mix by time of day |
| `Dashboard` | Executive dashboard with dynamic KPIs and location slicer |
| `datos_kpis` | Auxiliary pivots feeding the dashboard KPIs |

---

## Analysis Questions

### Q1 — When do customers buy at each location?

**Setup:** Pivot with `hour` in rows, `store_location` as series, `is_weekend` as filter. Grouped bar chart.

**Findings:**
- Hell's Kitchen peaks at ~7,000 transactions/hour between 8–10AM on weekdays — the highest AM spike across all three locations.
- Lower Manhattan has a "ghost afternoon": traffic drops from 3,000 to 1,400 transactions between 2PM and 6PM. Weekend activity is minimal.
- Astoria holds a stable ~3,500 transactions throughout the entire day, every day.
- The weekday vs. weekend comparison confirms distinct neighborhood profiles: office district (Lower Manhattan), residential (Astoria), mixed (Hell's Kitchen).

**Insight:** The three locations serve structurally different customer profiles, validated by differentiated behavior between weekdays and weekends.

---

### Q2 — Do the three locations have the same product mix?

**Setup:** Pivot with `product_category` in rows, `store_location` in columns, values as % of column total.

**Findings:**
- Coffee (38–40%) and Tea (29–32%) dominate across all three locations. Inter-location differences: 2–3 percentage points.
- Lower Manhattan has slightly more Bakery (16.5%) and Flavours (6.1%) — consistent with an office profile.
- Astoria has more Tea (32.1%) and Drinking Chocolate (8.5%) — consistent with a residential profile.

**Insight:** Location differentiation lies in *when* customers buy (Q1), not *what* they buy (Q2). The standardized menu works. The standardized operation doesn't.

---

### Q3 — Which products drive the most volume, and which drive the most revenue?

**Setup:** 4 pivots — two full tables (29 product types) for auditing, two filtered to Top 10 linked to horizontal bar charts.

**Findings:**
- Volume leader: Brewed Chai tea (17,183 transactions, $4.49 avg ticket).
- Revenue leader: Barista Espresso ($91,406 — sells 5% less than the volume leader but generates 18% more revenue, $5.57 avg ticket).
- Three product types identified: top performers (high volume + high revenue), premium (lower volume + high ticket), cheap volume (high rotation + low ticket).

**Insight:** Promoting the best-selling product is not the same as promoting the most profitable one.

---

### Q4 — Are there products that don't justify their place on the menu?

**Setup:** Bottom 10 pivot by transaction count, sorted ascending.

**Findings:**
- Redundant products (discontinuation candidates): Black tea (303), Herbal tea (305), generic Drinking Chocolate (266), Green tea (159), Green beans (134). Each competes with a Brewed variant that outsells it 30–40x.
- Strategic low-volume products (keep): take-home beans, Clothing, Organic Chocolate. They serve loyalty and branding functions, not revenue.

**Insight:** Low volume doesn't always mean zombie. The pruning recommendation applies to 5 specific products, not the full Bottom 10.

---

### Q5 — How did the business evolve over the first semester?

**Setup:** Two parallel pivots — volume and revenue — with `month` in rows and `store_location` in columns. Two linked line charts.

**Findings:**
- All three locations grew at the same pace: nearly identical month-to-month curves.
- February was the weakest month (–6% vs. January).
- Sustained growth March–June: +68% in volume over 4 months.
- Growth is generalized — not attributable to any single location.

**Insight:** The operational differentiation between locations (Q1 and Q2) doesn't carry over to the time dimension. The growth factor is macro, not operational.

---

### Q6 — Does the product mix change by time of day?

**Setup:** Pivot with `time_period` in rows, `product_category` in columns, values as % of row total. Filter by `store_location`. No chart (documented decision — see Technical Decisions).

**Findings:**
- Bakery has a slight morning peak (14–17%) across all three locations.
- Tea gains share progressively from morning to evening.
- Cross-location comparison during the morning slot confirms differentiated profiles: more Flavours in Lower Manhattan, more Tea in Astoria, mixed in Hell's Kitchen.

**Insight:** The differentiated profiles from Q1 and Q2 hold when disaggregated by time slot — reinforcing the project's core insight.

---

## Core Insight

> The 3 Maven Roasters locations are NOT the same business. They have similar volumes and revenue but structurally different operational profiles. The standardized menu works. The standardized operation doesn't. They should be managed differently.

---

## Executive Dashboard

![Dashboard Maven Roasters](assets/dashboard.png)

The dashboard includes:
- 4 dynamic KPIs that update in runtime when filtering by location.
- Slicer connected to all 4 charts and KPIs simultaneously.
- 2×2 chart grid: peak hours · top products by revenue · monthly evolution · product mix.
- Consistent color palette with location-based color assignment across all charts.

---

## Technical Decisions

### 1. Minimal necessary cleaning
The dataset had no nulls or duplicates. Only the transformations required for analysis were applied: data type conversion, decimal separator adjustment (`unit_price`: `.` → `,` for regional settings), and calculated column creation. Raw data was not modified.

### 2. `raw_data` kept as auditable source of truth
`clean_data` is a copy of `raw_data`. The original sheet remains unmodified to guarantee reproducibility and auditability. Any analyst can reconstruct the full analysis from scratch.

### 3. Structured Table (`tbl_clean`)
`clean_data` was converted to a named Excel Table (`tbl_clean`), enabling structured references, auto-filters, and automatic range expansion — standard practice for scalable Excel analysis.

### 4. Analytically justified calculated columns
Each new column addresses a specific grouping need: `hour` (separated from `transaction_time`) to enable hourly pivot grouping; `time_period` for time-slot analysis; `is_weekend` for weekday/weekend comparison; `month_num` for correct chronological sorting in charts.

### 5. Time slot cuts based on business logic
Morning `<11`, Midday `11–13`, Afternoon `14–17`, Evening `18+`. Cuts reflect real coffee shop behavior (morning rush, lunch, stable afternoon, residual evening traffic) — not arbitrary mathematical divisions.

### 6. Dual pivot per question (audit + visualization)
Q3 and Q4 use two pivots each: one complete table (all products, for auditing) and one filtered to Top/Bottom 10 linked to the chart. This preserves full data visibility without sacrificing visual readability.

### 7. PivotCharts linked directly to pivots
No duplicate auxiliary tables were created. Charts connect directly to their source pivots to maintain referential integrity and eliminate manual data duplication errors.

### 8. Deliberate decision not to chart Q6
Inter-location and inter-slot differences in Q6 are subtle (2–3 percentage points). A chart would have low explanatory power. The formatted percentage table communicates the insight more clearly. Decision explicitly documented.

### 9. Dynamic KPIs via auxiliary pivot layer
The 4 dashboard KPIs don't use direct formulas on `tbl_clean` — they reference pivots in the `datos_kpis` sheet. This allows the dashboard slicer to control charts and KPIs simultaneously. Values update in runtime on slicer selection change.

### 10. Neighborhood profile hypotheses treated as interpretive
Conclusions about neighborhood type (office district, residential, mixed) are based exclusively on transactional patterns. They are explicitly framed as interpretive hypotheses requiring validation with external demographic data (e.g., NYC Open Data). This distinction is documented in the dashboard disclaimer and in the client report.

### 11. Audience-differentiated terminology
"Operational Analysis" in the dashboard title (executive audience — what was analyzed). "Descriptive analysis" in subtitles and README (technical audience — how it was analyzed).

---

## Limitations & Next Steps

**Limitations:**
- Only 6 months of data — annual seasonality patterns cannot be identified.
- No cost data — real margin per product cannot be calculated.
- Neighborhood profile hypotheses require validation with external sources.
- Interactive tooltips per chart not available in native Excel (planned for Power BI version).

---

## Stack

`Excel` · Structured Tables · PivotTables · PivotCharts · Slicers · Formulas (`SUMIF`, `AVERAGE`, nested `IF`, `TEXT`, `HOUR`, `MONTH`)

---

*Developed by Bonhome María Sol · Data Analytics Portfolio ·*
*Dataset: [Coffee Shop Sales — Kaggle]*

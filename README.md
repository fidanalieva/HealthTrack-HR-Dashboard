# HealthTrack HR: Workforce Analytics Dashboard

A four-page Power BI report analysing headcount, budget, turnover and pay for a fictional healthcare company, built as Portfolio Project.

**Author:** Fidan Aliyeva · **Tools:** Power BI Desktop, DAX, PowerPoint · **Data as of:** Nov 2024

**Live report:** https://app.powerbi.com/links/K6uWdxW0n7?ctid=43728c20-4447-4b27-ac2e-4bdabb3c0121&pbi_source=linkShare&bookmarkGuid=a2a27e97-03d9-44ce-afcf-1b7011c492d4

![Headcount Overview](Images/Headcount%20Overview.png)

## Business questions

1. How big is the workforce, and how is it changing over time?
2. How does actual headcount compare with budget, by department?
3. Who is leaving, why, and where is turnover highest?
4. Is pay consistent across genders, bands and performance levels?

## Key findings

| Metric | Value |
|---|---|
| Active employees | 194 (+12.8% vs prior year) |
| Budget attainment | 86% (14% below total budget) |
| Turnover rate | 19.5%, above the 10–15% healthy range |
| Exit type | 29 voluntary (69%), 13 involuntary (31%) |
| Highest turnover | Customer Success 29.4%, Sales 28.9% |
| Gender pay gap | 5.8% (men $103K vs women $98K) |

- Every department is below its budgeted headcount; Sales (−8) and Customer Success (−6) are furthest behind.
- Work-life balance is the most common exit reason (9 exits), followed by redundancy (6), career change (5) and higher compensation (5).
- Customer Success has the lowest average salary ($70K) as well as the highest turnover. The report shows the two together but does not claim one causes the other.
- Team sizes are small, so department-level pay gaps should prompt a review by role and level, not a conclusion.

A one-slide summary with these metrics, insights and recommendations is in [`HealthTrack_Summary_Slide.pptx`](HealthTrack_Summary_Slide.pptx).

## Report pages

| Page | What it shows |
|---|---|
| 1. Headcount Overview | KPI cards, hiring vs departures trend, active employees by department, key insight |
| 2. Department Analysis | Headcount vs budget, average salary by department, department summary table with variance and turnover |
| 3. Turnover & Retention | Turnover and retention rates, voluntary vs involuntary exits, rolling 3-month exits, turnover by department, exit reasons |
| 4. Salary & Performance | Gender pay gap cards, performance vs salary scatter, salary band distribution, pay comparison by department |

Navigation, a shared filter bar (Year, Department, Status) and a Reset Filters button are on every page.

## Data

Six CSV files from an HR system (sample data): departments (8), salary bands (6), roles (27), employees (236: 194 active, 42 terminated), hiring records (236) and exit records (42). Hire dates run 2018–2024 and exits 2022–2024.

## Data model

A star schema with two fact tables and five dimensions.

| Table | Type | Notes |
|---|---|---|
| `FactHiring` | Fact | One row per hire event |
| `FactExit` | Fact | One row per exit event |
| `DimEmployee` | Dimension | Links to department, role and salary band |
| `DimDepartment` | Dimension | Carries `BudgetHeadcount` |
| `DimRole`, `DimSalaryBand` | Dimension | |
| `DimDate` | Dimension | Built in DAX, marked as a date table |

- All relationships are one-to-many with single-direction filtering.
- `FactHiring[HireDate]` and `FactExit[ExitDate]` are both active relationships to `DimDate`.
- Department, role and salary band reach the fact tables through `DimEmployee`. The duplicate keys on the fact tables and on `DimRole` are kept as inactive relationships, to avoid ambiguous filter paths.

## Key DAX decisions

- **`Headcount`** counts hires up to the selected date minus exits up to that date. The simpler "count employees with status Active" cannot respond to a date slicer, because `DimDate` does not filter `DimEmployee`. `Total Active Employees` is kept for pages without a date filter. With no filters, `Headcount` returns 194.
- **`Turnover Rate %`** divides exits by the average of everyone ever employed (236) and current headcount (194). `Retention Rate %` is its complement.
- **`Dept Turnover %`** divides exits by active employees plus exits, per department.
- **`Rolling 3M Exits`** sums exits over a rolling three-month window.
- **`New Hires`** counts only hires of type "New Hire", excluding rehires and internal transfers. **`Total Hires`** counts every hire record.
- **`Gender Pay Gap %`** is (average male salary − average female salary) ÷ average male salary.

Measures are organised in display folders: Headcount, Department, Turnover and Salary.

## Design decisions

- **Headcount is point-in-time.** A simple count of "Active" employees can't respond to the Year slicer, so `Headcount` is calculated from hire and exit records up to the selected date.
- **Rolling 3-month exits.** A 3-month window shows turnover trends more clearly than 6 months for this dataset.
- **Status slicer kept.** It's available for filtering the report, but it doesn't affect `Headcount`, which is built from hire and exit records.
- **Consistent styling.** Navy, teal, amber, green, red and slate palette, with Calibri throughout.

## Repository contents

```
HealthTrack_Dashboard.pbix        Power BI report
HealthTrack_Summary_Slide.pptx    One-slide executive summary
images/                           PDF file of board screenshots
README.md
```

## How to open

1. Download the `.pbix` file.
2. Open it in Power BI Desktop (free for Windows).
3. If prompted, refresh the data and point it to your local copy of the six CSV files.

## License

Portfolio project using synthetic sample data. Not for commercial use.

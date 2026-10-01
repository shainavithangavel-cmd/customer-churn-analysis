# Customer Churn Analysis — Fitness Subscription App

A data analysis project that identifies why subscribers cancel a fitness app and gives an evidence-backed retention plan, using SQL, Python and Power BI.

---

## Business Problem

Cancellations have been rising for a subscription fitness app. As the analyst on the membership team, the task was to find out why members leave and recommend specific, measurable actions before the next budget cycle.

## Data

Two files covering 4,200 customers who signed up between July 2024 and October 2025:
- **subscriptions.csv** — one row per customer (plan, price, channel, device, age group, churn status, cancel reason)
- **usage.csv** — one row per customer per active month (workouts, minutes, classes booked, support tickets)

## Tools and Process

| Stage | Tool | What it did |
|---|---|---|
| Database & analysis | PostgreSQL / SQL | Cleaned the data, calculated churn rates, segmented customers |
| Validation & visuals | Python (pandas, scipy, matplotlib) | Re-created key SQL numbers independently, ran statistical tests, built charts |
| Dashboard | Power BI | Visual summary of the findings |

**AI usage note:** AI was used to draft SQL queries, Python code and this write-up more quickly. Every output was checked rather than used as-is: SQL results were independently reproduced in Python (three key numbers matched exactly: 42.10% overall churn, 2.97%/8.07% churn by plan type, and 55.25% churn at the annual renewal point), and two SQL/DELETE steps were corrected after re-checking the data (duplicate count was 160, not an earlier estimate of 157, and an initial claim of "mixed date formats" was found to be incorrect on closer inspection and corrected).

## Data Cleaning

- Removed 160 exact duplicate rows from the usage data
- Standardized inconsistent capitalization in the plan tier field (e.g. "Basic"/"basic" treated as one group)
- Converted text dates to proper date columns and verified no values were lost
- Checked for missing values, negative values, and broken links between the two tables — none found, except 503 blank `minutes_active` readings, which were left blank rather than filled with zero
- Verified date logic (no usage after a churn date, no churn date before a signup date)

Full queries and explanations are in the progress log documents in this repo.

## Key Findings

**1. Churn is rising.** The overall monthly churn rate is 5.79%, up from about 5.0% in late 2024 to about 6.25% in late 2025, peaking at 7.40% in November 2025.

**2. Plan type is the strongest driver, but for different reasons at different times.**
- Monthly-plan members churn heavily in their first 4 months (peaking at 12.71% in month 2) — an onboarding problem.
- Annual-plan members churn very little until month 11, then 55.25% fail to renew — a renewal problem, not an early-engagement problem.

**3. Low first-month engagement predicts later churn.** Customers who completed 0-1 workouts in their first month churned 2 to 7 times as often as those who completed 6+, and this held true even when comparing customers at the same point in their subscription (not just an artifact of tenure).

**4. Promo-offer customers churn early, likely over price.** They churn at 9.33% in their first 3 months (vs 3.2-5.4% for other channels), and they cite "too expensive" as their cancel reason roughly twice as often as other channels (28.4% vs 12-16%). After month 3, they have the lowest churn of any channel — this is an early-churn problem, not a quality-of-customer problem.

**5. Statistically confirmed:** a chi-square test confirmed plan type and acquisition channel are highly significant drivers (p < 0.000001), and device is a real but smaller effect (p = 0.016, Android slightly higher than iOS/web). Plan tier and age group showed no statistically significant relationship with churn (p = 0.69 and p = 0.74) — worth knowing so the company doesn't spend retention budget targeting them.

**6. Stated reasons mostly agree with the data.** "Not using it enough" is the top stated reason (32.2%) and matches the engagement finding. "Too expensive" (18.2%) is concentrated in promo customers rather than higher-priced tiers, suggesting it is really a post-discount price shock, not a general pricing problem.

## Risk Segments

Customers were scored using only information available in their first month (first-month workout count and acquisition channel), then grouped into High/Medium/Low risk:

| Segment | Plan | Monthly churn rate |
|---|---|---|
| High | monthly | 16.15% |
| Medium | monthly | 8.65% |
| Low | monthly | 5.01% |
| High | annual | 4.68% |
| Medium | annual | 3.45% |
| Low | annual | 1.96% |

High-risk customers are 30.5% of the subscriber base but account for 41.7% of cancellations.

## Recommendation

| Segment | Action |
|---|---|
| High-risk, monthly | Onboarding push in the first 2 weeks (prompt to book a first class); specific plan for when a promo offer ends (e.g. transition email before the discount expires) |
| High-risk, annual | Re-engagement check-ins through months 1-10; start a renewal conversation around months 9-10, before the cliff at month 11 |
| Medium-risk | Light-touch nudges (reminder emails, suggested workouts) |
| Low-risk | No retention spend needed; good candidates for referral requests, since referred customers churn the least of any channel |

**How to measure it:** hold out a random 10-20% of High-risk customers as an untreated comparison group, and compare their churn rate against the treated group after 2-3 months.

**Estimated scale (illustrative, based on this dataset):** if High-risk monthly churn fell by 1 percentage point, that would be roughly 31 fewer cancellations based on their 3,083 customer-months of exposure in this data. This is simple arithmetic on the sample, not a forecast.

## Caveats

- The risk-scoring rule was built and tested on the same data it describes, so its accuracy is likely a bit optimistic; it is a simple rule, not a predictive model.
- Findings describe association, not proof of cause (e.g. "low engagement is linked to churn," not "low engagement causes churn").
- Annual plan and promo-offer effects partly overlap with timing (renewal cliff, discount expiry) rather than being independent variables — this is noted explicitly in the analysis rather than assumed away.

## Repository Contents

- `01_Project_Progress_Log_Setup_and_Cleaning.md` — database setup and full data-cleaning process with SQL
- `02_Project_Progress_Log_Analysis_and_Risk_Segments.md` — full SQL analysis, all findings, and the risk-segmentation query
- `churn_analysis.ipynb` — Python validation, charts, and chi-square tests
- `churn_charts.png` — four summary charts
- `risk_segments.csv` — the risk-segment output table
- `Churn_Analysis_Dashboard.pbix` — Power BI dashboard
- This file — project overview and recommendation

## Skills Demonstrated

SQL (joins, CTEs, window functions, data cleaning, CASE logic) · Python (pandas, data validation, chi-square testing, data visualization) · Power BI (dashboard design) · Statistical reasoning (tenure adjustment, significance testing) · Business communication (translating analysis into a resourced recommendation)

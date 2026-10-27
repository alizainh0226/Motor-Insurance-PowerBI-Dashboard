# Motor Insurance Portfolio: Loss Ratio Analysis (Power BI)

A Power BI dashboard analyzing ~800,000 motor insurance policy-years (2011–2018). The portfolio's loss ratio is 260%, and the dashboard breaks it down into claim frequency and claim severity to show which vehicle segments lose money and why.

![Dashboard overview](images/overview.png)

## Guiding question
Why is this portfolio's loss ratio so high, and which vehicle segments drive it?

## Key findings
- **Severity, not frequency, drives the loss ratio.** About 7.5% of policies have a claim each year, a normal level for motor insurance. But the average claim (~257,000) is about 35× the average premium (~7,400).
- **Heavy commercial vehicles lose money through rare but very costly claims.** Trailers and semitrailers have the worst loss ratio (456%), followed by trucks (327%) and tankers (235%). Their claims are infrequent but average 550,000–715,000.
- **Buses and station wagons lose money through frequent claims.** Both have claim frequencies near 12%, about 1.6× the portfolio average.
- **Trucks are the most important segment to fix.** They are the largest group (151,000 policies) and the only one worse than average on both frequency and severity.
- **Motorcycles (32%) and tractors (31%) are the only profitable segments.**
- **The two problem types need different fixes.** Severity problems point to pricing, coverage limits or reinsurance. Frequency problems point to underwriting.

## Validating the loss ratio
A 260% loss ratio is unusually high, so I tested it before reporting it:
- **One bad year?** No. The loss ratio was 255–343% every year from 2011 to 2016.
- **A few giant claims?** Partly. Just 132 claims over 10 million account for 17.5% of all claim money, but excluding them still leaves a loss ratio of about 214%.
- **The 2017–2018 drop** (to 169% and 88%) reflects immature data, not improvement. Recent policies haven't had time to produce claims; 2018's claim frequency (3.5%) is about half the normal level.

Because the overall level couldn't be fully validated, the dashboard focuses on **comparisons between segments**, which remain meaningful even if the absolute level is off.

## Data preparation (Power Query)
- Combined two CSV files (802,037 rows) using the Folder connector.
- **Fixed misread dates:** Power BI auto-detected `08-AUG-13` as 13 Aug 2008. I removed the auto-detected type step and parsed the dates explicitly with `Date.FromText` using the `dd-MMM-yy` format.
- Set data types manually, keeping ID and code columns as text so they aren't summed.
- Removed 198 duplicate rows, leaving 801,839 policy-years.
- Excluded blank vehicle types and "Trade plates" (19 policies, too few to analyze).

## DAX measures
| Measure | Definition |
|---|---|
| Total Premium | `SUM(Policies[PREMIUM])` |
| Total Claims | `SUM(Policies[CLAIM_PAID])` |
| Loss Ratio | `DIVIDE([Total Claims], [Total Premium])` |
| Policy Count | `COUNTROWS(Policies)` |
| Claim Count | `CALCULATE(COUNTROWS(Policies), Policies[CLAIM_PAID] > 0)` |
| Claim Frequency | `DIVIDE([Claim Count], [Policy Count])` |
| Avg Claim Severity | `DIVIDE([Total Claims], [Claim Count])` |
| Large Claim Share | Share of claim money from claims over 10 million |

## Dashboard
- **Overview page:** KPI cards, loss ratio by vehicle type with a 100% break-even line, a frequency vs. severity scatter chart split into quadrants by portfolio averages, the yearly trend, and a usage slicer.
- **Detail page:** the supporting tables by year and vehicle type.

![Detail page](images/detail.png)

## Limitations
- Amounts are in Ethiopian birr, and the data isn't Canadian, so results show analytical method rather than local market conditions.
- The dataset has no documentation explaining what claims cover. The large claims on commercial vehicles likely include liability (e.g. passenger injuries), but this can't be confirmed.
- Code columns such as `SEX` and `INSR_TYPE` were not used because their meanings aren't documented.
- Short-term policies have partial premiums, which affects premium-based comparisons.

## Data source
Ethiopian Insurance Corporation motor vehicle insurance data, via Kaggle: (https://www.kaggle.com/datasets/imtkaggleteam/vehicle-insurance-data). The raw data isn't included in this repo.

## Tools
Power BI Desktop (Power Query, DAX)

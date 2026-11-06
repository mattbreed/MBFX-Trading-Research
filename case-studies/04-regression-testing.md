# 04. Regression Testing and Investigating Unexpected Results

**Focus:** Regression testing, performance analysis, controlled comparisons and defect investigation

## The Problem

As MBFX developed, making changes to one part of the system could affect decisions elsewhere.

For example, changing how the software identified important price movements could affect whether a potential trading opportunity passed its initial checks, progressed to further validation or was eventually rejected. The difficulty was that a change might appear to fix one problem while introducing another. A successful test run didn't necessarily tell me whether the underlying logic was behaving as intended.

I needed a way to compare the original behaviour against modified versions using the same historical data, rather than judging a change simply by whether the final results looked better.

## Establishing a Performance Baseline

One of the ways I monitored development was by retaining historical test results as a reference point.

In October 2025, an approximately one-month historical test produced a return of 26.96%, increasing a simulated starting balance of £10,000 to £12,695.80 across 26 recorded positions. This became an important performance reference when investigating subsequent changes to the system.

When I adjusted detection rules, inputs or other parts of the software, I could rerun historical tests and compare the results against the original performance. If a change produced a significant decline, I knew something had affected the system's behaviour and needed investigating.

However, a lower return didn't automatically mean the change was incorrect. It might have removed trades that should never have been accepted, changed which opportunities qualified or exposed a weakness in the original strategy. Similarly, a higher return didn't necessarily mean the change was an improvement. A single month could produce encouraging results that wouldn't hold up across a longer period.

The October result therefore acted as an initial regression benchmark, rather than a target that every subsequent version needed to exceed.

## Comparing Changes and Investigating Differences

I also needed to establish what had actually changed between tests. For example, if a modified version produced fewer trades or a different financial result, I wanted to understand whether that was caused by changes in the detection logic, rejected opportunities, entry timing or other system behaviour. This meant comparing more than the final account balance.

Where the data and settings were comparable, I could examine differences in the number of opportunities detected, the stages they reached, rejection reasons and simulated trading outcomes. I also recognised that comparisons could become unreliable if multiple inputs changed at the same time. A difference in profitability couldn't confidently be attributed to one adjustment unless the other relevant conditions were controlled.

This was one reason I increasingly focused on isolated tests that changed a specific part of the logic while keeping the underlying historical data consistent.

### Example: Investigating a Performance Regression

One example occurred when I tested a change to how the system handled correlated positions. These are trades involving markets that can move in similar directions, potentially increasing overall exposure.

The original historical test produced a return of 26.96%. Following the change, a subsequent test reported a return of 15.09%, a reduction of 11.87%. Rather than just looking at the final balance, I compared the reports to understand what had changed.

| Measurement | Original Test | Modified Test |
|---|---:|---:|
| Starting balance | £10,000 | £10,000 |
| Final equity | £12,695.80 | £11,508.64 |
| Reported return | +26.96% | +15.09% |
| Trades recorded | 26 | 34 |
| Winning trades | 11 | 11 |
| Losing trades | 14 | 22 |
| Win rate | 42.31% | 32.35% |
| Maximum drawdown | 9.30% | 14.73% |

*The reports include incomplete trade outcomes, so winning and losing trade counts do not add up to the total number of trades.*

The modified run recorded more trades and more losing trades, while the maximum drawdown also increased. This gave me several areas to investigate, rather than simply concluding that the change had reduced profitability. Allowing correlated positions increased the number of trades from 26 to 34, but the additional exposure did not improve performance. Historical returns fell from 26.96% to 15.09%, while drawdown increased from 9.30% to 14.73%.

However, one historical comparison was not sufficient to conclude that restricting correlated positions would consistently improve performance. I needed to understand how the change affected trade selection, overlapping exposure and overall risk before drawing a broader conclusion. A restriction intended to manage risk could produce lower returns during a particular month while still being beneficial over longer periods. The comparison also had limitations, including an incomplete result in the modified run. It therefore provided evidence of a performance difference requiring further investigation, rather than proof that one version was definitively better.

## Example: Investigating Structural Detection Errors

In October 2026, I carried out a separate investigation into the part of Module 2 responsible for identifying price patterns and determining market direction. The audit identified two problems in the existing code.

**1. Incorrect ordering of historical data**

The software identified important highs and lows in historical price movements, but the order in which those points were stored was inconsistent with how later functions expected to use them. This meant subsequent calculations could use an older reference point when they were intended to use a more recent one.

**2. Inconsistent measurement units**

Another calculation compared values expressed in different units without correctly converting them. This could affect the threshold used to decide whether a price movement was significant enough to include in the analysis. Both issues had the potential to change which patterns the software recognised and whether those patterns passed subsequent checks.

## Measuring the Impact

To investigate the effects, I used isolated tests comparing the original logic with versions where the issues were addressed separately. The tests used the same historical data across six financial instruments and four separate months, producing 293,516 directional evaluations per version.

The results showed:

| Change tested | Initial structure decisions changed | Later alignment decisions changed |
|---|---:|---:|
| Correct historical point ordering | 50.71% | 7.37% |
| Correct measurement unit conversion | 3.09% | 1.90% |

These results were important because they showed that a relatively small code change could have a substantial effect on how the system interpreted the same information. They also demonstrated why I needed to examine more than the final output. Although correcting the historical ordering changed over half of the initial structure decisions, only 7.37% of the later alignment decisions changed. The later checks were filtering out many of the differences introduced earlier in the process.

## Interpreting the Results

The two types of testing helped me answer different questions. The performance baseline showed when a change had affected historical trading results. The structural tests helped identify which underlying decisions had changed and how those differences moved through the system.

Neither was sufficient on its own. The October performance result covered a relatively short period, so differences needed to be investigated across longer historical periods before drawing conclusions. The structural audit was also limited to isolated detection calculations rather than complete simulated trading results.

The main lesson was that I needed to understand **what had changed, why it had changed and whether the new behaviour was consistent with the requirements**, before deciding whether an adjustment represented an improvement.

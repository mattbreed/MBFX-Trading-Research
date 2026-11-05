# 05. Research Methodology, Experimental Design and Statistical Analysis

**Focus:** Independent research, hypothesis development, research methodology, experimental design, statistical analysis and critical evaluation

## The Research Question

A major part of MBFX involved investigating whether certain patterns in historical financial market data could reliably predict future price movements.

This began with observations from several years of manual trading research. However, identifying patterns visually was not enough to establish whether they offered a genuine predictive advantage. I wanted to determine whether the patterns I had identified performed differently from ordinary market behaviour, and whether any apparent advantage remained consistent across different instruments and historical periods.

To answer these questions, I designed and directed a series of historical research experiments, using Python and structured datasets to examine large numbers of observations. The research evolved as new evidence emerged. Initial findings led to further questions, alternative explanations and additional tests, particularly when results differed from what I had expected. The objective was to establish what the evidence actually supported, rather than simply find results that confirmed the original theory.

## Developing and Investigating Competing Hypotheses

As the research progressed, I began looking beyond the overall performance of the strategy to understand why particular decisions were producing poor results. Rather than assuming that the entire approach was incorrect, I wanted to investigate whether the problems originated in specific parts of the process.

I developed four main hypotheses to investigate:

| Hypothesis | What I wanted to establish |
|---|---|
| Target selection | Whether the system was identifying targets that were too far away to be realistically reached |
| Pattern identification | Whether opportunities were being identified in less favourable market conditions or locations |
| Entry and risk management | Whether the system was correctly anticipating a price movement but entering at the wrong time or placing its stop-loss too close |
| Directional prediction | Whether the system was incorrectly predicting which direction the market would move |

Each hypothesis required different measurements and comparisons. For example, to investigate entry timing, I examined what happened after simulated trades were stopped out. If price subsequently moved in the originally predicted direction, that raised the possibility that the entry or stop placement was contributing to poor results.

However, this observation alone was not sufficient evidence. I needed to establish how frequently it occurred across a larger sample, whether similar movements occurred by chance and whether any alternative entry approach remained viable after trading costs. Initial exploratory samples suggested some potentially interesting patterns, but the sample sizes were too small to support firm conclusions. This led to a more structured research approach: identifying a specific question, selecting the relevant variables, checking data quality, comparing outcomes and determining whether further testing was justified.

I also separated information available before a simulated trade from information only known afterwards. This was particularly important when considering whether historical data could be used to predict future outcomes without accidentally introducing information that would not have been available at the time. The objective was to determine which explanations were supported by the available evidence and which required further investigation, rather than modifying large sections of the system based on assumptions.

## Research Example: Testing Whether Historical Price Levels Had Predictive Value

One of my larger investigations focused on whether price levels that had repeatedly influenced market movements were more likely to affect future prices. The original theory was that if price had approached a particular level several times and moved away, that level might be more significant than an ordinary price level.

However, simply observing price reacting to these levels wasn't enough. Financial markets regularly move towards and away from prices for reasons unrelated to the pattern I was investigating. I wanted to establish whether these levels behaved differently from what could ordinarily be expected.

### Designing the Research

The investigation examined approximately 95,000 historical hourly price levels across 19 financial instruments, using data from January 2020 to September 2026. I established a consistent definition for identifying a level based on historical price movements, then classified levels according to how many times they had previously been approached and respected. A respect meant that price had approached a level and subsequently moved away without breaking through it. The test used defined movement thresholds rather than relying on visual judgement.

The main question was straightforward: when price was approaching one of these levels, was it more likely to reach that level before moving the same distance in the opposite direction?

To establish whether the results were meaningful, I also included comparison observations using ordinary reference levels at equivalent distances. These controls were important because a 50% success rate might initially seem encouraging, but would provide little predictive value if ordinary reference levels produced the same result. I also examined whether the identified levels were reached within five days, rather than relying on a single measurement.

### Results

The results were grouped according to the number of previous respects recorded for each level.

| Previous respects | Evaluations | Identified level reached first | Comparison level reached first |
|---|---:|---:|---:|
| 0 | 87,908 | 49.8% | 49.9% |
| 1 | 44,280 | 50.5% | 49.8% |
| 2 | 31,315 | 50.3% | 49.7% |
| 3 | 22,684 | 50.5% | 50.0% |
| 4+ | 16,922 | 49.9% | 49.6% |

*Evaluations represent historical test observations, not necessarily unique levels or independent trading opportunities.*

Across the different groups, the results were remarkably similar to the comparison levels. Even levels that had previously been respected multiple times showed only small differences from the controls.

The five day analysis produced a similar finding. Although many of the identified levels were eventually reached, ordinary comparison levels were reached at broadly comparable rates. This was an important distinction. A price level being reached frequently did not necessarily mean that identifying it provided useful predictive information.

### Statistical Validation and Limitations

I also examined whether the small differences observed were sufficiently consistent to support a meaningful conclusion. The identified levels performed slightly better than the comparison group, but the difference was small. Further statistical testing showed that the results were not strong enough to establish a genuine predictive advantage. The difference could have occurred through ordinary variation in the historical data.

The investigation also required careful attention to how historical information was recorded. During related testing, I identified timing problems where information from later market movements could incorrectly influence how earlier price levels were classified. These issues reinforced the importance of ensuring that research decisions were based only on information that would have been available at the relevant point in time. There were limitations to the study. The automated level definitions were not identical to every level I would have selected manually, and the results did not evaluate the complete trading strategy.

The observations were also drawn from overlapping historical periods, meaning they could not all be treated as independent events.

## Supporting Research and Evidence

**[12-Month Statistical Research Report](../evidence/filled_trade_feature_analysis.pdf)**

This independent investigation assisted by Claude AI examined 330 simulated trades across a 12-month historical period to identify whether information available before a trade could help distinguish successful outcomes from unsuccessful ones.

Claude AI conducted 241 statistical comparisons, including checks across separate historical periods and investigations into misleading relationships caused by information leakage or differences between financial instruments.

I directed the research questions and used the findings to evaluate which explanations were supported by the data and which required further investigation.

The analysis did not establish a reliable predictive signal within the variables tested, reinforcing the importance of comparison groups, data quality and testing findings beyond the initial sample.

### Research Conclusion

The investigation did not provide convincing evidence that these automatically identified price levels offered a meaningful predictive advantage over the comparison levels. Although some groups showed small positive differences, the effects were inconsistent and insufficient to establish a reliable advantage.

This changed how I approached the research. Instead of assuming that a visually convincing market pattern contained useful information, I placed greater emphasis on comparing it against ordinary market behaviour. The most important lesson was that identifying a recurring pattern and demonstrating that it has predictive value are two different things.

## Research Decisions and What I Learned

One of the biggest changes in my approach throughout the project was moving away from relying on individual examples or encouraging short term results, towards designing experiments that could challenge my own assumptions. Early in development, much of my work involved investigating whether the software could recognise the patterns I was looking for. As the project progressed, I realised that correctly identifying a pattern was a separate question from whether that pattern had any genuine predictive value.

This changed how I approached the research. I began placing greater emphasis on comparison groups, clearly defined measurements, larger historical samples and identifying alternative explanations for the results. I also learned how easily research findings could become misleading. Small samples could produce encouraging results by chance, historical information could be introduced at the wrong point in time, and repeatedly adjusting parameters against the same dataset could make results appear stronger than they really were. The research also taught me to separate different questions. If a strategy performed poorly, that didn't necessarily establish whether the problem was the underlying hypothesis, the software implementation, the timing of decisions or the assumptions used in the analysis. Each required its own investigation.

Ultimately, the research did not establish a reliable predictive advantage for the main hypotheses I tested. Although that wasn't the outcome I originally wanted, it was an important result of the work.

I had developed a much more structured approach to conducting research: turning observations into testable questions, collecting and validating evidence, comparing alternative explanations and being prepared to reject an idea when the results didn't support it.

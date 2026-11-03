# 02. Investigating Module 2 and Developing Visual Verification

**Focus:** Requirements analysis, testing, data validation and root cause investigation

## The Challenge

Module 2 was the most difficult part of MBFX to develop and test.

While Module 1 focused on identifying and validating important price levels, Module 2 had to recognise the different conditions that could lead to a potential trade. These included changes in market direction, liquidity sweeps, inducements, the formation of trading areas and price returning to those areas. The difficulty was that a valid setup didn't always follow the same sequence. Different market conditions could produce different variations of the same trading opportunity, meaning the detection logic needed to account for several possible scenarios. This made it particularly difficult to translate the original trading rules into code that behaved consistently.

## Three Development Attempts

I rebuilt Module 2 three times while trying to get the detection logic to match the original requirements. Although I could compile and test the code, I repeatedly struggled to establish whether the system was identifying the correct trading behaviour. The numerical logs could tell me that a particular condition had passed, a sweep had been detected or a potential trading area had formed. What they couldn't easily tell me was whether that decision made sense when compared against the actual price movement.

This created a problem with my testing process. I had information about what the system was reporting, but not enough evidence to determine whether those reports were correct.

## Changing the Testing Approach


## Visual Verification in Practice

The Python charting system allowed me to examine the same recorded trading setup across different timeframes.

The example below shows a GBPJPY setup that reached the entry preparation stage. Rather than relying solely on the event log, I could inspect the proposed trading area, entry and stop loss levels, and surrounding price movements.

### Detailed View (M1)

![GBPJPY M1 visual verification](../assets/screenshots/TESTER__GBPJPY__M1__M2_ENTRY_ARMED__S_GBPJPY_M1_A_sell_199.86400_1762421580__1773837316.png)

### Wider Market Context (H1)

![GBPJPY H1 visual verification](../assets/screenshots/TESTER__GBPJPY__M1__M2_ENTRY_ARMED__S_GBPJPY_M1_A_sell_199.86400_1762421580__CTX_H1__1773837318.png)

Both charts relate to the same recorded setup, but show it from different perspectives.

This was useful because Module 2 decisions depended on conditions across multiple timeframes. A detection that appeared reasonable on a one minute chart could still need investigation against the broader market structure.

Linking events to charts made it easier to identify questionable detections, inspect event timing and decide which conditions needed further testing.


Rather than continuing to modify the code without fully understanding what was happening, I decided to improve the information available during testing. I directed the development of a Python polling and visual verification system that collected important events from Module 2 and recreated them on historical price charts. This meant I could examine the recorded event, its timing, the price levels involved and the surrounding market conditions together. I could then compare what the system had detected against the original trading requirements, identify questionable decisions and narrow down which part of the logic needed investigating.

The main improvement wasn't that the new tools automatically corrected Module 2. It was that **I now had a way to investigate why the system was behaving differently from what I expected.** This changed how I approached subsequent testing and helped shape the more detailed data collection and reporting tools developed later in the project.

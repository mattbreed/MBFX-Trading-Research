# 03. Building and Validating Research Datasets

**Focus:** Data collection, data quality, validation, reporting and problem solving

The software examined price movements, checked them against a set of rules and recorded what happened. 

Some opportunities were rejected because they didn't meet the requirements, while others progressed through further checks or simulated trading. As the project grew, I needed much more information about the decisions the system was making. For example, if the software identified a potential trade, I wanted to know which conditions it had passed, whether it was rejected later, why it was rejected and what happened if it progressed to a simulated trade.

The problem was that these decisions were recorded separately, often at different times. A single potential trade could produce several individual records, and not every opportunity would reach the same stage. I needed a way to bring this information together so that I could analyse what was happening without manually searching through thousands of logs.

## Building the Dataset

I directed the development of a Python script that collected information from the trading software and organised it into structured CSV datasets. One of the main challenges was making sure records belonging to the same potential trade were correctly linked. For example, the software might identify an opportunity at 9:00am, complete another check at 9:05am and record a result at 10:30am. Those were three separate events, but they needed to be connected to the same opportunity.

The Python script used identifiers and other recorded information to group related events into individual research records. It also needed to handle repeated events, inconsistent identifiers and incomplete records.

The main reports included:

| Report | What it was used for |
|---|---|
| `master_candidates.csv` | A detailed record of individual events and decisions |
| `merged_setups.csv` | Combined related information into one record per potential trade |
| `field_coverage.csv` | Checked how consistently information was being recorded |
| `blank_field_report.csv` | Identified missing information and records requiring investigation |
| `run_summary.csv` | Summarised the results of each historical test |

This meant I could review an entire test, compare individual opportunities and investigate problems without relying on the original software logs alone.

## Investigating Missing Information

As the dataset expanded to almost a thousand fields, I began finding information that was either missing or not being recorded correctly. However, not every blank field represented a problem. For example, if a potential trade was rejected at an early stage, it wouldn't have a completed trade result. That information was genuinely unavailable because the process had stopped. 

Although, if a simulated trade had been completed and its result or price movement statistics were missing, that was something I needed to investigate. This distinction became an important part of the data quality checks.

I used Python to produce reports showing which fields were populated, which records were missing information and whether those values should have existed at that stage. I could then trace individual records back through the system to establish where the missing information originated.

I also found that automatically filling blank fields with default values could make the reports appear more complete than they really were. A populated cell wasn't necessarily evidence that the system had successfully recorded the required information. I therefore needed to distinguish between actual recorded values, values that didn't apply and information that was genuinely missing.

## Using Data Quality Reports to Investigate Problems

The reports helped me narrow down problems much more efficiently. Rather than searching through the entire dataset whenever something appeared incorrect, I could identify the affected field, find examples of records where it was missing and investigate which part of the system was responsible. As an example, when a result was missing from a completed simulated trade, I could check whether the original software had recorded it, whether the Python script had collected it and whether it had been assigned to the correct research record. 

This made it easier to identify problems with data collection, processing and reporting. It also gave me a more consistent way to compare the results of different historical tests, knowing which information was available and where limitations remained.

Another issue I encountered was the timing of data arriving from the trading software. Sometimes Python would attempt to process an event before all the associated information was available, resulting in incomplete records or missing chart data. To investigate this, I added diagnostic print statements at different stages of the Python script. These helped me establish whether an event had been received, whether it could be read correctly and whether the information had reached the reporting process. 

I also made changes to how delayed information was handled, allowing the system to check again when the required data became available rather than assuming everything was ready immediately. This was particularly useful because a missing value in the final report didn't necessarily mean the original software had failed to produce it. The problem could have occurred anywhere between the original event, the Python processing and the final dataset.


## Example: Identifying Missing Performance Data

One historical test highlighted missing information from a completed simulated trade. The trade had progressed through the system, but several measurements describing its price movements were missing from the research dataset.

An extract from the actual `blank_field_report.csv` showed:

| Measurement | Expected records | Populated | Unexpected blanks |
|---|---:|---:|---:|
| Reached 1:1 reward-to-risk | 1 | 0 | 1 |
| Maximum favourable price movement | 1 | 0 | 1 |
| Maximum adverse price movement | 1 | 0 | 1 |

These measurements were used to assess how far price moved in favour of or against a simulated trade. The important finding was that the fields were not simply empty. The report classified them as missing **even though the trade had reached a stage where that information was expected**. 

This gave me specific fields and a specific research record to investigate, rather than having to search through the entire dataset.

The report did not establish the underlying cause. Further checks were needed to determine whether the information had been generated, collected and processed correctly.

## How the Python Validation Worked

The following extract from `ai_bridge_watcher.py` shows part of the missing field validation process.

```python
for field in MERGED_SETUP_COLUMNS:
    populated_values = []
    missing_setup_ids = []
    unexpected_missing_setup_ids = []
    expected_rows = 0
    unexpected_blank_rows = 0

    for row in rows:
        value = row.get(field, "")
        expected_here = _field_expected_for_row(field, row)

        if expected_here:
            expected_rows += 1

        if _coverage_value_present(value):
            populated_values.append(value)
        else:
            sid = row.get("setup_id", "")
            if sid and len(missing_setup_ids) < 5:
                missing_setup_ids.append(sid)

            if expected_here:
                unexpected_blank_rows += 1
                if sid and len(unexpected_missing_setup_ids) < 5:
                    unexpected_missing_setup_ids.append(sid)

    populated_count = len(populated_values)
    blank_count = total_rows - populated_count

    if blank_count <= 0:
        continue

    populated_pct = round(
        (populated_count / total_rows) * 100.0, 2
    ) if total_rows else 0.0

    owner = _field_owner(field)
    priority = _field_priority(field)
    status = _field_status(populated_pct, owner)
    suggested_action = _blank_field_action(owner, field)
    classification = _blank_field_classification(
        owner, field, blank_count, unexpected_blank_rows
    )
```

*Extract from the original Python reporting script.*

The code checked each field across the research records and determined whether a value was expected at that stage. When information was missing, it recorded the affected setup identifiers, counted unexpected blanks and assigned a classification for further investigation.

This was useful because not all missing values had the same meaning. An unavailable result from an early rejection was different from a missing result on a completed trade. The aim was to make the reports useful for identifying and investigating data quality problems, rather than simply measuring how many cells were populated.

## Outcome and What I Learned

The main improvement was having a more organised and reliable way to collect and investigate research data. Instead of working through individual logs, I could follow a potential trade through the different stages of the system and examine the information recorded at each point.

The field coverage and missing data reports also made it easier to identify problems. I could see which fields were incomplete, find the affected records and investigate whether the issue originated in the trading software, Python processing or final reporting.

One of the biggest lessons was that a report appearing complete didn't necessarily mean the information was correct. Default values, duplicated events and data arriving at different times could all affect the results. I learned to question where the information came from, whether it should have been recorded and whether it matched the original events. Although these checks didn't guarantee that every record was accurate, they gave me a much better way to identify problems before relying on the data for further analysis.

## Supporting Evidence

The full research dataset is included to demonstrate the scale of the data collection and reporting system.

The [merged_setups.csv](../evidence/merged_setups.csv) file contains hundreds of fields covering the different stages of each potential trade, from initial detection and validation through to simulated execution, outcomes and performance measurements. Not every field is populated for every record. Some information only becomes available when a potential trade reaches a particular stage, while other missing values were investigated through the data quality reports. 

The full `merged_setups.csv` dataset may be too large to preview directly on GitHub. A smaller [dataset preview](../evidence/merged_setups_preview.csv) has therefore been provided, retaining all 805 original fields with fewer records for easier inspection. The complete dataset remains available to download and examine.

The accompanying `blank_field_report.csv` shows how I monitored missing information and identified records requiring further investigation.

**[View the missing-data validation report](../evidence/blank_field_report.csv)**

This report was generated from the same historical test as the full research dataset. It examines field coverage across 174 progressed opportunities, identifies missing information, records affected setup identifiers and classifies fields requiring further investigation.

The report distinguishes between information that may legitimately be absent and information that was expected but not recorded. Its classifications were used to guide investigation rather than treated as definitive proof of individual software defects.

These files are included as evidence of the data collection and validation process.

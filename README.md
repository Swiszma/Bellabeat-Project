# Bellabeat Smart Device Analysis

### Table of Contents
- [Project Overview](#project-overview)
- [Phase 1: Ask Phase](#ask-phase)
- [Phase 2: Prepare Phase](prepare-phase)
- [Phase 3: Process Phase](process-phase)
- [Phase 4: Analyze Phase](analyze-phase)
- [Phase 5: Share Phase](share-phase)
- [Phase 6: Act Phase](act-phase)

### Poject Overview
Bellabeat is a successful, high-tech manufacturer of health-focused smart products designed specifically to empower and inspire women. Founded by Urška Sršen and Sando Mur, the company has grown rapidly since 2013 by leveraging data on activity, sleep, stress, and reproductive health to inform their beautifully designed wellness technology.
While currently a small company, the executive team believes there is significant potential to become a larger player in the global smart device market. They invest heavily in digital marketing—including Google Search, YouTube video ads, and active engagement across major social media platforms.

The core objective of this project is to analyze smart device fitness data to uncover high-level insights into how consumers are currently using their wellness trackers. These behavioral trends must then be applied to one specific Bellabeat product (the Bellabeat App) to generate data-driven, strategic recommendations that will optimize the company's future digital marketing strategy and unlock new avenues for growth.

### Data Sources
The primary dataset for this analysis is the [FitBit Fitness Tracker Data](https://www.google.com/url?sa=E&q=https%3A%2F%2Fwww.kaggle.com%2Fdatasets%2Farashnic%2Ffitbit) , hosted publicly on Kaggle by user Mobius.

#### Dataset Selection
To prevent redundant analysis and focus purely on actionable, high-level business trends, the 18 files were triaged. The following three core files were selected for the ETL(Extract, Transform, Load) pipeline:
1. dailyActivity_merged.csv: Contains daily summaries of steps, distance, calories, and activity intensities. (The Master Anchor Table)
2. sleepDay_merged.csv: Contains daily sleep logs. (To be joined to the Master Table)
3. hourlySteps_merged.csv: Contains hourly breakdowns of steps to identify peak user activity times. (Kept separate to maintain granularity)

#### Data Credibility & Bias (The ROCCC Framework)
Before moving to the Process phase, the dataset was evaluated for credibility using the ROCCC framework (Reliable, Original, Comprehensive, Current, Cited):
- R - Reliable (LOW): The sample size is only 33 unique users, which is not large enough to definitively represent the global fitness tech market with high confidence.
- O - Original (LOW): This is third-party data collected via a distributed survey (Amazon Mechanical Turk), not first-party proprietary data directly from Fitbit.
- C - Comprehensive (MEDIUM): While it contains excellent granular data on daily habits, it critically lacks demographic data. Most notably, there is no gender data. Since Bellabeat explicitly targets women, applying this generalized data to Bellabeat’s customer base introduces a potential bias.
- C - Current (LOW): The data was collected in 2016. The wearable tech landscape and consumer habits have evolved significantly since then.
- C - Cited (HIGH): The data source is well-documented and properly cited on Kaggle.

 Despite its limitations, this dataset provides a highly detailed snapshot of real-world fitness tracker usage. By identifying the behavioral correlations within this sample, we can generate the exact high-level trends requested to drive Bellabeat's marketing strategy, provided stakeholders are made aware of the demographic bias.

 ### Tools and Uses
 - Microsoft Excel: Utilized for initial raw data triage, structural formatting correction, and resolving complex regional date-format conflicts (US vs. Global formatting).
 - MySQL (Workbench): Engineered advanced SQL queries to validate data integrity (identifying and eliminating duplicates and "ghost" users), handle mixed 12hr/24hr time formats, and perform relational database merging using Composite Keys.
 - Microsoft Power BI: Architected an interactive, executive-facing Business Intelligence dashboard utilizing custom DAX measures, dynamic conditional formatting (benchmarked against CDC health standards), and a custom-built matrix calendar heatmap.

### Process Phase
Before importing data into a SQL database, the raw CSV files were inspected to correct critical formatting errors that would break SQL joins.
- Primary Key Standardization: The Id column defaulted to scientific notation (e.g., 1.5E+09). This was converted to a standard numeric format (1503960366) across all files.
- Date Standardization: The SleepDay column contained extraneous time strings (" 12:00:00 AM"). Find & Replace was used to isolate pure dates (MM/DD/YYYY).
- Resolving the Regional Date Trap: ActivityHour contained mixed 12hr/24hr formats due to regional OS conflicts. The "Text-to-Columns" wizard was used to force standard MDY recognition, then a custom format (yyyy-mm-dd hh:mm:ss) unified all 22,099 rows before SQL import.

The three cleaned CSVs were imported into the bellabeat database schema. All Date/Time columns were explicitly imported as TEXT to prevent MySQL from overwriting unrecognized formats with NULL values.
Rigorously verified data integrity through targeted SQL sanity checks:
- ID Length Check: Verified all IDs were exactly 10 digits (100% pass).
- NULL Check: Checked primary keys and core metrics for missing values (0 NULLs found).
- Data Discovery: Identified 3 duplicate rows in the sleep data, and 77 "Ghost Days" where users logged exactly 0 steps (indicating the device was not worn).
See [SQL Querries Here](SQL-queries)

### Analyze Phase

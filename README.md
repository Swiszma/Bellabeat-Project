# Bellabeat Smart Device Analysis

### Table of Contents
- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools and Uses](#tools-and-uses)
- [Process Phase](#process-phase)
- [Analyze Phase](#analyze-phase)
- [Share Phase](#share-phase)
- [Act Phase](#act-phase)

### Project Overview
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
[See SQL Queries Here](https://docs.google.com/document/d/1vvOy3jkLcEeu5tSUtB55LF1UDCKMA_vY/edit?usp=drive_link&ouid=117575260738791169612&rtpof=true&sd=true)

### Analyze Phase
To uncover behavioral trends and answer the CEO's guiding questions, targeted SQL queries were executed against the cleaned master_daily_activity and hourly_steps_clean tables.
[you can find the SQL Quries here](https://docs.google.com/document/d/10TeIYLkdP3Bw5FURfKeRk6nfh0fiXkyM/edit?usp=drive_link&ouid=117575260738791169612&rtpof=true&sd=true)

The analysis results are summarizes as follows:
1. Users are highly consistent during the day, wearing the device for an average of 26 out of 31 days. However, sleep tracking plummeted. Out of 863 total tracked days, sleep was only recorded on 413 days. Users take the device off at night 52% of the time.
2. When worn, users average 8,319 steps per day.
3. Users are highly sedentary, sitting for an average of 16.0 hours per day.
4. Activity peaks heavily on Tuesdays (8,949 steps) and Saturdays (8,947 steps).
5. Sundays see the lowest activity (7,627 steps), indicating it is treated strictly as a rest day.
6. Mondays see the highest amount of sedentary behavior, peaking at 16.5 hours of sitting.
7. Identified two distinct hourly peaks in the 24-hour cycle. The "Lunch Break" Peak: A moderate spike in activity occurs between 12:00 PM and 2:00 PM. The "After-Work" Peak: The massive, undisputed peak of user activity occurs between 5:00 PM and 7:00 PM, hitting its absolute highest point at 18:00 (6:00 PM).

### Share Phase
Below is a snapshot of the Dashboard
<img width="1378" height="742" alt="image" src="https://github.com/user-attachments/assets/637c8d8e-f2db-4109-a44e-ebe5630e88ab" />

### Act Phase
#### Recommendations
To drive user engagement, improve consumer health outcomes, and optimize digital marketing spend, I recommend implementing the following four data-driven strategies within the Bellabeat App ecosystem.

1. The "Pre-Peak" Push Notification Strategy (Time-Based Targeting)
- The Data: User activity spikes sharply at 12:00 PM and peaks massively at 6:00 PM (18:00).
- The Action: Bellabeat should shift away from generalized email blasts. Instead, program the Bellabeat App to send highly targeted push notifications exactly 30 minutes prior to these established peak hours.
11:30 AM: "Almost lunchtime! Grab your walking shoes and hit your step goal."
5:00 PM: "Work is almost over. Don't head to the couch yet...let's get that evening workout in!"

2. The "Charge & Sleep" Campaign (Addressing the 52% Drop-off)
- The Data: Users tracked 863 active daytime logs, but only 413 sleep logs. Users are removing the device at night 52% of the time, representing massive lost engagement.
- The Action: We must determine if this is a behavioral habit (charging) or a physical design issue (discomfort).
- In-App Questionnaire: Trigger a targeted, one-question survey in the Bellabeat App for users who frequently log 0 sleep minutes: "How comfortable is your device to sleep in?"
- Product Design Opportunity: If the survey reveals a comfort issue, Urška Sršen’s design team can use this data to develop and market a softer, sleep-specific accessory band.
- Behavioral Fix & Upsell: Implement an 8:00 PM "Wind Down" notification reminding users to charge their device. Use this notification to upsell the premium Bellabeat Membership, offering exclusive sleep-meditation audio tracks to listen to in bed.

3. Combatting "Sedentary Monday" (Behavioral Intervention)
- The Data: Users sit for an average of 15.95 hours a day, peaking at 16.5 hours on Mondays. They are exceeding the healthy 8-hour sedentary limit by over 100%.
- The Action: Launch a "Beat the Desk" digital marketing campaign targeting office workers.
- App Feature: Add an "Anti-Sedentary" feature to the app that gently vibrates the user's Bellabeat Leaf or Time watch if they have registered 0 steps for two consecutive hours between 9:00 AM and 5:00 PM.

4. Synchronizing Ads with the "Weekly Rhythm" (Budget Optimization)
- The Data: Activity peaks on Tuesdays and Saturdays, but plummets on Sundays.
- The Action: Reallocate the digital marketing budget (Google Ads, Instagram, YouTube) to match consumer psychology.
- Mon/Tue & Fri/Sat: Spend heavily on high-energy, active lifestyle ads and fitness challenges to capitalize on existing motivation.
- Sundays: Slash the ad budget for fitness gear. Shift all Sunday marketing copy to focus on "Rest, Recovery, and Self-Care," heavily promoting the Bellabeat App's stress-tracking and mindfulness features.

#### Next Steps & Additional Data Required
To ensure these recommendations perfectly align with Bellabeat’s specific brand identity, the following next steps should be taken:
- Demographic Verification: The public Kaggle dataset lacked gender demographics. Bellabeat must pull a sample of its own first-party female user data to verify if these exact hourly peaks (6:00 PM) hold true specifically for women.
- A/B Testing: Roll out the "Pre-Peak" push notifications to a small test group of 5,000 app users for two weeks. Measure the actual percentage increase in steps before deploying the feature globally.


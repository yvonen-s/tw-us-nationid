# Comparing Trends in Taiwanese Election and Democratization Data and U.S.-to-Taiwan Military Aid
## Project Overview and Context
_//////////////DRAFT///////////_

'Cross-strait' relations between Taiwan (R.O.C.) and mainland China (P.R.C.) have always been deeply contentious. China maintains it has ownership over Taiwan as a "renegade province" and condemns any country which extends Taiwan official diplomatic recognition. Despite this, for many years the two have tenously coexisted without violent conflict, and Taiwan has operated with _de facto_ independence. In recent years, China has escalated its threats of forcible reunification while the Taiwanese government has increased efforts to strengthen its policy relationships with the U.S. 

The U.S. is pledged to provide Taiwan with defensive arms under the Taiwan Relations Act of 1979, though they have refused to formally acknowledge Taiwan as a sovereign nation. Bills proposing additional, explicit diplomatic and defensive committments to Taiwan are currently active in the U.S. federal legislature (e.g. Taiwan Defense Act, Arm Taiwan Act of 2021). It is Taiwan's largest source of military aid in all forms. U.S. support of Taiwan can be viewed as part of an overall foreign policy mission to support democratic governance over autocratic regimes.

This project: 
1. Creates a comprehensive aggregated dataframe of U.S. military aid to Taiwan which does not currently exist publicly and can be used to analyze military support spending trends over time. Identifies years with highest/lowest spending by aid type and overall.
2. Pulls, translates and organizes years of Taiwanese election, democratization and security survey data specifically describing Taiwanese resident citizens' political attitudes towards independence, national defense, and U.S./Taiwanese/Chinese governments. I have specifically focused on questions which ask about **stances on seeking _de jure_ independence**, **whether they believe U.S. will indeed provide military assistance in an instance of Chinese invasion**, **degree of trust in Taiwanese government's handling of national security** and **degree of trust towards the mainland Chinese government**. 
3. Charts and compares Chinese and Taiwanese democratic/autocratic regime characteristics over time using Polity Score data, building visualizations for each country which illustrate the different dimensions of political governance. 
4. Generates graphs from these three new datasets illustrating trends in aid expenditures, political attitudes and regime characteristics and comparing them to one another, observing for patterns that might be jumping off points for more rigorous study.

Since input data is drawn directly from regularly published data reports, ideally the scripts can continue to be run on future releases from the same sources.

Summary of repository files in `README_CONTENTS.md`.
## Data 
placeholder
## Scripts
_////////DRAFT. scripts currently private/////////_

Six total scripts. Script 1 (`translate.py`) should be run first to translate Chinese input files, and Script 6 (`compgraphs.py`) should be run last as it depends on outputs from the previous scripts. The rest can be run in-between in any order.
### 1. translate.py
----
#### Translating Chinese character data to English text
This script runs a replacement translation function through Taiwanese-originating datasets which will later be used in scripts `teds.py` and `twusop.py`. In addition to English translation it also prevents issues with Chinese characters becoming unreadable when saved to local .csv or .xlsx files. I only coded translations for the data entries I planned to analyze as the rest will later be dropped, but this script could be built out to quickly translate these specific surveys in full.  

Reads datasets into variable `teds`. Renames Chinese character column headings to English text using positional commands `teds.columns.values[0] = 'Survey Wave Number', teds.columns.values[1] = 'Satisfaction Level'`, etc. Creates dictionaries for each column using the predefined Chinese character answers as keys and English character names as values: (e.g. `approvallist = {'非常滿意':'vsatisfact', '非常不滿意':'nsatisfact'}`, etc.). Uses a for loop to automatically apply `.replace()` method with `{'Satisfaction Level':approvallist}` (and so on) as arguments and saves translated .csv.
### 2. teds.py 
----
#### Aggregating and cleaning TEDS election and democratization data
Taiwan’s Election and Democratization Study (TEDS) is a continual large-scale survey research project supported by the  Taiwanese Ministry of Science and Technology. Its chief purpose is to integrate several election-oriented face-to-face surveys in Taiwan.

I selected four major questions from the "TEDS Telephone and Mobile Phone Interview Survey of the Presidential Satisfaction", which has been conducted 4 times a year since 2012:
1. Considering the relationship between Taiwan and mainland China, which of the following six positions do you agree with: (1) Immediate unification; (2) Immediate independence; (3) Maintain status quo and move towards unification in future; (4) Maintain status quo and move towards independence in future; (5) Maintain status quo, decide on unification or independence in future; (6) Maintain status quo forever
2. How satisfied are you with the president's performance in national defense?
3. What is the highest priority concern the president should address (other than COVID-19, for the most recent years)?
4. How much do you trust the mainland Chinese government (scale of 0-10)?

Each survey "wave" is originally a separate datafile. I chose to use aggregate data with all responses expressed as the percentage of total respondents who held that sentiment. They were first run through by the `translate.py` script.

This script goes through each file and filters out only the data for the four questions (including question number). It converts percentages to integers and adds a `dateID` column identifying the survey wave: as original name format is `TEDS{year}_PA{quarter}`, script cuts and joins the string to form a new 6-character ID (`TEDS2013_PA09` becomes `201309`). It then adds everything to a new dataframe eventually containing total survey data between 2012-2020. Data is reformatted and grouped by dateID with columns for each corresponding response value of Q1 (`unification`, `independence`, `squnification`, `sqindependence`, `sqidk`, `sqforever`) and Q2 (`vsatisfact`, `satisfact`, `nsatisfact`, `nallsatisfact`, `noopinion`, `refuse` and `idk`). With regard to Q3, the code extracts only the proportion of people who answered Cross-strait relations as their highest priority concern, inputs this number for the survey group as new column `xstrait` and drops the rest of the input answers for that question. With regard to Q4, the code computes a weighted average of all responses, inputs into new column `prctrust` and drops the rest of the response data. Output as **_`tedssorted.csv`_**.
#### Charts and analysis
chop strings so can be charted properly. placeholder
### 3. twusop.py
----
_////DRAFT///_

#### Aggregating and cleaning national security study data
very similar process to `teds.py`, just using question from a different dataset. decide if want to append together or not. may also just drop this data
#### Charts and analysis
placeholder
### 4. aidmerge.py
----
#### Building merged dataframe combining and summing different types of U.S. military aid each year
Tracks six major types of military aid that the U.S. has provided to Taiwan: major arms sales (`cip_arms_sales_by_year.csv`), security assistance spending (`cip_security_asst_by_year.csv`), foreign military training (`cipfmt.csv`), excess defense articles (`EDA_Public_Report.csv`), foreign assistance purposed for military assistance objectives (`foreignassistance.csv`) and overseas loans and grants for military spending (`us_foreign_greenbook.csv`, pulled from API). These datasets were all issued by different agencies. Filters out Taiwan-specific and purpose-specific data from global datasets, converts all year and spending values to `integers` and trims datasets to drop all years before 2003 (the earliest record of the most limited dataset). Uses six individualized command sets (since they are all formatted differently) to pull this data into a standardized dataframe `aidmerge`, with a new `sourcetype` column that groups each set of data according to type of aid (source). (Major Arms Sales data is split into three types - see notes below.) Checks each group to ensure it contains at least one value for all years in that time period, and if not creates and appends new data entry for each missing year with expenditures of $0. For groups with multiple data entries per year (meaning the original dataset broke down aid by individual allocations), sums all into single totals per year then drops the individual allocations. Now that each group has exactly one corresponding value per year, merges all together on `year` into **_`aidmerge.csv`_**, a file listing the value of military foreign aid the U.S. provided to Taiwan each year, by type and overall (new `totalsum` column).
#### Calculating basic statistics
Prints a summary report identifying years in this range with the **highest** and **lowest** amounts of military aid spending, in total and for each subcategory. 
#### Charts and analysis
Output line graph of total spending over time, **_`aidmerge.png`_**. Output charts of select types of spending over time, relative to each other, **_`aidtypes.png`_**.
#### Important notes
- **This is likely not an exact accounting of U.S. military aid to Taiwan.** Assistance is issued by multiple federal agencies and can be vaguely classified and/or tangentially related; some transactions may be unavailable to the public. However, I hope they capture enough of the largest foreign transactions so that their relationships to each other sufficiently simulate actual trends. This is why the analysis focuses only on changes over time and relative statistics.
- When graphing trends in this project I am valuating all forms of aid absolutely regardless of whether they are grants, loans or sales. 'In kind' aid (foreign military training and excess defense articles) is converted to representative monetary amounts based on approximation values that were provided in the raw data. Delineated types of aid could be extracted from the dataframe separately to do different kinds of analysis. 
- Because of differences between the amounts of U.S. foreign arms sales that are notified, approved and delivered, major arms sales is represented as three `Notified Sales`, `Approved Sales`, `Delivered Sales`. The `totalsum` of aid provided per year uses only the amount `Delivered Sales`. 
**_`aidtypes.png`_**.
### 5. dippol.py
----
_///draft draft draft_
#### Scrapes Polity Score data on China and Taiwan regime characteristics
Polity Scores are a third-party metric examining "concomitant qualities of democratic and autocratic authority in governing institutions", which assigns scores to countries based on different regime characteristics. This script reads score data from the global Polity5 Project report `polityscores.csv` into a dataframe, filters to data on `Taiwan` and `China` only, groups by `country` and outputs new dataset **_`roc-prc-polity.csv`_**. 
#### Charts China and Taiwan government development towards democratic characteristics over time
Examines each country's development in different governance scores. I selected five specific variables: `democ` (institutionalized democracy indicator), `autoc` (institutionalized autocracy indicator), `polity` (overall regime authority score ranked on 21-pt scale, ranging from -10 hereditary monarchy to +10 consolidated democracy), `xcont` (executive constraints, measure of executive authority independence) and `xrcomp` (competitiveness of executive selection). Detailed definitions and methodology behind these variables is in `polity5manual.pdf`. 

Script generates charts for each country showing year-to-year changes as well as visualizations of how data could be interpreted to understand dimensions of democracy and autocracy. It also generates comparison line graphs layering Chinese and Taiwanese raw score trends on top of each other, using a year-trimmed version of the full dataset (Chinese data starts from 1800 whereas Taiwanese data begins in 1949, the year that the R.O.C. government evacuated mainland China and relocated to the island of Taiwan).
#### Output analysis
placeholder
### 6. compgraphs.py
----
_//VERY DRAFT_

This script combines data from the previous scripts and builds graphs layering different trends from different dataframes over each other.
#### Trim data for comparison down to matching time periods and add common merge keys
## Summary/Conclusions
placeholder
## Sources
#### Taiwanese Election and Democratization Data:
- Taiwan's Election and Democratization Study (TEDS), Taiwan Ministry of Science and Technology: http://teds.nccu.edu.tw/main.php
- Taiwan National Security Study (SNSS), Election Study Center of Taiwan’s National Chengchi University (supported by Duke University Program in Asian Security Studies)
#### Governance Data
- Polity IV Scores for Individual Country Regime Trends, Center for Systemic Peace: http://www.systemicpeace.org/polityproject.html
- Diplomatic Recognition of Taiwan, Timothy S. Rich/Inter-University Consortium for Public and Social Research: https://www.icpsr.umich.edu/web/ICPSR/studies/30802/summary
#### Military Aid and Spending Data: 
- Taiwan: Major U.S. Arms Sales Since 1990, Shirley A. Kan/Congressional Research Service  
- Arms Sales, Security Assistance Spending and Foreign Military Training Databases, CIP Security Assistance Monitor: https://securityassistance.org
- Excess Defense Articles (EDA) Public Report, U.S. Department of State
- Section 655 U.S. Annual Military Assistance Reports, U.S. Defense Security Cooperation Agency (DSCA)
- U.S. Foreign Assistance Database, U.S. Department of State and USAID: http://foreignassistance.gov
- U.S. Overseas Loans and Grants Greenbook, USAID

_//need to attach sources to specific datasets_

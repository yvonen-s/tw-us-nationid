# Comparing Trends in Taiwanese Election and Democratization Data and U.S.-to-Taiwan Military Aid
## Project Overview and Context
_//////////////DRAFT///////////_

'Cross-strait' relations between Taiwan (R.O.C.) and mainland China (P.R.C.) have always been deeply contentious. China maintains it has ownership over Taiwan as a "renegade province" and condemns any country which extends Taiwan official diplomatic recognition. Despite this, for many years the two have tenously coexisted without violent conflict, and Taiwan has operated with _de facto_ independence. In recent years, China has escalated its threats of forcible reunification while the Taiwanese government has increased efforts to strengthen its policy relationships with the U.S. 

The U.S. is pledged to provide Taiwan with defensive arms under the Taiwan Relations Act of 1979, though they have refused to formally acknowledge Taiwan as a sovereign nation. Bills proposing additional, explicit diplomatic and defensive committments to Taiwan are currently active in the U.S. federal legislature (e.g. Taiwan Defense Act, Arm Taiwan Act of 2021). It is Taiwan's largest source of military aid in all forms. U.S. support of Taiwan can be viewed as part of an overall foreign policy mission to support democratic governance over autocratic regimes.

This project: 
1. Creates a comprehensive dataframe of U.S. military aid to Taiwan which can be used to analyze U.S. military support spending trends over time. Identifies years with highest/lowest spending by aid type and overall.
2. Combines, translates and organizes years of Taiwanese election, democratization and security survey poll data on Taiwanese resident citizens' political attitudes towards seeking _de jure_ independence, the Taiwanese government's handling of national security, the trustworthiness of the mainland Chinese government and whether or not the U.S. will actually honor its military commitment in instance of China's invasion.
3. Briefly examines and compares Chinese and Taiwanese democratic/autocratic regime characteristics over time using Polity Score data, building visualizations for each country which illustrate their democratic development in different dimensions of governance. 
4. Generates graphs from these three new datasets illustrating trends in aid expenditures, political attitudes and regime characteristics and comparing them to one another, observing for patterns that might be jumping off points for more rigorous study.

Since most of the input data is drawn directly from regularly published data reports, ideally the scripts can continue to be run on future releases from the same sources.

Summary of repository files in `README_CONTENTS.md`. Final outputs are two sets of **translated Chinese input files**, three **dataframes** and TBD **charts**.
## Data 
There are three categories of source data. Descriptions and detailed comments on how to retrieve data are in notes.
#### 1. Taiwanese Election and Democratization Data
- Taiwan's Election and Democratization Study (TEDS), Taiwan Ministry of Science and Technology. _Multiple datafiles of survey data published quarterly._ 
- Taiwan National Security Study (SNSS), Election Study Center of Taiwan’s National Chengchi University supported by Duke University Program in Asian Security Studies. _Multiple datafiles of survey data published annually._
#### 2. Regime Governance Data
- Polity IV Scores for Individual Country Regime Trends, Center for Systemic Peace. _Global datafile covering years 1800-2018._
#### 3. Military Aid and Spending Data
- Arms Sales, Security Assistance Spending and Foreign Military Training Databases, CIP Security Assistance Monitor. _Three datafiles covering each category over multiple years. You can download global or individual country datafiles, Taiwan-only data used here._
- Excess Defense Articles (EDA) Public Report, U.S. Department of State. _Global datafile covering years XXXX(need to check)_.
- Section 655 U.S. Annual Military Assistance Reports, U.S. Defense Security Cooperation Agency (DSCA). _Multiple global budget datafiles published annually._
- U.S. Foreign Assistance Database, U.S. Department of State and USAID. _Global datafile covering multiple years. You can pre-filter variables using their interface then download the customized datasets, which was done here._
- U.S. Overseas Loans and Grants Greenbook, USAID. _Global datafile covering multiple years. Retrieved via API._
## Scripts
_////////DRAFT////////_

Six scripts. Script 1 (`translate.py`) should be run first to translate Chinese input files, and Script 6 (`compgraphs.py`) should be run last as it depends on outputs from the previous scripts. The rest (`teds.py`, `twusop.py`, `aidmerge.py` and `dippol.py`) can be run in-between in any order.

Scripts 2-5 each draw from different data source(s) and generate graphs based only on that specific data, while Script 6 draws from the cleaned dataframes produced in those four scripts to generate graphs comparing trends from all sources. Step-by-step comments included in all scripts.
### 1. translate.py
----
#### Translating Chinese character data to English text

### 2. teds.py 
----
#### Aggregating and cleaning TEDS election and democratization data
I selected four major questions from the quarterly administered "Taiwan's Election and Democratization Study (TEDS) Telephone and Mobile Phone Interview Survey of the Presidential Satisfaction":
1. Considering the relationship between Taiwan and mainland China, which of the following six positions do you agree with: (1) Immediate unification; (2) Immediate independence; (3) Maintain status quo and move towards unification in future; (4) Maintain status quo and move towards independence in future; (5) Maintain status quo, decide on unification or independence in future; (6) Maintain status quo forever
2. How satisfied are you with the president's performance in national defense?
3. What is the highest priority concern the president should address (other than COVID-19, for the most recent years)?
4. How much do you trust the mainland Chinese government (scale of 0-10)?

I chose to use aggregate data with all responses expressed as the percentage of total respondents who held that sentiment.

This script goes through each separate survey file and filters out only the data for the four questions (including question number). It converts percentages to integers and adds a `dateID` column identifying the survey wave: as original name format is `TEDS{year}_PA{quarter}`, script cuts and joins the string to form a new 6-character ID (`TEDS2013_PA09` becomes `201309`). It then adds everything to a new dataframe eventually containing total survey data between 2012-2020. Data is reformatted and grouped by dateID with columns for each corresponding response value of Q1 (`unification`, `independence`, `squnification`, `sqindependence`, `sqidk`, `sqforever`) and Q2 (`vsatisfact`, `satisfact`, `nsatisfact`, `nallsatisfact`, `noopinion`, `refuse` and `idk`). With regard to Q3, the code extracts only the proportion of people who answered Cross-strait relations as their highest priority concern, inputs this number for the survey group as new column `xstrait` and drops the rest of the input answers for that question. With regard to Q4, the code computes a weighted average of all responses, inputs into new column `prctrust` and drops the rest of the response data. Output as **_`tedssorted.csv`_**.
#### Charts and analysis
strings/integers so can be charted properly. placeholder
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
Tracks six major types of military aid that the U.S. has provided to Taiwan: major arms sales (`cip_arms_sales_by_year.csv`), security assistance spending (`cip_security_asst_by_year.csv`), foreign military training (`cipfmt.csv`), excess defense articles (`EDA_Public_Report.csv`), foreign assistance purposed for military assistance objectives (`foreignassistance.csv`) and overseas loans and grants for military spending (`us_foreign_greenbook.csv`, pulled from API). These datasets were all issued by different agencies. 

Filters out Taiwan-specific and purpose-specific data from global datasets, converts all year and spending values to `integers` with common units, and trims datasets to drop all years before 2003 (the earliest record of the most limited dataset). Uses six individualized command sets (since they are all formatted differently) to pull this data into a standardized dataframe `aidmerge`, with a new `sourcetype` column that groups each set of data according to type of aid (source). (Major Arms Sales data is split into three types - see notes below.) Checks each group to ensure it contains at least one value for all years in that time period, and if not creates and appends new data entry for each missing year with expenditures of $0. For groups with multiple data entries per year (meaning the original dataset broke down aid by individual allocations), sums all into single totals per year then drops the individual allocations. Now that each group has exactly one corresponding value per year, merges all together on `year` into **_`aidmerge.csv`_**, a file listing the value of military foreign aid the U.S. provided to Taiwan each year, by type and overall (new `totalsum` column).
#### Calculating basic statistics
Prints a summary report identifying years in this range with the **highest** and **lowest** amounts of military aid spending, in total and for each subcategory. 
#### Charts and analysis
Output line graph of total spending over time, **_`aidmerge.png`_**. Output charts of select types of spending over time, relative to each other, **_`aidtypes.png`_**.
#### Important notes
- **This is not an exact accounting of U.S. military aid to Taiwan.** Assistance is issued by multiple federal agencies and can be vaguely classified and/or tangentially related; some transactions may be unavailable to the public. However, I hope they capture enough of the largest foreign transactions so that their relationships to each other sufficiently simulate actual trends. This is why the analysis focuses only on changes over time and relative statistics.
- I am valuating all forms of aid absolutely regardless of whether they are grants, loans or sales. This is bad accounting practice but sufficient for project goal as peoples' perceptions of foreign aid tends to come from news headline dollar amounts, and the U.S.' intention in selling arms to Taiwan is not purely profit-seeking.
- 'In kind' aid (foreign military training and excess defense articles) is converted to representative monetary amounts based on approximation values that were provided in the raw data. 
- Major arms sales are represented as three data types `Notified Sales`, `Approved Sales`, `Delivered Sales`. The `totalsum` of aid provided per year uses only the amount of actual `Delivered Sales`. 
**_`aidtypes.png`_**.
### 5. dippol.py
----
_///draft draft draft_
#### Extracting Polity Score data on China and Taiwan regime characteristics
Polity Scores are a third-party metric examining "concomitant qualities of democratic and autocratic authority in governing institutions", which assigns scores to countries based on different regime characteristics. This script reads score data from the global Polity5 Project report `polityscores.csv` into a dataframe, filters to data on `Taiwan` and `China` only, groups by `country` and outputs new dataset **_`roc-prc-polity.csv`_**. 
#### Charting China and Taiwan government development towards democratic characteristics over time
Examines each country's development in different governance scores. I selected five specific variables: `democ` (institutionalized democracy indicator), `autoc` (institutionalized autocracy indicator), `polity` (overall regime authority score ranked on 21-pt scale, ranging from -10 hereditary monarchy to +10 consolidated democracy), `xcont` (executive constraints, measure of executive authority independence) and `xrcomp` (competitiveness of executive selection). Detailed definitions and methodology behind these variables is in `polity5manual.pdf`. 

Script generates charts for each country showing year-to-year changes as well as visualizations of how data could be interpreted to understand dimensions of democracy and autocracy. It also generates comparison line graphs layering Chinese and Taiwanese raw score trends on top of each other, using a year-trimmed version of the full dataset (Chinese data starts from 1800 whereas Taiwanese data begins in 1949, the year that the R.O.C. government evacuated mainland China and relocated to the island of Taiwan).
#### Charts and analysis
placeholder
### 6. compgraphs.py
----
_//VERY DRAFT_

This script combines data from the previous scripts and builds graphs layering different trends from different dataframes over each other.
#### Trim data for comparison down to matching time periods and add common merge keys
#### Charts and analysis
placeholder
## Summary/Conclusions
placeholder
## Sources
#### Taiwanese Election and Democratization Data:
- Taiwan's Election and Democratization Study (TEDS), Taiwan Ministry of Science and Technology: http://teds.nccu.edu.tw/main.php
- Taiwan National Security Study (SNSS), Election Study Center of Taiwan’s National Chengchi University (supported by Duke University Program in Asian Security Studies)
#### Governance Data
- Polity IV Scores for Individual Country Regime Trends, Center for Systemic Peace: http://www.systemicpeace.org/polityproject.html
#### Military Aid and Spending Data: 
- Taiwan: Major U.S. Arms Sales Since 1990, Shirley A. Kan/Congressional Research Service  
- Arms Sales, Security Assistance Spending and Foreign Military Training Databases, CIP Security Assistance Monitor: https://securityassistance.org
- Excess Defense Articles (EDA) Public Report, U.S. Department of State
- Section 655 U.S. Annual Military Assistance Reports, U.S. Defense Security Cooperation Agency (DSCA)
- U.S. Foreign Assistance Database, U.S. Department of State and USAID: http://foreignassistance.gov
- U.S. Overseas Loans and Grants Greenbook, USAID

_//need to attach sources to specific datasets_

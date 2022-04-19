# Comparing Trends in Taiwanese Election and Democratization Data and U.S.-to-Taiwan Military Aid
## Project Overview and Context
//////////////TBD, DRAFT 

'Cross-strait' relations between Taiwan (R.O.C.) and mainland China (P.R.C.) have always been deeply contentious. China maintains it has ownership over Taiwan as a "renegade province" and condemns any country which extends Taiwan official diplomatic recognition. Despite this, for many years the two have tenously coexisted as separately governed bodies without violent conflict, and Taiwan has operated with _de jure_ independence. In recent years, China has escalated its threats of forcible reunification while the Taiwanese government has made increasing efforts to strengthen its policy relationships with the U.S. 

The U.S. is statutorily committed to providing Taiwan with arms for its national defense under the Taiwan Relations Act of 1979, though they have refused to formally acknowledge Taiwan as a sovereign nation. Bills proposing additional, explicit military aid committments to Taiwan are currently active in the U.S. federal legislature (e.g. Taiwan Defense Act, Arm Taiwan Act of 2021). U.S. support of Taiwan can be viewed as part of an overall foreign policy mission to support democratic governance over authoritative regimes.

This project uses Taiwanese Election and Democratization polling data, public U.S. federal government military spending data and the third party Polity Score metric for assessing democratic/autocratic governance to explore the following questions:

- How have Taiwanese people's political attitudes towards **Cross-strait relations** (specifically, declaring indepence vs. pursuing reunification), **government handling of Cross-strait relations and national defense** and the **urgency of these issues** changed over time? 
- How has the amount of **U.S. military aid to Taiwan** changed over time, and how does this compare to the trends in Taiwanese political attitudes?
- _(may remove this component, data results are not looking helpful rn)_ Do these trends in Taiwanese political attitudes have any relation to the **development of Taiwanese democratic governance** and/or changes in its **diplomatic alliancess**? 

Note: Taiwan's primary (pretty much only) national defense concern is potential Chinese invasion, which is why it is closely associated to Cross-strait relations. It maintains a defensive military with questionability capability and limited active recruits despite mandatory conscription terms. The U.S. is Taiwan's primary source of military aid.

Summary of repository files in `README_CONTENTS.md`.
## Data 
//TBD, DRAFT NOTES
- Sources at end 
- Analysis runs 2012-2020 as these are the only years with complete election/democratization data collection begain in 2012 and not all military data had updates for 2021
- Not using TEDS microdata so will not be charting changes in individual attitudes over time, only national percentages
- No regression analysis or causal conclusions, they could run either way anyways
- More specific information for datasets and commands in scripts themselves
## Scripts
//TBD, DRAFT 
### 1. translate.py
----
#### Translating Chinese character data to English text
This script runs a replacement function through all datasets which will later be aggregated in script `teds.py`. I only coded translations for the data I planned to analyze as the rest will later be dropped.  

Reads Taiwanese-originating Election and Democratization datasets into variable `teds`. Renames Chinese character column headings to English text using positional commands `teds.columns.values[0] = 'Survey Wave Number', teds.columns.values[1] = 'Satisfaction Level'`, etc. For response values, creates dictionaries for each column using the predefined Chinese character answers as keys and English character names as values: (`approvallist = {'非常滿意':'vsatisfact', '非常不滿意':'nsatisfact'}`, etc.). Uses `.replace()` method with `{'Satisfaction Level':approvallist}` as argument and saves to `.csv` for later use.
### 2. aidmerge.py
----
#### Building merged dataframe summing different types of U.S. military aid each year
Tracks six major types of military aid that the U.S. has provided to Taiwan: major arms sales (`cip_arms_sales_by_year.csv`), security assistance spending (`cip_security_asst_by_year.csv`), foreign military training (`cipfmt.csv`), excess defense articles (`EDA_Public_Report.csv`), foreign assistance purposed for military assistance objectives (`foreignassistance.csv`) and overseas loans and grants for military spending (`us_foreign_greenbook.csv`, pulled from API). Original datasets are not consistently formatted, so filters out Taiwan-specific and purpose-specific data from global datasets, trims to analysis time period, and converts all year and spending values to `integers`. For datasets breaking down aid by individual allocations, codes equation to `sum()` all into single totals per year. Searches each dataset to ensure it contains values for all years in that time period, and if not creates and appends new data entry for each missing year with expenditures of $0. Merges these six cleaned datasets together under common key value `year` and saves into **_`aidmerge.csv`_**, a file listing the value of military foreign aid the U.S. provided to Taiwan each year, by type and overall (new `totalsum` column).

Notes:
- **This is likely not an exact accounting of U.S. military aid to Taiwan.** Assistance is issued by multiple federal agencies and can be vaguely classified and/or tangentially related; some transactions may be unavailable to the public. I could not find existing datasets collecting all this information in one place. I also suspect significant overlap between the `foreignassistance.csv` data and `us_foreign_greenbook.csv` entries, which cannot be addressed in simple code matching redundant values as Foreign Assistance lists loan/grant allocations by `Project Activity` while Greenbook data pre-aggregates them by `Funding Account Name` (data is collected by different agencies). However, though the amounts are questionable, I hope they capture enough of the largest foreign transactions so that their relationships to one another will sufficiently simulate actual trends in amount of spending. This is why the analysis focuses on changes over time.
- Because of differences between the amounts of U.S. foreign arms sales that are notified, approved and delivered, major arms sales is represented in three corresponding columns `Notified Sales`, `Approved Sales`, `Delivered Sales`. The `totalsum` of aid provided per year uses only the amount `Delivered Sales`. 
- Foreign military training and excess defense articles, which are 'in kind' forms of aid, had to be converted to representative monetary amounts; approximation values were pre-provided by the issuing bodies. 
#### Charts and analysis
Output line graph of total spending over time, **_`aidmerge.png`_**. Output charts of select types of spending over time, relative to each other, **_`aidtypes.png`_**.
### 3. teds.py 
----
#### Aggregating and cleaning election and democratization data
I selected four major questions from the "TEDS Telephone and Mobile Phone Interview Survey of the Presidential Satisfaction" which is conducted 4 times a year since 2012:
1. Considering the relationship between Taiwan and mainland China, which of the following six positions do you agree with: (1) Immediate unification; (2) Immediate independence; (3) Maintain status quo and move towards unification in future; (4) Maintain status quo and move towards independence in future; (5) Maintain status quo, decide on unification or independence in future; (6) Maintain status quo forever
2. How satisfied are you with the president's performance in national defense?
3. How satisfied are you with the president's performance in Cross-strait relations?
4. What is the highest priority concern the president should address (other than COVID-19, for the most recent years)?
5. _//There is also a 'how much do you trust the mainland Chinese government' scale question which might yield interesting analysis, may code weighted average for each survey if i have time_

Each survey "wave" is originally a separate datafile. I chose to use aggregate data with all points expressed as the percentage of total respondents holding each sentiment. This script goes through each file and filters out only the data for the three questions (including question number), converts percentages to integers and adds a `date` column containing the date of each survey as a string (originally formatted as `TEDS_{year}_PA{quarter}` - I cut trailing characters to only include `{year}_PA{quarter}` string). It then adds this to a dataframe which eventually contains total survey data from years 2012-2021 grouped by `question`. Data is reformatted into columns for each response value of Q1 (`unification`, `independence`, `squnification`, `sqindependence`, `sqidk`, `sqforever`), Q2 (`vsatisfact`, `satisfact`, `nsatisfact`, `nallsatisfact`, `noopinion`, `refuse` and `idk`) and Q3 (same as Q2), which are grouped by `question`: . With regard to Q4, the code extracts only the proportion of people who answered Cross-strait relations as their highest priority concern, inputs this number for the survey group as new column `xstrait` and drops the rest of the input answers for that question. Output as **_`tedssorted.csv`_**.
#### Charts and analysis
TBD, have to convert data to chartable values / `matplotlib.pyplot`, `int()`
### 4. dippol.py
----
**_//DRAFT - WILL PROBABLY REMOVE_**. _planned script output would not be chartable against other data so can only function as dictionary of snapshot of distribution per year, probably getting too tangential + also it hurts my brain_

This script reads data from `dipreg.csv`, a table of all countries, their diplomatic relationship status with Taiwan and their Polity Scores in each year. Polity Scores are a third-party metric examining "concomitant qualities of democratic and autocratic authority in governing institutions" and ranks regime authority on a 21-point scale ranging from -10 (hereditary monarchy) to +10 (consolidated democracy). 
### 5. compgraphs.py
----
//TBD, VERY DRAFT
This script combines data from the previous scripts and builds graphs layering different data trends over each other.
#### Parsing total military aid data to standardize units of time (x-axis variable)
As the TEDS data is collected quarterly while military aid statistics are yearly, grouped aid data in the merged aid dataframe needs to be split so each year in dataset would instead be represented by four rows containing identical spending values but with `year` values renamed to correspond with the TEDS data increments (e.g. from `2013` to `2013_PA03`, `2013_PA06`, `2013_PA09` and `2013_PA12`). Plan to do this with function which appends quarter names to year, adds to new dataframe and repeats for each quarter. Then can run same commands as previous script which converts to matching chartable values. 
_//is there an easier way to do this?? probably_
#### Comparing TEDS data to U.S. military aid
Output graph(s)
#### Comparing TEDS data to Taiwan's democracy ratings
_//prob won't run this analysis_ Output graph
## Summary/Conclusions
TBD
## Data and Sources
#### Taiwanese Election and Democratization Data:
- Taiwan's Election and Democratization Study (TEDS), Taiwan Ministry of Science and Technology: http://teds.nccu.edu.tw/main.php
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

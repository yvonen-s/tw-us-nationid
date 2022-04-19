# Comparing Trends in Taiwanese Election and Democratization Data and U.S.-to-Taiwan Military Aid
## Project Overview 
//TBA, DRAFT

'Cross-strait' relations between Taiwan (R.O.C.) and mainland China (P.R.C.) have always been deeply contentious. China maintains it has ownership over Taiwan as a "renegade province" and condemns any country which extends Taiwan official diplomatic recognition. Despite this, for many years the two have tenously coexisted as separately governed bodies without violent conflict, and Taiwan has operated with _de jure_ independence. In recent years, China has escalated its threats of forcible reunification while the Taiwanese government has made increasing efforts to strengthen its policy relationships with the U.S. 

The U.S. is statutorily committed to providing Taiwan with arms for its national defense under the Taiwan Relations Act of 1979, though they have refused to formally acknowledge Taiwan as a sovereign nation. Bills proposing additional, explicit military aid committments to Taiwan are currently active in the U.S. federal legislature (e.g. Taiwan Defense Act, Arm Taiwan Act of 2021). U.S. support of Taiwan can be viewed as part of an overall foreign policy mission to support democratic governance over authoritative regimes.

This project uses Taiwanese Election and Democratization polling data, public U.S. federal government military spending data and the third party Polity Score metric for assessing democratic/autocratic governance to explore the following questions:

- How have Taiwanese people's political attitudes towards **Cross-strait relations** (specifically, declaring indepence vs. pursuing reunification), **government handling of national defense** and the **urgency of these issues** changed over time? Are there any relationships between the three?
- How has the amount of **U.S. military aid to Taiwan** changed over time, and how does this compare to the trends in Taiwanese political attitudes?
- Do these trends in Taiwanese political attitudes have any relation to the **development of Taiwanese democratic governance**?
- (_almost definitely dropping this one_) Considering Taiwan's public perception as a symbol of democracy, what are the governance qualities of its few diplomatic allies?

Note: Taiwan's primary (pretty much only) national defense concern is potential attack from China, which is why it is linked to Cross-strait relations. It maintains a defensive military. The U.S. is Taiwan's primary source of military aid.
## Data 
//TBA, DRAFT 
- Sources at the end 
- Analysis runs 2012-2021 as these are the only years with complete election/democratization data
- Might remove polity score data altogether if results are unhelpful (it's looking like they might be)
## Scripts
//TBA, DRAFT 
### 1. translate.py
----
#### Translating Chinese character data to English text
Used `PANDAS` to read Taiwanese-originating Election and Democratization dataset into variable `teds`. I only coded translations for the questions I planned to analyze as the rest of the data will be dropped.

First renamed Chinese character column headings to English text using positional commands `teds.columns.values[0] = 'Survey Wave Number', teds.columns.values[1] = 'Satisfaction Level'`, etc. For response values, created dictionaries for each column using the predefined Chinese character answers as keys and English character names as values: (`approvallist = {'非常滿意':'vsatisfact', '非常不滿意':'nsatisfact'}`, etc.). Used `.replace()` method with `{'Satisfaction Level':approvallist}` as argument and saved to _`teds.csv`_ for use in later scripts.
### 2. aidmerge.py
----
#### Building merged dataframe summing different types of U.S. military aid 
Tracked six major types of military aid that the U.S. has provided to Taiwan: major arms sales (`majorsalessince1990.csv`, `ciparmssales.csv`), security assistance spending (`cipsas.csv`), foreign military training (`cipfmt.csv`), excess defense articles (`EDA_Public_Report.csv`), foreign assistance purposed for conflict, peace and security (`foreignassistance.csv`) and overseas loans and grants for military spending (`us_foreign_greenbook.csv`). Datasets were not consistently formatted, so filtered out Taiwan-specific and purpose-specific data from global datasets and trimmed to time period for analysis. Coded equation to sum all individual allocations into single totals per year. Merged these six cleaned datasets together under common key value `year` and saved into _`aidmerge.csv`_, a file listing the amount of military aid the U.S. provided to Taiwan each year, by type and overall (new `totalsum` column). Checked to ensure all tracked years contain data for all six types of aid (expenditures of $0 are not missing values).

Notes: Because there are differences between the amounts of U.S. foreign arms sales that are notified, approved and delivered, major arms sales is represented in three corresponding columns `Notified Sales`, `Approved Sales`, `Delivered Sales`. However, the equation for `totalsum` of aid provided per year uses only the amount `Delivered Sales`. Foreign military training and excess defense articles, which are 'in kind' forms of aid, had to be converted to representative monetary amounts; approximation rates were provided by the issuing bodies. 
#### Charts and analysis
Output line graph of total spending over time, _`aidmerge.png`_. Output clustered bar chart of types of spending over time, _`aidtypes.png`_.
### 3. teds.py 
----
#### Aggregating and cleaning election and democratization data
I selected three major questions from the "TEDS Telephone and Mobile Phone Interview Survey of the Presidential Satisfaction", which is conducted 4 times a year since 2012:
1. Considering the relationship between Taiwan and mainland China, which of the following six positions do you agree with: (1) Immediate unification; (2) Immediate independence; (3) Maintain status quo and move towards unification in future; (4) Maintain status quo and move towards independence in future; (5) Maintain status quo, decide on unification or independence in future; (6) Maintain status quo forever
2. How satisfied are you with the president's performance in national defense?
3. What is the highest priority concern the president should address (other than COVID-19, for the most recent years)?

This script aggregates total survey data from years 2012-2021, filters out only the data for those three questions and arranges it into a dataframe grouped by survey year/quarter and expressing all data points as the percentage of total respondents who held each sentiment. The dataframe contains columns for all values of Q1 and Q2, named `unification`, `independence`, `squnification`, `sqindependence`, `sqidk`, `sqforever`, `vsatisfact`, `satisfact`, `nsatisfact`, `nallsatisfact`, `noopinion`, `refuse` and `idk`. (This information was translated from Chinese in the first script.) With regard to Q3, the code filters out only the proportion of people who chose Cross-strait relations as the highest priority concern, inputs it as column `xstrait` and drops the rest of the input values for that question. Output as _`tedssorted.csv`_.
#### Charts and analysis
TBA
### 4. dippol.py
----
//DRAFT - MAY REMOVE ENTIRELY, DIDN'T ACTUALLY WRITE THIS SCRIPT YET

This script reads data from `dipreg.csv`, a table of all countries, their diplomatic relationship status with Taiwan and their Polity Scores in each year. Polity Scores are a third-party metric examining "concomitant qualities of democratic and autocratic authority in governing institutions" and ranks regime authority on a 21-point scale ranging from -10 (hereditary monarchy) to +10 (consolidated democracy). 
#### Creating table of Taiwan's democracy ratings
Filtered out Taiwan's polity score for each year and saved this brief table as _`taiwanpol.csv`_.
#### (Charting Taiwan's diplomatic relationships and governance qualities of those allies)
Cleaned data into two dictionaries: `allies` paired each year (key) with a list of all countries that did have official diplomatic relations with Taiwan at that time, and `polity` paired each country (key) with a second dictionary using years as keys and polity scores as values. From this, the script translated each year into a list of all polity scores associated with Taiwan's diplomatic partners and output one dataframe grouping country lists and polity score lists by year. I added column `totalnum` summing the number of diplomatic allies (number of values in either list) per year. Saved into _`dippol.csv`_.
#### Charts and analysis
Output line graph _`taiwanpol.png`_, a visualization of how 'democratic' Taiwan's government has been over time. Output line graph _`allies.png`_, a chart of `totalnum` over `year`. Output chart set _`ex1.png`_, a yearly snapshot of the types of regimes that chose to recognize Taiwan each year based on their polity scores. more detail TBA
### 5. compgraphs.py
----
//TBA, VERY DRAFT

This script combines data from the previous scripts and builds graphs with `PANDAS` layering different data trends over each other.

#### Parsing total military aid data to standardize units of time (x-axis variable)
//is there an easier way to do this??

As the TEDS data is collected quarterly while military aid statistics are yearly, grouped aid data in the merged aid dataframe needed to be split so each year in dataset would instead be represented by four rows containing identical spending values but with `year` values renamed to correspond with the TEDS data increments (e.g. from `2013` to `2013Q1`, `2013Q2`, `2013Q3` and `2013Q4`).

To do this, first simplified aid data by trimming to `year` and `totalsum` columns only. Created empty dataframe `quarterlyaidmerge`. Used `for` loop adding `Q1` to the end of every year name and appending full rows to `quarterlyaidmerge` (without saving changes to original data). Repeated three more times, adding `Q2`, `Q3` and `Q4`. Used `quarterlyaidmerge` in subsequent graph comparisons.
`
#### Comparing TEDS data to U.S. military aid
Output _`placeholder.png`_.
#### Comparing TEDS data to Taiwan's democracy ratings
Output _`moreplaceholder.png`_.
## Summary/Conclusions
//TBA, VERY DRAFT 
#### Output charts and graphs
TBA

Causal relationships could go either way
#### Notes on military aid dataframe
Merged data generated in _`aidmerge.py`_ should not be considered an exact accounting of U.S. military aid to Taiwan. It is a piecemeal combination of all the major transactions I could identify, but there are other types of military aid that are unavailable to the public, vaguely classified, tangentially related and/or originating in other unexpected U.S. federal agencies (thus easily overlooked). Hopefully these data points still hold a roughly similar relationship to each other year-over-year, so this comparison of relative data _trends_ is more meaningful than their stated values. 
#### Comparing diplomatic relationship trends to other data???
My attempts to compare partners' Polity Scores with Taiwan's election and democratization sentiments were extremely shallow. There are too many major geopolitical factors which have affected countries' diplomatic recognition of Taiwan that are not explored in this analysis, and formal diplomatic alliances are not of much concern to the average voter. Comparing this data to U.S. foreign aid is also unlikely to be meaningful as it cannot be condensed into simple annual values (averaging scores would be nonsensical, histograms would be uninformative). So, I didn't do anything else with this data.
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

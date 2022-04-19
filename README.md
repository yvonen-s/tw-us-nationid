# Comparing Trends in Taiwanese Election and Democratization Data with U.S.-to-Taiwan Military Aid
## Project Overview 
TBA
## Data 
Taiwanese Election and Democratization data tracking voter opinions 

Military aid and spending data 

All sources listed at end of readme file
## Scripts
### 1. aidmerge.py
----
#### Building merged dataframe summing different types of U.S. military aid 
Tracked six major types of military aid that the U.S. has provided to Taiwan: major arms sales (`majorsalessince1990.csv`, `ciparmssales.csv`), security assistance spending (`cipsas.csv`), foreign military training (`cipfmt.csv`), excess defense articles (`eda.csv`), foreign assistance purposed for conflict, peace and security (`foreignassistance.csv`) and overseas loans and grants for military spending (`greenbook.csv`). Datasets were not consistently formatted, so filtered out Taiwan-specific and purpose-specific data from global datasets and used code to sum individual allocations into single totals per year. (Foreign military training and excess defense articles, which are forms of 'in kind' aid, had to be converted to representative monetary amounts; approximation rates were provided by the issuing bodies. Major arms sales were defined by values of sales _delivered_, not amounts notified or authorized.) Merged these six cleaned datasets together to _`aidmerge.csv`_ listing the amount of military aid the U.S. provided to Taiwan each year, by type and overall. All years which did not contain data for all six types of aid (expenditures of $0 are not missing values) were dropped.
#### Calculating U.S. arms sales as % of Taiwan national spending
Read in Taiwanese yearly national budget data (`twbudget.csv`) and filtered out their total `military spending`. Focusing on `major arms sales` only, created a new column in merged dataset calculating Taiwan's expenditures on U.S. arms each year as a percentage of total military spending. Because the Taiwanese fiscal year begins on January 1 and the U.S. fiscal year begins on October 1 of the previous year, it was easier to return to original major arms sales data, which contained both month and year that sales were made necessary to return to the original major arms sales data which contained both month and year that major arms sales were approved by the U.S.

taken from the Taiwanese yearly Because the Taiwanese fiscal Using Taiwanese yearly national budget data (`twbudget.csv`), pulled values of `major arms sales` and created a new column for Taiwan's expenditures on U.S. arms sales each year as a percentage of their total military spending. Because the Taiwanese fiscal year begins on January 1 and the U.S. fiscal year begins on October 1 of the previous year, Due to timing differences between when sales were approved by the U.S. and when those payments were recorded in the Taiwanese goverment budget, had to ensure years matched 
### 2. teds.py 
----
#### Aggregating and filtering election and democratization data for voter national identification and foreign policy positions
#### Standardizing government approval data across data collected during different government administrations
#### Charting annual changes in diplomatic recognition and charting polity score distributions of diplomatic partners
#### Combining voter position, government approval and polity score data into one database
Output as `teds.csv`
### 3. graphs.py
----
#### Graphing and comparing trends in both databases year-by-year
Outputs `placeholder.png`

## Summary

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
#### Shapefiles:

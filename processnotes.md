## dippol.py script outline
Didn't actually write this yet but this is how I originally imagined it would work

Research question: Considering Taiwan's public perception as a symbol of democracy, what are the governance qualities of its few diplomatic allies?

Polity Scores are a third-party metric examining "concomitant qualities of democratic and autocratic authority in governing institutions" and ranks regime authority on a 21-point scale ranging from -10 (hereditary monarchy) to +10 (consolidated democracy). 
#### Creating table of Taiwan's democracy ratings
Filtered out Taiwan's polity score for each year and saved this brief table as _`taiwanpol.csv`_.
#### (Charting Taiwan's diplomatic relationships and governance qualities of those allies)
Cleaned data into two dictionaries: `allies` paired each year (key) with a list of all countries that did have official diplomatic relations with Taiwan at that time, and `polity` paired each country (key) with a second dictionary using years as keys and polity scores as values. From this, the script runds function `readlist` translating each year into a list of all polity scores associated with Taiwan's diplomatic partners and output one dataframe grouping country lists and polity score lists by year. I added column `totalnum` summing the number of diplomatic allies (number of values in either list) per year. Saved into _`dippol.csv`_.
#### Charts and analysis
Output line graph _`taiwanpol.png`_, a visualization of how 'democratic' Taiwan's government has been over time. Output line graph _`allies.png`_, a chart of `totalnum` over `year`. Output chart set _`ex1.png`_, a yearly snapshot of the types of regimes that chose to recognize Taiwan each year based on their polity scores. more detail TBA
#### Comparing diplomatic relationship trends to other data???
My attempts to compare partners' Polity Scores with Taiwan's election and democratization sentiments were extremely shallow. There are too many major geopolitical factors which have affected countries' diplomatic recognition of Taiwan that are not explored in this analysis, and formal diplomatic alliances are not of much concern to the average voter. Comparing this data to U.S. foreign aid is also unlikely to be meaningful as it cannot be condensed into simple annual values (averaging scores would be nonsensical, histograms would be uninformative). So, I didn't do anything else with this data.


----
## Process Notes
- There are many other interesting questions in the TEDS data which I considered examining, including a measure of national identitification and degree of overall engagement in politics and the democratic process. For manageability, I chose questions that seemed most directly applicable to Cross-strait military concerns.
- Would also have been interesting to compare data to actual actions taken by Taiwanese government, or actions taken by mainland Chinese government, but this is not easy data to find or create. Same applies to attitudes of U.S. citizens regarding U.S. aid to Taiwan.
- Originally wanted to calculate major arms sales as a percentage of Taiwanese military spending, but because the Taiwanese fiscal year begins on January 1 and the U.S. fiscal year begins on October 1 of the previous year, and the U.S. data does not specify specific months where sales were made, it became too complicated to sort and synchronize with Taiwanese government budget data.

----
#### Parsing military data for charting

Simplify aid data by trimming to `year` and `totalsum` columns only. Created empty dataframe `quarterlyaidmerge`. Used `for` loop adding `Q1` to the end of every year name and appending full rows to `quarterlyaidmerge` (without saving changes to original data). Repeated three more times, adding `Q2`, `Q3` and `Q4`. Used `quarterlyaidmerge` in subsequent graph comparisons.


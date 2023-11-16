# Introduction

Imagine you are training a machine learning model on your company server, and unexpectedly, the power goes off! Now, the model that you have spent the whole afternoon training on is gone. You realize that you have lost your chance of getting a promotion this year. As data scientist whose life depends on the supply of electricity, we are curious about the driving factors that cause major power outages with high severity.

This project will investigate the relationship between economic performance, measured by Gross State Product(GSP), and the severity of major power outages, measured by outage duration in minutes in the United States from January 2000 to July 2016. The major outages are defined by the Department of Energy as those affecting at least 50,000 customers or causing an unplanned firm load loss of at least 300 MW. (create citation).  Each row in the dataset describes a specific event of a major power outage. There are in total 56 columns and separated into 7 major categories. They are Time, Geographical information, causes, consumption, customer population, economic outcome, and land use characteristics. With our purpose in discovering the weight of economic factors that contributes to major power outages, the columns that we will be analyzing in depth on are specifically the columns named: ```OUTAGE.DURATION```, ```PC.REALGSP.REL```, and ```UTIL.REALGSP. CONTRI.PROP```. In ```OUTAGE.DURATION```, the data are in the format of floats containing the duration of the outage in minutes. ```PC.REALGSP.REL```, is the relative per captia of GSP compared to total per capital real GDP of the US. ```UTIL.REALGSP.CONTRI.PROP```, this column is produced in similar format to the column from above taking the relative per captia of GSP for industry related contributions over the total real GSP. This step is performed later during the Cleanign and EDA phase. 

Considering the fact that industrial customers require the highest amount of energy and their output can be directly represented by the states' GSP statistic. A robust GSP statistic may indicate a state's capacity to invest in and maintain critical infrastructure, potentially reducing the severity and duration of power outages. Conversely, states with weaker economic performance may experience longer outage times due to less well-made infrastructure.

Therefore, the central question for this study is: whether a state's GSP is a significant factor that contributes to the severity of power outages. This analysis will be vital to multiple parties, including policymakers and utility companies. The findings of the project will act as strong evidence for them to impose strategic investments in states' infrastructure to contribute to the propersity of the states. However, as the project progresses, the guiding question may evolve in response to emerging data patterns. For example, we might specifically focus on the proportion of GSP contributions made solely by the utility industries of each state.

# Cleaning and EDA
The initial data came in the style of Excel(.xlsx), with descriptions for columns. When directly reading in the excel file it returns a disorted dataframe with unescessay columns. Pre-cleaning was done on the initial file and the dataset was saved in 'outageClean.csv' as a csv file. This dataset was read in as powerOutage afterwards. 
TODO: can this precleaning be done this way? 

When observing the dataset we realized some redudancy in columns that can be combined. Such as, the start date and start time of the outage duration can be combined as one time marker including both the date and time. This step is also done with the restoration day and time. Column that are involved are later droped from the orginal dataframe. Next, in understanding the question, we wanted to work with a smaller dataframe instead as we dropped all other unecssary columns keeping only the general information, GSP, and ```OUTAGE.DURATION ```. 
#TODO Step 3, should not be included in the pipe line but rather an manipulation check later in the progress. 

However, the information given is not enough to do cross comparsion between columns. Which we assigned a new column ```UTIL.REALGSP.CONTRI.PROP```, this column contains the utility industries contibution to total GSP in relation to the state's over all GSP. It is computed with ```UTIL.REALGSP``` divided by ```TOTAL.REALGSP```. 

All the manipulation of the dataframe is written in as functions and later piped on to the orginal dataframe. 

## Univariate Analysis
The main focuse point of the project is to analysis the weight of different factor's contribution on outage duration. A histogram is generated from the ```OUTAGE.DURATION``` column to observe the density of outage duration's overall spread.
 
<iframe src="assets/hist-duration.html" width=800 height=600 frameBorder=0></iframe>

# Assessment of Missingness

# Hypothesis Testing
# Power Outage Analysis with States Economic Performance
Qirui Zheng, Guoxuan Xu

# Introduction
Imagine you are training a machine learning model on your company server, and unexpectedly, the power goes off! Now, the model that you have spent the whole afternoon training on is gone. You realize that you have lost your chance of getting a promotion this year. As data scientist whose life depends on the supply of electricity, we are curious about the driving factors that cause major power outages with high severity.

This project will investigate the relationship between economic performance, measured by Gross State Product(GSP), and the severity of major power outages, measured by outage duration in minutes in the United States from January 2000 to July 2016. The major outages are defined by the Department of Energy as those affecting at least 50,000 customers or causing an unplanned firm load loss of at least 300 MW. (Mukherjee et al. 2018).  Each row in the dataset describes a specific event of a major power outage. There are in total 56 columns and separated into 7 major categories. They are Time, Geographical information, causes, consumption, customer population, economic outcome, and land use characteristics. With our purpose in discovering the weight of economic factors that contributes to major power outages, the columns that we will be analyzing in depth on are specifically the columns named: `OUTAGE.DURATION`, `PC.REALGSP.REL`, and `UTIL.REALGSP. CONTRI.PROP`. In `OUTAGE.DURATION`, the data are in the format of floats containing the duration of the outage in minutes. `PC.REALGSP.REL`, is the relative per captia of GSP compared to total per capital real GDP of the US. `UTIL.REALGSP.CONTRI.PROP`, this column is produced in similar format to the column from above taking the relative per captia of GSP for industry related contributions over the total real GSP. This step is performed later during the Cleanign and EDA phase. 

Considering the fact that industrial customers require the highest amount of energy and their output can be directly represented by the states' GSP statistic. A robust GSP statistic may indicate a state's capacity to invest in and maintain critical infrastructure, potentially reducing the severity and duration of power outages. Conversely, states with weaker economic performance may experience longer outage times due to less well-made infrastructure.

Therefore, the central question for this study is: whether a state's GSP is a significant factor that contributes to the severity of power outages. This analysis will be vital to multiple parties, including policymakers and utility companies. The findings of the project will act as strong evidence for them to impose strategic investments in states' infrastructure to contribute to the propersity of the states. However, as the project progresses, the guiding question may evolve in response to emerging data patterns. For example, we might specifically focus on the proportion of GSP contributions made solely by the utility industries of each state.

# Cleaning and EDA
The initial data came in the style of Excel(.xlsx), with descriptions for columns. When directly reading in the excel file it returns a disorted dataframe with unescessay columns. Pre-cleaning was done on the initial file and the dataset was saved in 'outageClean.csv' as a csv file. This dataset was read in as powerOutage afterwards. 
TODO: can this precleaning be done this way? 

When observing the dataset we realized some redudancy in columns that can be combined. Such as, the start date and start time of the outage duration can be combined as one time marker including both the date and time. This step is also done with the restoration day and time. Column that are involved are later droped from the orginal dataframe. Next, in understanding the question, we wanted to work with a smaller dataframe instead as we dropped all other unecssary columns keeping only the general information, GSP, and `OUTAGE.DURATION `. 
#TODO Step 3, should not be included in the pipe line but rather an manipulation check later in the progress. 

However, the information given is not enough to do cross comparsion between columns. Which we assigned a new column `UTIL.REALGSP.CONTRI.PROP`, this column contains the utility industries contibution to total GSP in relation to the state's over all GSP. It is computed with `UTIL.REALGSP` divided by `TOTAL.REALGSP`. 

All the manipulation of the dataframe is written in as functions and later piped on to the orginal dataframe. 

Below is the cleaned dataset's first 5 rows 

|    |   OBS |   YEAR |   MONTH | U.S._STATE   | POSTAL.CODE   | NERC.REGION   |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   PC.REALGSP.STATE |   PC.REALGSP.USA |   PC.REALGSP.REL |   PC.REALGSP.CHANGE |   UTIL.REALGSP |   TOTAL.REALGSP |   UTIL.CONTRI |   PI.UTIL.OFUSA |   POPULATION |   POPPCT_URBAN |   POPPCT_UC |   POPDEN_URBAN |   POPDEN_UC |   POPDEN_RURAL | OUTAGE.START        | OUTAGE.RESTORATION   |   UTIL.REALGSP.CONTRI.PROP |
|---:|------:|-------:|--------:|:-------------|:--------------|:--------------|------------------:|-----------------:|---------------------:|-------------------:|-----------------:|-----------------:|--------------------:|---------------:|----------------:|--------------:|----------------:|-------------:|---------------:|------------:|---------------:|------------:|---------------:|:--------------------|:---------------------|---------------------------:|
|  0 |     1 |   2011 |       7 | Minnesota    | MN            | MRO           |              3060 |              nan |                70000 |              51268 |            47586 |          1.07738 |                 1.6 |           4802 |          274182 |       1.75139 |             2.2 |      5348119 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 | 2011-07-01 05:00:00 | 2011-07-03 08:00:00  |                  0.0175139 |
|  1 |     2 |   2014 |       5 | Minnesota    | MN            | MRO           |                 1 |              nan |                  nan |              53499 |            49091 |          1.08979 |                 1.9 |           5226 |          291955 |       1.79    |             2.2 |      5457125 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 | 2014-05-11 06:38:00 | 2014-05-11 06:39:00  |                  0.0179    |
|  2 |     3 |   2010 |      10 | Minnesota    | MN            | MRO           |              3000 |              nan |                70000 |              50447 |            47287 |          1.06683 |                 2.7 |           4571 |          267895 |       1.70627 |             2.1 |      5310903 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 | 2010-10-26 08:00:00 | 2010-10-28 10:00:00  |                  0.0170627 |
|  3 |     4 |   2012 |       6 | Minnesota    | MN            | MRO           |              2550 |              nan |                68200 |              51598 |            48156 |          1.07148 |                 0.6 |           5364 |          277627 |       1.93209 |             2.2 |      5380443 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 | 2012-06-19 04:30:00 | 2012-06-20 11:00:00  |                  0.0193209 |
|  4 |     5 |   2015 |       7 | Minnesota    | MN            | MRO           |              1740 |              250 |               250000 |              54431 |            49844 |          1.09203 |                 1.7 |           4873 |          292023 |       1.6687  |             2.2 |      5489594 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |                  0.016687  |

## Univariate Analysis
The main focuse point of the project is to analysis the weight of different factor's contribution on outage duration. A histogram is generated from the `OUTAGE.DURATION` column to observe the density of outage duration's overall spread.

<iframe src="assets/hist-duration.html" width=800 height=600 frameBorder=0></iframe>

It is a right skewed graph, so that means majority power outage is within 20,000 mins. The tail of the histogram drag till 100k,this resembles the presense of outliers. Thus, when considering a valid aggration method, using the **median** will be more accurate in measuring the central tendency of the outage duration.

## Bivariate Analysis

Tracking back to the question, we wanted to observe which factor weighted the most in a sever power outage. First, it is important to see the trend of outage duration over the past years. 

<iframe src="assets/duration-year.html" width=800 height=600 frameBorder=0></iframe>

There is a decreasing trend on the utility GSP contribution over times. It correponds to the previous line plot on the year vs. outage time duration. This implies that as the times go both outage time duration and relative GSP constribution of utility industry decrease. 

we wanted to observe the weight of economic factors on the severity of the power outage. A bivariate graph is created to analysis the trend in utility industies' GSP against years. 

<iframe src="assets/GSP-relation-to-duration.html" width=800 height=600 frameBorder=0></iframe>

Apperantly, there is a decreasing trend on the utility GSP contribution over time. It correponds to the previous line plot on the year vs. outage time duration. This implies that as the times go both outage time duration and relative GSP constribution of utility industry decrease 

## Pivot Table 
To look more into the trend of the power outages relationship to economic standards, we assigned high and low labels on GSP accourding to the mean GSP. 

| stateGSPaboveAvg   |   False |   True |
|:-------------------|--------:|-------:|
| False              |   272   |   1440 |
| True               |   369.5 |    870 |

Base on the table above, suprisingly we can make new modification on our question. It seems like the state with low relative utility GSP has the shortest power outage duration. In other words, the state with high relative untility GSP will cause a more sever power outage base on the duration. From this result, we hypothesis that the states with large relative GSP utility are more likely to have large utility secotor. If they experience power outage, it **might** takes the large utility sectors more time to restore the outage. However, we need more domain knowledge to assest the claim.

This then guides our question in: whether the states' relative utility GSP is one of the leading factors that impact the severity of the power outage?

# Assessment of Missingness

This dataset is collected from multiple sources and when merging the data together there is a possiblity of unconsistence. If possible, we would like to get access to all the sub datasets that are used to generate this dataset to assess the missingness better. There are 5 sub datasets that all sorced from governemnt agencies, it would also be helpful to compare the information with other sources. Since all the current data are less likley to contain any biases when collected by governement agencies, if compared to other sources we may be able to draw significant patterns accounding to the missingness of values. 

We think that there is column whoes missingness is __NMAR__. If we are able to obtain more data such as the total cost behind the power outage, we might be able to asscess the missingness of the other columns that exist in the dataset due to it's relationship. 
TODO: Check if we need to specify a certain column

## Missingness Dependency
Guided by our question, more infomation is needed for the `OUTAGE.DURATION` column so that we will be able to use it in detailed later during hypothese testing. 

### Relationship between `POPULATION` and `OUTAGE.DURATION`
Intuitively, we hypothesis that the missingness of power outage duration might depends on the populations. The population decide the amount of people that will be impacted by the power outage. If population of the state is relative lower, then less people will be impacted by the power outage. Therefore, they are more likely not to record the outage duration. 

Before running permutation test, we want to decide the test statistic that will be used to measure the difference between two distributions. If the distributions are similar to each other, we can use the absolute mean difference. If the distribute has different shapes, we can use Kolmogorov Smirnov Test statistic (KS test) which measures the difference between graphs'cumulative distribution.

<iframe src="assets/missingDurationPopulation.html" width=800 height=600 frameBorder=0></iframe>

The shape of two distributions look similar to each other, so we can use absolute mean difference to measure the similarity between two distributions. We will use the abstrct function `abs_diff_p_val` we defined previously to caluclate the p value

<iframe src="assets/empricalPopulationKS.html" width=800 height=600 frameBorder=0></iframe>

# Hypothesis Testing
<iframe src="assets/durationGSP.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/hypTestKS.html" width=800 height=600 frameBorder=0></iframe>
# References
Mukherjee, S., Nateghi, R., & Hastak, M. (2018). Data on major power outage events in the continental U.S. Data in Brief, 19, 2079–2083. https://doi.org/10.1016/j.dib.2018.06.067
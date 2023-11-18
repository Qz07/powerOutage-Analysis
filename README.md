# Power Outage Analysis with States Economic Performance
By: Guoxuan Xu, Qirui Zheng

# Introduction
Imagine you are training a machine learning model on your company server, and unexpectedly, the power goes off! Now, the model that you have spent the whole afternoon training on is gone. You realize that you have lost your chance of getting a promotion this year. As data scientist whose life depends on the supply of electricity, we are curious about the driving factors that cause major power outages with high severity.

This project will investigate the relationship between state utility industries' economic performance, measured by its Gross State Product(GSP), and the severity of major power outages, measured by outage duration in minutes in the United States from January 2000 to July 2016. The major outages are defined by the Department of Energy as those affecting at least 50,000 customers or causing an unplanned firm load loss of at least 300 MW. (Mukherjee et al. 2018).  There are 1534 rows in the Data set and each row in the dataset describes a specific event of a major power outage. There are also in total 56 columns and separated into 7 major categories. They are Time, Geographical information, causes, consumption, customer population, economic outcome, and land use characteristics. With our purpose in discovering the weight of economic factors that contributes to major power outages, the columns that we will be analyzing in depth on are specifically the columns named: `OUTAGE.DURATION`, `PC.REALGSP.REL`, `UTIL.REALGSP`, and `TOTAL.REALGSP`. In `OUTAGE.DURATION`, the data are in the format of floats containing the duration of the outage in minutes. `PC.REALGSP.REL`, is the relative per captia of GSP compared to average per capital real GSP of all US states each year. `UTIL.REALGSP`, and `TOTAL.REALGSP` will combined into `UTIL.REALGSP.CONTRI.PROP`, this column is produced in similar format to the column from above taking the relative per captia of GSP for utility industry related contributions over the total real GSP each year. This step is performed later during the data cleaning and EDA phase. 

Considering the fact that industrial customers require the highest amount of energy and their output can be directly represented by the states' GSP statistic. A robust GSP statistic may indicate a state's capacity to invest in and maintain critical infrastructure, potentially reducing the severity and duration of power outages. Conversely, states with weaker economic performance may experience longer outage times due to less well-made infrastructure.

Therefore, the central question for this study is: __whether a state utility industries' GSP is a significant factor that contributes to the severity of power outages__. This analysis will be vital to multiple parties, including policymakers and utility companies. The findings of the project will act as strong evidence for them to impose strategic investments in states' infrastructure to contribute to the propersity of the states. Our original question only considered the impact of overall GSP, however, as the project progresses, the guiding question evolved in response to emerging data patterns. And intuitively, it makes more sense to look at the GSP of utility industries because they are the main party that regulate the electrity supply.

# Cleaning and EDA
The initial data came in the style of Excel(.xlsx), with descriptions for columns. When directly reading in the excel file it returns a disorted dataframe with unescessay columns. Pre-cleaning was done on the initial file by manually deleted information that is outside of the data set and the data set was saved in 'outageClean.csv' as a csv file. This dataset was read in as `powerOutage` afterwards.

When observing the dataset we realized some redudancy in columns that can be combined. Such as, the start date and start time of the outage duration can be combined as one time marker including both the date and time. This step is also done with the restoration day and time. Column that are involved are later droped from the orginal dataframe. 
```
def clean_time(df, col1, col2, name)

Parameter: 
df: dataframe being cleaned on
col1: the name of the column containinng dates str
col2: the name of the column contaning timestamps of the day
name: The new name for the column str

```

Next, in understanding the question, we wanted to work with a smaller dataframe instead as we dropped all other unecssary columns keeping only the general information, GSP, and `OUTAGE.DURATION `. 
```
def filter_columns(df, *dropRanges)

Parameter: 
df: dataframe being cleaned on
*dropRanges: the range of columns that can be droped on
```

However, the information given is not enough to do cross comparsion between columns. Which we assigned a new column `UTIL.REALGSP.CONTRI.PROP`, this column contains the utility industries contibution to total GSP in relation to the state's over all GSP. It is computed with `UTIL.REALGSP` divided by `TOTAL.REALGSP`. 
```
def add_util_prop(df)

Parameter:
df: dataframe being cleaned on
```

Furthermore, we also categorize the state GSP and utility GSP into two groups by comparing them with their yearly average. We defined GSP that is higher than average as high GSP and GSP that is lower than the average low GSP. We categorize `PC.REALGSP.REL` by defining every number that is higher than 1, high GSP and the rest low GSP. We categorize `UTIL.REALGSP.CONTRI.PROP` by the average `UTIL.REALGSP.CONTRI.PROP` each year.

All the manipulation of the dataframe is written in as functions and later piped on to the orginal dataframe. 

Below is the cleaned dataset's first 5 rows 

|    |   OBS |   YEAR |   MONTH |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   RES.PRICE |   COM.PRICE |   IND.PRICE |   TOTAL.PRICE |   RES.SALES |   COM.SALES |   IND.SALES |   TOTAL.SALES |   RES.PERCEN |   COM.PERCEN |   IND.PERCEN |   PC.REALGSP.STATE |   PC.REALGSP.USA |   PC.REALGSP.REL |   PC.REALGSP.CHANGE |   UTIL.REALGSP |   TOTAL.REALGSP |   UTIL.CONTRI |   PI.UTIL.OFUSA |   POPULATION | OUTAGE.START        | OUTAGE.RESTORATION   |   UTIL.REALGSP.CONTRI.PROP | stateGSPaboveAvg   | untilGSPaboveAvg   |
|---:|------:|-------:|--------:|------------------:|-----------------:|---------------------:|------------:|------------:|------------:|--------------:|------------:|------------:|------------:|--------------:|-------------:|-------------:|-------------:|-------------------:|-----------------:|-----------------:|--------------------:|---------------:|----------------:|--------------:|----------------:|-------------:|:--------------------|:---------------------|---------------------------:|:-------------------|:-------------------|
|  0 |     1 |   2011 |       7 |              3060 |              nan |                70000 |       11.6  |        9.18 |        6.81 |          9.28 | 2.33292e+06 | 2.11477e+06 | 2.11329e+06 |   6.56252e+06 |      35.5491 |      32.225  |      32.2024 |              51268 |            47586 |          1.07738 |                 1.6 |           4802 |          274182 |       1.75139 |             2.2 |      5348119 | 2011-07-01 05:00:00 | 2011-07-03 08:00:00  |                  0.0175139 | True               | False              |
|  1 |     2 |   2014 |       5 |                 1 |              nan |                  nan |       12.12 |        9.71 |        6.49 |          9.28 | 1.58699e+06 | 1.80776e+06 | 1.88793e+06 |   5.28423e+06 |      30.0325 |      34.2104 |      35.7276 |              53499 |            49091 |          1.08979 |                 1.9 |           5226 |          291955 |       1.79    |             2.2 |      5457125 | 2014-05-11 06:38:00 | 2014-05-11 06:39:00  |                  0.0179    | True               | True               |
|  2 |     3 |   2010 |      10 |              3000 |              nan |                70000 |       10.87 |        8.19 |        6.07 |          8.15 | 1.46729e+06 | 1.80168e+06 | 1.9513e+06  |   5.22212e+06 |      28.0977 |      34.501  |      37.366  |              50447 |            47287 |          1.06683 |                 2.7 |           4571 |          267895 |       1.70627 |             2.1 |      5310903 | 2010-10-26 08:00:00 | 2010-10-28 10:00:00  |                  0.0170627 | True               | False              |
|  3 |     4 |   2012 |       6 |              2550 |              nan |                68200 |       11.79 |        9.25 |        6.71 |          9.19 | 1.85152e+06 | 1.94117e+06 | 1.99303e+06 |   5.78706e+06 |      31.9941 |      33.5433 |      34.4393 |              51598 |            48156 |          1.07148 |                 0.6 |           5364 |          277627 |       1.93209 |             2.2 |      5380443 | 2012-06-19 04:30:00 | 2012-06-20 11:00:00  |                  0.0193209 | True               | True               |
|  4 |     5 |   2015 |       7 |              1740 |              250 |               250000 |       13.07 |       10.16 |        7.74 |         10.43 | 2.02888e+06 | 2.16161e+06 | 1.77794e+06 |   5.97034e+06 |      33.9826 |      36.2059 |      29.7795 |              54431 |            49844 |          1.09203 |                 1.7 |           4873 |          292023 |       1.6687  |             2.2 |      5489594 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |                  0.016687  | True               | True               |

## Univariate Analysis
The main focuse point of the project is to analysis the weight of different factor's contribution on outage duration. A histogram is generated from the `OUTAGE.DURATION` column to observe the density of outage duration's overall spread.

<iframe src="assets/hist-duration.html" width=800 height=600 frameBorder=0></iframe>

It is a right skewed graph, so that means majority power outage is within 20,000 mins. The tail of the histogram drag till 100k, this resembles the presense of outliers. Thus, when considering a valid aggration method, using the **median** will be more accurate in measuring the central tendency of the outage duration. The choice of median can be traced back to our orginal quesiton of the weight of economic factors on outage duration, when only taking the median, we can cut off on the influence of outlier's impact on the dataset. 

## Bivariate Analysis

Tracking back to the question, we wanted to observe which factor weighted the most in a sever power outage. First, it is important to see the trend of outage duration over the past years. We groupby the year to choose the aggrate method of median for the test statists to observe the change of outage duration and how it differents over the years. 

<iframe src="assets/duration-year.html" width=800 height=600 frameBorder=0></iframe>

Apperantly, there is a decreasing trend on the utility GSP contribution over time. It correponds to the previous scatter plot on the year vs. outage time duration. This implies that as the times go both outage time duration and relative GSP constribution of utility industry decrease 

<iframe src="assets/GSP-relation-to-duration.html" width=800 height=600 frameBorder=0></iframe>

There is a decreasing trend on the utility GSP contribution over times. It correponds to the previous scatter plot on the year vs. outage time duration. This implies that as the times go both outage time duration and relative GSP constribution of utility industry decrease. 

we wanted to observe the weight of economic factors on the severity of the power outage. A bivariate graph is created to analysis the trend in utility industies' GSP against years. 

## Pivot Table 
To look more into the trend of the power outages relationship to economic standards, we assigned high and low labels on GSP accourding to the mean GSP. 

| GSP Above Average  |   High |   Low |
|:-------------------|--------:|-------:|
| High              |   272   |   1440 |
| Low               |   369.5 |    870 |

Base on the table above, suprisingly we can make new modification on our question. It seems like the state with low relative utility GSP has the shortest power outage duration. In other words, the state with high relative untility GSP will cause a more sever power outage base on the duration. From this result, we hypothesis that the states with large relative GSP utility are more likely to have large utility secotor. If they experience power outage, it **might** takes the large utility sectors more time to restore the outage. However, we need more domain knowledge to assest the claim.

This then guides our question in: whether the states' relative utility GSP is one of the leading factors that impact the severity of the power outage?

# Assessment of Missingness

This dataset is collected from multiple sources and when merging the data together there is a possiblity of unconsistence. If possible, we would like to get access to all the sub datasets that are used to generate this dataset to assess the missingness better. There are 5 sub datasets that all sorced from governemnt agencies, it would also be helpful to compare the information with other sources. Since all the current data are less likley to contain any biases when collected by governement agencies, if compared to other sources we may be able to draw significant patterns accounding to the missingness of values. 

We think that there is column whoes missingness is __NMAR__. If we are able to obtain more data such as the total cost behind the power outage, we might be able to asscess the missingness of the other columns that exist in the dataset due to it's relationship.

For example, in the column `DEMAND.LOSS.MW`, there is a total of 705 missing values, when reading through the the dataset description on the website this column is described as the peak demand lost amount during an otage event(MW), (some values in the column might be the total outage lost instead). If we were able to collect more data on the aspect of loss in terms of total cost, then there might be a correlation between the actual total cost (dollars) in relation to the peak demand lost. It can be possible that when peak demand lost are high that the total cost will also be high. Thus, with the extra data we might be able to state that the missingness of `DEMAND.LOSS.MW` is __MAR__ and later perform imputation based on the dependent columns. 


## Missingness Dependency
Guided by our question, more infomation is needed for the `OUTAGE.DURATION` column so that we will be able to use it in detailed later during hypothese testing. 

### Relationship between `POPULATION` and missingness of `OUTAGE.DURATION` (MCAR)
Intuitively, we hypothesize that the missingness of power outage duration might depends on the populations. The population decide the amount of people that will be impacted by the power outage. If population of the state is relative lower, then less people will be impacted by the power outage. Therefore, they are more likely not to record the outage duration. 

Before running permutation test, we want to decide the test statistic that will be used to measure the difference between two distributions. If the distributions are similar to each other, we can use the absolute mean difference. If the distribute has different shapes, we can use Kolmogorov Smirnov Test statistic (KS test) which measures the difference between graphs'cumulative distribution.

<iframe src="assets/missingDurationPopulation.html" width=800 height=600 frameBorder=0></iframe>

The distribution is different which tell us that we should implement K-S test statistic in our permutation test.

<iframe src="assets/empricalPopulationKS.html" width=800 height=600 frameBorder=0></iframe>
During the test:
- Null Hypothesis: The missingness of `OUTAGE.DURATION` is independent from `POPULATION`
- Alternative Hypothesis: The missingness of `OUTAGE.DURATION` is dependent on `POPULATION`

Since the p value after running permutation test is 0.35 which is greater than 0.05, we failed to reject the null that the `POPULATION` distribution with missing power outage duration comes from the same population as the `POPULATION` distribution without missing power outage.

The missingness of `OUTAGE_DURATION` is indepdent from the `POPULATION`

### Relationship between `RES.PRICE` and missingness of `OUTAGE.DURATION`(MAR)

We also hypothesize that the `RES.PRICE` might impact missingness of the `OUTAGE_DURATION` column. `RES.PRICE` columns inform us the monthly electricty price in the residential sector. We think that the residents in the sector with high monthly electricity price care more about the electricty usage statistic which includes the outage duration so they are more likely to report the outage duration with detailized starting and ending time. On the other hand, residents live in sector with low monthly electricity price might not care about the outage duration since there is no much of differences on their electricity bill if there is an outage.

<iframe src="assets/miss2dist.html" width=800 height=600 frameBorder=0></iframe>

The distribution is different which tell us that we should implement K-S test statistic in our permutation test. The Emprical Distribution of test static below:

<iframe src="assets/miss2ts.html" width=800 height=600 frameBorder=0></iframe>

After running permutation test, the p value is 0.0089 which is less than the standard significance level 0.05. We reject the null hypothesis and accept the alternative hypothesis that the distributions of `RES.PRICE` with and without missing outage duration is different from each other.

The missingness of `OUTAGE_DURATION` is dependent on the `RES.PRICE` column, this may imply the missingness of `OUTAGE.DURATION` is missing at random (MAR)

For future work,
with the conclusion from above, we can impute Outage_duration missing value using the `RES.PRICE` column. Since the existance of outliers in `OUTAGE.DURATION` weights more, the imputation planned is median imputation. 

# Hypothesis Testing

After sufficent amount of data cleaning & EDA, careful review on the missingness of one of the important column, we are able to answer the question we mentioned eariler. To answer the qestion: whether the states' relative utility GSP is one of the leading factors that impact the severity of the power outage? We will run permutation test on `OUTAGE_DURATION` column and `UTIL.REALGSP.CONTRI.PROP`. We define the states with relative utility GSP higher than or equal to the average US utility GSP of that specific year to be the state with high utility GSP. On the other hand, the state with utility GSP lower than the average US utility GSP will be define as low utlity GSP state. By doing this, we split our data set to two groups

In doing this, our hypothesis will be:
- Null hypothesis: Both states with high utility GSP or states with low utility GSP will __likely to share same__ distribution of power outage duration
- Alternative hypothesis: States with high utility GSP will have __different__ power outage duration from the states with low utility GSP

<iframe src="assets/durationGSP.html" width=800 height=600 frameBorder=0></iframe>

The shape of two distributions look similar to each other, so we can use absolute mean difference to measure the similarity between two distributions. We will use the abstract function `abs_diff_p_val` we defined previously to caluclate the p value. 

```
abs_diff_p_val(df, col1, co2, N)

Parameters: 
df: the dataframe being used
col1: the name of a column in str
col2: the name of a column in str
N: the number of time to run the permutation int
return: tuple contain three values: p_value, generated test stat, observed value, repectively

```
With this we make the function call on our cleaned dataframe and columns `untilGSPaboveAvg` and `OUTAGE.DURATION`. Where we shuffled the values in `OUTAGE.DURATION` and used the test stat of absolute mean differences between the two group that are in the column `untilGSPaboveAvg`. 

Function call given below:
```
p_value, emprical, obs = abs_diff_p_val(powerOutage, 'untilGSPaboveAvg','OUTAGE.DURATION',10_000)
```

After 10,000 iterations, we were able to obtain the empirical distribution below: 
<iframe src="assets/hypTestPerm.html" width=800 height=600 frameBorder=0></iframe>

since the p value is 0.0 which is less than the standard significance level 0.05, we reject the null hypothesis. It is possible that the distribution for States with high utility GSP will have different distribution of outage duration apart from the states with low utility GSP. 

We can say that the economic performance of a state on its utility industries is a main factor that impact the severity of its major power outages.

# References
Mukherjee, S., Nateghi, R., & Hastak, M. (2018). Data on major power outage events in the continental U.S. Data in Brief, 19, 2079–2083. https://doi.org/10.1016/j.dib.2018.06.067

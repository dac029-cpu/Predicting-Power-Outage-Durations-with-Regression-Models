# Predicting Outage Duration with Regression Models

## Final Project for DSC80 - UCSD
By David Chen

# Introduction

In this project, I analyzed a power outage dataset, which covers all instances of power outage from January 2000 to July 2016 in the United States. This dataset is from Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure (https://engineering.purdue.edu/LASCI/research-data/outages). 

The dataset details power outage data, such as outage duration, cause of outage, outage start time. It also contains other relevant information including geographical data, climate data, electric consumption data, etc. 

The question I will be tackling in my analysis will be what are the most critical predictors for outage duration, that is to say, which variables have the largest effect on outage duration length. To do this, I will be building a regression model that finds the variables with the most impact on outage duration. This will be very important for the energy sector, as it allows them to know what exactly to focus on preventing when trying to reduce the length of outages.

The original DataFrame consists of 1534 rows, each corresponding to a specific outage, and 57 columns, each correpsonding to a variable. I will only be focusing on a few variables for my analysis:

<table border="1">
  <thead>
    <tr>
      <th>Variable</th>
      <th>Description</th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>YEAR</td>
      <td>The year an outage occurred</td>
      <td>Numeric</td>
    </tr>
    <tr>
      <td>MONTH</td>
      <td>The month an outage occurred</td>
      <td>Temporal</td>
    </tr>
    <tr>
      <td>US.STATE</td>
      <td>The State an outage occurred in</td>
      <td>Categorical</td>
    </tr>
    <tr>
      <td>NERC.REGION</td>
      <td>North American Electric Reliability Corporation (NERC) region involved in the outage event</td>
      <td>Categorical</td>
    </tr>
    <tr>
      <td>CLIMATE.REGION</td>
      <td>U.S. Climate regions as specified by National Centers for Environmental Information the outage occured in</td>
      <td>Categorical</td>
    </tr>
    <tr>
      <td>CAUSE.CATEGORY</td>
      <td>Categories of all events causing outages</td>
      <td>Categorical</td>
    </tr>
    <tr>
      <td>CAUSE.CATEGORY.DETAIL</td>
      <td>Detailed description of the event categories causing the major power outages</td>
      <td>Categorical</td>
    </tr>
    <tr>
      <td>OUTAGE.DURATION</td>
      <td>Total length of the power outage (minutes)</td>
      <td>Numeric</td>
    </tr>
    <tr>
      <td>OUTAGE.START.DATE</td>
      <td>The start date of the power outage</td>
      <td>Temporal</td>
    </tr>
    <tr>
      <td>OUTAGE.START.TIME</td>
      <td>The start time of the power outage</td>
      <td>Temporal</td>
    </tr>
    <tr>
      <td>OUTAGE.RESTORATION.DATE</td>
      <td>The end date of the power outage</td>
      <td>Temporal</td>
    </tr>
    <tr>
      <td>OUTAGE.RESTORATION.TIME</td>
      <td>The end time of the power outage</td>
      <td>Temporal</td>
    </tr>
    <tr>
      <td>DEMAND.LOSS.MW</td>
      <td>Megawatts of demand lost during the outage</td>
      <td>Numeric</td>
    </tr>
    <tr>
      <td>CUSTOMERS.AFFECTED</td>
      <td>Number of customers affected by the outage</td>
      <td>Numeric</td>
    </tr>
    <tr>
      <td>TOTAL.CUSTOMERS</td>
      <td>Total number of customers served in the U.S. State the outage occured in</td>
      <td>Numeric</td>
    </tr>
  </tbody>
</table>

# Data Cleaning

First, I dropped all columns not used in my analysis, and kept only the variables from above, which are <code>YEAR</code>, <code>MONTH</code>, <code>US.STATE</code>, <code>NERC.REGION</code>, <code>CLIMATE.REGION</code>, <code>CAUSE.CATEGORY</code>, <code>CAUSE.CATEGORY.DETAIL</code>, <code>OUTAGE.DURATION</code>, <code>OUTAGE.START.DATE</code>, <code>OUTAGE.START.TIME</code>, <code>OUTAGE.RESTORATION.DATE</code>, <code>OUTAGE.RESTORATION.TIME</code>, <code>DEMAND.LOSS.MW</code>, <code>CUSTOMERS.AFFECTED</code>, <code>TOTAL.CUSTOMERS</code>

Next, we combine <code>OUTAGE.START.DATE</code> and <code>OUTAGE.START.TIME</code> columns into <code>OUTAGE.START</code>, and simiarly for <code>OUTAGE.RESTORATION.DATE</code> and <code>OUTAGE.RESTORATION.TIME</code> into <code>OUTAGE.RESTORATION</code>. Essentially we condensed two columns describing time into one single column. This helps ease our analysis of these variables later on.

I also replaced all values of 0 in <code>OUTAGE.DURATION</code>, <code>DEMAND.LOSS.MW</code>, and <code>CUSTOMERS.AFFECTED</code> with nan, since it is fair to assume that values of 0 for these columns is likely due to missingness. This will help ease our missingness analysis later on. 

The first rew rows of the dataframe is shown below.

<div style="overflow-x: auto; white-space: pre;">
<pre>
|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION | OUTAGE.START        | OUTAGE.RESTORATION   |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   TOTAL.CUSTOMERS | duration_bin   | DURATION_MISSING   |
|-------:|--------:|:-------------|:--------------|:-------------------|:-------------------|:------------------------|------------------:|:--------------------|:---------------------|-----------------:|---------------------:|------------------:|:---------------|:-------------------|
|   2011 |       7 | Minnesota    | MRO           | East North Central | severe weather     | nan                     |              3060 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |              nan |                70000 |           2595696 | 1–3 days       | False              |
|   2014 |       5 | Minnesota    | MRO           | East North Central | intentional attack | vandalism               |                 1 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |              nan |                  nan |           2640737 | <1 hr          | False              |
|   2010 |      10 | Minnesota    | MRO           | East North Central | severe weather     | heavy wind              |              3000 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |              nan |                70000 |           2586905 | 1–3 days       | False              |
|   2012 |       6 | Minnesota    | MRO           | East North Central | severe weather     | thunderstorm            |              2550 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |              nan |                68200 |           2606813 | 1–3 days       | False              |
|   2015 |       7 | Minnesota    | MRO           | East North Central | severe weather     | nan                     |              1740 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |              250 |               250000 |           2673531 | 1–3 days       | False              |
</pre>
</div>

# Exploratory Data Analysis

## Univariate Analysis

<iframe
  src="assets/figure_1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Figure 1 depicts the distribution of binned outage durations. The plot shows that as each day passes, the number of outage still not resolved decrease significantly. This logarithmic relationship shows that extended outages are rare compared to shorter ones.
<iframe
  src="assets/figure_2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Figure 2 shows the distribution of the cause categories. The most populated categories are <b> severe weather <\b> and <b> intentional attack <\b>, while the least populated categories are <b> fuel supply emergency <\b> and <b> islanding <\b>

<iframe
  src="assets/figure_3.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Figure 3 shows the number of outages with a detailed cause for each cause category. Unsurprisingly <b> severe weather <\b> has the highest count, but weirdly enough <b> intentional attack <\b> has one of the lowest, despite being one of the most populated categories from Figure 2. This is likely due to the fact that intentional attacks are a quite straightforward cause, meaning there is not much need to add additional details.

<iframe
  src="assets/figure_4.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Figure 4 shows the comparison between raw demand loss and log transformed demand loss. The raw demand loss values are extremely right-skewed, with most outages involving very small demand loss and a few rare events reaching high loss. After applying a log transformation, the distribution becomes much more balanced and approximately bell-shaped.

## Bivariate Analysis

<iframe
  src="assets/figure_5.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Figure 5 depicts the relationship between cause category and outage duration. We see that outage durations vary widely across cause categories, with severe weather showing both the highest spread and some of the longest outages overall. Categories like intentional attack, equipment failure, and system operability disruption tend to have shorter and more consistent outage durations, reflected by their much tighter boxplots. Meanwhile, fuel supply emergencies exhibit several extreme long-duration outliers, suggesting that despite being rare, these events can lead to prolonged outages.

## Aggregates

'| CAUSE.CATEGORY                |   avg_duration |   median_duration |   count |\n|:------------------------------|---------------:|------------------:|--------:|\n| fuel supply emergency         |      13484     |            3960   |      38 |\n| severe weather                |       3899.71  |            2464   |     741 |\n| equipment failure             |       1850.56  |             224   |      54 |\n| public appeal                 |       1468.45  |             455   |      69 |\n| system operability disruption |        747.092 |             222   |     120 |\n| intentional attack            |        521.934 |              92.5 |     332 |\n| islanding                     |        200.545 |              77.5 |      44 |'

I grouped on the cause categories and found the mean, median, and count for each category. We see that <b> fuel supply emergency <\b> and <b> severe weather <\b> has the highest averages (both median and mean), while <b>intentional attacks <\b> and <b> islanding <\b> has the lowest. 
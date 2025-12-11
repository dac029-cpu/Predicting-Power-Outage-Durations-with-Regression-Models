# Predicting Outage Duration with Regression Models

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
      <td>Numeric</td>
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
      <td>Numeric</td>
    </tr>
    <tr>
      <td>OUTAGE.START.TIME</td>
      <td>The start time of the power outage</td>
      <td>Numeric</td>
    </tr>
    <tr>
      <td>OUTAGE.RESTORATION.DATE</td>
      <td>The end date of the power outage</td>
      <td>Numeric</td>
    </tr>
    <tr>
      <td>OUTAGE.RESTORATION.TIME</td>
      <td>The end time of the power outage</td>
      <td>Numeric</td>
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

---
layout: post
comments: true
title: Visualising West Nile Virus cases in the US
---

The West Nile Virus is spread to humans by mosquitos. Most people that are infected don't have any symptoms but in some cases the virus invades the nervous system where the infection becomes life-threatening. These cases are called neuroinvasive. 

I am analysing the public data provided by the Center for Disease Control (CDC) on the number of neuroinvasive and non-neuroinvasive West Nile virus cases in the United States. The data can be found through the CDC's National Notifiable Diseases Surveillance System which reports on weekly pages like [this](https://www.cdc.gov/mmwr/preview/mmwrhtml/mm6401md.htm#tab4). However, I downloaded the data from Kaggle which automatically creates a [dataset](https://www.kaggle.com/cdc/nndss-west-nile-virus-disease) from the CDC website. 

I will analyse the 2017 and 2018 data for West Nile and compare that to the data for the Zika virus.
 
## Importing and cleaning the data  

To begin I must import the data and clean the data as there are a lot of missing values. I will use python to do this;


$$	
import pandas as pd

# Import the data
data18 = pd.read_csv('nndss-west-nile-virus-disease/nndss-table-ii.-west-nile-to-zika.csv')
# data17 = pd.read_csv('nndss-west-nile-virus-disease/nndss-table-ii.-west-nile-virus-disease.csv')

# Tidying the headings
data18.columns = data18.columns.str.strip().str.lower().str.replace(' ', '_').str.replace('?', '').str.replace('§', '').str.replace('?', '').str.replace(',', '').str.replace('west_nile_virus_disease', 'WNVD').str.replace('mmwr_', '').str.replace('nonneuroinvasive', 'NN').str.replace('neuroinvasive', 'N')

# Drop UNITED STATES and Regions
State18 = \
data18[(data18.reporting_area != 'UNITED STATES') & 
        (data18.reporting_area != 'NEW ENGLAND') &
        (data18.reporting_area != 'MID. ATLANTIC') &
        (data18.reporting_area != 'E.N. CENTRAL') &
        (data18.reporting_area != 'W.N. CENTRAL') &
        (data18.reporting_area != 'E.S. CENTRAL') &
        (data18.reporting_area != 'W.S. CENTRAL') &
        (data18.reporting_area != 'S. ATLANTIC') &
        (data18.reporting_area != 'MOUNTAIN') &
        (data18.reporting_area != 'PACIFIC') &
        (data18.reporting_area != 'AMER. SAMOA') &
        (data18.reporting_area != 'C.N.M.I.') &
        (data18.reporting_area != 'GUAM') &
        (data18.reporting_area != 'PUERTO RICO') &
        (data18.reporting_area != 'VIRGIN ISL.')]

# Tidying the state names
states = []
for i in range(len(list(State18['reporting_area']))):
    states.append(list(State18['reporting_area'])[i].replace('DIST. OF COL.', 'District of columnbia').capitalize())
State18['State'] = states

# Adding country for Tableau mapping 
State18['Country'] = ['US'] * len(State18)

# Fill NaN with 0
State18.fillna(0, inplace = True)

# Save dataframe as csv for Tableau 
State18.fillna(0).to_csv('state_data18.csv')
$$

Now I have a dataset that specifies the State, week of the year, and cumulative number of neuroinvasive and non-neuroinvasive cases of West Nile virus and Zika virus. I upload this into Tableau and created some visualisations to better understand West Nile virus in the US.


## Total number of West Nile cases by state

I began by examining if there was any geological pattern to the number of cases. Perhaps tropical areas have more mosquitos and therefore more cases. The following graph shows the total number of West Nile cases per State over 2017 and 2018 (white states have zero cases);

![_config.yml]({{ site.baseurl }}/images/west-nile-map.png)  

Personally, I don't see much geographic patterns other than California consistently have a lot of cases. 


## Seasonality and Neuroinvasive cases

Next I examined the seasonality of West Nile virus cases by plotting the cumulative number of cases over 2017 and 2018. 

![_config.yml]({{ site.baseurl }}/images/west-nile-cum.png)  

Outbreaks have a distinct seasonality where most new cases happen between weeks 30 and 40 (end of July to start of October). The outbreak was slightly later in 2018 than in 2017 but it reached a higher total. 

I then examined the neuroinvasive compared to non-neuroinvasive cases in 2018. I created a histogram of the new cases.  

![_config.yml]({{ site.baseurl }}/images/west-nile-hist.png)  

Despite non-neuroinvasive cases being more common we see similar report rates. This is likely due to people without symptoms not being identified and neuroinvasive cases being over represented in the data. Again, we see similar seasonality as we did above. 

## Comparing West Nile and Zika cases

Lastly, since both West Nile and Zika are transmitted by mosquitos I wanted to check for a correlation between the two diseases. Below is a graph that shows the number of West Nile cases (size of the dot) and Zika cases (colour of the dot). If there was a correlation I would expect big dots to be red and small dots to be orange. 

![_config.yml]({{ site.baseurl }}/images/west-nile-vs-zika.png)

There appears to be no correlation between West Nile and Zika cases in 2018. Perhaps this could be because Zika is only beginning to spread across the US or the viruses are carried by different types of mosquitoes.   
 
 This graph shows numerous aspects of the data; geographical relationships, West Nile cases, and Zika cases. There are lots of wonderful ways to display data especially for effective communication in [data science](https://towardsdatascience.com/the-art-of-effective-visualization-of-multi-dimensional-data-6c7202990c57). 
 
 In summary, _West Nile virus is a seasonal disease that did not correlate with the Zika virus._  


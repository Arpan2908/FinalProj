# Analyzing deforestation in Brazil and Bhutan

## Introduction: 
Forests are a critical component in climate regulation and isolating carbon emission. The Amazon forests in Brazil have been supporting the locals for energy, household needs and construction. These forests have been declining at an alarming rate due to deforestation and influenced by land use land cover change. On the other hand, there have been a lot of national policies aimed at forests protection in Bhutan. This was made successful by the Bhutanese forest protection policies by accounting for trade in wood products with India which have helped maintain a high forest cover. By mostly importing wood charcoal for its emerging chemical industries, Bhutan has managed to support the economic development while protecting the forests. 
Performing Land use change detection using GIS and Remote Sensing has proven to be an important tool to fully understand the extent of land cover loss or gain over time. The patterns and trends of the Land use land cover change(LULC) is important for the decision making process. 
In this project, the extent of deforestation in Brazil Amazon Forest and the forest across Bhutan are visualized over a period of 15 years from 2000-2015. 
This is carried out on Google Earth Engine using Javascript. 

## Data:
The data used in this project is the Hansen et. al Global Forest Change 2000-2014 data.

It can be accessed through the [web page](https://developers.google.com/earth-engine/tutorials/tutorial_forest_02)

IMAGE

The Global forest change dataset 2013 contains the forest change globally between 2000-2014. This dataset can be accessed and imported through Earth Engine from the top bar for search places and locations.

IMAGE

or by running some lines of code.

IMAGE

The data contains information in various different bands including:

IMAGE

## Tutorial

Start by creating a variable for the country of study as var country and using the 2 letter code for the country from [The list of FIPS Country Codes](https://en.wikipedia.org/wiki/List_of_FIPS_country_codes)

```
var country = 'BR';
```


Then, load the country features from Large Scale International Boundary dataset:

IMAGE












































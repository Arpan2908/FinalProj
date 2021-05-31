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

```
var countries = ee.FeatureCollection('USDOS/LSIB_SIMPLE/2017');
var selected = countries.filter(ee.Filter.eq('country_co', ee.String(country)));
```

Add the layer to the code by using:

```
Map.addLayer(selected, {}, "Brazil")
```

You can set the map center and zoom level of your choice. It is preferred to set the zoomm level such that it includes all of the study area and not a lot of surrounding area

```
Map.setCenter(-53.2, -10.6, 3);
```

use ee.Image() to add the Hansen forest cover 2000-2012 data set to the map. 

```
var gfc2012 = ee.Image('UMD/hansen/global_forest_change_2013'); 
```

Set the visual parameters which can include the band to represent, minimum and maximum extent, and the pallette used for the data

```
var visParams = {
  bands: ['treecover2000'],
  min: 0.0,
  max: 100.0,
  palette: [
    "3d3d3d","080a02","080a02","080a02","106e12","37a930",
    "03ff17",
  ]
};
```

Add the layee to the map based on the parameters set and give the image a name

```
Map.addLayer(gfc2012, visParams, "Tree Canopy Cover");
print(gfc2012)
```


























































































# Analyzing deforestation in Brazil and Bhutan

## Introduction: 
Forests are a critical component in climate regulation and isolating carbon emission. The Amazon forests in Brazil have been supporting the locals for energy, household needs and construction. These forests have been declining at an alarming rate due to deforestation and influenced by land use land cover change. On the other hand, there have been a lot of national policies aimed at forests protection in Bhutan. This was made successful by the Bhutanese forest protection policies by accounting for trade in wood products with India which have helped maintain a high forest cover. By mostly importing wood charcoal for its emerging chemical industries, Bhutan has managed to support the economic development while protecting the forests. 
Performing Land use change detection using GIS and Remote Sensing has proven to be an important tool to fully understand the extent of land cover loss or gain over time. The patterns and trends of the Land use land cover change(LULC) is important for the decision making process. 
In this project, the extent of deforestation in Brazil Amazon Forest and the forest across Bhutan are visualized over a period of 15 years from 2000-2015. 
This is carried out on Google Earth Engine using Javascript. 

## Data:
The data used in this project is the Hansen et. al Global Forest Change 2000-2014 data.

It can be accessed through the [web page](https://developers.google.com/earth-engine/tutorials/tutorial_forest_02)

<img src = FP1.png>

The Global forest change dataset 2013 contains the forest change globally between 2000-2014. This dataset can be accessed and imported through Earth Engine from the top bar for search places and locations.

<img src = FP.png>

or by running some lines of code.

<img src = FP3.png>

The data contains information in various different bands including:

<ing src = FP4.png>

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

<img src = FP0.png>  
  
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

Add the layer to the map based on the parameters set and using the variable set for image(gfc2012)

```
Map.addLayer(gfc2012, visParams, "Tree Canopy Cover");
print(gfc2012)
```

The code below sets parameters for measuring deforestation.  - "cc" sets the minimum canopy cover for the year 2000. We are considering forest that had 50% canopy cover in 2000. 
 - "pixels" sets the minimum connected area for forest in 2000 (so a forest area must have at least 6 connected pixels)
 - "losspixels" sets the minimum connected area for forest loss. We set the mininum pixels for the features to be considered. For Example- Here, setting this minimum number to 6 tells us that there is approximately 0.5 ha of area considered minimum. We also set the minimum mapping area for tree loss which is usually same as the minimum forest area.

```
var cc = ee.Number(50);
var pixels = ee.Number(6);
var lossPixels = ee.Number(6);
```

Now, the areas with below 50% Canopy Cover are masked as they are insignificant in our study. We also mask out the areas that do not have atleast 6 connected pixels as they dont fall under our criteria of minimum area of forest cover or minimum area for tree loss. 

```
var canopyCover = gfc2012.select(['treecover2000']);
var canopyCover50 = canopyCover.gte(cc).selfMask();
```

```
var contArea = canopyCover50.connectedPixelCount();
var minArea = contArea.gte(pixels).selfMask();
print('min area', minArea)
```

```
Map.addLayer(minArea, {
    palette: ['#96ED89']
}, 'tree cover: >= min canopy cover & area (light green)');
```

## Examining deforested area by year

- the "lossAreaImage" below shows the area of deforestation loss in square meters. This is because "ee.Image.pixelArea()" calculates area in sq meters.
We want to convert the "lossAreaImage" to sqaure kilometers. For doing this we would have to divide the image by 10^6. To do this, we use the function '.multiply'
```
var validForestMask = minArea.mask()
var lossImage = gfc2012.select(['loss']).mask(validForestMask);

var lossAreaImage = lossImage.multiply(ee.Image.pixelArea())
var lossAreaImage_sqkm = lossAreaImage.divide(1e-6)
```

## Calculate loss by year
The code below calculates deforestation area by year. The "lossByYear" object gives us the deforested area by year in sqaure kilometers. When run, the "lossByYear" contains two different properties which can be seen in the console. One of this property is 'group' and the other is 'sum'
Group represents the loss year of the deofrestation whereas 'sum' represents the total area of loss.

```
var lossYear = gfc2012.select(['lossyear']);
var lossByYear = lossAreaImage_sqkm.addBands(lossYear).reduceRegion({
  reducer: ee.Reducer.sum().group({
    groupField: 1
    }),
  geometry: selected,
  scale: 30,
  maxPixels: 1e8,
  bestEffort: true
});
print('loss by year', lossByYear);
```
  
<img src = BrazilFinal.png>  
  

## To represent the forest loss by year as charts
```
var statsFormatted = ee.List(lossByYear.get('groups'))
  .map(function(el) {
    var d = ee.Dictionary(el);
    return [ee.Number(d.get('group')).format("20%02d"), d.get('sum')];
  });
var statsDictionary = ee.Dictionary(statsFormatted.flatten());
print('statsDictionary', statsDictionary);

var chart = ui.Chart.array.values({
  array: statsDictionary.values(),
  axis: 0,
  xLabels: statsDictionary.keys()
}).setChartType('ColumnChart')
  .setOptions({
    title: 'Yearly Forest Loss (sq km)',
    hAxis: {title: 'Year', format: '####'},
    vAxis: {title: 'Area (sq km)'},
    legend: { position: "none" },
    lineWidth: 1,
    pointSize: 3
  });
print(chart);
```
  
<img src = FPchart.png>  

## Forest Cover change for Bhutan

To identify the forest cover change (deforestation/aforestation) we would carry out the same analysis except for the starting of the code where we set Bhutan as our country of study in the variable declaration, add the layer to the map after extracting the data through the data link, and set map center and zoom level. 

```
var country = 'BT';
```

```
// add Map.addLayer code  
Map.addLayer(selected, {}, "Bhutan")


// add Map.setCenter code
Map.setCenter(90.533226,27.483672, 7);
```

<img src = FPBhutan.png>
  
<img src  = Bhutan.png>  

<img src = Bhutanchart.png>








































































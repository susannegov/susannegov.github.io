---
layout: post
title:  "Generating Markings Asset Counts based on Crew Assigned"
date:   2019-09-03
tags: [python, jupyter notebook, arcmap, pavement markings, counts, crew assigned,gis]
---
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CREW_ASSIGNED</th>
      <th>YEARS</th>
      <th>TOTALS</th>
      <th>ANNUAL_TOTALS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BIKE</td>
      <td>4</td>
      <td>9590</td>
      <td>2398.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>CBD</td>
      <td>4</td>
      <td>2367</td>
      <td>592.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>OTHER</td>
      <td>6</td>
      <td>13077</td>
      <td>2180.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SIGNAL</td>
      <td>6</td>
      <td>8326</td>
      <td>1388.0</td>
    </tr>
  </tbody>
</table>


This post will demonstrate how to access attribute table from shapefiles in order to create a markings asset counts for each street segment.
[Here is the link to notebook](https://nbviewer.jupyter.org/github/susannegov/Signs-and-Markings-Projects/blob/master/Markings_Dataset.ipynb)

<!--more-->

The purpose of this notebook is to quickly create datasets for reporting purposes


```python
gis = GIS("https://austin.maps.arcgis.com/home/index.html")
url = r"https://services.arcgis.com/0L95CJ0VTaxqcmED/arcgis/" +
r"rest/services/TRANSPORTATION_{}/FeatureServer/0"
col = ['CREW_ASSIGNED','TOTALS']
```

## Create Feature Layers and DataFrames
This will create feature layers and convert to dataframe. This will take a while since there are over 30000 rows


```python
sdf = pd.DataFrame.spatial.from_layer(
FeatureLayer(url.format("markings_specialty_point")))
year = pd.DataFrame(
{'YEARS':[4,4,6,6],col[0]:['CBD','BIKE','SIGNAL','OTHER']})
```

## Create Tables

```python
sp_sdf = sdf.copy().rename(columns={'SHAPE':'TOTALS'})
sp_sdf = sp_sdf.groupby([col[0]]).count()[[col[1]]].reset_index().filter(
items=col)

final = pd.merge(sp_sdf,year,on=[col[0]])
final['ANNUAL_TOTALS'] = round(final.TOTALS / final.YEARS)
final =final[[final.columns[0],final.columns[2],
final.columns[1],final.columns[3]]]
```

```python
final.to_csv(r'C:\Users\Govs\Projects\Files\specialty_markings_totals.csv')
```


This project would not have been completed without the help from:
- [pandas](https://pandas.pydata.org/) to create dataframe of extracted table and transform the data
- [arcgis](https://esri.github.io/arcgis-python-api/apidoc/html/) to search for markings feature layer dataset

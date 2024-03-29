---
layout: post
title:  "Finding Pavement Markings Impact Related to Street Overlays in 2020 - Part 1"
date:   2019-09-16
categories: [GIS]
tags: [Python, Jupyter Notebook, ArcMap, Traffic Signs, GIS]
---
<img src = "/assets/images/counts_table_thumbnail.PNG" width="800px">
This post will demonstrate how to access attribute table from shapefiles in order to create a markings asset counts for each street segment.
[Here is the link to notebook](https://nbviewer.jupyter.org/github/susannegov/Signs-and-Markings-Projects/blob/master/SBO_Markings_Counts_2020.ipynb)

<!--more-->

The purpose is to estimate the total pavement markings asset counts over the next 5 years from fiscal year 2020 to 2024 using the markings asset feature layers from ArcGIS Online.

<i><b>Disclaimer:</b> This product is for informational purposes and may not have been prepared for or be suitable for legal, engineering, or surveying purposes. No warranty is made by the City of Austin regarding specific accuracy or completeness.</i>

UPDATE: Added bike projects and delineators to asset counts to determine input from Active Transportation. Check it out [here]()

This project would not have been completed without the help from:
- [pandas](https://pandas.pydata.org/) to create dataframe of extracted table and transform the data
- [geopandas](http://geopandas.org/mapping.html) to access attribute table of 5 year selected segments for SBO
- [arcgis](https://esri.github.io/arcgis-python-api/apidoc/html/) to search for markings feature layer dataset

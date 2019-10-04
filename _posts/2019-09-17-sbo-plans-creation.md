---
layout: post
title:  "Generate Pavement Markings Plans using Python, Jupyter Notebook, and ArcMap"
date:   2019-09-17
categories: [Engineering]
tags: [Python, Jupyter Notebook, ArcMap, Pavement Markings, GIS]
---
<img src = "/assets/images/plans_thumbnail.png" height="150px" width="800px">
This post describes creating a work order plan template using surface streets report to create a pavement markings plan.

Check it out here:
- [Part 1](https://nbviewer.jupyter.org/github/susannegov/Signs-and-Markings-Projects/blob/master/Whereabouts/WhereaboutsStreets.ipynb)
- [Part 2](https://nbviewer.jupyter.org/github/susannegov/Signs-and-Markings-Projects/blob/master/Whereabouts/PlansTemplate.ipynb)

<!--more-->

The purpose of this notebook is to create a Street and Bridge Work Order plans based on segment IDs and additional comments on long line. Markings feature layers are published in the City of Austin ArcGIS Portal page available for public view as well.

The schedule for when and where sealcoat and overlay streets are completed is received through email by Street and Bridge Operations on a weekly basis.

<b>The only manual process the user will have to do is to:</b>
- Input Segment IDs
- Make comments on long line markings
- Specify file path to retrieve the table of completed streets paved for PDF name and file path
- Create any missing markings assets that are not visible in aerial imagery

This process will cut down on the previous process of manually editing a plans layout through copy-pasting imagery and writing Location IDs, work groups, markings found, and the exporting plans one at a time.

An excel document will be created based on this input and read segment IDs to find all short line and specialty point markings. This will ideally generate multiple PDF plans in a faster and shorter time frame.

Below is a table comparing the old process versus the new process for time it
takes to complete on a busy seasonal schedule.

| Process | Time to complete   |
|:--------:|----|
| Manual Plan Creation  |24 hours per month|
| New Plan Creation |6 hours per month|

This project would not have been completed without the help from:
- [pandas](https://pandas.pydata.org/) to create dataframe of extracted table and transform the data
- [openpyxl](https://openpyxl.readthedocs.io/en/stable/) to edit excel files
- [arcgis](https://esri.github.io/arcgis-python-api/apidoc/html/) to search for markings feature layer dataset
- [pathlib](https://docs.python.org/3/library/pathlib.html) to find path to excel document if it exists
- [PyPDF2](https://pythonhosted.org/PyPDF2/) to merge PDFs for cover and pages in order
- [archook](https://github.com/JamesRamm/archook) to search for arcgis and makes arcpy available to python
- [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/get-started/what-is-arcpy-.htm) to create whereabouts markings plans using ESRI ArcMap Desktop software

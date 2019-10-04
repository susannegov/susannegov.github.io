---
layout: post
title:  "Automating Overhead Signs Work Orders Using Python and Microsoft Office 365"
date:   2019-07-30
categories: [Engineering]
tags: [Traffic Signs, Work Orders, Python]
---
<img src = "/assets/images/7_E.png" height="150px" width="150px" style="padding-right: 20px; padding left: 20px;"><img src = "/assets/images/7_N.png" height="150px" width="150px" style="padding-right: 20px; padding left: 20px;"><img src = "/assets/images/7_S.png" height="150px" width="150px" style="padding-right: 20px; padding left: 20px;"><img src = "/assets/images/7_W.png" height="150px" width="150px" style="padding-right: 20px; padding left: 20px;">

This post will demonstrate how to create work orders of overhead traffic signs as part of an established maintenance plan.
[Here is the link to notebook](https://nbviewer.jupyter.org/github/susannegov/Signs-and-Markings-Projects/blob/master/2019WorkOrderCreation.ipynb)

<!--more-->

As fiscal year 2019 is going to be the first year for establishing a maintenance plan for overhead signs, it was imperative that a method would be created to
automatically create work orders based on existing streetview imagery and planned operational maintenance areas.

These are the files required to start automation.

| Required Files | Time to complete (hours)   |
|:--------:|----|
| StreetView Imagery Screenshots  |8-16|
| Traffic Signs Workbook |8|
| Overhead Sign Locations |0.5|
| PowerPoint Work Orders Template |1|

The purpose is to automate the creation of whereabouts plans. This is important because these plans are subject to change from other division such as traffic signals and having an excel file on hand will allow for quick edits.

An estimated comparison of how long it would take to do it manually versus automatically.

| Process | Time to complete   |
|:--------:|----|
| Manual work orders |3-4 weeks|
| Automatic work orders |3-4 days|

Once the new traffic sign data gets collected, we could decrease the time takes to create the new work orders for the whole fiscal year down to several hours.

This project would not have been completed without the help from:
- [pandas](https://pandas.pydata.org/) to create dataframe of extracted table and transform the data
- [pptx](https://python-pptx.readthedocs.io/en/latest/) to create the work orders template
- [Pillow](https://pillow.readthedocs.io/en/stable/) to modify screenshot streetview imagery into a set width and height without cropping

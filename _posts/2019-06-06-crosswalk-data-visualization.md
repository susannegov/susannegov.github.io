---
layout: post
title:  "Visualizing Installation of Crosswalks"
date:   2019-06-06
tags: [traffic signs, overhead, work orders, python,gis,jupyter notebook, pandas, seaborn]
---
<img src = "/assets/images/crosswalk_install.png">

The purpose of this notebook is to visualize the markings assets represented by installation date and total pavement markings assets maintained within the City of Austin full jurisdiction. Packages used for visualization include `arcgis`, `pandas`, `seaborn`, and `bokeh`.

[Here is the link to notebook](https://nbviewer.jupyter.org/github/susannegov/Signs-and-Markings-Projects/blob/master/MarkingsTableVisualization.ipynb.ipynb)

<!--more-->

```python
import datetime
import math
import pandas as pd
import numpy as np
import seaborn as sns
from IPython.display import display, HTML
from arcgis.gis import GIS
from arcgis.features import SpatialDataFrame

from bokeh.palettes import Spectral11, colorblind, Inferno, BuGn, brewer
from bokeh.layouts import row
from bokeh.plotting import figure, output_file, show
from bokeh.models import (HoverTool, value, LabelSet, Legend,
                          ColumnDataSource, CDSView,LinearColorMapper,
                          BasicTicker, PrintfTickFormatter,ColorBar,
                          DatetimeTickFormatter)
from bokeh.io import output_notebook
output_notebook()
```
Data analysis of pavement markings are used for financial, performance measures, and maintenance goals.

```python
class CreateSDF(SpatialDataFrame):
    _metadata = ['my_attr']

    @property
    def _constructor(self):
        return SpatialDataFrame

    def __init__(self, *args, **kwargs):
        self.my_attr = kwargs.pop('my_attr', None)
        super().__init__(*args, **kwargs)

    def agol(title):
        gis = GIS("http://austin.maps.arcgis.com/home/index.html")
        search = gis.content.search(query="title:" + title,
                                    item_type="Feature Layer")
        item = search[0]
        layer = item.layers[0]
        sdf = SpatialDataFrame.from_layer(layer)
        return sdf
```

Here we create a spatial dataframe using the `arcgis` package from an existing feature layer dataset in ArcGIS Online called markings_short_line.

The dataset [markings_short_line](https://data.austintexas.gov/Transportation-and-Mobility/TRANSPORTATION-markings_short_line/3p2i-pqdc) can also be found in the Open Data Portal.


```python
fields = ['SUBTYPE','BARS','CREW_ASSIGNED','INSTALL_DATE']
cols = ['Crosswalk', 'Total Continental Bars', 'Total', 'Date']
q = "INSTALL_DATE == INSTALL_DATE & SHORT_LINE_TYPE == 'CROSSWALK'"

sdf = CreateSDF.agol("markings_short_line").query(q).filter(
    items=fields).rename(columns=dict(zip(fields,cols)))
```


```python
new_sdf = sdf.groupby(
    [sdf['Date'].dt.year.rename('Year'), sdf['Date'].dt.month.rename(
        'Month'),"Crosswalk"],observed = False).agg(
    {'Total Continental Bars':'sum','Total':'count'})
col_names = ['Year','Month','Crosswalk']
for x in range(len(col_names)):
    new_sdf[col_names[x]] = new_sdf.index.values
    new_sdf[col_names[x]] = new_sdf[col_names[x]].apply(lambda y: y[:][x])
new_sdf['Date'] = pd.to_datetime(
    new_sdf.Year.apply(str) + '/' + new_sdf.Month.apply(str),
    format = "%Y/%m")
new_sdf['Crosswalk'] = new_sdf['Crosswalk'].apply(
    lambda x: x.lower().capitalize())
new_sdf.reset_index(drop=True,inplace=True)
new_sdf = new_sdf.query('Year > 2015')
display(new_sdf.filter(
    items=['Crosswalk','Month','Year','Total']).query(
    "Year == 2018 & Crosswalk == 'Continental'"))
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Crosswalk</th>
      <th>Month</th>
      <th>Year</th>
      <th>Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>52</th>
      <td>Continental</td>
      <td>1</td>
      <td>2018</td>
      <td>46</td>
    </tr>
    <tr>
      <th>54</th>
      <td>Continental</td>
      <td>2</td>
      <td>2018</td>
      <td>44</td>
    </tr>
    <tr>
      <th>56</th>
      <td>Continental</td>
      <td>3</td>
      <td>2018</td>
      <td>20</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Continental</td>
      <td>4</td>
      <td>2018</td>
      <td>54</td>
    </tr>
    <tr>
      <th>60</th>
      <td>Continental</td>
      <td>5</td>
      <td>2018</td>
      <td>40</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Continental</td>
      <td>6</td>
      <td>2018</td>
      <td>105</td>
    </tr>
    <tr>
      <th>64</th>
      <td>Continental</td>
      <td>7</td>
      <td>2018</td>
      <td>87</td>
    </tr>
    <tr>
      <th>66</th>
      <td>Continental</td>
      <td>8</td>
      <td>2018</td>
      <td>122</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Continental</td>
      <td>9</td>
      <td>2018</td>
      <td>30</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Continental</td>
      <td>10</td>
      <td>2018</td>
      <td>17</td>
    </tr>
    <tr>
      <th>71</th>
      <td>Continental</td>
      <td>11</td>
      <td>2018</td>
      <td>38</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Continental</td>
      <td>12</td>
      <td>2018</td>
      <td>45</td>
    </tr>
  </tbody>
</table>
</div>


After the spatial dataframe has been created, a dataframe is created to show the total number of crosswalks installed based on crosswalk type, month, and year. The table above shows the total number of continental crosswalks installed for 2018.

Next task is to visualize the total installations of crosswalks done in the City of Austin. The bar graph below shows how many crosswalks were installed/maintained grouped by month, year and crosswalk type.


```python
data_m = []
width = datetime.timedelta(days=20)
colors = ["#247ba0","#b2dbbf","#ff1654"]
c_type = ["Continental","Transverse","Total"]
hover = HoverTool(names=["bar"])

mean= new_sdf.Total.mean()
total = new_sdf.groupby('Date').agg({'Total':'sum',
                                     'Month':'mean','Year':'mean'})

p = figure(
    plot_width=600,plot_height=350,
    title="Last Crosswalk Installation by Date",
    x_axis_type="datetime", y_axis_type='linear',
    toolbar_location="below", tools=[hover,'save'])

for x in (new_sdf[new_sdf['Crosswalk'] == c_type[0]],
          new_sdf[new_sdf['Crosswalk'] == c_type[1]],total):
    data_m.append(ColumnDataSource(x))

p.select_one(HoverTool).tooltips = (
    "There are @Total @Crosswalk crosswalk(s) installed in @Month/@Year")

for x in range(len(colors) - 1):
    p.vbar(x='Date',top='Total',source=data_m[x],color=colors[x],
           width=width,name="bar",legend='Crosswalk')
p.line(x='Date',y=mean,source=data_m[2],line_color='red',
       line_dash='dotted',line_width=3,legend='average')
show(p)
```

The average number of crosswalks installed per month is 22.


```python
colors = brewer['YlGnBu'][9][::-1]
axis = ['Year','Month']
high = new_sdf.Total.max()
ticker = BasicTicker(desired_num_ticks=len(colors))
mapper = LinearColorMapper(palette=colors, low=0, high=high)

hm = figure(title="Month-Year crosswalk installations",
            tools="hover,help,save", toolbar_location='above',
           plot_width=500,plot_height=500)
color_bar = ColorBar(color_mapper=mapper,
                     major_label_text_font_size="9pt",ticker=ticker,
                     label_standoff=9,border_line_color='white',
                     location=(0, 0))

hm.add_layout(color_bar, 'right')
hm.xaxis.axis_label = axis[0]
hm.yaxis.axis_label = axis[1]
hm.outline_line_color = None
hm.grid.grid_line_color = None
hm.xaxis.major_tick_line_color = None  
hm.xaxis.minor_tick_line_color = None
hm.yaxis.minor_tick_line_color = None
hm.select_one(HoverTool).tooltips = [
    (axis[0], '@Year'),(axis[1], '@Month'),('Total', '@Total crosswalks')]

hm.rect(x=axis[0], y=axis[1],width=1,height=1,source = data_m[2],
        fill_color={'field': 'Total','transform': mapper},
        line_color='white')
show(hm)
```
Generally, crosswalk maintenance and installations peaks around summer-fall season. Good weather condition ensure that the crosswalk lasts in good condition.

We can look on the crosswalks installed based on month and year to see if there is a correlation with seasonal-yearly installation by calculating the correlation coefficient and plotting it using `seaborn`.

```python
corr_sdf = total.pivot_table(values='Total',index='Year',
                             columns='Month',aggfunc=np.sum)
sns.heatmap(corr_sdf.T,annot=False,fmt=".2f",linewidths=.2,cmap="YlGnBu")
```
<img src = "/assets/images/crosswalk_install.png">

---
layout: post
title:  "Measuring Retroreflectivity for Traffic Signs"
date:   2019-09-23
tags: [traffic signs, retroreflectivity, field work]
---
<img src = "/assets/images/sign_reflect.jpg" height=250><img src = "/assets/images/sign_reflect1.jpg" height=250><img src = "/assets/images/sign_reflect2.jpg" height=250>

Retroreflectivity is an optical phenomena where a surface returns directed light back at its source. Without retroreflectivity, traffic signs would not be visible at night when headlights hit the sign's surface. The MUTCD outlines that the retroreflectivity of signs shall be replaced if it fails retroreflectivity tests.

To record the retroreflectivity of traffic signs, a device called a retroreflectometer was used. It is a device that measures the light reflecting properties of signs accurately and reliably.

[Here is the link to notebook](https://nbviewer.jupyter.org/github/susannegov/Signs-and-Markings-Projects/blob/master/Sign_Reflectivity_FY19.ipynb)

<!--more-->

The purpose of the retroreflectivity Project status is to assess the retroreflectivity of traffic signs to determine whether these sample traffic signs should be replaced. Furthermore, this will also serve as a benchmark for determining sign condition as well.

## Standard
<b>"Public agencies or officials having jurisdiction shall use an assessment or management method that is designed to maintain sign retroreflectivity at or above the minimum levels in [Table2A-3](https://mutcd.fhwa.dot.gov/htm/2009/part2/part2a.htm#table2A03)."</b>


```python
import pandas as pd
import matplotlib
file_path = "Files/Sign_Reflectivity_Sample_FY19.xlsx"
df = pd.read_excel(file_path,index_col="Sign ID")
df['Sign Colors'] = df['Legend Color'] + ' on ' + df['Background Color']
df = df.set_index('Sign Colors')
mutcd = pd.read_excel(file_path,sheet_name='MUTCD',index_col='Sign')
display(mutcd)
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
      <th>Background Color</th>
      <th>Legend Color</th>
      <th>Background Standard</th>
      <th>Legend Standard</th>
      <th>Standards</th>
      <th>Additional Criteria</th>
    </tr>
    <tr>
      <th>Sign</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>White on Green</th>
      <td>Green</td>
      <td>White</td>
      <td>15</td>
      <td>120</td>
      <td>W ≥ 120; G ≥ 15</td>
      <td>Post-mounted</td>
    </tr>
    <tr>
      <th>Black on Yellow</th>
      <td>Yellow</td>
      <td>Black</td>
      <td>50</td>
      <td>0</td>
      <td>Y ≥ 50; O ≥ 50</td>
      <td>Signs measuring at least 48 inches</td>
    </tr>
    <tr>
      <th>Black on Orange</th>
      <td>Orange</td>
      <td>Black</td>
      <td>75</td>
      <td>0</td>
      <td>Y ≥ 75; O ≥ 75</td>
      <td>Signs measuring less than 48 inches</td>
    </tr>
    <tr>
      <th>White on Red</th>
      <td>Red</td>
      <td>White</td>
      <td>7</td>
      <td>35</td>
      <td>W ≥ 35; R ≥ 7</td>
      <td>Sign standard ratio ≥ 3:1 (W/R)</td>
    </tr>
    <tr>
      <th>Black on White</th>
      <td>White</td>
      <td>Black</td>
      <td>50</td>
      <td>0</td>
      <td>W ≥ 50</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>


<i>Table 2A-3.</i>


```python
# go thrrough every row in the table
for index,row in df.iterrows():
    legend = int(row['Legend Ra 0.2'])
    bg = int(row['Background Ra 0.2'])

    # No retroreflective standard on No truck or Stop Ahead legend
    if index == 'Red on White' or index == 'Red on Yellow':
        df.loc[index,'Condition'] = 'Pass'
    else:
        # Set the standards base on legend color
        # and background color according to MUTCD
        bg_standard = mutcd.at[index,'Background Standard']
        legend_standard = mutcd.at[index,'Legend Standard']

        # Check if passes standards
        if legend >= legend_standard and bg >= bg_standard:
            if index == 'White on Red' and (legend_standard/bg_standard) < 3:
                df.loc[index,'Condition'] = 'Fail'
            else:
                pass
            df.loc[index,'Condition'] = 'Pass'
        else:
            df.loc[index,'Condition'] = 'Fail'
```

## Report
Here is the number of signs measured for reflectivity:


```python
signs = df.groupby('MUTCD').count()[['User']].rename(
columns={'User':'Sign Totals'}).sort_values(
    by=['Sign Totals'],ascending=False)
display(signs)
signs.to_csv('Files/sign_total.csv')
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
      <th>Sign Totals</th>
    </tr>
    <tr>
      <th>MUTCD</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Stop Sign</th>
      <td>13</td>
    </tr>
    <tr>
      <th>No Truck</th>
      <td>4</td>
    </tr>
    <tr>
      <th>School Pedestrian Crossing</th>
      <td>3</td>
    </tr>
    <tr>
      <th>Speed Limit</th>
      <td>2</td>
    </tr>
    <tr>
      <th>End School Zone</th>
      <td>1</td>
    </tr>
    <tr>
      <th>Handicap Sign</th>
      <td>1</td>
    </tr>
    <tr>
      <th>No Outlet</th>
      <td>1</td>
    </tr>
    <tr>
      <th>Stop Ahead</th>
      <td>1</td>
    </tr>
    <tr>
      <th>Street Name Sign</th>
      <td>1</td>
    </tr>
    <tr>
      <th>W1-3</th>
      <td>1</td>
    </tr>
    <tr>
      <th>Yield</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>


Below is the data collected from the field using the retroreflectometer from May and September. Two retroreflectometers's were used for collection. 29 sign samples were collected total, and 71 sign samples will be collected this week.


```python
cols = ['MUTCD','Road','User','Legend Color','Legend Ra 0.2',
'Background Color','Background Ra 0.2',
        'Direction', 'Sheeting Legend','Condition']

display(df.filter(cols).reset_index())
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
      <th>Sign Colors</th>
      <th>MUTCD</th>
      <th>Road</th>
      <th>User</th>
      <th>Legend Color</th>
      <th>Legend Ra 0.2</th>
      <th>Background Color</th>
      <th>Background Ra 0.2</th>
      <th>Condition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>White on Red</td>
      <td>Stop Sign</td>
      <td>Bissonett &amp; Guadalupe</td>
      <td>Kati Alcantara</td>
      <td>White</td>
      <td>377.233327</td>
      <td>Red</td>
      <td>91.733332</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>1</th>
      <td>White on Red</td>
      <td>Stop Sign</td>
      <td>Bissonett &amp; Guadalupe</td>
      <td>Kati Alcantara</td>
      <td>White</td>
      <td>213.699997</td>
      <td>Red</td>
      <td>70.866666</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>2</th>
      <td>White on Red</td>
      <td>Stop Sign</td>
      <td>Croslin &amp; Guadalupe</td>
      <td>Kati Alcantara</td>
      <td>White</td>
      <td>698.866659</td>
      <td>Red</td>
      <td>140.766668</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Black on White</td>
      <td>Speed Limit</td>
      <td>Croslin near Guadalupe</td>
      <td>Kati Alcantara</td>
      <td>Black</td>
      <td>5.600000</td>
      <td>White</td>
      <td>189.866669</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>4</th>
      <td>White on Red</td>
      <td>Stop Sign</td>
      <td>Kenniston &amp; Guadalupe</td>
      <td>Kati Alcantara</td>
      <td>White</td>
      <td>207.233332</td>
      <td>Red</td>
      <td>49.266666</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Red on White</td>
      <td>No Truck</td>
      <td>Kenniston &amp; Guadalupe</td>
      <td>Kati Alcantara</td>
      <td>Red</td>
      <td>245.066671</td>
      <td>White</td>
      <td>369.700012</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Red on White</td>
      <td>No Truck</td>
      <td>Kenniston &amp; Guadalupe</td>
      <td>Kati Alcantara</td>
      <td>Red</td>
      <td>110.833333</td>
      <td>White</td>
      <td>557.566661</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Black on White</td>
      <td>No Truck</td>
      <td>Swàynee &amp; Isabelle</td>
      <td>Kati Alcantara</td>
      <td>Black</td>
      <td>0.066667</td>
      <td>White</td>
      <td>202.599996</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>8</th>
      <td>White on Green</td>
      <td>Street Name Sign</td>
      <td>Swàynee &amp; Isabelle</td>
      <td>Kati Alcantara</td>
      <td>White</td>
      <td>704.133321</td>
      <td>Green</td>
      <td>141.933334</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>9</th>
      <td>White on Red</td>
      <td>Stop Sign</td>
      <td>Quail Park &amp; Quail Field</td>
      <td>Christina Tremel</td>
      <td>White</td>
      <td>306.874992</td>
      <td>Red</td>
      <td>72.349999</td>
      <td>Pass</td>
    </tr>
    <tr>
      <th>10</th>
      <td>White on Red</td>
      <td>Stop Sign</td>
      <td>Collinfield &amp; Quail Park</td>
      <td>Christina Tremel</td>
      <td>White</td>
      <td>605.124992</td>
      <td>Red</td>
      <td>216.574997</td>
      <td>Pass</td>
    </tr>
  </tbody>
</table>
</div>


## Conclusions
All 29 signs collected in the field passed retroreflectivity standards according to the MUTCD. The project is 29% completed. Measuring retroreflectivity of 1 sign takes about 5-10 minutes not including travel time, so it should be possible to collect all 100 signs within next week.

There are some signs that are suspected to be in poor condition, even though the sign fulfills retroreflective MUTCD standards. After project completion, we can create a new sign condition standard.

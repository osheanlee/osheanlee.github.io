+++
author = "Oshean Lee Garonita"
categories = ["forests", "python", "earth"]
date = "2019-09-22"
description = ""
featured = "2019/09/Selangor_Malaysia.jpg"
featuredalt = "Selangor_Malaysia"
featuredpath = ""
linktitle = ""
title = "Why We Need to Choose The Earth"
type = "post"

+++

<i>Second Entry</i>


## How It Started

I was never a nature-lover, and I always took it for granted. Growing up, I’ve always assumed that the trees will always be there and that it never really gave too much significance since it was not moving, or that it does not have the same morphological features as other living things. Sure, we were taught that trees are important. They give oxygen, and we give carbon dioxide in return. I’ve always thought that we will never run out of it – not until I’ve seen things.

I came to college with only a glimpse of what I was about to study: Human Ecology. Basically, the degree is a multi-disciplinary field that focuses on the relationship between humans and their environment. I took the degree because just like other Filipino high school kids, I want to study in UP, and I thought that Human Ecology would be an easy degree to enter UP and just shift to another degree. But apparently, it was not a mistake and taking the degree was life-changing! It’s like going down the unknown rabbit hole.

My eyes became open to the most crucial element that our world has: our environment. For the past 12 years I’ve heard stories from the ground on how there is social and environmental injustice occurring especially on Indigenous People communities. These people are the [best stewards](https://www.yaleclimateconnections.org/2019/03/wri-researcher-indigenous-people-are-best-forest-stewards) of our environment. They ensure that our natural resources would always be available to us; however, there are some industries that heavily exploit these without undergoing legal processes. And in the end, these IPs would lose their homes with tampered culture and way of life.

For so many years, our forest resources are dwindling. As of 2015, we only have [23.38%](http://forestry.denr.gov.ph/index.php/statistics/philippines-forestry-statistics) of our forest cover compared to 1960s when it was still at [50%](http://ree.ph/qloss-of-biodiversity/). This is alarming as it could have negative potential impacts to other living organisms depending on it.

<center>
  <a href="https://ibb.co/Q9k9PWM"><img src="https://i.ibb.co/Tc1c4Qt/Historical-Deforesation-Philippines.png" alt="Historical-Deforesation-Philippines" border="0"></a>

  _Forest loss incurred from 1950s up to present. Source: [Coral Triangle Conservancy, Inc.](http://ree.ph/loss-of-biodiversity)_
</center>


## Protected Areas: One of the Nation's Way to #ChooseTheEarth

In 1992, the Philippines passed the **National Integrated Protected Areas System (NIPAS) Act of 1992** which paved way to designate certain areas as (of course) **protected areas**. In summary (or at the minimum), these protected areas must have the following:

	1.) Buffer zones;
	2.) Management plans;
	3.) Administration and Management System; and
	4.) **Protected Areas Management Board** or PAMB;

I will not discuss the other requirements. But if you are interested to see the full law, click [here](https://www.lawphil.net/statutes/repacts/ra1992/ra_7586_1992.html).

### A closer look at the Philippines' Protected Areas

Getting data from [PhilGIS](http://philgis.org/general-country-datasets/protected-areas), we can do some Exploratory Data Analysis (EDA) to learn more about our PAs. Again, I cannot guarantee the accuracy of this data. If we still want the most authoritative data, please refer to the Biodiversity Management Bureau of the Department of Environment and Natural Resources.

We will first import the necessary python modules:

```python
import pandas as pd
import geopandas as gp
import matplotlib.pyplot as plt
from seaborn import palplot
import palettable as pltt
import folium
import numpy as np
from matplotlib.widgets import Cursor
```

#### Data Cleaning

```python
# Load geodata
# Better practice is to re-project files in your GIS software (just to make things easier)
gdf_pa = gp.read_file('D:\GIS Database\From PhilGIS\Protected Areas\ProtectedAreas_PRS92_Zone4.gpkg')
gdf_phl = gp.read_file('D:\GIS Database\PH Boundaries from OCHA (PSA and NAMRIA)\PH-Barangay_Boundaries_PRS92_Zone4.gpkg')
```



```python
# Import modules

import pandas as pd
import geopandas as gp
import matplotlib.pyplot as plt
from seaborn import palplot
import palettable as pltt
import folium
import numpy as np
from matplotlib.widgets import Cursor
import json
import requests
```

## Data Cleaning


```python
# Load geodata
# Better practice is to re-project files in your GIS software (just to make things easier)
gdf_pa = gp.read_file('D:\GIS Database\From PhilGIS\Protected Areas\ProtectedAreas_PRS92_Zone4.gpkg')
gdf_phl = gp.read_file('D:\GIS Database\PH Boundaries from OCHA (PSA and NAMRIA)\PH-Barangay_Boundaries_PRS92_Zone4.gpkg')
```


```python
gdf_pa.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1af43ac8908>




![png](PA_analysis_files/PA_analysis_3_1.png)


Seems that we have a problem in our PA dataset, so I opened it in QGIS and found out that `wdpaid = 305980` is the "outlier". Since the data is opensource, I don't like to determine anymore if where in Philippines should this PA be located. I'll just drop it. (Don't try this when you are working with your work datasets! This is just for practice purposes.


```python
gdf_pa['wdpaid'].loc[gdf_pa['wdpaid'] == 305980]
```




    252    305980
    Name: wdpaid, dtype: int64




```python
gdf_pa = gdf_pa.drop(252)
```


```python
# Check if the "outlier" is still there:
gdf_pa.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1af45ef6198>




![png](PA_analysis_files/PA_analysis_7_1.png)


For the purpose of our analyses, I'll "try" to define the column names:


```python
gdf_pa.columns
```




    Index(['wdpaid', 'objectid', 'wdpa_pid', 'country', 'sub_loc', 'name',
           'orig_name', 'desig', 'desig_eng', 'desig_type', 'iucn_cat', 'marine',
           'rep_m_area', 'rep_area', 'status', 'status_yr', 'gov_type',
           'mang_auth', 'int_crit', 'mang_plan', 'official', 'is_point', 'no_take',
           'no_tk_area', 'metadata_i', 'action', 'area_sqkm', 'geometry'],
          dtype='object')



## Data Dive (Deeper)

### 1.) How many % land area are Protected Areas in the Philippines?

It seems that some of the PAs are intersecting with each other. Our calculation will be wrong if we would compute for the area of our PAs per row as it will also compute the intersecting areas. To fix this, we will aggregate them via `dissolve`.


```python
gdf_pa_dissolve = gdf_pa.dissolve(by='country')
gdf_pa_dissolve['area_sqm'] = gdf_pa_dissolve['geometry'].area
gdf_phl['area_sqm'] = gdf_phl['geometry'].area
gdf_pa_dissolve['area_sqm']/sum(gdf_phl['area_sqm']) * 100
```




    country
    PHL    19.792148
    Name: area_sqm, dtype: float64



### 2.) What is the largest Protected Area (in terms of land area)?


```python
gdf_pa['area_sqm'] = gdf_pa['geometry'].area
```


```python
gdf_pa
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
      <th>wdpaid</th>
      <th>objectid</th>
      <th>wdpa_pid</th>
      <th>country</th>
      <th>sub_loc</th>
      <th>name</th>
      <th>orig_name</th>
      <th>desig</th>
      <th>desig_eng</th>
      <th>desig_type</th>
      <th>...</th>
      <th>mang_plan</th>
      <th>official</th>
      <th>is_point</th>
      <th>no_take</th>
      <th>no_tk_area</th>
      <th>metadata_i</th>
      <th>action</th>
      <th>area_sqkm</th>
      <th>geometry</th>
      <th>area_sqm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>840</td>
      <td>25575</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Apo</td>
      <td>Mount Apo</td>
      <td>Marine Reserve</td>
      <td>Natural Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>6.313626e+04</td>
      <td>(POLYGON ((757315.6873592746 784859.0996727811...</td>
      <td>6.323164e+08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>841</td>
      <td>50723</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mounts Banahaw - San Cristobal</td>
      <td>Mounts Banahaw - San Cristobal</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.132692e+04</td>
      <td>(POLYGON ((331404.319340662 1548677.954027078,...</td>
      <td>1.133351e+08</td>
    </tr>
    <tr>
      <th>2</th>
      <td>842</td>
      <td>50759</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mayon Volcano</td>
      <td>Mayon Volcano</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>5.769067e+03</td>
      <td>(POLYGON ((576840.7380170472 1470612.506661169...</td>
      <td>5.769406e+07</td>
    </tr>
    <tr>
      <th>3</th>
      <td>844</td>
      <td>50915</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Aurora Memorial Park</td>
      <td>Aurora Memorial Park</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>7.090904e+03</td>
      <td>(POLYGON ((327017.1498461195 1742519.809907154...</td>
      <td>7.095521e+07</td>
    </tr>
    <tr>
      <th>4</th>
      <td>845</td>
      <td>50917</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Hundred Islands</td>
      <td>Hundred Islands</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>750</td>
      <td>None</td>
      <td>1.939221e+02</td>
      <td>(POLYGON ((183787.5302234282 1793000.190379641...</td>
      <td>1.943867e+06</td>
    </tr>
    <tr>
      <th>5</th>
      <td>846</td>
      <td>50919</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Quezon</td>
      <td>Quezon</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>8.706129e+02</td>
      <td>(POLYGON ((371609.587768511 1549527.004765501,...</td>
      <td>8.708975e+06</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1337</td>
      <td>50953</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mounts Iglit - Baco</td>
      <td>Mounts Iglit - Baco</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>8.482358e+04</td>
      <td>(POLYGON ((313788.8108993148 1396319.561449043...</td>
      <td>8.489327e+08</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2092</td>
      <td>296713</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Puerto Galera</td>
      <td>Puerto Galera</td>
      <td>UNESCO-MAB Biosphere Reserve</td>
      <td>UNESCO-MAB Biosphere Reserve</td>
      <td>International</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>1</td>
      <td>None</td>
      <td>0.0</td>
      <td>750</td>
      <td>None</td>
      <td>2.310949e+04</td>
      <td>(POLYGON ((360676.9956918512 1541808.481822829...</td>
      <td>2.311705e+08</td>
    </tr>
    <tr>
      <th>8</th>
      <td>5208</td>
      <td>51145</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Agoo - Damortis</td>
      <td>Agoo - Damortis</td>
      <td>Protected Landscape/Seascape</td>
      <td>Protected Landscape/Seascape</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>750</td>
      <td>None</td>
      <td>1.058794e+04</td>
      <td>(POLYGON ((215427.6930466259 1811258.163323495...</td>
      <td>1.060813e+08</td>
    </tr>
    <tr>
      <th>9</th>
      <td>5209</td>
      <td>51147</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Fuyot Spring</td>
      <td>Fuyot Spring</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>8.280648e+02</td>
      <td>(POLYGON ((390627.1306387488 1902661.267740236...</td>
      <td>8.282432e+06</td>
    </tr>
    <tr>
      <th>10</th>
      <td>5210</td>
      <td>51153</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Arayat</td>
      <td>Mount Arayat</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>3.814432e+03</td>
      <td>(POLYGON ((259715.5591419709 1679537.589514492...</td>
      <td>3.819666e+07</td>
    </tr>
    <tr>
      <th>11</th>
      <td>5211</td>
      <td>51155</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Bicol</td>
      <td>Bicol</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>5.259171e+03</td>
      <td>(POLYGON ((492419.8166688971 1545799.817045946...</td>
      <td>5.258726e+07</td>
    </tr>
    <tr>
      <th>12</th>
      <td>5213</td>
      <td>51157</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Sohoton Natural Bridge</td>
      <td>Sohoton Natural Bridge</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>4.276162e+02</td>
      <td>(POLYGON ((736298.2548422762 1258183.794816168...</td>
      <td>4.281726e+06</td>
    </tr>
    <tr>
      <th>13</th>
      <td>5215</td>
      <td>51159</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Pulag</td>
      <td>Pulag</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>1.169272e+04</td>
      <td>(POLYGON ((274009.709636789 1849355.681896811,...</td>
      <td>1.170621e+08</td>
    </tr>
    <tr>
      <th>14</th>
      <td>5216</td>
      <td>51161</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Balbalasang - Balbalan</td>
      <td>Balbalasang - Balbalan</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>3.922113e+03</td>
      <td>(POLYGON ((288160.7904440777 1921507.589607698...</td>
      <td>3.926031e+07</td>
    </tr>
    <tr>
      <th>15</th>
      <td>5218</td>
      <td>51163</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Apo Reef</td>
      <td>Apo Reef</td>
      <td>Marine Reserve</td>
      <td>Natural Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>750</td>
      <td>None</td>
      <td>1.585604e+04</td>
      <td>(POLYGON ((228016.5228033265 1394320.876830805...</td>
      <td>1.588425e+08</td>
    </tr>
    <tr>
      <th>16</th>
      <td>5219</td>
      <td>51165</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Isarog</td>
      <td>Mount Isarog</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>9.978828e+03</td>
      <td>(POLYGON ((543083.6410606013 1516733.128847893...</td>
      <td>9.978418e+07</td>
    </tr>
    <tr>
      <th>17</th>
      <td>5220</td>
      <td>51167</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Bulusan Volcano</td>
      <td>Bulusan Volcano</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>3.721467e+03</td>
      <td>(POLYGON ((614119.5016919402 1416262.912259589...</td>
      <td>3.722386e+07</td>
    </tr>
    <tr>
      <th>18</th>
      <td>7205</td>
      <td>51169</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Paoay Lake</td>
      <td>Paoay Lake</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.610089e+03</td>
      <td>(POLYGON ((239404.6667383171 2008254.199765153...</td>
      <td>1.612651e+07</td>
    </tr>
    <tr>
      <th>19</th>
      <td>7206</td>
      <td>51175</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Cassamata Hill</td>
      <td>Cassamata Hill</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>5.385146e+01</td>
      <td>(POLYGON ((247490.9807549053 1947954.830222897...</td>
      <td>5.393178e+05</td>
    </tr>
    <tr>
      <th>20</th>
      <td>7207</td>
      <td>51177</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Bessang Pass</td>
      <td>Bessang Pass</td>
      <td>Monument al naturii</td>
      <td>Natural Monument</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>5.081662e+02</td>
      <td>(POLYGON ((252012.0801710159 1879065.634563397...</td>
      <td>5.089023e+06</td>
    </tr>
    <tr>
      <th>21</th>
      <td>7208</td>
      <td>51179</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Northern Luzon Heroes Hill</td>
      <td>Northern Luzon Heroes Hill</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.261115e+03</td>
      <td>(POLYGON ((234104.3630386371 1936779.729068279...</td>
      <td>1.263285e+07</td>
    </tr>
    <tr>
      <th>22</th>
      <td>7209</td>
      <td>51181</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Data</td>
      <td>Mount Data</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>3.333850e+03</td>
      <td>(POLYGON ((276989.7696246488 1873670.712017246...</td>
      <td>3.337846e+07</td>
    </tr>
    <tr>
      <th>23</th>
      <td>7210</td>
      <td>51183</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Manleluang</td>
      <td>Manleluang</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.050181e+02</td>
      <td>(POLYGON ((208811.5736299244 1739148.481764128...</td>
      <td>1.052295e+06</td>
    </tr>
    <tr>
      <th>24</th>
      <td>7211</td>
      <td>51185</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Minalungao</td>
      <td>Minalungao</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.992244e+03</td>
      <td>(POLYGON ((302754.3857917059 1694107.71335108,...</td>
      <td>1.994016e+07</td>
    </tr>
    <tr>
      <th>25</th>
      <td>7212</td>
      <td>51187</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Capas Death March Monument</td>
      <td>Capas Death March Monument</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>6.906162e+02</td>
      <td>(POLYGON ((237939.6949981814 1698539.101321059...</td>
      <td>6.917330e+06</td>
    </tr>
    <tr>
      <th>26</th>
      <td>7213</td>
      <td>50093</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Olongapo</td>
      <td>Olongapo</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>6.242356e+03</td>
      <td>(POLYGON ((213196.8582907669 1650499.143480413...</td>
      <td>6.254505e+07</td>
    </tr>
    <tr>
      <th>27</th>
      <td>7214</td>
      <td>50783</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Roosevelt</td>
      <td>Roosevelt</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>8.028345e+02</td>
      <td>(POLYGON ((216896.9711351347 1644333.315487085...</td>
      <td>8.043750e+06</td>
    </tr>
    <tr>
      <th>28</th>
      <td>7215</td>
      <td>51149</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Bataan</td>
      <td>Bataan</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>750</td>
      <td>None</td>
      <td>2.564682e+04</td>
      <td>(POLYGON ((216747.9766135792 1640380.992904268...</td>
      <td>2.569503e+08</td>
    </tr>
    <tr>
      <th>29</th>
      <td>7216</td>
      <td>51189</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Taal Volcano</td>
      <td>Taal Volcano</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>2.619864e+04</td>
      <td>(POLYGON ((289531.8203934958 1559466.863907768...</td>
      <td>2.622646e+08</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>362</th>
      <td>306524</td>
      <td>53467</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mahoganao</td>
      <td>Mahoganao</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.384622e+02</td>
      <td>(POLYGON ((686661.466737974 934794.8380128922,...</td>
      <td>1.385708e+06</td>
    </tr>
    <tr>
      <th>363</th>
      <td>306527</td>
      <td>75045</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Baganga</td>
      <td>Baganga</td>
      <td>Landskapsvernomr�de</td>
      <td>Protected Landscape</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.172263e+02</td>
      <td>(POLYGON ((892422.2625084065 835689.7497426231...</td>
      <td>1.176635e+06</td>
    </tr>
    <tr>
      <th>364</th>
      <td>306528</td>
      <td>29889</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mabini</td>
      <td>Mabini</td>
      <td>Protected Landscape/Seascape</td>
      <td>Protected Landscape/Seascape</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>750</td>
      <td>None</td>
      <td>6.138286e+03</td>
      <td>(POLYGON ((814151.4949989255 807467.8505216407...</td>
      <td>6.152512e+07</td>
    </tr>
    <tr>
      <th>365</th>
      <td>306529</td>
      <td>29891</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mainit Hot Spring Buffer Zone</td>
      <td>Mainit Hot Spring Buffer Zone</td>
      <td>National Park - Buffer Zone</td>
      <td>National Park - Buffer Zone</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>4.185855e+02</td>
      <td>(POLYGON ((831790.0041962324 830960.5085853246...</td>
      <td>4.197044e+06</td>
    </tr>
    <tr>
      <th>366</th>
      <td>306530</td>
      <td>29893</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Malagos</td>
      <td>Malagos</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>2.239895e+02</td>
      <td>(POLYGON ((766279.7679067666 795360.0478220722...</td>
      <td>2.243630e+06</td>
    </tr>
    <tr>
      <th>367</th>
      <td>306531</td>
      <td>29895</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mati</td>
      <td>Mati</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.466018e+02</td>
      <td>(POLYGON ((850772.6145227134 775794.173239237,...</td>
      <td>1.470369e+06</td>
    </tr>
    <tr>
      <th>368</th>
      <td>306533</td>
      <td>29897</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Allah Valley</td>
      <td>Allah Valley</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>7.938295e+04</td>
      <td>(POLYGON ((679389.0654356817 710655.8014484957...</td>
      <td>7.944517e+08</td>
    </tr>
    <tr>
      <th>369</th>
      <td>306534</td>
      <td>256441</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Allah Valley Buffer Zone</td>
      <td>Allah Valley Buffer Zone</td>
      <td>Watershed Forest Reserve - Buffer Zone</td>
      <td>Watershed Forest Reserve - Buffer Zone</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>2.093824e+04</td>
      <td>(POLYGON ((672914.1902859113 692482.2541935185...</td>
      <td>2.095573e+08</td>
    </tr>
    <tr>
      <th>370</th>
      <td>306536</td>
      <td>75047</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Libungan</td>
      <td>Libungan</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>5.183342e+04</td>
      <td>(POLYGON ((673624.3036601625 840447.2916311589...</td>
      <td>5.186643e+08</td>
    </tr>
    <tr>
      <th>371</th>
      <td>306539</td>
      <td>53469</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Surigao</td>
      <td>Surigao</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>9.932264e+02</td>
      <td>(POLYGON ((770029.3626021186 1069748.850379337...</td>
      <td>9.949135e+06</td>
    </tr>
    <tr>
      <th>372</th>
      <td>306540</td>
      <td>53471</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Andanan River</td>
      <td>Andanan River</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.785974e+04</td>
      <td>(POLYGON ((813558.2715464894 990576.6209943054...</td>
      <td>1.790073e+08</td>
    </tr>
    <tr>
      <th>373</th>
      <td>306541</td>
      <td>53479</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Cabadbaran</td>
      <td>Cabadbaran</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.595546e+04</td>
      <td>(POLYGON ((799725.6178952246 1019188.418648356...</td>
      <td>1.598874e+08</td>
    </tr>
    <tr>
      <th>374</th>
      <td>306548</td>
      <td>53481</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Timolan Buffer Zone</td>
      <td>Mount Timolan Buffer Zone</td>
      <td>Protected Landscape/Seascape - Buffer Zone</td>
      <td>Protected Landscape/Seascape - Buffer Zone</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>7.421153e+02</td>
      <td>(POLYGON ((529303.1942577998 864656.0879855943...</td>
      <td>7.420684e+06</td>
    </tr>
    <tr>
      <th>375</th>
      <td>306549</td>
      <td>53483</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Apo Reef Buffer Zone</td>
      <td>Apo Reef Buffer Zone</td>
      <td>Natural Park - Buffer Zone</td>
      <td>Natural Park - Buffer Zone</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>1.170262e+04</td>
      <td>(POLYGON ((222964.6211378719 1413725.813450011...</td>
      <td>1.172347e+08</td>
    </tr>
    <tr>
      <th>376</th>
      <td>306550</td>
      <td>53485</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Kalatungan Range Buffer Zone</td>
      <td>Mount Kalatungan Range Buffer Zone</td>
      <td>Natural Park - Buffer Zone</td>
      <td>Natural Park - Buffer Zone</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>1.368708e+04</td>
      <td>(POLYGON ((706075.1999111193 887205.3665529024...</td>
      <td>1.369954e+08</td>
    </tr>
    <tr>
      <th>377</th>
      <td>306551</td>
      <td>53487</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Manupali</td>
      <td>Manupali</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>3.765813e+04</td>
      <td>(POLYGON ((707648.7276617078 898331.9498621896...</td>
      <td>3.769659e+08</td>
    </tr>
    <tr>
      <th>378</th>
      <td>306552</td>
      <td>29899</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>PNOC Geothermal Site</td>
      <td>PNOC Geothermal Site</td>
      <td>Not Reported</td>
      <td>Collaborative Fishery Management Area</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>7.769616e+02</td>
      <td>(POLYGON ((748799.7961814294 774866.6714082339...</td>
      <td>7.780722e+06</td>
    </tr>
    <tr>
      <th>379</th>
      <td>313617</td>
      <td>274399</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Iglit Baco</td>
      <td>Mount Iglit Baco</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>1</td>
      <td>None</td>
      <td>0.0</td>
      <td>607</td>
      <td>None</td>
      <td>9.641362e+04</td>
      <td>(POLYGON ((275807.8998613865 1396858.092608082...</td>
      <td>9.650413e+08</td>
    </tr>
    <tr>
      <th>380</th>
      <td>317292</td>
      <td>50873</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Aklan River</td>
      <td>Aklan River</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>5.616427e+03</td>
      <td>(POLYGON ((392513.8859091688 1307501.397846026...</td>
      <td>5.617645e+07</td>
    </tr>
    <tr>
      <th>381</th>
      <td>317293</td>
      <td>50875</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Canlaon</td>
      <td>Canlaon</td>
      <td>Marine Reserve</td>
      <td>Natural Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>2.469913e+04</td>
      <td>(POLYGON ((515874.200363483 1167843.317271589,...</td>
      <td>2.469733e+08</td>
    </tr>
    <tr>
      <th>382</th>
      <td>317294</td>
      <td>50877</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Chocolate Hills</td>
      <td>Chocolate Hills</td>
      <td>Monument al naturii</td>
      <td>Natural Monument</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.182273e+04</td>
      <td>(POLYGON ((621123.9415231562 1095965.558702781...</td>
      <td>1.182631e+08</td>
    </tr>
    <tr>
      <th>383</th>
      <td>317295</td>
      <td>25529</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Lake Sebu</td>
      <td>Lake Sebu</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>9.176437e+03</td>
      <td>(POLYGON ((691213.8130650553 682390.6653765431...</td>
      <td>9.183787e+07</td>
    </tr>
    <tr>
      <th>384</th>
      <td>317296</td>
      <td>50879</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Northern Sierra Madre Mountain Range</td>
      <td>Northern Sierra Madre Mountain Range</td>
      <td>Not Reported</td>
      <td>Collaborative Fishery Management Area</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>3.568372e+05</td>
      <td>(POLYGON ((418859.2532704887 1939262.082084657...</td>
      <td>3.568609e+09</td>
    </tr>
    <tr>
      <th>385</th>
      <td>317297</td>
      <td>261751</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Palawan</td>
      <td>Palawan</td>
      <td>Game Refuge and Bird Sanctuary</td>
      <td>Game Refuge and Bird Sanctuary</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.177369e+06</td>
      <td>(POLYGON ((104599.1692952072 1148204.888813832...</td>
      <td>1.184263e+10</td>
    </tr>
    <tr>
      <th>386</th>
      <td>317298</td>
      <td>50881</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Polilio</td>
      <td>Polilio</td>
      <td>Watershed Forest Reserve</td>
      <td>Watershed Forest Reserve</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.308332e+02</td>
      <td>(POLYGON ((386954.9807094857 1632080.556206781...</td>
      <td>1.308633e+06</td>
    </tr>
    <tr>
      <th>387</th>
      <td>901240</td>
      <td>29819</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Apo Natural Park</td>
      <td>Mount Apo Natural Park</td>
      <td>ASEAN Heritage</td>
      <td>ASEAN Heritage</td>
      <td>International</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>6.313626e+04</td>
      <td>(POLYGON ((757315.6873592746 784859.0996727811...</td>
      <td>6.323164e+08</td>
    </tr>
    <tr>
      <th>388</th>
      <td>901241</td>
      <td>52861</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mounts Iglit-Baco National Park</td>
      <td>Mounts Iglit-Baco National Park</td>
      <td>ASEAN Heritage</td>
      <td>ASEAN Heritage</td>
      <td>International</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>8.482358e+04</td>
      <td>(POLYGON ((313788.8108993148 1396319.561449043...</td>
      <td>8.489327e+08</td>
    </tr>
    <tr>
      <th>389</th>
      <td>555512130</td>
      <td>323941</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Takot Fish and Marine Sanctuary</td>
      <td>Takot Fish and Marine Sanctuary</td>
      <td>Aire marine prot�g�e</td>
      <td>Marine Protected Area</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>1</td>
      <td>None</td>
      <td>0.0</td>
      <td>1720</td>
      <td>None</td>
      <td>3.508621e+02</td>
      <td>(POLYGON ((415253.3388633814 1398024.02789155,...</td>
      <td>3.508939e+06</td>
    </tr>
    <tr>
      <th>390</th>
      <td>555512131</td>
      <td>323943</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Zone within Takot Fish and Marine Sanctuary</td>
      <td>LMMA Takot Fish and Marine Sanctuary</td>
      <td>Aire marine prot�g�e</td>
      <td>Marine Protected Area</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>1</td>
      <td>None</td>
      <td>0.0</td>
      <td>1720</td>
      <td>None</td>
      <td>3.508621e+02</td>
      <td>(POLYGON ((415253.3388633814 1398024.02789155,...</td>
      <td>3.508939e+06</td>
    </tr>
    <tr>
      <th>391</th>
      <td>555512164</td>
      <td>254385</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Apo Reef</td>
      <td>Apo Reef</td>
      <td>Marine Reserve</td>
      <td>Natural Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>1720</td>
      <td>None</td>
      <td>1.585606e+04</td>
      <td>(POLYGON ((216837.0968892581 1400996.590603376...</td>
      <td>1.588427e+08</td>
    </tr>
  </tbody>
</table>
<p>391 rows × 29 columns</p>
</div>




```python
gdf_pa['area_sqm'].max()
```




    11842628138.924446




```python
gdf_pa.loc[gdf_pa['area_sqm'] == 11842628138.924446]
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
      <th>wdpaid</th>
      <th>objectid</th>
      <th>wdpa_pid</th>
      <th>country</th>
      <th>sub_loc</th>
      <th>name</th>
      <th>orig_name</th>
      <th>desig</th>
      <th>desig_eng</th>
      <th>desig_type</th>
      <th>...</th>
      <th>mang_plan</th>
      <th>official</th>
      <th>is_point</th>
      <th>no_take</th>
      <th>no_tk_area</th>
      <th>metadata_i</th>
      <th>action</th>
      <th>area_sqkm</th>
      <th>geometry</th>
      <th>area_sqm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>385</th>
      <td>317297</td>
      <td>261751</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Palawan</td>
      <td>Palawan</td>
      <td>Game Refuge and Bird Sanctuary</td>
      <td>Game Refuge and Bird Sanctuary</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>1.177369e+06</td>
      <td>(POLYGON ((104599.1692952072 1148204.888813832...</td>
      <td>1.184263e+10</td>
    </tr>
  </tbody>
</table>
<p>1 rows × 29 columns</p>
</div>



Oh it makes sense. **Palawan** is the largest Protected Area in the Philippines.


```python
gdf_pa.loc[gdf_pa['area_sqm'] == 11842628138.924446].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1af5417f710>




![png](PA_analysis_files/PA_analysis_20_1.png)


### 3.) What is the smallest Protected Area?


```python
gdf_pa['area_sqm'].min()
```




    13613.293737011954




```python
gdf_pa.loc[gdf_pa['area_sqm'] == 13613.293737011954]
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
      <th>wdpaid</th>
      <th>objectid</th>
      <th>wdpa_pid</th>
      <th>country</th>
      <th>sub_loc</th>
      <th>name</th>
      <th>orig_name</th>
      <th>desig</th>
      <th>desig_eng</th>
      <th>desig_type</th>
      <th>...</th>
      <th>mang_plan</th>
      <th>official</th>
      <th>is_point</th>
      <th>no_take</th>
      <th>no_tk_area</th>
      <th>metadata_i</th>
      <th>action</th>
      <th>area_sqkm</th>
      <th>geometry</th>
      <th>area_sqm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>127</th>
      <td>305548</td>
      <td>296741</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Binlanan</td>
      <td>Binlanan</td>
      <td>Restricted Area</td>
      <td>Restricted Area</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>1</td>
      <td>All</td>
      <td>0.0137</td>
      <td>751</td>
      <td>None</td>
      <td>1.361375</td>
      <td>(POLYGON ((542485.3480078606 1102893.672105119...</td>
      <td>13613.293737</td>
    </tr>
  </tbody>
</table>
<p>1 rows × 29 columns</p>
</div>




```python
gdf_pa.loc[gdf_pa['area_sqm'] == 13613.293737011954].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1af5417f5c0>




![png](PA_analysis_files/PA_analysis_24_1.png)



```python
gdf_pa.head()
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
      <th>wdpaid</th>
      <th>objectid</th>
      <th>wdpa_pid</th>
      <th>country</th>
      <th>sub_loc</th>
      <th>name</th>
      <th>orig_name</th>
      <th>desig</th>
      <th>desig_eng</th>
      <th>desig_type</th>
      <th>...</th>
      <th>mang_plan</th>
      <th>official</th>
      <th>is_point</th>
      <th>no_take</th>
      <th>no_tk_area</th>
      <th>metadata_i</th>
      <th>action</th>
      <th>area_sqkm</th>
      <th>geometry</th>
      <th>area_sqm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>840</td>
      <td>25575</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mount Apo</td>
      <td>Mount Apo</td>
      <td>Marine Reserve</td>
      <td>Natural Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>63136.257133</td>
      <td>(POLYGON ((757315.6873592746 784859.0996727811...</td>
      <td>6.323164e+08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>841</td>
      <td>50723</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mounts Banahaw - San Cristobal</td>
      <td>Mounts Banahaw - San Cristobal</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>11326.920501</td>
      <td>(POLYGON ((331404.319340662 1548677.954027078,...</td>
      <td>1.133351e+08</td>
    </tr>
    <tr>
      <th>2</th>
      <td>842</td>
      <td>50759</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Mayon Volcano</td>
      <td>Mayon Volcano</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>620</td>
      <td>None</td>
      <td>5769.067448</td>
      <td>(POLYGON ((576840.7380170472 1470612.506661169...</td>
      <td>5.769406e+07</td>
    </tr>
    <tr>
      <th>3</th>
      <td>844</td>
      <td>50915</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Aurora Memorial Park</td>
      <td>Aurora Memorial Park</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>491</td>
      <td>None</td>
      <td>7090.903882</td>
      <td>(POLYGON ((327017.1498461195 1742519.809907154...</td>
      <td>7.095521e+07</td>
    </tr>
    <tr>
      <th>4</th>
      <td>845</td>
      <td>50917</td>
      <td>0</td>
      <td>PHL</td>
      <td>Not Reported</td>
      <td>Hundred Islands</td>
      <td>Hundred Islands</td>
      <td>Ethniko Parko</td>
      <td>National Park</td>
      <td>National</td>
      <td>...</td>
      <td>None</td>
      <td>1</td>
      <td>0</td>
      <td>None</td>
      <td>0.0</td>
      <td>750</td>
      <td>None</td>
      <td>193.922129</td>
      <td>(POLYGON ((183787.5302234282 1793000.190379641...</td>
      <td>1.943867e+06</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>



### 4. Information on our PA Designations

The dataset also contains the kind of PA designation, if an area is a *National Park*, or a *Protected Landscape*, and so on. There are **49** designation types from our dataset.


```python
gdf_pa['desig_eng'].nunique()
```




    49




```python
gdf_pa['desig_eng'].unique()
```




    array(['Natural Park', 'National Park', 'UNESCO-MAB Biosphere Reserve',
           'Protected Landscape/Seascape', 'Natural Monument',
           'Watershed Forest Reserve', 'Natural Biotic Area',
           'Protected Landscape', 'Natural Monument - Buffer Zone',
           'Game Refuge', 'Wilderness Area', 'Wilderness Sanctuary',
           'Game Refuge and Bird Sanctuary', 'National Marine Park',
           'Sanctuary', 'Marine Reserve', 'World Heritage Site',
           'Managed Natural Resource and Protected Area',
           'Wetlands of International Importance (Ramsar)',
           'Protected Seascape', 'Marine Sanctuary',
           'Marine Reserve with Fish Sanctuary', 'Fish Sanctuary',
           'Marine Park and Sanctuary', 'Fishery Refuge and Sanctuary',
           'Seagrass and Fish Sanctuary', 'Protected Landscape - Buffer Zone',
           'Marine Fish Sanctuary', 'Restricted Area', 'Seagrass Sanctuary',
           'Marine Park', 'Fish sanctuary', 'Reserve',
           'Marine Refuge and Sanctuary', 'Marine Santuary',
           'Marine Sanctuary and Reservation Area', 'Fiah Sanctuary',
           'Marine Sancturay', 'Resource Reserve',
           'Protected Landscape/Seascape - Buffer Zone',
           'Mangrove Forest Reserve', 'Natural Biotic Area - Buffer Zone',
           'Resource Reserve - Buffer Zone', 'Natural Park - Buffer Zone',
           'National Park - Buffer Zone',
           'Watershed Forest Reserve - Buffer Zone',
           'Collaborative Fishery Management Area', 'ASEAN Heritage',
           'Marine Protected Area'], dtype=object)



We are curious if which among these have the most number of PAs. Let us put it in a chart!


```python
gdf_pa.columns
```




    Index(['wdpaid', 'objectid', 'wdpa_pid', 'country', 'sub_loc', 'name',
           'orig_name', 'desig', 'desig_eng', 'desig_type', 'iucn_cat', 'marine',
           'rep_m_area', 'rep_area', 'status', 'status_yr', 'gov_type',
           'mang_auth', 'int_crit', 'mang_plan', 'official', 'is_point', 'no_take',
           'no_tk_area', 'metadata_i', 'action', 'area_sqkm', 'geometry',
           'area_sqm'],
          dtype='object')




```python
gdf_pa['mang_auth'].unique()
```




    array(['Not Reported', 'Philippine Tourism Authority\r\n',
           'Community Environment and Natural Resources Office (CENRO), Department of Environment and Natural Resources (DENR)',
           'Protected Area Management Board (PAMB)\r\n',
           'Department of Environment and Natural Resources (DENR), Palawan Council for Sustainable Development (PCSD), Department of Environment and Natural Resources (DENR)\r\n\r\n\r\n',
           'Department of Environment and Natural Resources(DENR)',
           'Protected Area Management Board for Sagay Marine Reserve (PAMB-SMR)\r\n',
           'Protected Area Management Board (PAMB)',
           'Department of Environment and Natural Resources (DENR)\r\n',
           'Department of Environment and Natural Resources (DENR), Protected Area Office (PAO) of DENR Region 9\r\n\r\n',
           'Municipal Fisheries and Aquatic Resource Management Council (MFARMC)',
           'Antipolo Fisherfolks Association (AFA)',
           "Agan-an Small Fishermen's Association, Bantay Dagat",
           'Apalan Fisherfolks Association (APFA)', "Arthur's Place Resort",
           'Nagkahiusang Mananagat sa Asinan (NAMASIN)',
           'Local Government Unit of Bacon',
           "Bagasawe Fishermen's and Farmers Association (BAFFA)",
           'Municipal Local Government Units (MLGU), Barangay Local Government Units (BLGU)',
           'PAMB',
           "Balicasag Island United People's Organization for Progress (BIUPOP), Barangay Local Goverment Unit (BLGU)",
           'Dalaguete Coastal Police',
           "Barangay officials, Banban Fishermen's Association",
           'Barangay Council of Bantigue',
           "San Rafael Cabacongan Fishermen's Association",
           "Basdiot Fishermen's Association (BFA)",
           'Samahan Tungo sa Kaunlaran ng Santo Tomas (STKST), Batalang Bato Management Committee (BBMC)',
           "United Batasan Fishermen's Association (UBFA), Barangay Local Government Unit (BLGU)",
           "Bato Fishermen's Association (BFA)",
           'Fisherfolks Association\r\nLocal Government Unit (LGU) of Alabel\r\n',
           "Biasong Young Fishermen's Association",
           'Kapunungan sa mga Mananagat nga Nagdumala ug Sanktwaryo sa Bilang-bilangan (KAMANASABI)',
           'Bilang-bilangan Fishermen Association,\r\nLocal Government Unit of Getafe',
           'Bil-isan Fishermen Association,\r\nBarangay officials,\r\nMunicipal officials,\r\nProvincial officials,\r\n',
           'Nagkahiusang Mananagat sa Polo',
           'Samahang Mangingisda sa Binlod (SMB)',
           'San Vicente Bantay Dagat Association (SVBDA)',
           "Bogo Fishermen's Association (BOFA)",
           'Barangay Local Government Unit (BLGU)',
           "Botigues Fishermen's Association (BFA)",
           "Bonbon Fishermen's Association",
           "Bulasa Fishermen's-Farmers Association (BUFFA)",
           "Bunga Mar Fishermen's Association",
           "Barangay Buntis Bantay Dagat Small Fishermen's Association",
           "Busogon Fishermen's and Farmers Association",
           'Nagkahiusa Gamay Manangat sa Cabacongan (NAGMACA)',
           "Cabantian Fishermen's Association",
           "Municipal Government of Anda,\r\nMunicipal Agriculture and Fisheries Office,\r\nBarangay Council,\r\nBureau of Fisheries and Aquatic Resources,\r\nPeople's organization",
           'Cagawasan Mangrove Planters Association (CAMPA)',
           "Camboang Fishermen's Association (CFA)",
           "Campao Occidental Fishermen's Association",
           "Municipal Local Government Unit (MLGU), Campuyo Fishermen's Organization (CFO)",
           "Bantay Dagat\r\nCangmating Fishermen's Association (CAFA),\r\nSt. Joseph Fishermen's Association",
           'Bantay Dagat Commission', "Cantagay Fishermen's Association",
           'Municipal Local Government Unit (MLGU), Municipal Agriculture and Fisheries Office, Barangay Council of Carot, Bureau of Fisheries and Aquatic Resources Management Council (BFARMC)',
           'Dalaguete Coastal Police (DACOP)',
           "Casay Fishermen's Association (CAFA)", 'Fish Wardens',
           'Dive 7000 Resort',
           "Colase Fishermen's Association, Baryohanong Nagpakabana sa Colase (BANAC)",
           "Municipal officials, Corte-Baud Fishermen's Association, Barangay officials",
           'Nagkahiusang Pundok nga mag-amping sa Kalikupan (NPMSK',
           'Alcoy Kingfishers Organization (AKO)',
           'Barangay Local Government Unit (BLGU), Municipal Local Government Unit (MLGU), Danao Fishermen Association',
           'Fisherfolks Association, Barangay Local Government Unit (BLGU)',
           "Doong Fishermen's Association (DOFA)",
           'Bacong Bacong Fisherfolk Association, Inc.',
           'Barangay Fisheries and Aquatic Resource Management Council (BFARMC), Management Council\r\n\r\n',
           'Guinacot Community Resource Management Organization\r\n',
           "Guiwanon Fishermen's-Farmers Association (GUFFA), \r\nPoblacion Fishermen's Association (PFA)\r\n",
           'Barangay Local Government Units of Aplaya and Paligue, Fisheries and Aquatic Resource Management Council (FARMC), Municipal Local Government Unit (MLGU)\r\n\r\n\r\n',
           'Jandayan Island Coastal Development Council, Barangay Council, Kapunungan sa Nagkahiusang Mananagat ug Lumulupyo sa Handumon (KANAGMALUHAN)\r\n\r\n\r\n',
           'Panaytayon, Matabao, Pandan Management Council\r\n',
           'Bantay Dagat\r\n',
           "Dakong Baybay Fishermen's and Farmers Association (DBFFA)\r\n",
           'Nagpakabanang Mananagat sa Hinablan (NAMAHIN)\r\n',
           "Iniban Fishermen's Association\r\n",
           'Provincial Local Government Unit (PLGU), Municipal Local Government Unit (MLGU), Barangay Local Government Unit (BLGU)\r\n\r\n\r\n',
           'Jagolia Fishermen Association, Local Government Unit of Getafe\r\n\r\n',
           'Barangay Council, Jandayan Norte Fishermen Association\r\n\r\n',
           'Barangay Local Government Unit (BLGU)\r\n',
           "Kinawahan Young Fishermen's Association\r\n",
           "LABUKA Fishermen's Association\r\n",
           'Nagkahiusang Mananagat sa Lambog (NAGMALAMBO)\r\n',
           "Langtad Fishermen's Association (LAFA)\r\n",
           "Larapan Fishermen's Association, Barangay Local Government Unit (BLGU)\r\n\r\n",
           "Lawis Fishermen's and Farmers Association, Barangay Local Government Unit (BLGU), Municipal Local Government Unit (MLGU)\r\n\r\n\r\n",
           "Lawis Inabangmon's Livelihood and Fishermen's Association (LILFA), \r\n",
           'Legaspi Fisherfolks Association (LEFA)\r\n',
           "Municipal Local Government Unit (MLGU), Liboron Farmers and Fishermen's Association, Barangay Local Government Unit (BLGU)\r\n\r\n\r\n",
           'Lomboy Farmers, Fishers and Carpenters Association, Municipal Local Government Unit (MLGU)\r\n (LFFCA), Barangay Local Government Unit (BLGU), Bohol Integrated Development Foundation (BIDEF)\r\n\r\n\r\n',
           'Municipal Local Government Unit (MLGU), Barangay Local Government Unit (BLGU), Provincial Local Government Unit (PLGU)\r\nLooc Fishermen Association\r\n\r\n\r\n',
           'Environmental Legal Assistance Center - Bohol, Municipal Local Government Unit (MLGU)\r\n\r\n',
           'Luyang Community Based Resource Management Association (LCBRMA)\r\n',
           'Barangay Council of Luyong-baybay\r\n',
           "Macaas Fishermen's Association\r\n",
           'Barangay Local Government Unit (BLGU), Municipal Local Government Unit (MLGU), Madangog Marine Land Association (MAMLA)\r\n\r\n\r\n',
           "Madridejos Small Fishermen's Association(MASFA), Owners of Villanueva's Beach Resort \r\n \r\n",
           "Palanas Fishermen's Association\r\n",
           "Magtongtong Farmers and Fishermen's Association (MFFA), Barangay Local Government Unit (BLGU)\r\n\r\n",
           'Municipal Local Government Unit (MLGU)\r\n',
           'Barangay Local Government Unit (BLGU) of Baybay and Bulacan, Municipal Local Government Unit (MLGU), Fisheries and Aquatic Resource Management Council (FARMC)\r\n\r\n\r\n',
           "Barangay Local Government Unit (BLGU), Mantatao Fishermen's Association (MAFA), Municipal Local Government Unit (MLGU)\r\n\r\n\r\n",
           'Sanctuary Management Board (SMB), Samahan ng Maliliit na Mangingisda ng Buanavista (SAMMABU) \r\n\r\n',
           "Masaplod Norte Fishermen's Association\r\n",
           'Barangay Management Committee\r\n',
           'Pundok sa Mananagat nga Nagpakabana (PUMANA)\r\n',
           'Naatang Community-Based Resource Management\r\n',
           'Nahawan Fishermen Organization\r\n', 'Barangay Council\r\n',
           'Mendoza-Ca�ete (MenCa) Development Corporation, Barangay Gilutongan, Municipality of Cordova\r\n\r\n\r\n',
           "Nausok Fishermen's Association\r\n",
           'Municipal Government of Boljoon\r\n',
           "Olang Fishermen's Association\r\n",
           'Barangay Local Government Unit (BLGU) of Piape, Municipal Local Government Unit (MLGU), Fisheries and Aquatic Resource Management Council (FARMC)\r\n\r\n\r\n\r\n',
           'Pamilacan Island Dolphin and Whale Watching Organization, `Bantay Dagat` Fish Wardens, Barangay Council\r\n\r\n\r\n',
           'Pandanon Mananagat Sanktwaryo Organisasyon (PAMASO), Barangay Local Government Unit (BLGU)\r\n',
           'Pangdan Marine Sanctuary Association (PAMASA), Pangdan barangay council\r\n\r\n',
           'Bohol Integrated Development Foundation (BIDEF)\r\n',
           "Pasil Fisherfolk's Association",
           'Panas, Cambuyao, Pangpang Fisherfolk Association (PCPFA), Barangay Local Government Unit (BLGU), Municipal Local Government Unit (MLGU)\r\n\r\n\r\n',
           "Patao Small Fishermen's Association (PASFA)\r\n",
           'Nagkahiusang Gagmayng Mananagat sa Pinamgo (NAGAMAPI)\r\n',
           "Lupa Fishermen's Association\r\n",
           "Poblacion Fishermen's Association\r\n",
           "Lupa United Fishermen's Association (LUFA)\r\n",
           "Poblacion Fishermen's Association (POFA)\r\n", 'Fish Wardens\r\n',
           "Tayabas Reef Fishermen's Organization (TRFO)\r\n",
           "Albaguen Fishermen's Association \r\n",
           "Pulang Yuta Fishermen's Association\r\n",
           "Saavedra Fishermen's Association (SFA)\r\n",
           'Kapunungan sa Mangingisda sa San Isidro, Barangay Local Government Unit (BLGU), Municipal Local Government Unit (MLGU)\r\n\r\n\r\n',
           'Lupong Tagapangasiwa ng Kapaligiran',
           "Sandugan Fishermen's Association\r\n",
           "Baha-baha Fishermen's Association\r\n",
           "Sta. Cruz Fishermen's Association\r\n",
           "Sta. Filomena Fishermen's Association (SAFFA)\r\n",
           'Sto. Ni�o United Resource Protectors (SURP)\r\n',
           'Fisheries and Aquatic Resource Management Council (FARMC), Municipal Local Government Unit (MLGU), Barangay Local Government Unit (BLGU) of Sto. Ni�o and Basiawan\r\n\r\n\r\n',
           "Panitugan Fishermen's and Farmers Association (PAFFA)\r\n",
           'Oslob Fish Wardens Association and Brgy. Bancogon\r\n',
           'Nagkahiusang Gagmayng Mananagat ug Mag-uuma sa Talisay, Municipal Local Government Unit (MLGU), Barangay Local Government Unit (BLGU)\r\n\r\n\r\n',
           'Nagkahiusang Mananagat sa Talo-ot (NAGMATA)\r\n',
           "Tambongon United Fishermen's and Farmers Association (TUFFA)\r\n",
           'Barangay Council, Bantay Dagat\r\n\r\n',
           'Taongon Can-adam Fisherfolk Association\r\n',
           'Tayong Occidental Fishers and Fish Sanctuary Organization (TOFFSO)\r\n',
           'Tayong Oriental Fishers and Fish Sanctuary Organization (TOFFSO)\r\n',
           'Bantay Dagat Commission\r\n',
           "Tubod Mar Fishermen's Association\r\n",
           "Marine Management Committee, Tubod Fishermen's Association\r\n\r\n",
           "Banagbanak Fishermen's Association\r\n",
           'Tulapos Marine Management Committee\r\n',
           'Kapunungan sa Mananagat sa Tulic (KAMATU)\r\n',
           'Samahang Pangkanluran ng Sto. Tomas, Inc.',
           'Victoria Community Based Resource Management Association (VCBRMA)\r\n',
           "Tambobo Fishermen's Association",
           "Banacon Island Fishermen's Association",
           'Barangay Management Committee',
           "Valencia Fishermen's Association\r\n",
           'Protected Area Management Board (PAMB), Department of Environment and Natural Resources (DENR)\r\n\r\n',
           'Barangay Fisheries and Aquatic Resource Management Council (BFARMC)\r\n',
           'Department of Environmental and Natural Resources (DENR)\r\n',
           'Department of Environment and Natural Resources (DENR), Protected Area Office (PAO)',
           'Protected Area Office (PAO)\r\nDepartment of Environmental and Natural Resources (DENR)',
           'Department of Environment and Natural Resources (DENR), Protected Area Management Board (PAMB)\r\n\r\n',
           'Protected Area Office (PAO)\r\n', 'SAMMANASEKAP, SIKAT',
           'Apo Reef Natural Park (ARNP) Protected Area Office (PAO) and Protected Area Management Board (PAMB) under the Department of Environment and Natural Resources (DENR)'],
          dtype=object)




```python

```

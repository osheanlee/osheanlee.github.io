+++
author = "Oshean Lee Garonita"
categories = ["forests", "python", "earth", "protected area"]
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
# Import modules
import pandas as pd
import geopandas as gp
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
```

#### Data Cleaning

```python
# Load geodata
# Better practice is to re-project files in your GIS software (just to make things easier)
gdf_pa = gp.read_file(insert_directory_here)
gdf_phl = gp.read_file(insert_directory_here)
gdf_pa.plot()
```

<center>
	<img src="https://github.com/osheanlee/osheanlee.github.io/tree/master/static/img/2019/09/PA_analysis_3_1.png" border="0">
</center>

Seems that we have a problem in our PA dataset, so I opened it in QGIS and found out that `wdpaid = 305980` is the "outlier". Since the data is opensource, I don't like to determine anymore if where in Philippines should this PA be located. I'll just drop it. (Don't try this when you are working with your work datasets! This is just for practice purposes.

```python
gdf_pa['wdpaid'].loc[gdf_pa['wdpaid'] == 305980]
```

Using the code above, we were able to determine the index number of the row that we want to remove, which is **252**

```python
gdf_pa = gdf_pa.drop(252)
```

To check the outlier if it's still there, we plot it again.

```python
gdf_pa.plot()
```
<center>
	<img src="https://github.com/osheanlee/osheanlee.github.io/tree/master/static/img/2019/09/PA_analysis_7_1.png" border="0">
</center>

## Data Dive (Deeper)

### 1.) How many % land area are Protected Areas in the Philippines?

It seems that some of the PAs are intersecting with each other. Our calculation will be wrong if we would compute for the area of our PAs per row as it will also compute the intersecting areas. To fix this, we will aggregate them via `dissolve`.

```python
gdf_pa_dissolve = gdf_pa.dissolve(by='country')
gdf_pa_dissolve['area_sqm'] = gdf_pa_dissolve['geometry'].area
gdf_phl['area_sqm'] = gdf_phl['geometry'].area
gdf_pa_dissolve['area_sqm']/sum(gdf_phl['area_sqm']) * 100
```

Running the code above will tell us that **19% of the Philippines' land area are our protected areas. It seems really low! How I wish the government could identify more proteced areas.

### 2.) What is the largest Protected Area (in terms of land area)?

```python
gdf_pa['area_sqm'] = gdf_pa['geometry'].area
gdf_pa['area_sqm'].max()
```

The output of the code above gives us 11842628138.924446, so we just need to locate it:

```python
gdf_pa.loc[gdf_pa['area_sqm'] == 11842628138.924446].plot()
```

Can you guess where this is? 

<center>
	<img src="https://github.com/osheanlee/osheanlee.github.io/tree/master/static/img/2019/09/PA_analysis_20_1.png" border="0">
</center>	

### 3.) 3.) What is the smallest Protected Area?

```python
gdf_pa['area_sqm'].min()
```

This results to 13613.293737011954, then again we locate it.

```python
gdf_pa.loc[gdf_pa['area_sqm'] == 13613.293737011954].plot()
```

This one's hard to guess, so the name of this PA is **Binlanan** (weirdly) located inside the Tanon Strait in Cebu.
<center>
	<img src="https://github.com/osheanlee/osheanlee.github.io/tree/master/static/img/2019/09/PA_analysis_20_1.png" border="0">
</center>

### 4. Information on our PA Designations

The dataset also contains the kind of PA designation, if an area is a *National Park*, or a *Protected Landscape*, and so on. There are **49** designation types from our dataset.

```python
gdf_pa['desig_eng'].nunique()
```


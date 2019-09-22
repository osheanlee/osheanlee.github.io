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
gdf_pa = gp.read_file(insert_directory_here)
gdf_phl = gp.read_file(insert_directory_here)
```

```python
gdf_pa.plot()
```

<center>
	<img src="https://github.com/osheanlee/osheanlee.github.io/tree/master/static/img/2019/09/PA_analysis_3_1.png" border="1">
</center>
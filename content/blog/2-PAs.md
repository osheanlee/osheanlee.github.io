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
	<a href="https://imgbb.com/"><img src="https://i.ibb.co/BrRyDrn/PA-analysis-3-1.png" alt="PA-analysis-3-1" border="0"></a>
</center>

Seems that we have a problem in our PA dataset, so I opened it in QGIS and found out that `wdpaid = 305980` is the "outlier". Since the data is opensource, I don't like to determine anymore if where in Philippines should this PA be located. I'll just drop it. (Don't try this when you are working with your work datasets! This is just for practice purposes.

```python
gdf_pa['wdpaid'].loc[gdf_pa['wdpaid'] == 305980]
```

Using the code above, we were able to determine the index number of the row that we want to remove, which is **252**.

```python
gdf_pa = gdf_pa.drop(252)
```

To check the outlier if it's still there, we plot it again.

```python
gdf_pa.plot()
```
<center>
	<a href="https://imgbb.com/"><img src="https://i.ibb.co/kGkdZPr/PA-analysis-7-1.png" alt="PA-analysis-7-1" border="0"></a>
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

Running the code above will tell us that **19% of the Philippines** land area are our protected areas. It seems really low! How I wish the government could identify more proteced areas.

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
	<a href="https://imgbb.com/"><img src="https://i.ibb.co/QYgyChz/PA-analysis-20-1.png" alt="PA-analysis-20-1" border="0"></a>
</center>	

### 3.) What is the smallest Protected Area?

```python
gdf_pa['area_sqm'].min()
```

This results to 13613.293737011954, then again we locate it.

```python
gdf_pa.loc[gdf_pa['area_sqm'] == 13613.293737011954].plot()
```

This one's hard to guess, so the name of this PA is **Binlanan** (weirdly) located inside the Tanon Strait in Cebu.
<center>
	<a href="https://imgbb.com/"><img src="https://i.ibb.co/Lrs1KPJ/PA-analysis-24-1.png" alt="PA-analysis-24-1" border="0"></a>
</center>

### 4. Information on our PA Designations

The dataset also contains the kind of PA designation, if an area is a *National Park*, or a *Protected Landscape*, and so on. There are **49** designation types from our dataset.

```python
gdf_pa['desig_eng'].nunique()
```

```python
gdf_pa['desig_eng'].unique()
```

The code above produces an array of the values inside the `desig_eng` column:

```python
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
```

We are curious if which among these have the most number of PAs. Let us put it in a chart!

```python
desig_eng_bar = gdf_pa['desig_eng'].value_counts().to_frame().reset_index()
desig_eng_bar.rename({'index': 'Designation Type', 'desig_eng': 'Count'}, axis=1, inplace=True)

sns.set(style="whitegrid")
f, ax = plt.subplots(figsize=(6,15))

sns.set_color_codes("pastel")
sns.barplot(x="Count", y="Designation Type", data=desig_eng_bar,
            label="Number of PAs", color="b")
```

<center>
	<h5><b> Number of Protected Areas per Designation </b></h5>
	<a href="https://ibb.co/Cs8njQS"><img src="https://i.ibb.co/fGFC3HT/PA-analysis-bar-chart.png" alt="PA-analysis-bar-chart" border="0"></a>
</center>

Based on our chart above, we can say that we have a lot of Marine Sanctuaries in the Philippines. And we also have a lot of designated Watershed Forest Reserve!

Speaking of watershed, the Sustainable Forest Management Bill is currently filed in the legislative branch of the Philippine Government. I personally encourage everyone who's reading this to support the bill as it will provide a comprehensive watershed management system for all the watersheds in our country! To give support, you may contact [Haribon Foundation](https://haribon.org.ph).

### 5. Where are the Protected Areas Most Located?

I made a nifty tableau map (again! Because I love Tableau!) to show where most of the PAs are located. Can you guess where?

<div class='tableauPlaceholder' id='viz1569148695258' style='position: relative'>
	<noscript>
		<a href='#'><img alt=' ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Pr&#47;ProtectedAreas-Philippines&#47;DensityofProtectedAreasinthePhilippines&#47;1_rss.png' style='border: none' />
		</a>
	</noscript>
		<object class='tableauViz'  style='display:none;'>
			<param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' />
			<param name='embed_code_version' value='3' />
			<param name='site_root' value='' />
			<param name='name' value='ProtectedAreas-Philippines&#47;DensityofProtectedAreasinthePhilippines' />
			<param name='tabs' value='no' />
			<param name='toolbar' value='yes' />
			<param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Pr&#47;ProtectedAreas-Philippines&#47;DensityofProtectedAreasinthePhilippines&#47;1.png' />
			<param name='animate_transition' value='yes' />
			<param name='display_static_image' value='yes' />
			<param name='display_spinner' value='yes' />
			<param name='display_overlay' value='yes' />
			<param name='display_count' value='yes' />
		</object>
	</div>  

<script type='text/javascript'>                   
	var divElement = document.getElementById('viz1569148695258');
	var vizElement = divElement.getElementsByTagName('object')[0];
		vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';                    
	var scriptElement = document.createElement('script');                    
		scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    
		vizElement.parentNode.insertBefore(scriptElement, vizElement);                
</script>

## In Summary

Given that we only have ~more or less~ 20% of our forest areas, we must act! Even the simplest ways will help our country to sustain itself and provide natural resources for us without compromising the needs of the future generation.

Here are some ways on how you can help:

* **Volunteerism!** There are a lot of NGOs working for us to have a better environment. I would really recommend **Haribon Foundation** if you want to do some tree planting activities. Or if you love maps just like me, volunteer at [OpenStreetMap Philippines](https://fb.com/OSMPH) whenever they host Mapathons! Recently, I started to pledge to myself that I should join more on OSM-PH's Mapathons because I heavily rely on their data, so I guess it is just a matter of giving back.
* **Practice zero waste!** This one is a bit tricky/hard. I know that we live in a life of convenience, and it would be really hard to change. But change does not happen overnight. I admit that even myself sometimes cannot still reduce my use of plastics, but **promise** I do my best! You can start on the small things, such as rejecting straws and bringing your own mug.
* **Be informed of our environmental policies**. A good citizen knows what's happening in our country, so it would be best that we read existing laws and bills currently filed in our legislative branch. **Do not be that troll on social media who spouts nonsense. Practice critical thinking!**

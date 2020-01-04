+++
author = "Oshean Lee Garonita"
categories = ["environment", "clup", "python"]
date = "2020-01-01"
description = ""
featured = "2019/08/PPFP_CLUP.jpg"
featuredalt = "PPFP_CLUP"
featuredpath = ""
linktitle = ""
title = "Bridging the Gap v.2.0"
type = "post"

+++

<i>Fourth Entry</i>

# Happy New Year!

Remember my [first blog entry](https://oshean.netlify.com/blog/1-ppfpandlup) about Land Use Plans and Provincial Physical Framework Plans? One thing that was missing during that time is that I was not able to take the granularity up to the City/Municipal level because I lack the necessary skillset in data cleaning before. Also, the data that I used before was as of 2015.

But thanks to the people from the Philippine Environmental Planning community that I was able to get a [more recent data](http://hlurb.gov.ph/wp-content/uploads/2019/07/CLUP-Status-as-of-2nd-Quarter-2019.pdf), which was as of 2nd Quarter of 2019.
This dataset looks a lot better and cleaner compared to the previous one that I got. The older one has a really messy **date** column.

So then, presenting the **CLUP Status Monitoring Dashboard**! (Oops it looks a lot better in full screen! Click the full screen mode on the lower right of the dashboard)

**Simple instructions on how to use it**:

1. Hover your mouse on the map to see the ff: city/municipality, province, PSGC Code, date of approval of latest CLUP, and status of CLUP.
2. Check the legend on the upper right! For formulation means that the specific city or municipality does not have any CLUP at all.
3. Use filters at the top. You can also search a specific city/municipality; search bar is on the map.
4. The progress bar can also serve as filters. Just click on it. Will improve this later on. There's one action that I want to include there.

<div class='tableauPlaceholder' id='viz1577876620510' style='position: relative'>
  <noscript>
    <a href='#'>
      <img alt=' ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;CL&#47;CLUPMonitoringDashboard&#47;CLUPMonitoringDashboard&#47;1_rss.png' style='border: none' />
    </a>
  </noscript>
  <object class='tableauViz'  style='display:none;'>
    <param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' />
    <param name='embed_code_version' value='3' />
    <param name='site_root' value='' />
    <param name='name' value='CLUPMonitoringDashboard&#47;CLUPMonitoringDashboard' />
    <param name='tabs' value='no' />
    <param name='toolbar' value='yes' />
    <param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;CL&#47;CLUPMonitoringDashboard&#47;CLUPMonitoringDashboard&#47;1.png' />
    <param name='animate_transition' value='yes' />
    <param name='display_static_image' value='yes' />
    <param name='display_spinner' value='yes' />
    <param name='display_overlay' value='yes' />
    <param name='display_count' value='yes' />
    <param name='filter' value='publish=yes' />
  </object>
</div>

<script type='text/javascript'>
  var divElement = document.getElementById('viz1577876620510');
  var vizElement = divElement.getElementsByTagName('object')[0];
  if ( divElement.offsetWidth > 800 ) { vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';}
  else if ( divElement.offsetWidth > 500 ) { vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';}
  else { vizElement.style.width='100%';vizElement.style.height='1377px';}
  var scriptElement = document.createElement('script');
    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
  vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>

## How I Made the Dashboard (and other data janitorial stuff)

I got the data from HLURB, but as usual, the problem is that it's in PDF format. So I had to [convert](http://online2pdf.com) it into an excel file. But...it imposed more problems. The raw data of the PDF file seems to have it on repeat header rows, so I had to **manually** erase those rows one-by-one.

After so, I did the rest of the cleaning using python. You can check the code [here](https://github.com/osheanlee/data-analysis/blob/master/CLUP_Data_Cleaning_v2.ipynb).

But to summarize the steps on further cleaning the excel file:

1. I had to convert some of the columns to `string`, convert them to uppercase to make things easier, replace some weird names like `NAN`, remove values with parenthesis (huhu thank you RegEx super helpful), remove **weird extra spaces** which I suspect came from PDF conversion.

2. I had to separate Metro Manila from the excel file first. The reason for this is that the `shapefle` of the City/Municipality Admin Level (Level 3) aggregated Metro Manila into districts. Which is also a problem because the Barangay Admin Level (Level 4) aggregated the Manila City dataset into different districts (e.g. Tondo, Sta. Ana, etc.). That's basically what we do not need. We only need the **16 cities and 1 muncipality of Metro Manila**, nothing more and nothing less in terms of aggregation.

3. I merged the excel file and the city/municipality shapefile, but this has been a long process as well. Because for every merging that I did, I had to check if there are `null` values (i.e. values that did not match). Since the excel file -- or rather the PDF file -- did not contain any PSGC codes, I had to do the merging based on their names. Oh and let us not forget that there might be cities/municipalities with the same name but different provinces. So I had to write a code also for that. Going back to the null values, I had to make sure that all of the values should match. And thankfully after some trial-and-error, I was able to merge all of the values.

4. Oof but wait, we're not done yet because we still had to clean the Metro Manila dataset. This one's a bit easier already because we're only dealing with 17 LGUs. But basically I used the barangay dataset of Metro Manila that I got years ago, and had to dissolve them into city/municipality level, replaced the districts of Metro Manila to `CITY OF MANILA`. Once done, I merged the Metro Manila dataframe with its geodataframe counterpart.

5. Now that the Metro Manila and the rest of the Philippines datasets are clean already, I concatenated these 2 using `pd.concat`. (Pandas is such a powerful python library. I think I can't live without it already.) Then I just saved the concatenated files into a shapefile!

Once I'm done with the dataset, I loaded them to Tableau. For the dashboard, I chose two (2) charts: 1.) Categorical Map and 2.) Stacked Bar Charts. I just made a few interactions for the two especially on the click-and-action stuff so that it would be easier for those who will use.

## Some Fresh New EDAs

### 1. The provinces of Maguindanao and Lanao Del Sur have the most number of cities and municipalities who do not have CLUPs yet.

<a href="https://ibb.co/3MbstXg"><img src="https://i.ibb.co/0FLXPNH/EDA-For-Formulation-per-Province.jpg" alt="EDA-For-Formulation-per-Province" border="0"></a>

It might be unfair for them since there might be provinces who have fewer number of cities/municipalities, so what we're going to do is to normalize our data first.

### 2. We have a lot of outdated CLUPs, and this could be a future problem on how we use our land in the Philippines.

If you noticed the dashboard, we have a lot of areas still colored in <span style="color:yellow"> yellow </span>. Outdated CLUPs are a big problem as we would not know how to shape our land as a resource. Outdated CLUPs also mean that they are not anymore aligned with the national plans of our country - such as [railroads to be constructed in the future](https://www.rappler.com/newsbreak/iq/239702-new-railways-to-look-out-for).

Out of the 1,634 LGUs in our dataset, **1,037** LGUs have outdated CLUPs already. 

<center><a href="https://ibb.co/zf2pQzp"><img src="https://i.ibb.co/x5gWXNW/Chart-Number-of-Outdated-Updated-and-For-Formulation.jpg" alt="Chart-Number-of-Outdated-Updated-and-For-Formulation" border="0"></a>

*Too much yellowwwwwww* </center>
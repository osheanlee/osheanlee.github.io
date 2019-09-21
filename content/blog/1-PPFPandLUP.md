+++
author = "Oshean Lee Garonita"
categories = ["forests", "python", "environment"]
date = "2019-09-20"
description = ""
featured = "2019/08/PPFP_CLUP.jpg"
featuredalt = "PPFP_CLUP"
featuredpath = ""
linktitle = ""
title = "On Physical Framework and Land Use Plans: Let's Bridge the Gap!"
type = "post"

+++

<i>First Entry</i>


## Plans are powerful

We make plans for things that we want to achieve. It could be listing down our to-do list for the day, or writing
grocery list, or even just a simple reminder could be a plan. Plans give us a sense of purpose as to what we want to do in the future.

But let us try scaling it up. Have you ever wondered how our houses were made? Okay, we have architects and engineers for that; and yes! They also make plans before building stuff. But what about a community? Are plans needed to make a community?

If your answer is yes, then you're on the right track. If your answer is a no, then hope you learn something new! :)

Plans are already being used even before so that we could try to improve the lives of other people. In a community level, one plan that could help us on this is having a <u>Land Use Plan</u>. A lot of countries already practice having these plans in their respective jurisdictions, including our own Philippines. In fact, some Philippine [laws](http://hlurb.gov.ph/law-issuances/land-use-planning/) already mandate that all cities and municipalities should have a Comprehensive Land Use Plan (CLUP) while provinces should have a Provincial Physical Framework Plan (PPFP), and that they should be updated after a specified timeframe or life cycle (usually 10 years for CLUP and greater for PPFPs). But are all of these updated? And for Environmental Planners (EnPs), maybe we could help some communities who do not have yet!

## Analyzing the Data

I got the data from HLURB's [website](http://www.hlurb.gov.ph/wp-content/uploads/services/lgu/Approved%20CLUP.pdf). Based on the PDF file, the data is as of **2015**.

**DISCLAIMER**: I cannot guarantee the accuracy of the CLUP and PPFP approvals. For further information, it is still best to consult the HLURB and the Local Government Units (LGUs). This data analysis that I did is **meant to help** fellow Environmental Planners to get a glimpse of which areas would need for their plans to be updated. While I did not make a data viz for the names of the municipalities, I hope the numbers would help fellow Envi Planners to assist our communities in crafting their plans.

I made a simple analysis that could hopefully help as a jumpstart for planners on which areas to help. Based on my analysis, there are still
**1,302** cities and municipalities that would still need for the CLUPs to be updated as of <i>August 18, 2019</i>. On the other hand, there are **198** cities and municipalities who do not have CLUPs yet.

For provinces, there are **19** areas with no PPFPs, while **58** provinces would need to update their PPFPs. This is out of 79 provinces based on the data.

I made a dashboard to visualize which provinces need for their PPFPs to be updated, and how many CLUPs would we need to update per province.

<div class='tableauPlaceholder' id='viz1566751363857' style='position: relative'>
  <noscript>
    <a href='#'>
      <img alt=' ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;PP&#47;PPFPandCLUPforUpdating&#47;PPFPandCLUP&#47;1_rss.png' style='border: none' />
    </a>
  </noscript>
    <object class='tableauViz'  style='display:none;'>
      <param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' />
      <param name='embed_code_version' value='3' /> <param name='site_root' value='' />
      <param name='name' value='PPFPandCLUPforUpdating&#47;PPFPandCLUP' />
      <param name='tabs' value='no' /><param name='toolbar' value='yes' />
      <param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;PP&#47;PPFPandCLUPforUpdating&#47;PPFPandCLUP&#47;1.png' /> <param name='animate_transition' value='yes' />
      <param name='display_static_image' value='yes' />
      <param name='display_spinner' value='yes' />
      <param name='display_overlay' value='yes' />
      <param name='display_count' value='yes' />
    </object>
</div>                

<script type='text/javascript'>                    
  var divElement = document.getElementById('viz1566751363857');                   
  var vizElement = divElement.getElementsByTagName('object')[0];                    
    vizElement.style.minWidth='420px';
    vizElement.style.maxWidth='650px';
    vizElement.style.width='100%';
    vizElement.style.minHeight='587px';
    vizElement.style.maxHeight='887px';
    vizElement.style.height=(divElement.offsetWidth*0.75)+'px';                   
  var scriptElement = document.createElement('script');                    
  scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    
    vizElement.parentNode.insertBefore(scriptElement, vizElement);                
</script>

I will not include the list of provinces and municipalities that needs to be updated. If you wanna see them, you may wish to visit the link that I posted.

If you are an Environmental Planner residing in the Philippines, please coordinate with your [Philippine Institute of Environmental Planners Chapter](https://www.piep.org.ph/) or Local Government Unit to assist them.

Wanna know how I did my analysis? Sure! Happy to share the [code](https://github.com/osheanlee/data-analysis/blob/master/PPFP_and_CLUP_Analysis.ipynb) that I used for my analyses. If you can improve this, feel free to fork and send a pull request on my github account! :)

Bonus insight: Top 3 oldest CLUPs? 1st is in CAR Region, then top 2 is in Region VI, then top 3 is in CAR too.
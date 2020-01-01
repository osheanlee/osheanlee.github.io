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

## Happy New Year!

Remember my [first blog entry](https://oshean.netlify.com/blog/1-ppfpandlup) about Land Use Plans and Provincial Physical Framework Plans? One thing that was missing during that time is that I was not able to take the granularity up to the City/Municipal level because I lack the necessary skillset in data cleaning before. Also, the data that I used before was as of 2015.

But thanks to the people from the Philippine Environmental Planning community that I was able to get a [more recent data](http://hlurb.gov.ph/wp-content/uploads/2019/07/CLUP-Status-as-of-2nd-Quarter-2019.pdf), which was as of 2nd Quarter of 2019.
This dataset looks a lot better and cleaner compared to the previous one that I got. The older one has a really messy *date* column.

So then, presenting the *CLUP Status Monitoring Dashboard*!

<div class='tableauPlaceholder' id='viz1577875118394' style='position: relative'>
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
  </object>
</div>                

<script type='text/javascript'>                    
  var divElement = document.getElementById('viz1577875118394');
  var vizElement = divElement.getElementsByTagName('object')[0];
    if ( divElement.offsetWidth > 800 ) { vizElement.style.width='1366px';vizElement.style.height='795px';} 
    else if ( divElement.offsetWidth > 500 ) { vizElement.style.width='1366px';vizElement.style.height='795px';}
    else { vizElement.style.width='100%';vizElement.style.height='1327px';}
  var scriptElement = document.createElement('script');
    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
    vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>
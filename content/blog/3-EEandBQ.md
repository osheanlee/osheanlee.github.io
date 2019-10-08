+++
author = "Oshean Lee Garonita"
categories = ["bigquery", "gcp", "landsat", "sentinel"]
date = "2019-10-20"
description = ""
featured = "2019/09/Selangor_Malaysia.jpg"
featuredalt = "Selangor_Malaysia"
featuredpath = ""
linktitle = ""
title = "Accessing LandSat and Sentinel Imagery via GCP Public Dataset"
type = "post"

+++

<i>Third Entry</i>


## Preliminaries

Before proceeding, it is assumed that we have the following already:

1.) Access to [Google Bigquery](https://cloud.google.com/bigquery/)
2.) An account at the [USGS Earthexplorer](https://earthexplorer.usgs.gov)

_____________________________________________________________________________________


For most GIS people working on satellite imagery (like me), we would usually use the [Bulk Downloader Application](https://www.usgs.gov/media/images/earthexplorer-bulk-download-application-bda) for retrieval. While this is a good interface to download these stuff, I noticed that the download rate from here is really slow. This would be a bit of a blocker if we need lots of satellite imagery. Fortunately, the company where I am working right now uses [Google BigQuery](https://stories.thinkingmachin.es/coding-safely-in-the-cloud/) as its data warehouse. And another cool feature that it has? BQ has a [Public Dataset](https://cloud.google.com/bigquery/public-data/)! And public dataset means*FREE DATA* including *FREE* satellite imagery.

So the next question is: how do we access these free data?

## EarthExplorer.USGS + BigQuery = Powerrrrrrr

The EarthExplorer will be your friend if you want to look for a specific satellite imagery. You need to get the `ID/s` of the imagery that you wish to download. The ID format should look something like this: ` LC08_L1TP_113056_20150715_20170407_01_T1`

<iframe src="https://giphy.com/embed/hWjOWQLpmwLl1KYbut" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/usgs-hWjOWQLpmwLl1KYbut">via GIPHY</a></p>

List down all of the IDs that you wish to download in BQ. Once you're done, open your BQ and run a simple SQL code:

```
SELECT
  *
FROM
  `bigquery-public-data.cloud_storage_geo_index.landsat_index`
WHERE
  product_id IN ('LC08_L1TP_116047_20151227_20170331_01_T1',
    'LC08_L1TP_113056_20150715_20170407_01_T1',
    'LC08_L1TP_116048_20150720_20170406_01_T1',
    'LC08_L1TP_117055_20150727_20180205_01_T1',
    'LC08_L1TP_117055_20150828_20170405_01_T1',
    'LC08_L1TP_115053_20150830_20170405_01_T1',
    'LC08_L1TP_112054_20150825_20170405_01_T1',
    'LC08_L1TP_116050_20151024_20180205_01_T1',
    'LC08_L1TP_116049_20151024_20180205_01_T1',
    'LC08_L1TP_117048_20151031_20170402_01_T1',
    'LC08_L1TP_116048_20151109_20170402_01_T1',
    'LC08_L1TP_117049_20151116_20170402_01_T1',
    'LC08_L1TP_117048_20151116_20170402_01_T1',
    'LC08_L1TP_117047_20151116_20170402_01_T1',
    'LC08_L1TP_118054_20151123_20170401_01_T1',
    'LC08_L1TP_117050_20151202_20170401_01_T1',
    'LC08_L1TP_117049_20151202_20170401_01_T1',
    'LC08_L1TP_117048_20151202_20170401_01_T1',
    'LC08_L1TP_114056_20151213_20170401_01_T1',
    'LC08_L1TP_114055_20151213_20170401_01_T1',
    'LC08_L1TP_116055_20151227_20170331_01_T2',
    'LC08_L1GT_116054_20151227_20170331_01_T2',
    'LC08_L1TP_116052_20151227_20170331_01_T1',
    'LC08_L1TP_116049_20151227_20170331_01_T1',
    'LC08_L1TP_116048_20151227_20170331_01_T1')
```


---
layout: post
title:  "Satellite Image Processing"
date:   2019-06-04
excerpt: "Obtaining vegetation greenness using satellite image measurements of the Fraction of Absorbed Photosynthetically Active Radiation."
image: "http://www.wildfirerisktool.com/images/satellite_greenness.jpg"
author: "Scott George"
---

_Image credit: [Copernicus Global Land Service](https://land.copernicus.eu/global/products/fapar)_

Wildfires require fuel to burn and are more likely to ignite in areas of drier vegetation. Information about the coverage and greenness of local vegetation was an essential part of our modelling. 

## Satellite monitoring

We wanted to incorporate satellite data into our modeling because we wanted features that updated regularly and covered the entire California geography.  Easily available aerial data does not update regularly.  Satellite data has the further advantage that it is freely available.

## Choosing a Satellite

We began looking at data available from various earth monitoring satellites.  This [blog post by Antti Lipponen](https://medium.com/@anttilip/comparison-of-spatial-resolutions-in-satellite-images-3185963a2e96) was helpful in summarizing the resolution of various satellite monitoring missions. Both NASA and the ESA have easily accessible data. NASA’s data goes back further, but because its missions launched earlier, it necessarily used older imaging technology, and that translates to lower resolution.

We began looking into Sentinel-2 data from the European Space Agency (ESA) because the resolution was high and the update frequency was good.  Unfortunately the cost of high resolution was a [lack of production data](https://forum.step.esa.int/t/processing-level-2a-vs-2ap/12699%20https://sentinels.copernicus.eu/web/sentinel/news/-/article/upcoming-sentinel-2-level-2a-product-evolution), which only dates back to March of 2018, with pre-production availability to May 2017.

Ultimately we decided on [data from the PROBA-V mission](https://land.copernicus.vgt.vito.be/PDF/portal/Application.html#Home), which is [s]pecific to vegetation monitoring](https://www.esa.int/Our_Activities/Observing_the_Earth/Proba-V/About_Proba-V), has [regular global coverage](https://esamultimedia.esa.int/multimedia/publications/BR-310/offline/download.pdf), and produces various vegetation metrics.

## Vegetation Indices

We found a number of [different vegetation indices](https://land.copernicus.eu/global/products/) that could be used as the satellite data source.  We were interested in getting at vegetation flammability, which is not specifically  measured.   Most of the vegetation indices are [closely related](https://land.copernicus.eu/global/products/ndvi).  Of the candidates we chose [FAPAR](https://land.copernicus.eu/global/products/fapar) (Fraction of Absorbed Photosynthetically Active Radiation), which seemed like it should be highly correlated with vegetation health or dryness.  Per the prior link, “the FAPAR quantifies the fraction of the solar radiation absorbed by live leaves for the photosynthesis activity. Then, it refers only to the green and alive elements of the canopy. “

The ESA provides FAPAR at ⅓ km resolution.  New data becomes available on the 10th, 20th, and last day of each month, representing aggregation over the previous 10 day period.

## Data Processing

We chose to focus on the period 2016-2018 inclusive as a manageable chunk of wildfire history.  This still leaves a lot of data to process.  Each 10 day period of global FAPAR data creates a 2.5 GB file in netCDF format, corresponding to the entire globe.  For 3 years, with 36 images per year,  that means about 270 GB of data - manageable but time consuming.

The [official manual](https://land.copernicus.vgt.vito.be/PDF//image/Data_pool_manual.pdf) for the data source gives directions on downloading using the wget command line utility, although the option to subset by coordinate region did not seem to work for the FAPAR data.  I wanted to work locally so I opted to download images serially, subset the files to the relevant portion of California, and write them to python pickles.  Here is the [download and pickle script](https://github.com/jayzuniga/w210_Wildfire/blob/master/sat_process/download_sat.sh~).  I used a [very helpful tutorial by Chris Slocum](http://schubert.atmos.colostate.edu/~cslocum/netcdf_example.html) at Colorado State to help me figure out how to work with the netCDF format.

We had decided to use [S2 cells](http://www.wildfirerisktool.com/blog/s2_cell/) as the unit of aggregation for data.  The satellite data pixels, at 333m to an edge, are much smaller than the smallest S2 Cell we are using (Level 11), which is approximately 4km on an edge.  This ruled out the need to do fancy spatial processing or interpolation to fit the pixels into the S2 Cell grid.

The naive approach to aggregating the data would be to:
* Loop over the S2 Cells
* Determine for each pixel whether it belongs to that S2 Cell
* Assign the pixel to the cell
* Aggregate (minimum, maximum, median, mean, standard deviation) by S2 Cell

The problem with this approach is that it is _O(lnm)_ where _l_ is the number of S2 Cells, and the latitude - longitude grid is _n x m_.  Performing a single geospatial “in” operation is also not trivial.  The number of required operations gets big fast.

Instead, for each pixel, we generated the S2 cell id at each of the three S2 cell levels of interest (9, 10, 11).  This gets around doing any geospatial joins-- each pixel knows what S2 cells it fits within.  We then merged by S2 cell id with the target list of S2 Cells.  [We did this for each image](https://github.com/jayzuniga/w210_Wildfire/blob/master/sat_process/sat2_S2.py).  (Although we did not implement it, this could have been further sped up by building a table of pixel latitude, longitude, S2 cell id and storing it for later use, rather than building it each time).

![FAPAR](http://www.wildfirerisktool.com/images/FAPAR.jpg)

There was one more catch.  The netCDF data uses a Masked Array format to store the data.  If, for example, there’s cloud cover and the satellite couldn’t see the vegetation, it returns no data for that pixel.  They say the pixel or data point is masked. There is a second accompanying boolean array that says whether the data point was masked or not.  After sorting all the other library dependencies, and as of the time of data processing, [numpy support for masked array was having some issues](https://github.com/numpy/numpy/issues/7330) in numpy 1.16, so we wrote simple aggregation functions.  While these worked, they likely slowed the data processing versus a native numpy implementation.  A year of data took about 18 hours to aggregate on a 2 year old Dell desktop.

The final output is about 1.15 million records, indexed by a primary key of S2 Cell ID, and date, spanning 3 years for each of the roughly 10,600 S2 cells..  The features include some details on the latitude and longitude of the aggregate (which, due to averaging, might not exactly match the centroid of the S2 cell).  The data also gives statistics about how many pixels were masked (min, max, mean, median, standard deviation) and the actual value of interest, FAPAR (min, max, mean, median, standard deviation, count of unique values, count of non-null values, and total count).  Finally, we used backward fill to fill in the gaps between each released data set from ESA.



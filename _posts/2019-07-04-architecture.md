---
layout: post
title:  "Wildfire System Architecture"
date:   2019-07-04
excerpt: "Infrastructure for building a wildfire risk prediction system."
image: "http://www.wildfirerisktool.com/images/architecture.jpg"
author: "Jay Zuniga"
---

_Image credit: Gia Millinovich_

When our team started working together on the Wildfire Risk Project we identified a number of infrastructure requirements.

First, we needed a place where we could collaborate. Though we would work on separate pieces of data initially, the data needed to be merged for our machine learning models to consider all the data in its entirety.

Second, we needed a place that could scale. Some of our data was large, in particular our weather and satellite data - which became even larger when the data was merged and feature engineering introduced. In addition to scaling, it should be a system that we could start development on relatively quickly since we had a tight schedule to create our project.

Third, we needed a system that could easily integrate with web and visualization technologies, since we would need to output our predictions in a form that was easily accessible to both utility companies and governmental entities that deal with utility companies.

Lastly, there was the issue of cost. Being students mostly paying our own way, we wanted to minimize our out of pocket expenses to build the system.

For the most part, there was a strong push for us to use Google Cloud Platform (GCP) through individual credits given to the class. As GCP fulfilled all of our requirements, it turned out to be a good fit.

We ended up using three products:

* Google Cloud Storage - would store our raw files
* Google BigQuery - would be our BigData solution - storing our both our raw and individual tables and allowing query access to it
* Google Compute Engine - would become our solution for running our ML models

Our architecture can be seen in the following diagram.

![Pipeline](http://www.wildfirerisktool.com/images/pipeline.jpg)

The group evaluated one more GCP product, Data Studio, which could have been our visualization tool. However, we found its handling of geospatial data a bit more clumsy than Tableau - so we opted for the latter for our charts.

In GCP, the group opted for the use of multiple projects which enabled us to use our individual coupons for billing purposes as well as providing us the flexibility to develop assigned data sets independently until we were ready to integrate. Cross project access was provided using Identity and Access Management module, which allowed us to access each of the individual projects.

A compute engine instance accessed a feature table that consolidated all the key features and keys from the individual data sets. The compute engine instance ran our machine learning models, outputting a csv file that was uploaded to Tableau to visualize our predictions on fire risk.

Finally, our visualizations were uploaded to Tableau Public and embedded into our Jekyll based website hosted on GitHub Pages.

---
layout: post
title:  "Building A Dashboard"
date:   2019-07-06
excerpt: "Empowering utility companies using wildfire risk visualization."
image: "https://cassandraseney.github.io/wildfire/images/dashboard.jpg"
author: "Cassie Seney"
---

Designing a dashboard for a wildfire risk prediction tool involving satellite and weather data, transmission lines and spatial files, S2 cell technology, modelling algorithms and evaluation techniques could evolve into a product which is too technically complex to be useful.  We avoided this pitfall by taking a customer centric approach to designing the tool. By first understanding our customers needs, we were able to use our data driven predictions to provide answers to their questions.

Our customers are utility companies who are accustomed to making high stakes decisions involving transmission lines and fire risk using available weather data. A black box solution which produced a recommendation to shut down transmission lines would therefore not be appropriate. We had three goals:

Transparency. Allowing the customer to see not just the wildfire risk predictions but also the details of the data used to generate those estimates. 

Empowerment. Putting as much information as possible all in one place to make it easier for our customers to make better informed decisions.

Flexibility. Customizability to allow proprietary data to be incorporated into the tool.

As we started designing the first iteration of our product in [Lucid Charts](https://www.lucidchart.com) we found that the first two requirements would be best met with individual dashboards targeted to those purposes:

Wildfire Risk Dashboard. Providing an overall view of wildfire risk across California, with the ability to drill down to very localized predictions. This dashboard includes visualizations of the data used in our modelling. Our customers can see at a glance which areas are highest risk, cross reference against the weather, satellite and fuel moisture data and validate our conclusions with their own experience of similar conditions. They can also examine the data at previous dates to see how risk is changing or compare with previous years.

Transmission Line Dashboard. Focussing on areas where transmission lines are present to provide insight into the risk to utility companies. This dashboard provides the ability to view wildfire risk to specific utility companies, list highest risk transmission lines and drill down to the risk for specific transmission lines. Utility companies can identify which locations and transmission lines most need their attention and focus their resources.

Our dashboards were implemented in [Tableau](https://www.tableau.com/) which enabled us to create feature rich interfaces quickly and iterate through several versions of the product until we settled on one that best met the needs of our customers. The release versions of our [Wildfire Risk](https://public.tableau.com/profile/cassie6828#!/vizhome/WildfireRiskDashboard/Wildfiredatastory?publish=yes) and [Transmission Line](https://public.tableau.com/profile/cassie6828#!/vizhome/TransmissionLineDashboard/TransmissionLine?publish=yes) dashboards were uploaded to Tableau Public and the dashboards were embedded into our Jekyll based website on GitHub Pages.

User experience testing was performed via a Google form sent to volunteers to provide feedback into the components of the interface. Usability changes were incorporated into the product to replace controls with ones that were easier to use and improve labels and instructions. 

In future iterations of the product we would like to work with utility companies to include more proprietary information. The ability to associate transmission lines with particular population areas could provide insight into the cost of shutting down transmission lines to counterbalance the risk and provide a more complete picture. Information about the age and condition of utility equipment could be incorporated into the product as a factor to increase the risk of specific transmission lines. 

Our dashboards provide insight into wildfire risk and the conditions contributing to risk, as well as the impact on utility company transmission lines. We also have the flexibility to add more features as requested by our customers to better meet their needs.


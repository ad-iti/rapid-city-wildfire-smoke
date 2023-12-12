# Wildfires and the Agricultural Total Factor Productivity

#### Investigating the Relationship b/w Wildfire Smoke and Agricultural TFP in Rapid City, South Dakota

*UW M.S. Data Science DATA 512 Course Project*

Aditi Shrivastava

## Introduction

In Rapid City, South Dakota, the agricultural sector’s resilience solidifies its role as a key source of growth, acting not only as a huge economic driver, but also embodying a way of life for the farmers and ranchers working the land. What threatens to disrupt this economy is the stark (over 900%) increase in wildfires and wildfire smoke since 1960.

The Agricultural Total Factor Productivity (TFP) represents a critical metric in assessing the overall efficiency of agricultural production, encompassing various inputs such as labor, capital, and technology. In the context of this study, I focus on understanding how fluctuations in TFP, induced by exposure to wildfire smoke, can impact the agricultural landscape of Rapid City. Wildfire smoke, with its complex composition of particulate matter and chemical compounds, poses potential threats to crop health, soil quality, and the overall productivity of the agricultural sector. As the city's economic landscape is intricately tied to the success of its agricultural endeavors, a lowered TFP could lead to reduced yields, economic downturns for local farmers, and potential food supply chain disruptions. Moreover, the broader community may experience negative repercussions, including increased unemployment and a strain on resources. Understanding and quantifying these effects is pivotal for creating informed strategies to enhance the resilience of Rapid City's agricultural sector in the face of environmental challenges such as wildfire smoke. 

In this exploration, I comprehensively analyze how wildfire smoke, an increasingly pervasive environmental factor, influences the efficiency and output of the agricultural sector in the city of Rapid City, South Dakota. Specifically, I forecast the Agricultural TFP in Rapid City to the year 2050 based on a number of wildfire smoke related factors, and find that the agriculture sector will fail to meet productivity expectations in the year 2050 if wildfire smoke continues to increase at the same pace.

This analysis is heavily inspired by the work done in the paper [*Air pollution as a substantial threat to the improvement of agricultural total factor productivity: Global evidence*](https://www.sciencedirect.com/science/article/pii/S0160412023001150?via%3Dihub) by researchers Daxin Dong and Jiaxin Wang at the Institute of Western China Economic Research, Southwestern University of Finance and Economics in China. While the data and model bear no semblace to the results produced by Dong and Wang, the focus and motive behind this exploration was largely inspired by the aforementioned.

This repository contains the code, data, and documentation for all explorations and conclusions made in this project.

## Outline

#### Common Analysis

To begin this exploration, we analyze the prevalence of wildfire smoke in Rapid City, South Dakota via several steps of data acquisition, cleaning, and formatting detailed in ```/code/1.USGS_data_acquisition.ipnyb```. The resulting final USGS wildfire dataset is too large to store in this repo, but a subset of it is stored as ```/data/final_USGS_data_subset.csv```. The data is primarily sourced from the [Combined wildland fire datasets for the United States and certain territories, 1800s-Present (combined wildland fire polygons)](https://www.sciencebase.gov/catalog/item/61aa537dd34eb622f699df81) dataset, which was collected and aggregated by the [US Geological Survey](https://www.usgs.gov/). The dataset is relatively well documented, fire polygons are available in ArcGIS and GeoJSON formats. For this exploration, we specifically rely upon the large .JSON formatted file, which can be found in the combined .ZIP file on the website. The data is filtered to include only wildfires taking place during or after 1963, and within 1250 miles of Rapid City, South Dakota.

Next, we request data from the US Environmental Protection Agency (EPA) Air Quality Service (AQS) [API](https://aqs.epa.gov/aqsweb/documents/data_api.html), which is a historical API that provides data on the Air Quality Index (AQI) statistic. Information on how this statistic is calculate can be found [here](https://www.airnow.gov/sites/default/files/2020-05/aqi-technical-assistance-document-sept2018.pdf). The EPA reports that they only started broad based monitoring with standardized quality assurance procedures in the 1980's. Some [additional information on the Air Quality System can be found in the EPA FAQ](https://www.epa.gov/outdoor-air-quality-data/frequent-questions-about-airdata) on the system. We pull this data with the intention of comparing the previously computed smoke estimates for Rapid City to the AQI indices produced by the EPA on a year to year basis. Step by step process for the data requests are found in ```/code/2.EPA_data_acquisition.ipynb``` and the final EPA data is found in ```/data/final_EPA_data.csv```

We then create a model to find a unique wildfire smoke estimate, a single number to represent the effect and impact of the fire smoke that affects Rapid City every year. While smoke impact should generally be considered the health, tourism, economic or other social problems that result from the smoke, we create the annual estimate of wildfire smoke using the following model: 100*(Square miles burned)^2 / (Distance from Rapid City)

Then, we use this estimate to develop a predictive model based on the fire data and smoke estimate for Rapid City, SD. The model, a polynomial regression of degree 2, is then used to predict smoke estimates for every year for the next 25 years (i.e., 2024-2049), and the resulting fit and the associated confidence interval are both explored. Detailed description and parameters of both the estimate and the predictive model can be found in ```/code/3.smoke_estimate_viz.ipnyb```. Finally, we use all of the gathered data to illustrate some wildfire trends over time in Rapid City, South Dakota. These are detailed below, and code on how these visualization were produce can be found in ```/code/3.smoke_estimate_viz.ipynb```. The final outputs and their detailed descriptions can be found in ```/docs/reflection.pdf```.

#### Extension

The aforementioned general analysis serves as the basis for the agriculture industry-specific model that is produced in ```/code/4.agTFP_Model.ipnyb```. In this, we pull historical Agricultural TFP data in South Dakota to serve as training data for the predictive model from the [USDA](https://www.ers.usda.gov/data-products/agricultural-productivity-in-the-u-s/), which also details how the statistic is calculated. This data is used in an ARIMA model to forecast the expected AgTFP till the year 2050. We then use the previously gathered AQI data, as well as the previously gathered fire acreage and distance calculations, to create a [RandomForestRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html) model to predict the actual AgTFP till the year 2050 in response to changes in the three predictive variables (AQI, fire size, fire distance). To do so, the three variables were extrapolated into the future based on their previous behavior to act as input to the model between years 2021-2050.

#### Hypotheses
1. An increase in AQI should lead to a lower AgTFP based on the fact that it is calculated using the following pollutant levels, and is thereby harmful to agriculture and crops. 
- ground-level ozone (O3)
- particle pollution (also known as particulate matter, including PM2.5 and PM10)
- carbon monoxide
- sulfur dioxide
- nitrogen dioxide

2. A fire that is larger in size, as well as closer in distance to Rapid City, should lead to a lower AgTFP, given that smoke's influence on sunlight can act as a “hazy cloud cover”, potentially diminishing the light essential for photosynthesis, and posing a direct threat to crop productivity (Jeschke, Mark. [“Does Smoke from Wildfires Affect Crop Yields?”](https://www.no-tillfarmer.com/articles/12574-does-smoke-from-wildfires-affect-crop-yields)).

## Files 

#### ./data/

```USGS_Wildland_Fire_Combined_Dataset.json```
*This dataset is not stored in this repo because it is too large, but is detailed and linked below.*
- As per the USGS website, "These datasets were created by combining 40 different, published wildland fire data sources. Each one of these data sources has a different spatial scale, spatial resolution, and time period for their particular wildland fire dataset. The purpose of these new datasets is to combine these disparate wildfire datasets, using a common set of attributes, into a single set of polygons with a single fire boundary for each fire. This dataset is intended to create a more comprehensive fire dataset than the existing datasets while eliminating duplication of fire polygons and attributes" ([Combined wildland fire datasets](https://www.sciencebase.gov/catalog/item/61aa537dd34eb622f699df81)). 

```final_USGS_data.csv```
- This dataset is a filtered and flattened aggregation of the previous .JSON file. We extract only the relevant columns and convert the dataset into a dataframe to be saved as a .CSV. The data is filtered to include only wildfires taking place during or after 1963, and within 1250 miles of Rapid City, South Dakota.

```final_EPA_data.csv```
- We use the EPA AQI API to find monitoring stations near Rapid City, South Dakota, and then extract the average Air Quality Index for each station. The results are then averaged and stored to represent the AQI for each year.
- Note that AQI data is only available for Rapid City during and after the year 1985.

```agtfp_data.csv```
- Historical yearly Agricultural TFP levels for South Dakota pulled from the [USDA](https://www.ers.usda.gov/data-products/agricultural-productivity-in-the-u-s/) website.
- As per the website, "USDA's Economic Research Service (ERS) has constructed accounts for the farm sector consistent with a gross output model of production (see Shumway et al. 2017, Ball et al. 2016). Output is defined as gross production leaving the farm, as opposed to real value added. Inputs are not limited to labor and capital but include intermediate inputs as well. Intermediate goods produced and consumed within the farm are self-cancelling transactions and, therefore, do not enter either output or input accounts."
- Specific details on the how the AgTFP is calculated [here](https://www.ers.usda.gov/data-products/agricultural-productivity-in-the-u-s/methods/).

#### ./code/

```1.USGS_data_acquisition.ipnyb```
- This notebook details all code needed to produce ```/data/final_USGS_data.csv```.

```2.EPA_data_acquisition.ipynb```
- This notebook details all code needed to produce ```/data/final_EPA_data.csv```.
- Note that AQI data is only available for Rapid City during and after the year 1985.

```3.smoke_estimate_viz.ipynb```
- This notebook creates a unique smoke estimate for each year, and employs a quadratic regression model to predeict the future smoke estimate up to the year 2049. We also perform a brief visual exploration with all our gathered data-- specifically, we produce:
- a histogram showing the number of fires occurring every 50 mile distance from Rapid City
- a time series graph of total acres burned per year for the fires occurring within 1250 miles of Rapid City
- a time series graph containing the fire smoke estimate for Rapid City compared to the AQI estimate

```4.agtfp_prediction.ipynb```
- This notebook details all code needed to produce both the forecasting and predictive AgTFP models.
- All results from these findings are detailed in ```/docs/final_report.pdf```.
- Produces ```/docs/fig1.png```.

#### ./docs/

This directory contains the necessary .PDF submissions for this course project.

```reflection.pdf```
- Common analysis reflection. Contains all figures produced in ```/code/3.smoke_estimate_viz.ipynb```.

```final_report.pdf```
- Findings of this exploration. Results also detailed below.

```fig1.png```
- Titular figure produced via this analysis. Produced in ```/code/4.agtfp_prediction.ipynb```.

## Results
Blah blahdhbdaldh k bjhfdsb cdsjhpweid sdcbds osiwei cuewhc cnwcnd cdb ijweoeu. Copy from paper.

## Special Considerations and Limitations
- It takes ~1 hour to run all the notebooks in this repo and load all necessary data.
- The EPA only has AQI data available for Rapid City, SD during and after the year 1985.
- The predictive model (quadratic regression) produced in ```/code/3.smoke_estimate_viz.ipnyb```, forecast model (ARIMA) produced in ```/code/4.agTFP_model.ipnyb```, and predictive model (RandomForestRegressor) are all highly uncertain and likely not exactly representative of the next 25 years. Models of this nature are always prone to some degree of uncertainty, and their results should therefore be taken lightly.
- Data extrapolation always holds some level of risk. The three variables used in the predictive ensemble model were all extrapolated nearly 30 years into the future (methods detailed in ```/code/4.agTFP_model.ipnyb```) to then predict the associated AgTFP, which could never be 100% representative of what the true values of these variables will be.
- There is a varying level of granularity in the data sources used in this exploration, with the AgTFP metrics existing only at the state (South Dakota) level, rather than the city level. This of course implies that the results are likely more general to the larger area surrounding Rapid City. 
- Fire perimeter estimates are still a work in progress, and likely are not 100% accurate.
- It's important to consider the difference in the type of smoke produced by different types of wildfires (such as prescribed fires), which was not handled in this exploration.


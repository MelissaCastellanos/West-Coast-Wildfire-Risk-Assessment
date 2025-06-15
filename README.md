# Wildfire Risk Assessment: Predictive Modeling for High Wildfire Risk Zones in the West Coast 
## Introduction 
Washington, Oregon, and California, which comprise the West Coast of the United States, are no strangers to wildfires. Composed of large stretches of forests and grasslands, wildfires are a natural and recurring part of the region’s ecosystem [1][2]. Historically, these fires have served to clear out dead vegetation, preparing soil for new growth and generally maintaining the health of the area [1]. However, in recent years these fires have grown in frequency and intensity, transforming them from natural ecological processes into highly destructive events. The increase in frequency and intensity of wildfires in the West Coast, and across the United States, has been strongly linked to climate change [2]. As temperatures continue to rise and warmer, drier conditions become more common, the West Coast is expected to become more susceptible to destructive wildfires [2]. Although the primary objective should be to slow, and ultimately reverse climate change to reduce the occurrence and severity of these fires, predictive modeling can play a crucial role in mitigating wildfire outbreaks.

A predictive model is essentially a mathematical model which predicts outcomes by analyzing historical data. These models have been widely used to assess wildfire risk zones in academic research as well as by local and federal government agencies [3]. In this project, I develop a similar wildfire risk assessment by implementing a random forest algorithm to create a simple predictive model. Wildfire data from 1990 to 2025 were collected to provide a historical foundation for the models, while corresponding weather and population data were collected to identify potential trends and contributing factors. 

The following sections outline the development of the predictive model. Section II summarizes the rationale behind the data collected and analyzes hidden patterns and structures in the datasets. Section III explores the process of creating the model, and how it can be reproduced. While Section IV and V discuss the performance and results of the model created and conculdes how effective it was. 


## II. Data Description
Raw data encompassing wildfire records, daily weather observations, and county level population density across the West Coast were compiled from the following sources: 

#### Table I. Orginal Data Sources
|Dataset Name                             | Organization                                 | Visit Source                                                                                                         |
|-----------------------------------------|----------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| California Fire Perimeters (1950+)      | California Natural Resources Agency          |  [Source](https://gis.data.cnra.ca.gov/datasets/CALFIRE-Forestry::california-historical-fire-perimeters/about?layer=2)|
| ODF Fire Occurrence Data 2000 - 2022    | Oregon Department of Forestry                | [Source](https://data.oregon.gov/Natural-Resources/ODF-Fire-Occurrence-Data-2000-2022/fbwv-q84y/about_data)           |
| Washington Large Fires 1973 - 2023      | Washington Department of Natural Resources   | [Source](https://data-wadnr.opendata.arcgis.com/datasets/wadnr::washington-large-fires-1973-2023/about )              |
|Historical Weather Data                  | PRISM                                        | [Source](https://prism.oregonstate.edu/explorer/bulk.php)                                                             |
|County and Place Population              | US Census                                    | [Source](https://www.citypopulation.de/en/usa/places/washington/)                                                     |

### *A. Wildfire Data*
Wildfire data for the past 35 years was collected individually from each West Coast state to support the two models’ analyses  of wildfire trends. To ensure accuracy, all wildfire data was collected from official government sources. Data from California and Washington were available in GeoJSON format, providing the location and shape of each fire which occurred between 1990 and 2025. While data from the state of Washington included acreage burned in each fire, this information needed to be calculated for the state of California using geometry points. Data acquired from the state of Oregon, contained the same information regarding the location, shape, and acreage burned of each wildfire, but was provided in CSV format. All three datasets were combined into a larger, lower dimensional dataset which contained the following attributes: 

#### Table II. Lower Dimensional Historic Wildfire Data for Washington, Oregon and California
| Attribute   | Type               | Example Value | Description                                 |
|-------------|--------------------|----------------|---------------------------------------------|
| FIRE        | Nominal (string)   | "PALISADES"    | Fire name                                   |
| FIRE_NUM    | Numeric (integer)  | 1786           | Fire number assigned by state               |
| START_DATE  | Temporal (date)    | 1/7/2025       | First date fire was reported                |
| END_DATE    | Temporal (date)    | 1/31/2025      | Fire containment date                       |
| ACRES       | Numeric (real)     | 23751.21       | Acreage burned                              |
| YEAR        | Numeric (integer)  | 2025           | Year of fire                                |
| LAT         | Numeric (real)     | 34.0725        | Latitude of fire location                   |
| LONG        | Numeric (real)     | -118.574       | Longitude of fire location                  |
| STATE       | Nominal (string)   | "CA"           | West Coast state where fire occurred        |

To better understand the combined wildfire data, the distribution of acreage burned by state was visualized using a Kernel Density Estimate (KDE) plot. KDE plots are commonly used to approximate the underlying probability distribution of a dataset [4]. The KDE analysis for the wildfire data revealed that, although California experiences more fires overall, all three West Coast states exhibit a similar probability distribution of burned acreage (Figure 1). This means that despite differences in fire frequency between states, all three West Coast states have comparable expectations regarding the scale of future wildfires. Consequently, it can be assumed that predictive model results may be generalized across the entire West Coast Region.

![image](https://github.com/user-attachments/assets/ed1f696c-c1a6-487d-b346-87a7cc634700)

*Figure 1. Probability Distribution of Burned Acreage in West Coast Wildfire Incidents by State.*

### *B. Weather and Population Data*
Due to the documented link between the increase in frequency and intensity of wildfires and climate change [2], weather data were collected to support the historic wildfire data summarized above. Daily weather data for all three states were collected through the Parameter-elevation Regressions on Independent Slopes Model (PRISM), provided by the Northwest Alliance for Computational Science and Engineering. This dataset provided climate context for each wildfire occurrence, supplementing the wildfire location and dimension data with temperature, precipitation, and vapor-pressure deficit data. All attributes of the collected weather data are listed in the following table: 

#### Table III. PRISM Historic West Coast Weather Data
| Attribute        | Type                | Example Value | Description                                      |
|------------------|---------------------|----------------|--------------------------------------------------|
| Name             | Nominal (string)    | "Adams"        | County of weather station                        |
| Longitude        | Numeric (real)      | -118.5607      | Longitude of the weather station location        |
| Latitude         | Numeric (real)      | 46.9834        | Latitude of the weather station location         |
| Elevation        | Numeric (integer)   | 1598           | Elevation of the weather station                 |
| Date             | Temporal (date)     | 1990-01-01     | Date weather data was collected                  |
| ppt (inches)     | Numeric (real)      | 0.0            | Precipitation                                    |
| tmin (°F)        | Numeric (real)      | 25.7           | Minimum temperature recorded                     |
| tmean (°F)       | Numeric (real)      | 30.1           | Mean temperature recorded                        |
| tmax (°F)        | Numeric (real)      | 34.6           | Maximum temperature recorded                     |
| tdmean (°F)      | Numeric (real)      | 29.7           | Mean dew point temperature                       |
| vpdmin (hPa)     | Numeric (real)      | 0.06           | Vapor pressure-deficit min in hPa                |
| vpdmax (hPa)     | Numeric (real)      | 0.9            | Vapor pressure-deficit max in hPa                |
| State            | Nominal (string)    | WA             | State where weather location is located          |

Since humans play a critical role in climate change and human activity has been shown to increase wildfire occurrence [5], population data was also incorporated into the model. State census data was scraped directly from the [https://citypopulation.de/]([https://citypopulation.de/]) website and were utilized to enhance overall predictive accuracy. The population data collected included county, state and population by decade. The dataset’s attributes are outlined below:

#### Table IV. Census Population Data for the West Coast
| Attribute          | Type              | Example Value | Description                                |
|--------------------|-------------------|----------------|--------------------------------------------|
| County             | Nominal (string)  | "Adams"        | County name                                 |
| State              | Nominal (string)  | "WA"           | State where the county is located           |
| Population 1990    | Numeric (real)    | 15200          | County population in 1990                   |
| Population 2000    | Numeric (real)    | 16500          | County population in 2000                   |
| Population 2010    | Numeric (real)    | 17800          | County population in 2010                   |
| Population 2020    | Numeric (real)    | 19000          | County population in 2020                   |


## III. Methodology
### *The Random Forests Algorithm* 
The Random Forest method is a powerful, predictive modeling technique that creates multiple random decision trees to construct a single ensemble with high predictive accuracy [7]. While this process seems tedious, the Random Forest modeling algorithm is simple to implement using common Python libraries like SciKit-learn and Numpy. 

The development of the predictive model in this project followed a multi-step process (Figure 2) aimed at analyzing wildfire risk zones across the West Coast. Using historic fire, daily weather, and population density data from 1990 to 2025, the model attempts to identify fire prone areas to facilitate fire prevention efforts. The latitude and longitude coordinates from the historical fire dataset serve as critical reference points for pinpointing high-risk locations associated with key weather and population data features.

<img width="677" alt="image" src="https://github.com/user-attachments/assets/9df5d668-6054-4336-a84e-806a2410aa5b" />

*Figure 2. Random Forest Predictive Modeling Flow Chart*


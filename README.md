# Wildfire Risk Assessment: Predicting West Coast Wildfire Size and Severity
## Introduction
Wildfires on the West Coast of the United States, including Washington, Oregon, and California, are becoming increasingly severe due to climate change and human activity. Predicting the potential size and severity of these fires is critical for mitigation and prevention efforts. This project applies a Random Forest predictive model to estimate wildfire intensity and burned acreage, helping identify areas most at risk for large-scale fires.

This project analyzes publically available historical wildfire, weather, and population data from 1990 to 2025 to:

- Predict the potential size (acres burned) of future wildfires.
- Assess the severity of fires based on environmental and population factors.

Together, these methods provide actionable insights into wildfire risk, enabling more informed prevention, resource allocation, and response strategies.

## Data Sources
The project integrates multiple datasets from official sources into three reduced subsets (1) wildfire data, (2) weather data, and (3) population data. 

---

### Wildfire Data
Wildfire data for the past 35 years was collected individually from each West Coast state and combined into a lower-dimensional dataset which was utilized in the creation of the predictive model.

**Sources:**

| State | Dataset | Organization | Link |
|-------|--------|-------------|------|
| CA | California Fire Perimeters (1950+) | California Natural Resources Agency | [Link](https://gis.data.cnra.ca.gov/datasets/CALFIRE-Forestry::california-historical-fire-perimeters/about?layer=2) |
| OR | ODF Fire Occurrence Data (2000–2022) | Oregon Department of Forestry | [Link](https://data.oregon.gov/Natural-Resources/ODF-Fire-Occurrence-Data-2000-2022/fbwv-q84y/about_data) |
| WA | Washington Large Fires (1973–2023) | Washington State Department of Natural Resources | [Link](https://data-wadnr.opendata.arcgis.com/datasets/wadnr::washington-large-fires-1973-2023/about) |

**Final Dataset Attributes:**
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

---

### Weather Data
Weather data from all three West Coast states was collected from Oregon State University's PRISM Group. PRISM Historic Weather Data: includes temperature, precipitation, and vapor-pressure deficit for all three states.

[Visit PRISM Website](https://prism.oregonstate.edu/explorer/bulk.php)

**Dataset Attributes:**
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

---

### Population Data
County-level population data for Washington, Oregon, and California were collected by decade from US Census data collated by City Population.

[Visit City Population Website](https://www.citypopulation.de/)

**Dataset Attributes:**
| Attribute          | Type              | Example Value | Description                                |
|--------------------|-------------------|----------------|--------------------------------------------|
| County             | Nominal (string)  | "Adams"        | County name                                 |
| State              | Nominal (string)  | "WA"           | State where the county is located           |
| Population 1990    | Numeric (real)    | 15200          | County population in 1990                   |
| Population 2000    | Numeric (real)    | 16500          | County population in 2000                   |
| Population 2010    | Numeric (real)    | 17800          | County population in 2010                   |
| Population 2020    | Numeric (real)    | 19000          | County population in 2020                   |

## Methodology
Wildfire prediction relies on analyzing historical fire events, weather conditions, and population density to understand where fires are most likely to occur and how severe they might be. In this project, each fire is represented as a structured feature vector that is fed into a Random Forest Model, which predicts whether a fire is likely to be low-risk or high-risk. The model then identifies which factors have the biggest impact on fire size and intensity by calculating feature importance.

---

### Data Preprocessing
Before any modeling was performed, the dataset was carefully preprocessed and split into training and testing sets to ensure the model could learn patterns effectively and be evaluated reliably. Missing values were handled using appropriate imputation methods, categorical variables were encoded, and all features were standardized as needed to create consistent, structured inputs for the Random Forest model.

- Missing numeric values are imputed with the mean.
- Missing categorical values are imputed with the most frequent category and one-hot encoded (i.e. converted into binary format).
- Dataset split into training (80%) and testing (20%) sets, stratified by target.

**Training set size:** 31,692    
**Test set size:** 7,924


**Goal**: Ensure clean, consistent, and properly encoded data for modeling.

---

### Feature Representation
In the training dataset, each fire event was transformed into a feature vector summarizing all relevant environmental, demographic, and location information. Representing each fire in this way enabled the model to more effectively learn patterns associated with fire size and severity.

**Feature vectors created for this model included:**
- Daily temperature (min, mean, max)
- Precipitation
- Vapor pressure deficit (min and max)
- Population density by decade
- Location information (state, weather station)


**Goal:** Transform each fire into a structured input (feature vector) suitable for predictive modeling.

---

### Random Forest Modeling
The feature vectors summarizing each fire were used as input to train the Random Forest model, which aimed to predict wildfire risk and estimate potential severity. Random Forest is an ensemble learning method that generates multiple decision trees and combines their predictions to improve accuracy and generalization on unseen data (i.e., the testing set).

**Ensemble Learning:** The Random Forest model creates multiple decision trees using bootstrapped samples of the dataset. Each tree considers a random subset of features, reducing overfitting, increasing model stability, and allowing the system to capture complex patterns in weather, environmental, and population data.

**Prediction Aggregation:** The predictions from all trees are combined using majority vote for classification tasks and averaging for regression tasks. This produces a single prediction that balances variance and bias, improving reliability.

**Feature Importance:** The influence of each input variable on the model's predictions is calculated to determine which factors strongly drive wildfire size and severity.

**Goal:** Accurately predict wildfire occurrence, potential size, and intensity.

---


### Model Evaluation
After training, the Random Forest model was evaluated on the testing dataset to assess its performance on unseen data.

- Accuracy, AUC-ROC, Precision, Recall, and F1-Score were used to measure classification performance.

- Confusion matrix and ROC curve visualizations were generated to examine model quality and decision thresholds.

- Feature importance was calculated to identify which variables most strongly influence fire size and severity.

Goal: Quantify predictive performance on unseen data, understand contributing factors, and identify high-risk areas for effective wildfire mitigation.

## Results  
The Random Forest model was evaluated on the held-out testing dataset to measure its ability to generalize beyond the training data. Model performance was assessed using a combination of classification metrics (Accuracy, Precision, Recall, F1-Score, and AUC-ROC), as well as visualization tools such as the confusion matrix (Figure 1.) and ROC curve (Figure 2.). These results provide insight not only into the overall predictive power of the model, but also into its ability to correctly identify high-risk wildfire events.  

While the model shows strong overall accuracy and a high AUC-ROC score, performance is not uniform across classes. It performs particularly well at identifying low-risk fires, but faces challenges in consistently detecting high-risk cases — an important area for future improvement. Feature importance analysis further highlights the key environmental and demographic drivers of wildfire severity, offering actionable insights for prevention and mitigation efforts.


#### Model Evaluation Results  
**Accuracy:** 0.8718  
**AUC-ROC:** 0.8980  

**Classification Report:**  
| Class | Precision | Recall | F1-Score | Support |
|-------|-----------|--------|----------|---------|
| **0 (Low Risk)** | 0.88 | 0.98 | 0.93 | 6777 |
| **1 (High Risk)** | 0.68 | 0.21 | 0.33 | 1147 |
| **Accuracy** |       |        | **0.87** | 7924 |
| **Macro Avg** | 0.78 | 0.60 | 0.63 | 7924 |
| **Weighted Avg** | 0.85 | 0.87 | 0.84 | 7924 |

<p align="center">
  <img width="390" height="343" alt="Confusion Matrix" src="https://github.com/user-attachments/assets/e5813462-e639-4404-8c66-59fadc31809a" />
</p>
<p align="center"><i>Figure 1:</b> Confusion Matrix of wildfire predictions</i></p>

<p align="center">
  <img width="590" height="426" alt="ROC Curve" src="https://github.com/user-attachments/assets/50af86ee-6dba-4a37-a326-507bfdf027c3" />
</p>
<p align="center"><i>Figure 2:</b> ROC Curve showing model performance</i></p>

Key factors driving wildfire risk and potential size include temperature extremes (both minimum and maximum), vapor pressure deficit, and local population density. These variables influence both the likelihood and severity of fires, highlighting the combined role of climatic conditions and human activity in shaping wildfire behavior.
<p align="center">
  <img width="613" height="463" alt="image" src="https://github.com/user-attachments/assets/f64e8833-4502-417b-ac85-c73555fe9865" />
</p>
<p align="center"><i>Figure 3:</b> Bar graph showing the relative importance of each feature in the Random Forest model. Features such as temperature extremes, precipitation, vapor pressure deficit, and population density have the greatest influence on wildfire size and severity predictions.</i></p>

## Conclusions
- **Predictive Insights:** The Random Forest model effectively identifies high-risk wildfire areas and estimates potential fire size across the West Coast.  
- **Key Drivers:** Temperature extremes, vapor pressure deficit, precipitation, and local population density are the most influential factors in predicting wildfire severity.  
- **Risk Mitigation:** Results can inform proactive resource allocation, targeted fire prevention, and emergency planning.  
- **Future Work:**  
  - Incorporate **temporal trends** to capture seasonal or multi-year wildfire patterns.  
  - Explore **alternative machine learning models** (e.g., gradient boosting, neural networks) to improve high-risk fire detection.  
  - Integrate **additional environmental and human activity data** (e.g., vegetation type, land use) to enhance model accuracy and granularity.


## References
1. [Science Recent. *Why Does the Western US Have So Many Wildfires Every Year?* (2023)](https://sciencerecent.com/environment/why-does-the-western-us-have-so-many-wildfires-every-year/)  

2. [The Nature Conservancy. *Yes, Climate Change is Raising the Risks—and Stakes—of Extreme Wildfires* (2024)](https://www.nature.org/en-us/what-we-do/our-priorities/tackle-climate-change/climate-change-stories/extreme-wildfires-are-getting-worse-with-climate-change/)  

3. [USGS. *New Federal Partnership Will Advance Predictive Models of Wildfire Behavior* (2020)](https://www.usgs.gov/news/national-news-release/new-federal-partnership-will-advance-predictive-models-wildfire-behavior)  

4. Plesovskaya, E., & Ivanov, S. (2021). [*An Empirical Analysis of KDE-based Generative Models on Small Datasets.* Procedia Computer Science, 193, 442–452.](https://doi.org/10.1016/j.procs.2021.10.046)  

5. Mann, M. L., et al. (2016). [*Incorporating Anthropogenic Influences into Fire Probability Models: Effects of Human Activity and Climate Change on Fire Activity in California.* PLOS ONE, 11(4), e0153589.](https://doi.org/10.1371/journal.pone.0153589)  

6. Jones, M. W., Smith, A., Betts, R., Canadell, J. G., Prentice, I. C., & Le Quéré, C. (2020). [*Climate Change Increases the Risk of Wildfires.* JSTOR.](https://www.jstor.org/stable/resrep51248)  

7. Cheng, L., Chen, X., De Vos, J., Lai, X., & Witlox, F. (2019). [*Applying a Random Forest Method Approach to Model Travel Mode Choice Behavior.* Travel Behaviour and Society, 14, 1–10.](https://doi.org/10.1016/j.tbs.2018.09.002)  

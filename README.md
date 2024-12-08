# US Power Outages Analysis
Project for EECS 398-003 at the University of Michigan
By David Lisbonne

# Introduction
In this project, I investigate a comprehensive dataset published by Purdue University that catalogued power outages across the United States, along with a suite of external variables recorded for each outage. These additional features include geographical location of the outages, regional climate classifications, customer distributions, electricity consumption patterns and economic characteristics of the states affected by the outages.

Initially, I needed to clean the data and perform an initial foray into analyzing the dataset. It contains over 1500 rows, with some more niche columns –– for example, HURRICANE_NAME –– being largely null. As a result, I first needed to sanitize, organize and normalize the dataset.

Then, I explored a univariate analysis, focusing on understanding the distribution of causes in the "CAUSE.CATEGORY" column of the dataset. This was of particular interest because I thought it might be a key variable for a model to leverage while learning. 

Next, I wanted to dive deeper into a bivariate analysis, and here I did a lot of analysis, leveraging combined and related features to better understand patterns in the dataset, and thus build a better predictive model. First, I looked at correlations of two variables using scatter plots. These were: outage start time vs. outage duration, outage month vs. outage duration, and outage cause category vs outage duration. My hypotheses for these three variables were as follows: Do outages that begin outside of working hours take longer to fix? Is there a correlation between outage duration and peak energy grid usage hours? Further, do higher usage months –– like the winter months –– correlate to longer or shorter outages? Do certain causes of outages correlate to longer fix times, eg. hurricanes compared to vandalism? 

Then I expanded my multivariate analysis and looked at combinations of three variables. Here, the first combination of features I examined was climate category and cause category. I was particularly curious if harsher climate environments experiencing weather related outages took longer to fix than warmer climate areas. Finally, I looked at cause category and cause category detail to investigate if more granular documentation helped in finding correlations. 

Below is a table outlining the columns of interest in this dataset:
| Column                | Description |
|-----------------------|-------------|
| `'MONTH'`             | The month when the power outage occurred. |
| `'U.S._STATE'`        | The state in which the power outage took place. |
| `'CLIMATE.REGION'`    | One of the nine U.S. Climate regions as defined by the National Centers for Environmental Information. |
| `'ANOMALY.LEVEL'`     | The Oceanic El Niño/La Niña (ONI) index value, indicating whether the climate was experiencing cold (La Niña) or warm (El Niño) episodes during the season. |
| `'OUTAGE.START.DATE'` | The specific day of the year when the power outage began. |
| `'OUTAGE.START.TIME'` | The exact time during the day when the outage started. |
| `'CAUSE.CATEGORY'`    | A categorical classification of the main causes behind the power outage event. |
| `'OUTAGE.DURATION'`   | The length of time (in minutes) for which the power outage lasted. |\
| `'CUSTOMERS.AFFECTED'`| The number of residential, commercial, and industrial customers affected by the power outage. |
| `'TOTAL.CUSTOMERS'`   | The total number of customers (residential, commercial, and industrial) served by electricity providers in the state. |
| `'RES.CUST.PCT'`      | The percentage of region's customers who are residential |

# Data Cleaning and Exploratory Data Analysis
It is critical when working on any data science analysis of a large dataset to thoroughly sanitize and normalize the data. This is especially true when trying to later build a predictive model for generalizing on unseen data, as the more regularized the training data the better the model can recognize true patterns within the data. 

There were two cases of imputation necessary for this dataset: numerical and categorical imputation. For numerical imputation I decided to use the mean of all rows for the given state, to reduce the scope of the means to a more representative range. For categorical imputation, I took it one step further, devising a more detailed custom scheme. First, I calculated the most common values for the column in question, "cause.category.detail", for each postal code and annual quarter (calculated by binning the month values into four quarters). Then, I merged that new group back into the main DataFrame. Finally, I filled the missing values of the "cause.category.detail" column with the corresponding values, and dropped the temporary imputation columnn from the DataFrame. 

## Grouping and Aggregates
Before delving into building the predictive models, I was curious to see how anomaly levels and outages related to the cause category of outages. To better understand this, I created a pivot table with cause category as the index, and then displayed the ranges of anomaly levels, as well as the median outage duration for those categories. This pivot table is shown below. 

| CAUSE.CATEGORY                |   ('ANOMALY.LEVEL', 'max') |   ('ANOMALY.LEVEL', 'min') |   ('OUTAGE.DURATION', 'median') |
|:------------------------------|---------------------------:|---------------------------:|--------------------------------:|
| equipment failure             |                        1.3 |                       -1.4 |                           221   |
| fuel supply emergency         |                        2   |                       -1.4 |                          3960   |
| intentional attack            |                        2.3 |                       -1.3 |                            56   |
| islanding                     |                        2   |                       -1.5 |                            77.5 |
| public appeal                 |                        2   |                       -1.4 |                           455   |
| severe weather                |                        2.3 |                       -1.6 |                          2460   |
| system operability disruption |                        2.3 |                       -1.5 |                           215   |

## Univariate Analysis
One of the most interesting columns of the dataset is cause category, and so I wanted to uunderstand the univariate distribution of the column. This would help me gain insight into whether or not it was as a good training parameter -- for example, if the column were incredibly heavily weighted to one cause, then it would be hard for a model to learn anything from that feature. Below is the histogram of the cause category column values. 

<iframe
  src="assets/fig1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
**Figure 1**: Plot of outage start time vs outage duration

## Bivariate Analyses
As mentioned in the introduction, I performed three biivariate analyses to better understand correlations between features I expected might be important, and my ultimate target variable "outage duration". The first examined the outage start time vs the outage duration, under the hypothesis that perhaps outages ocurring outside of working hours might take longer to fix. This theory doesn't appear to be very well supported by the data, and the plot shows weak correlation between the timing of an outage and its duration. 

<iframe
  src="assets/fig1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
**Figure 2**: Plot of outage start time vs outage duration

The next analysis I performed was to plot the outage month vs the outage duration, testing the hypothesis that harsher environmental conditions in winter might make repairs or fixes more difficult. This also proved to have a weak direct correlation, largely due to the number of outages itself. 

<iframe
  src="assets/fig2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
**Figure 3**: Plot of outage month vs outage duration

Finally, the last univariate analysis I looked into was the outage cause category vs the outage duration. Here, the hypothesis was that harsher, more extreme causes like severe weather, might also correlate to longer outages. This proved to be true, especially as shown by the medians of the outage durations by categories. 

<iframe
  src="assets/fig3_2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
**Figure 4**: Bar chart of median outage durations by cause category

I also examined two multivariate correlations, the first between cause category and climate category, vs outage duration, and the second between cause category and cause category detail (more granular description of the cause) vs outage duration. My thought process for the first analysis was to understand whether certain types of outage causes are exascerbated by specific climate conditions, wherein they result in longer outages. This relationship proved to be evidently clear with fuel shortages, notably how normal climate conditions were the shortest duration. This makes sense because colder environment likely have higher fuel requirements, thus when shortages occur it would take longer to assemble the requisite fuel to end the outage. Similarly, for warmer climates, they are likely less prepared for such an eventuality so any shortage of fuel would take longer to rebuild what little supply they likely had originally. Below is the box plot for my first analysis.

<iframe
  src="assets/fig4.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
**Figure 5**: Bivariate box plot showing relationship between cause category and climate category vs outage duration. 

# Framing a Prediction Problem
I aim to predict the **outage duration** based on historical data and various features such as weather conditions, region demographics, and outage characteristics.

### Type of Problem:
This is a **regression problem**, since the response variable (outage duration) is continuous.

### Response Variable (What We Are Predicting):
The target variable is the **outage duration** (a continuous variable representing the duration in minutes of a given outage).

### Why I Chose This Response Variable:
The outage duration is a key metric for utility companies, as it helps to assess the severity of the outage. Knowing this allows better resource allocation, such as deploying repair crews, communicating with customers, and prioritizing areas for restoration. It also provides valuable insights into the reliability of the power grid in different regions and under different conditions.

### Features (Information Available at the Time of Prediction):
When making the prediction, I would only have access to the **features available before or during the early stages of the outage**. These could include:
- **Outage start time**: The time the outage began (could influence customer behavior, e.g., peak vs. off-peak hours).
- **Weather anomaly level**: The intensity of weather conditions (e.g., severe storms, heat waves) that may have caused the outage.
- **Cause category**: The category of cause (e.g., equipment failure, human error, weather-related).
- **Region demographics**: Includes **population density** (e.g., persons per square mile), which can influence how many customers are impacted by an outage in that region.
- **Region infrastructure and power utilization**: This includes characteristics of the power grid in that area and its capacity relative to actual usage, which can indicate the potential number of customers affected.

### Information Not Available at Time of Prediction:
- The total demand loss or actual recovery metrics would also not be available before the outage or during the initial period.
- Additionally, the actual number of affected customers, and their distributions, would likely not be known. 

### Modeling Approach:
We can approach this problem using a **regression model**. We'll train the model using the selected features and predict the continuous response variable: **outage duration**. I plan on using a linear regression for the baseline model, and then a Random Forest Classifier for the final predictive model. 

### Evaluation Metric:
For a regression problem, the most common evaluation metrics are:
- **Mean Absolute Error (MAE)**: This metric calculates the average of the absolute differences between predicted and actual values. It is easy to interpret and gives a straightforward measure of model accuracy in predicting the outage duration.
- **Root Mean Squared Error (RMSE)**: This metric penalizes larger errors more heavily and is useful when large prediction errors are particularly undesirable in this context.

### Why MAE and RMSE?
- **MAE** is chosen because it provides a clear and interpretable measure of how far, on average, the model's predictions are from the actual outage duration.
- **RMSE** is also valuable because it gives more weight to larger errors, which is important in cases where predicting a large number of affected customers correctly is critical (e.g., for resource allocation).

The goal is to minimize these metrics, and depending on the application, we might prefer one over the other based on whether we want to penalize larger errors more (RMSE) or simply understand average performance (MAE). It's worth noting that I also normalize the MAE to gain a better intuition for the quality of the model's prediction, given that the target variable's values are very large. 

## Additional Considerations for Future Model Improvement:
- **Feature Engineering**: It may be useful to create additional features such as:
  - **Time-based features**: For example, time of day or time of year (seasonality effects on outages).
  - **Weather-related features**: If there are specific weather patterns that affect outages, these should be encoded.
  - **Geospatial features**: If we know the geographic locations or infrastructure details, this could help determine the number of customers affected by proximity to power lines, substations, or urban areas.
  
- **Data Availability**: Ensure that data from different regions and varying weather conditions is available to train the model on a variety of outage scenarios, improving the generalizability of the model.

# Baseline Model
The baseline model uses a **RandomForestRegressor** to predict the **outage duration**. The model is trained using the following features:

- **Features:**
  1. **ANOMALY.LEVEL** (Quantitative): This feature indicates the level of weather anomaly affecting the outage. It is quantitative and is represented with a numerical value, which in this dataset has a range of [-1.6, 2.3].
  2. **TOTAL.CUSTOMERS** (Quantitative): This feature represents the population of the affected region. It is a continuous numerical feature.
  3. **CLIMATE.CATEGORY** (Nominal): This feature represents the type of climate for the region of the given outage
  4. **CAUSE.CATEGORY.DETAIL** (Nominal): This feature provides a more granular description of the cause category.
  5. **RES.CUST.PCT** (Quantitative): This feature indicates the percent of the region's total customers who are residential. 

- **Feature Preprocessing:**
  - **REGION.POPULATION** and **TOTAL.CUSTOMERS**: These numerical features are standardized using **StandardScaler** to normalize its scale, ensuring the model performs efficiently.

- **Imputation**: Missing values are handled as follows:
  - Numerical features are imputed with the **mean** of the column.
  - Categorical features are imputed with the **mode** (most frequent value).

- **Model Evaluation:**
  - **Mean Absolute Error (MAE)** and **Root Mean Squared Error (RMSE)** were used as performance metrics to evaluate the model.
  - **Cross-validation** was performed with 5 folds to assess the generalization ability of the model.

#### Model Performance:
- **MAE**: 113091.42
- **RMSE**: 361272.1
- **Cross-validated MAE**: 97469.11
- **Normalized MAE (NMAE)**: **0.04**

#### Model Assessment:
The baseline model provides a solid starting point, with reasonable accuracy based on the performance metrics. The model utilizes simple preprocessing steps (scaling and encoding) and does not incorporate advanced feature engineering or hyperparameter tuning.

Based on the performance of the baseline model, it can be considered a reasonable first step, albeit with quite good accuracy at 4% off. However, further improvements can be made by exploring additional feature engineering (e.g., combining features or adding new ones) and performing hyperparameter tuning to optimize the RandomForestRegressor for better predictive power.

While the model’s performance can be seen as satisfactory, especially in comparison to a non-optimized or simple model, there is still room for improvement with more sophisticated techniques, such as better feature transformations or using different algorithms.

# Final Model
For the final model, I chose the **RandomForestRegressor**, an ensemble learning algorithm that combines multiple decision trees to improve predictive performance and reduce overfitting. It is particularly effective for regression tasks with complex relationships between features and target variables.

#### Hyperparameters Tuned:
- **`n_estimators`**: Number of trees in the forest. Best value: **100**
- **`max_depth`**: Maximum depth of each tree. Best value: **20**
- **`min_samples_split`**: Minimum samples required to split an internal node. Best value: **2**
- **`min_samples_leaf`**: Minimum samples required at a leaf node. Best value: **2**

#### Hyperparameter Selection Method:
I used **GridSearchCV** with 3-fold cross-validation to search over the hyperparameter grid and select the best combination. The performance was evaluated using **Normalized Mean Absolute Error (neg_mean_absolute_error)** as the scoring metric.

#### Model Improvement:
- **Baseline Model**: Used a basic RandomForestRegressor with default hyperparameters, achieving an NMAE of **0.04**.
- **Final Model**: The tuned RandomForestRegressor, with feature engineering (e.g., log-transformed and derived features), resulted in a reduced error of **0.02**, improving the predictive accuracy over the baseline.
  
#### Model Performance:
- **MAE**: 2420.39
- **RMSE**: 5572.70
- **Cross-validated MAE**: 2742.59
- **Normalized MAE (NMAE)**: **0.02**

The Final Model outperformed the baseline by using optimal hyperparameters and additional feature transformations, leading to better generalization and lower error. I am happy with the Final Model's performance, cutting down NMAE by 50%. 

However, the model certainly isn't perfect, and can no doubt be improved upon with more complex methods and more training data. To get a sense of it's practical accuracy, here is a chart of the residuals of the final model. 

<iframe
  src="assets/finalmodelresiduals.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
**Figure 6**: Histogram of residuals for final model
# US Power Outages Analysis
Project for EECS 398-003 at the University of Michigan
By David Lisbonne

# Introduction
In this project, I investigate a comprehensive dataset published by Purdue University that catalogued power outages across the United States, along with a suite of external variables recorded for each outage. These additional features include geographical location of the outages, regional climate classifications, land-use characteristics, electricity consumption patterns and economic characteristics of the states affected by the outages.

Initially, I needed to clean the data and perform an initial foray into analyzing the dataset. It contains over 1500 rows, with some more niche columns –– for example, HURRICANE_NAME –– being largely null. As a result, I first needed to sanitize, organize and normalize the dataset.

Then, I explored a univariate analysis, focusing on three different variables in three different studies ultimately looking for correlations with my goal feature: outage duration. These were: outage start time vs. outage duration, outage month vs. outage duration, and outage cause category vs outage duration. My hypotheses for these three variables were as follows: Do outages that begin outside of working hours take longer to fix? Is there a correlation between outage duration and peak energy grid usage hours? Further, do higher usage months –– like the winter months –– correlate to longer or shorter outages? Do certain causes of outages correlate to longer fix times, eg. hurricanes compared to vandalism? 

Next, I wanted to dive deeper into a bivariate analysis, leveraging combined and related features to better understand patterns in the dataset, and thus build a better predictive model. The first combination of features I examined was climate category and cause category. I was particularly curious if harsher climate environments experiencing weather related outages took longer to fix than warmer climate areas. Next, I looked at cause category and cause category detail to investigate if more granular documentation helped in finding correlations. Finally, I investigated ... 

# Cleaning the Dataset
It is critical when working on any data science analysis of a large dataset to thoroughly sanitize and normalize the data. This is especially true when trying to later build a predictive model for generalizing on unseen data, as the more regularized the training data the better the model can recognize true patterns within the data. 

# Predicting the Number of Customers Affected by a Power Outage

## Problem Statement:
I aim to predict the **number of customers affected by a power outage** based on historical data and various features such as weather conditions, region demographics, and outage characteristics.

### Type of Problem:
This is a **regression problem**, since the response variable (number of customers affected) is continuous.

### Response Variable (What We Are Predicting):
The target variable is the **number of customers affected by a power outage** (a continuous variable representing the total count of affected residential customers).

### Why I Chose This Response Variable:
The number of customers affected by a power outage is a key metric for utility companies, as it helps to assess the severity of the outage. Knowing this allows better resource allocation, such as deploying repair crews, communicating with customers, and prioritizing areas for restoration. It also provides valuable insights into the reliability of the power grid in different regions and under different conditions.

### Features (Information Available at the Time of Prediction):
When making the prediction, I would only have access to the **features available before or during the early stages of the outage**. These could include:
- **Outage start time**: The time the outage began (could influence customer behavior, e.g., peak vs. off-peak hours).
- **Weather anomaly level**: The intensity of weather conditions (e.g., severe storms, heat waves) that may have caused the outage.
- **Cause category**: The category of cause (e.g., equipment failure, human error, weather-related).
- **Region demographics**: Includes **population density** (e.g., persons per square mile), which can influence how many customers are impacted by an outage in that region.
- **Region infrastructure and power utilization**: This includes characteristics of the power grid in that area and its capacity relative to actual usage, which can indicate the potential number of customers affected.

### Information Not Available at Time of Prediction:
- The actual duration of the outage and the recovery time would **not** be known at the time of prediction, as these depend on the unfolding events.
- The total demand loss or actual recovery metrics would also not be available before the outage or during the initial period.

### Modeling Approach:
We can approach this problem using a **regression model**. We'll train the model using the selected features and predict the continuous response variable: **number of customers affected**. I plan on using a linear regression for the baseline model, and then a Random Forest Classifier for the final predictive model. 

### Evaluation Metric:
For a regression problem, the most common evaluation metrics are:
- **Mean Absolute Error (MAE)**: This metric calculates the average of the absolute differences between predicted and actual values. It is easy to interpret and gives a straightforward measure of model accuracy in predicting the number of customers affected.
- **Root Mean Squared Error (RMSE)**: This metric penalizes larger errors more heavily and is useful when large prediction errors are particularly undesirable in this context.

### Why MAE and RMSE?
- **MAE** is chosen because it provides a clear and interpretable measure of how far, on average, the model's predictions are from the actual number of customers affected.
- **RMSE** is also valuable because it gives more weight to larger errors, which is important in cases where predicting a large number of affected customers correctly is critical (e.g., for resource allocation).

The goal is to minimize these metrics, and depending on the application, we might prefer one over the other based on whether we want to penalize larger errors more (RMSE) or simply understand average performance (MAE).

## Additional Considerations for Future Model Improvement:
- **Feature Engineering**: It may be useful to create additional features such as:
  - **Time-based features**: For example, time of day or time of year (seasonality effects on outages).
  - **Weather-related features**: If there are specific weather patterns that affect outages, these should be encoded.
  - **Geospatial features**: If we know the geographic locations or infrastructure details, this could help determine the number of customers affected by proximity to power lines, substations, or urban areas.
  
- **Data Availability**: Ensure that data from different regions and varying weather conditions is available to train the model on a variety of outage scenarios, improving the generalizability of the model.

# Baseline Model


# Final Model
### Modeling Algorithm and Hyperparameter Tuning

For the final model, I chose the **RandomForestRegressor**, an ensemble learning algorithm that combines multiple decision trees to improve predictive performance and reduce overfitting. It is particularly effective for regression tasks with complex relationships between features and target variables.

#### Hyperparameters Tuned:
- **`n_estimators`**: Number of trees in the forest. Best value: **100**
- **`max_depth`**: Maximum depth of each tree. Best value: **20**
- **`min_samples_split`**: Minimum samples required to split an internal node. Best value: **2**
- **`min_samples_leaf`**: Minimum samples required at a leaf node. Best value: **2**

#### Hyperparameter Selection Method:
I used **GridSearchCV** with 3-fold cross-validation to search over the hyperparameter grid and select the best combination. The performance was evaluated using **Negative Mean Absolute Error (neg_mean_absolute_error)** as the scoring metric.

#### Model Improvement:
- **Baseline Model**: Used a basic RandomForestRegressor with default hyperparameters, achieving an NMAE of **0.04**.
- **Final Model**: The tuned RandomForestRegressor, with feature engineering (e.g., log-transformed and derived features), resulted in a reduced error of **0.02**, improving the predictive accuracy over the baseline.

The Final Model outperformed the baseline by using optimal hyperparameters and additional feature transformations, leading to better generalization and lower error.

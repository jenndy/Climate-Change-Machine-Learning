# Climate_Change_Machine_Learning
What factors reduce building energy usage

### Goal
The macro goal was to fight climate change with data science. The specific goal was to predict the site eui (energy usage intensity) given a dataset. The target value reflects the amount of heat and electricity consumed by a building as reflected in utility bills. These insights can be used to reduce building energy usage and costs to help the environment.

### Dataset Overview
- The dataset was created by Climate Change AI and Berkeley Lab
- 100k records of building energy usage observations with each row being a single building
- Features include building characteristics and climate and weather variables
- Collected over 7 years from many US states
- Anonymized to make joining to outside data difficult for sake of competition
- The target variable or the task is to predict the energy usage of the building measured as Site EUI
- Accurate predictions of Site EUI can help policymakers target retrofitting efforts to maximize emission reductions, and minimize energy usage and minimize costs

### Summary of Phases

#### Data Exploration

##### Heatmap of missing values: 
- to visually see what covariates were missing values	
- 4 features for wind and fog had significant amounts of missing values (> 50%)
- For other 2 features with missing values -> dropped rows, tried imputing with mean
- Future: try coming up with formula or predicting the values (prediction on prediction)
##### Year built distribution plot
- Kdeplot and rugplot
- Saw year had year 0 values -> drop, treat as missing values
##### Correlation matrix heatmap: 
- to look at correlation between covariates and the target value (mine)
- Noticed site eui has positive correlation with energy star rating and weak direct correlations with all the other numerical features
- 
#### Data Cleaning and Preprocessing
- Dropped features with significant amount of missing values (more than 50%): max_wind_speed, direction_peak_wind_speed,direction_max_wind_speed, days_with_fog, etc. 
- Dropped rows where year built was 0
- Dropped rows where year built and energy star rating were missing
- Future: try to impute missing values with mean, formula, ore prediction - trade offs require more analysis *
- Wind speed does affect temperature of object due to thermodynamics from physics perspective but could not keep column since too few data in collection 
- Used one hot encoding for 4 categorical features (dummy variables)
- Facility type had 41 types of values
- Didn’t want to label encode since numeric encoding wouldn't make sense as numbers distance-wise (magnitudes and order would be weighted) 
- Future: make groupings by using clustering first, then use one hot encoding on the groupings (fewer unique types -> resulting matrix won’t be as sparse)

#### Feature Engineering

##### Principal component heatmap
- See variance explained by each feature for all the components (see what and how much the features contributed to each principal component)
- A mathematical reduction of the feature space, no real life meaning, ie. mapping 3D to 2D
- hen site eui was dropped the variance explained by all the features increased for all the components
##### Domain knowledge
- reduce number of features
- Temp covariates: min, max, avg temp for months, avg temp for a year, days below and above given temps, heating and cooling days
- Decided to drop some temp covariates and 2 facility types that did not contribute variance to any of the principal components
- Reasoning: Average doesn’t capture the distribution of temperatures, no precise temp fluctuations, for building energy consumption changes (don’t need avg for each month)
- Heating and cooling days: useful since heating generally takes more energy than cooling, and the dataset have more heating days than cooling days
- Days below and days above specific temps capture the frequency of the temps that require heating and cooling. Gives a better picture of how much heating and cooling was required. (duration of temp, with the significant variability range captured) (don’t need min and max for each month)
Decided to drop min, max, avg temp, and 2 facility types that did not contribute variance to any of the principal components: mixed_use_predominantly_residential and facility_type_multifamily_uncategorized

#### Modeling
Predicting a continuous value
Baseline model
Linear regression
Metrics: accuracy, R^2, MSE, RMSE, MAE
Accuracy on test 0.53
Improving models
Lasso & gradient boosting
0.51 and 0.66
Random forest regressor & support vector regressor model
0.72 and 0.41
Future models: principal component features with sequential deep learning model, multi-models script
Preparing Test Set
We were given a test csv with no target column, we predict and submit this column to be compared with the target values they withheld
Had to change the way data was processed to fit the test csv data and model accuracy was decreased
Do not think new test set should affect the way data was processed too much since we do not know what other configurations of data there would be
The categorical variables had different types in the training csv and the test csv so dummying them gave different numbers of columns
Can concatenate the datasets to process together to ensure uniformity and use a flag for the ones that have no target values
Can also add the missing columns to the test csv dataframe
May not be a hassle or issue if grouped into less columns with clusters
The model was run on the test csv after it was processed by dropping all the categorical features (same for train csv), result was that predicted column length was less than the ids column -> not sure why??

# DS-Projects_Analytics1

# Project 1: Mortality Prediction using k-NN

## What This Project Is About
This project uses real health data collected by the CDC to predict two things:
1. Whether a person will die within a 20 year follow up window (classification)
2. How many months a person is likely to survive (regression)

We used a machine learning algorithm called k-Nearest Neighbors (kNN) to make these predictions. The idea behind kNN is simple: to predict an outcome for a new person, the algorithm finds the k most similar people in the training data and uses their outcomes to make a prediction.

## Where The Data Comes From
The data comes from two sources that we merged together:

**NHANES 1999 to 2000 (National Health and Nutritional Examination Survey)**
This survey was collected by the CDC to measure the health and nutritional status of adults living in the United States. Each row in the dataset represents one survey participant. The survey includes information about demographics, body measurements, and blood pressure readings.

**CDC Linked Mortality File**
This file tracks whether NHANES participants later died, and if so, how many months after the survey they passed away. The mortality data goes up until December 31 2019. This gives us almost 20 years of follow up data for each participant.

## The Files In This Repo
- `DS_Analytics_Project_1.pdf` — The full project report including all visualizations, analysis, and written discussion
- `Project1_notebook.ipynb` — The Jupyter notebook containing all the code used in this project
- `linked_mortality_file_1999_2000.csv` — The pre-cleaned CDC mortality data
- `DEMO.xpt` — NHANES demographics data including age, gender, and race
- `BMX.xpt` — NHANES body measures data including BMI, weight, and height
- `BPX.xpt` — NHANES blood pressure data including systolic and diastolic readings

## The Variables We Used
We selected five variables to predict mortality and survival time:

**RIDAGEEX** --> Age in months at the time of the medical examination. We used this instead of age in years because it is more precise and captures differences that matter more at older ages.

**RIAGENDR** --> Gender of the participant. Males had a higher mortality rate (around 35%) compared to females (around 27%) in our data.

**RIDRETH1** --> Race and ethnicity of the participant. We found meaningful differences in mortality rates across groups. Non-Hispanic White participants the highest mortality rate at 37.4% and Other Hispanic participants had the lowest at 21.7%.

**BMXBMI** --> Body Mass Index. A measure of obesity related health risk. BMI showed almost no direct correlation with mortality on its own (r = 0.03) but was still included as it may interact with other variables.

**BPXSY1** --> Systolic blood pressure. High blood pressure is a major cause of cardiovascular disease and death. It showed a moderate correlation with mortality in our data (r = 0.44).

## What We Did Step By Step

**Step 1: Data Wrangling**

We downloaded the NHANES demographics file and the CDC Linked Mortality File and merged them together using a shared participant ID called SEQN. We then added body measures and blood pressure data using additional merges. The final dataset had 9965 rows and 217 columns. After dropping participants who were ineligible for mortality follow up we were left with 5445 participants.

**Step 2: Exploratory Data Analysis**

We explored the data using histograms, a correlation heatmap, scatter plots, boxplots, and contingency tables. Key findings included:
- Age is the strongest predictor of mortality with a correlation of 0.67
- Systolic blood pressure is moderately correlated with mortality at 0.44
- BMI has almost no correlation with mortality at 0.03
- Older participants had much lower survival times as confirmed by the age vs survival time scatter plot
- Males had a higher mortality rate than females
- Non-Hispanic White participants had the highest mortality rate across all racial and ethnic groups

**Step 3: Feature Scaling**

Before running kNN we standardized all features using StandardScaler. This is critical because kNN uses Euclidean distance to find similar observations. Without scaling, age in months (which ranges from 216 to 1019) would dominate over gender (which is just 1 or 2) purely because of the difference in scale.

**Step 4: kNN Mortality Classifier**

We trained a kNN classifier to predict whether a participant died (MORTSTAT). We tested k values of 1, 3, 5, 10, 20, and 50. The best result was at k = 50 with 83.7% accuracy on the test set. We confirmed this using 5 fold-cross validation which gave a mean accuracy of 85.3% with a standard deviation of only 1.1%, confirming the model is stable and not just the result of a lucky train test split.

**Step 5: kNN Life Expectancy Regressor**

We trained a kNN regressor to predict how many months a participant would survive (PERMTH_INT). We tested the same k values. The best result was at k = 50 with an RMSE of 49 months, meaning our predictions are off by about 4 years on average. The model struggles with exact predictions because most participants survived the full follow up window, making the target variable heavily skewed.

## Key Results
- Best classifier accuracy: 85.3% using 5 fold cross validation at k = 50
- Best regressor RMSE: 49 months at k = 50
- Age was the most important predictor in both models
- The confusion matrix showed 90 deaths were missed by the classifier, which is an important limitation in a real health context

## Limitations and Ethical Risks
- Age dominates the model and younger high risk patients may be overlooked
- The data is from 1999 to 2000 and may not reflect current health trends
- The mortality data is only tracked until 2019 so some participants may have died after the cutoff
- Using race as a predictor risks reinforcing existing health disparities rather than addressing their root causes
- The model should never be used to deny care or deprioritize patients
- Any real world use would require patient consent and HIPAA compliance
- This model is best used as a screening tool to support and not replace clinical judgment from medical professionals

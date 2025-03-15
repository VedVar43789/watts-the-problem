# Analyzing Power Outages: Watts the Problem?

A project by:
- Vedant Vardhaan
- Ishaan Gosain


## Introduction

Power outages can have significant economic and social impacts, affecting households, businesses, and essential services. Understanding the underlying causes of outages is crucial for improving power grid reliability and enabling quicker response times.

This project analyzes historical outage data (1540 rows × 57 columns) to develop a model that predicts the cause of an outage based on factors like anomaly level, outage duration, climate region, and customer impact. By identifying patterns in the data, we can provide insights that help power companies take preventative measures and optimize resource allocation during disruptions.

The focus of this project is on the following question:
**"Developing a machine learning model that can predict outage causes based on real-time factors."**

This predictive capability could help power companies anticipate issues, allocate resources more effectively, and minimize disruption. The description of the relevent variables is as follows:

| Variable Name                | Description |
|------------------------------|-------------|
| year                         | The year when the outage event occurred |
| state                  | The state when the outage event occurred |
| nerc                  | The North American Electric Reliability Corporation (NERC) regions involved in the outage event |
| climate_region               | U.S. Climate regions as specified by National Centers for Environmental Information |
| anomaly_level                | Represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season |
| climate_category             | Represents the climate episodes corresponding to the years |
| outage_start_timestamp           | Indicates the date and time when the outage event started |
| restoration_timestamp    | Indicates the date and time when power was restored to all customers |
| cause               | Categories of all the events causing major power outages |
| cause_detail        | Detailed description of the event categories causing major power outages |
| duration             | Duration of outage events (in minutes) |
| demand_loss              | Amount of peak demand lost during an outage event (in Megawatt)|
| customers_affected          | Number of customers affected by the power outage event |





## Data Cleaning


The raw dataset required multiple preprocessing steps to ensure it was structured correctly for analysis. Below is a breakdown of the key data cleaning steps performed:

### **1. Adjusting Column Names and Removing Extra Rows**
- The actual column names were stored in the **5th row**, so they were extracted and assigned as column headers.
- The first **five rows** were removed, and the index was reset to clean up any unnecessary metadata.
- The first column (which contained redundant information) was dropped.

### **2. Selecting Relevant Features**
- From the **57 available columns**, only **15 key features** were retained, including:

  - `year`, `state`, `nerc`, `climate_region`, `climate_category`

  - `anomaly_level`, `cause`, `cause_detail`, `duration`, `demand_loss`

  - `customers_affected`, `start_date`, `start_time`, `restoration_date`, `restoration_time`

### **3. Renaming Columns**
- Column names were updated for better readability and consistency. 

  - Example: `"CAUSE.CATEGORY"` → `"cause"`, `"OUTAGE.DURATION"` → `"duration"`

### **4. Handling Missing and Invalid Values**
- **Anomaly level and demand loss**:

  - Missing values were initially replaced with `0`, converted to `float`, and then **replaced back with NaN** if they were `0` to maintain data integrity.

- **Outage duration**:

  - Missing values were filled with `0` and converted to **integer** format.

### **5. Combining Date and Time Columns**

- The **outage start date & time** and **restoration date & time** were merged into two new **timestamp columns**:

  - `"outage_start_timestamp"`  

  - `"restoration_timestamp"`

- The original **date and time columns** were dropped to avoid redundancy.

The first few rows of our DataFrame `outage` look like this:

| year | state     | nerc | climate_region       | climate_category | anomaly_level | cause            | cause_detail    | duration | demand_loss | customers_affected | outage_start_timestamp   | restoration_timestamp    |
|------|----------|------|----------------------|------------------|---------------|------------------|----------------|----------|-------------|--------------------|--------------------------|-------------------------|
| 2011 | Minnesota | MRO  | East North Central  | normal           | -0.3          | severe weather   | NaN            | 3060     | NaN         | 70000              | 2011-07-01 17:00:00      | 2011-07-03 20:00:00     |
| 2014 | Minnesota | MRO  | East North Central  | normal           | -0.1          | intentional attack | vandalism      | 1        | NaN         | NaN                | 2014-05-11 18:38:00      | 2014-05-11 18:39:00     |
| 2010 | Minnesota | MRO  | East North Central  | cold             | -1.5          | severe weather   | heavy wind     | 3000     | NaN         | 70000              | 2010-10-26 20:00:00      | 2010-10-28 22:00:00     |
| 2012 | Minnesota | MRO  | East North Central  | normal           | -0.1          | severe weather   | thunderstorm   | 2550     | NaN         | 68200              | 2012-06-19 04:30:00      | 2012-06-20 23:00:00     |
| 2015 | Minnesota | MRO  | East North Central  | warm             | 1.2           | severe weather   | NaN            | 1740     | 250.0       | 250000             | 2015-07-18 02:00:00      | 2015-07-19 07:00:00     |

## Exploratory Data Analysis

### **Univariate Analysis**

#### 1. <u>Frequency of Power Outages Across Climate Regions  </u>

The bar chart illustrates the frequency of power outages across different climate regions, providing insight into how often outages occur in each area. The height of each bar represents the number of recorded outage events, making it easy to compare the distribution across regions. Regions with higher frequencies may experience more frequent extreme weather conditions, such as hurricanes, heavy snowfall, or heatwaves, which could contribute to power disruptions. Conversely, regions with lower frequencies may have more stable climates or resilient infrastructure.  

As evident, the **Northeast region** experiences the most outages, whereas the **West North Central region** experiences the least outages.

<iframe
  src="images/figure_3.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### 2. <u>Distribution of Power Outage Causes </u>

The pie chart illustrates the distribution of power outages based on different cause categories, showing the proportion of outages attributed to each factor. Each slice represents a specific cause, with its size corresponding to the frequency of outages in that category. This visualization provides a clear overview of the primary reasons for power disruptions, such as extreme weather events, equipment failures, human errors, or other environmental factors.  

By analyzing the distribution, we can identify that **severe weather** is the major cause of power outages, accounting for almost **49.7%** of them, followed by **intentional attack** with **27.2%**.

<iframe
  src="images/figure_4.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### **Bivariate Analysis**

#### 1. <u>Impact of Climate Region on Outage Duration  </u>

This **scatter plot** visualizes the relationship between climate region and outage duration. Each point represents an outage, with the x-axis showing the climate region and the y-axis representing the duration of the outage. Different colors distinguish the climate regions, making it easier to identify patterns. 

<iframe
  src="images/figure_5.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### 2. <u>Impact of Climate Category on Anomaly Level </u>

This **bivariate analysis** using the box plot visualizes the relationship between **Climate Category** and **Anomaly Level**. The x-axis represents different climate categories, while the y-axis shows the distribution of anomaly levels within each category. Here are some key observations:

- Normal Climate: The anomaly levels are centered around zero, with a relatively narrow interquartile range (IQR), indicating lower variability.
- Cold Climate: The anomaly levels are mostly negative, suggesting that colder conditions are associated with lower anomaly levels. The spread is slightly larger, with some extreme values on the lower end.
- Warm Climate: The anomaly levels tend to be positive, with a higher median compared to other categories. There is a larger spread, and a few potential outliers indicate extreme anomaly levels.

<iframe
  src="images/figure_6.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### **Interesting Aggregates**

We have created a pivot table to analyze the relationship between climate conditions and power outage duration, where the average outage duration is calculated for each combination of `climate_category` (such as cold, warm, or normal) and `climate_region` (geographical climate zones). This allows for a structured comparison of outage duration across different climate conditions.


| climate_category | Central    | East North Central | Northeast  | Northwest  | South      | Southeast  | Southwest  | West       | West North Central |
|-----------------------------------|-----------|-------------------|------------|------------|------------|------------|------------|------------|-------------------|
| cold                              | 2676.338235 | 6568.789474       | 3568.766129 | 874.680851 | 1977.403509 | 1707.066667 | 499.208333  | 1735.171875 | 200.000000        |
| normal                            | 2682.147059 | 5207.710843       | 2261.327778 | 733.612245 | 3685.441441 | 2392.267606 | 283.260870  | 1142.322581 | 28.428571         |
| warm                              | 2080.896552 | 3022.117647       | 3990.311111 | 2212.555556 | 1672.101695 | 2528.941176 | 5127.681818 | 1942.016667 | 2486.500000       |


As evident, East North Central experiences the highest impact during cold and normal conditions, suggesting grid vulnerabilities in harsh weather. The Southwest faces the most disruption in warm conditions, likely due to extreme heat and increased energy demand. In contrast, West North Central remains the most resilient, with minimal average disruptions across all climate conditions.


## Assessment of Missingness

### **NMAR Analysis**

In our dataset, the `customers_affected` column could potentially be **Not Missing at Random (NMAR)**. This is because the missing values in this column might depend on the severity of the outage or reporting practices rather than being randomly missing. For example, smaller outages affecting fewer customers might be less likely to be reported accurately, whereas larger outages may have more complete data due to higher scrutiny and impact.  

To determine if `customers_affected` is truly NMAR, we would need additional data, such as utility company reporting policies, regional reporting standards, or manual record-keeping biases. If we can obtain external data explaining why some values are missing—such as a pattern in which certain companies or states systematically fail to report small-scale outages—then the missingness could instead be classified as **Missing at Random (MAR)** rather than NMAR.

### **Missingness Dependency**
To test missingness dependency, we will focus on the distribution of `customers_affected`. We will test this against the columns `cause` and `climate_category`.

### <b>1. customers_affected vs cause</b>

**Null Hypothesis:** The distribution of outage `cause` is independent of whether the `customers_affected` value is missing or not.

**Alternative Hypothesis:** The distribution of outage `cause` differs based on whether the `customers_affected` value is missing or not. 

<iframe
  src="images/figure_9.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We used TVD (Total Variation Distance) for our observed test statistic, which was found to be **0.557**. Whereas, our p-value was found to be **0.001** after conducting a permutation test with 500 repetitions. Since our p-value is much below the standard significance level of 0.05, we **reject the null hypothesis in favour of the alternative hypothesis**, which means that the distribution of outage causes differs based on whether the `customers_affected` value is missing or not, and hence, the missingness of `customers_affected` is **dependent** on `cause`.

<iframe
  src="images/figure_10.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### <b>2. customers_affected vs climate_category</b>

**Null Hypothesis:** The distribution of `climate_category` is independent of whether the `customers_affected` value is missing or not.

**Alternative Hypothesis:** The distribution of `climate_category` differs based on whether the `customers_affected` value is missing or not. 

<iframe
  src="images/figure_13.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We used TVD (Total Variation Distance) for our observed test statistic, which was found to be **0.036**. Whereas, our p-value was found to be **0.366** after conducting a permutation test with 500 repetitions. Since our p-value is much above the standard significance level of 0.05, we **fail to reject the null hypothesis**, which means that the distribution of `climate_category` is independent of whether the `customers_affected` value is missing or not, and hence, the missingness of `customers_affected` is **not dependent** on `climate_category`.

<iframe
  src="images/figure_12.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


## Hypothesis Testing

### Analysis of the Difference in Mean Customers Affected by Power Outages in Texas and Washington

We aim to determine whether the **number of customers affected by power outages** is greater on average in **Texas** compared to **Washington**. The relevant columns for this test are **`customers_affected`** and **`state`**. We will only be using data from outages that occurred in Texas or Washington.

- **Null Hypothesis:** There is no difference in the mean number of customers affected by the power outages in the states Texas and Washington.  
- **Alternative Hypothesis:** The mean number of customers affected by the power outages in the state of Texas is greater than that in Washington.

### Test Statistic:
The **difference in means**, specifically:  

*Mean(Customers Affected in Texas) - Mean(Customers Affected in Washington)*

### Methodology:
We performed a **permutation test with 10,000 simulations** to generate an empirical distribution of the test statistic under the null hypothesis. In each permutation, the `customers_affected` values were shuffled while keeping the state labels fixed. This allowed me to assess how likely the observed difference would occur by random chance.

### Results:

- **Observed difference in means:** 118984.25602727494
- **p-value:** 0.0001 

With a standard significance level of **0.05**, the p-value is sufficiently small, allowing us to **reject the null hypothesis**. This suggests that the number of customers affected by outages in Texas is significantly higher on average compared to Washington.

### Visualization:
The plot below shows the distribution of permuted differences, with the observed difference marked as a dashed red line. 

<iframe
  src="images/figure_15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


## Framing a Prediction Problem

Our model will try to predict the **climate category** of a power outage. This will be a **binary classification** problem because we are only focusing on outages occurring in either `"normal"` or `"cold"` climate conditions.  

The metric I am using to evaluate my model is **accuracy**, as the dataset appears to be balanced, making accuracy a reasonable measure of overall performance. However, I will also consider **precision, recall, and F1-score** to ensure the model is not biased toward one class, especially if class imbalance exists.  

At the time of prediction, we would know the **state, climate region, cause of outage, number of customers affected, and outage duration**. These features are available at the time of an outage and can be used to classify whether the climate conditions at the time were `"normal"` or `"cold"`.

## Baseline Model

Our model is a **binary classifier** that predicts the climate category of a power outage as either **"normal"** or **"cold"**. Understanding the climate category of an outage could help energy companies anticipate seasonal disruptions and optimize resource allocation for infrastructure resilience. The model is built using a **Random Forest Classifier**, with numerical and categorical features properly preprocessed.


### <u>Features Used in the Model:</u>

#### **Quantitative Features (Numerical)**

- **customers_affected**: The number of customers impacted by the outage.

- **duration**: The length of the outage in hours.


Preprocessing:

- Missing values were imputed using the **median strategy**.

- Features were **standardized** using `StandardScaler` to normalize them.

#### **Nominal Features (Categorical)**

- **state**: The state where the outage occurred.

- **climate_region**: The broader climate classification of the region.

- **cause**: The reason for the power outage.

Preprocessing:

- Missing values were imputed with **"Unknown"** using `SimpleImputer`.

- Categories were **one-hot encoded** using `OneHotEncoder`, ensuring that unseen values during testing wouldn’t cause issues.



### Target Variable:

- **climate_category** was encoded as `1` for **"cold"** and `o` for **"normal"**.


### Model Performance and Analysis

- **Train Accuracy**: **0.9877**

- **Test Accuracy**: **0.6066**

- **Classification Report**:

  - **Cold**: Precision = 0.49, Recall = 0.39, F1-score = 0.44

  - **Normal**: Precision = 0.66, Recall = 0.74, F1-score = 0.70


While the train accuracy is high, indicating that the model may be overfitting, the **test accuracy** of 0.6066 suggests poor generalization to new data. The **low recall for the "cold" category** indicates that the model is not effectively identifying severe weather-induced outages. Additionally, the **imbalanced classes** could be contributing to the model's weaker performance, as the model appears to favor predicting "normal" outages.

### Possible Improvements:

1. **Hyperparameter tuning** will be employed using **GridSearchCV** to optimize the model's complexity.

2. **Feature engineering** will be applied to include more time-based features and refine the data input.

3. **Class imbalance** will be addressed to improve performance for both classes, especially "cold" outages.


## Final Model

### **Modeling Algorithm**

For the final model, we kept the **RandomForestClassifier**, as it is an ensemble learning method that helps in reducing overfitting and improving predictive accuracy by aggregating the results from multiple decision trees. Random forests are particularly well-suited for complex datasets with both categorical and numerical features, as they can effectively capture non-linear relationships and interactions between features.

### **Feature Engineering and Selection**

The final model included several **key features** that we believe significantly contribute to the predictive power of the model:

- **`duration_hours`** and **`recovery_time_hours`**: These time-related features help the model capture the length of outages and recovery times, providing important context for both normal and cold weather-related outages.

- **`outage_month`**, **`outage_dayofyear`**, and **`outage_weekday`**: These features capture the seasonal and temporal patterns in outages. They are especially important as certain months, days, or seasons could be more prone to specific types of weather disruptions (e.g., winter storms leading to cold-related outages).

- **Categorical features like `state`, `climate_region`, `cause`, and `nerc`**: These provide regional and cause-specific context, which is crucial for understanding how weather and infrastructure influence power outages.

These features, particularly the time-based and regional ones, help the model better understand the environmental and seasonal factors that drive power outages, improving its ability to predict outages under various conditions.

### **Hyperparameter Tuning**

To optimize the performance of the RandomForestClassifier, we used **GridSearchCV** to tune the hyperparameters:

- **`n_estimators`**: 500 trees for greater model stability and improved accuracy.

- **`max_depth`**: Set to 5 to avoid overfitting and ensure the trees remain generalizable.

- **`min_samples_split`**: Set to 40 to avoid overly fine splits that would lead to overfitting.

- **`min_samples_leaf`**: Set to 25 to ensure that each leaf node contains a sufficient number of samples, improving robustness.


Since this is a fairly balanced dataset for our prediction problem, we have used accuracy along with some other classification report metrics to rate our model. The final model achieved a test accuracy of **75.41%** and a balanced classification performance, with an F1 score of **0.75**, improving on the baseline model’s performance. Compared to the baseline, which had lower accuracy and recall for the "cold" class, the final model shows a more robust performance across both classes, particularly with higher recall for the "normal" class (85%). Additionally, the model’s cross-validation accuracy of **74.28%** indicates better generalization and stability. This improvement suggests that the adjustments made, including feature selection and hyperparameter tuning, contributed to a more reliable and efficient model.l.

## Fairness Analysis

Our groups for the fairness analysis are high-affected vs low-affected customer groups, defined as outages that impact more than 60,000 customers (high impact) versus those that affect fewer than 60,000 customers (low impact).

We chose these groups because the severity of the outage (in terms of customer impact) could influence the accuracy of the model’s predictions for the climate category. It's important to ensure that the model performs equally well across both groups to avoid potential bias.

The evaluation metric used is precision, specifically the weighted precision, which calculates the proportion of true positive predictions relative to all positive predictions, giving weight to the size of each class. This metric was chosen because it focuses on minimizing false positives, which is important in ensuring that the model is making reliable and correct positive predictions across different groups. Unfairness in this could lead to significant misinterpretations or costly decisions. We will use permutation tests to calculate the precision for high-affected vs low-affected customer groups (that are randomly shuffled), keeping a standard significance level of **0.05** (5%).

- **Null Hypothesis:** The model is fair. Its precision for high-affected and low-affected customer groups is roughly the same, and any difference is due to random chance.

- **Alternative Hypothesis :** The model is unfair. Its precision for high-affected customer groups is significantly different from the accuracy for low-affected customer groups.

**Test Statistic:** Absolute difference in means

The p-value is computed by comparing the absolute observed precision difference (calculated from the actual data) to the distribution of absolute precision differences from the 500 permutations. Since our p-value is **0.108**, which is above the standard significance level, we **fail to reject the null hypothesis**. This means that there is no statistically significant difference in precision between high-affected and low-affected customer groups, suggesting that the model performs equally well across both groups. Therefore, we conclude that our model seems to be fair with respect to the precision metric.

<iframe
  src="images/fig100.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


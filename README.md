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
\[
\text{Mean(Customers Affected in Texas)} - \text{Mean(Customers Affected in Washington)}
\]

### Methodology:
We performed a **permutation test with 10,000 simulations** to generate an empirical distribution of the test statistic under the null hypothesis. In each permutation, the `customers_affected` values were shuffled while keeping the state labels fixed. This allowed me to assess how likely the observed difference would occur by random chance.

### Results:

- **Observed difference in means:** 118984.25602727494` 
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




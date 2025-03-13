# Analyzing Power Outages: Watts the Problem?

A project by:
- Vedant Vardhaan
- Ishaan Gosain

## Introduction

Power outages can have significant economic and social impacts, affecting households, businesses, and essential services. Understanding the underlying causes of outages is crucial for improving power grid reliability and enabling quicker response times.

This project analyzes historical outage data (1540 rows × 57 columns) to develop a model that predicts the cause of an outage based on factors like anomaly level, outage duration, climate region, and customer impact. By identifying patterns in the data, we can provide insights that help power companies take preventative measures and optimize resource allocation during disruptions.

The focus of this project is on the following question:
**"Developing a machine learning model that can predict outage causes based on real-time factors."**

This predictive capability could help power companies anticipate issues, allocate resources more effectively, and minimize disruption.

## Data Cleaning and Exploratory Data Analysis


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


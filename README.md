
# Cargo Ship Data Analysis Project -> Python to Power BI

![Dataset To Python To PowerBI](https://github.com/user-attachments/assets/e6dd9c6c-d76d-4510-bdfe-3b9ae2a0f948)


## Table of Contents
- [Objective](#objective)
- [Data Source](#data-source)
- [Tools Used](#tools-used)
- [Data Cleaning and Preparation](#data-cleaning-and-preparation)
- [DAX Measures and Calculated Columns](#dax-measures-and-calculated-columns)
- [Visualization](#visualization)
- [Analysis and Findings](#analysis-and-findings)
- [Recommendations](#recommendations)
- [Conclusion](#conclusion)
- [Potential Improvements](#potential-improvements)

## Objective
The objective of this project is to analyze vessel operations and navigational efficiency using AIS data from the Denmark Maritime Authority. The analysis aims to uncover key insights into how different types of vessels perform across various metrics, such as speed, draught, and course deviation. By examining these factors, the project seeks to identify patterns in navigational behavior, assess the efficiency of different vessel types and sizes, and provide actionable recommendations for optimizing maritime operations, improving navigational safety, and enhancing overall fleet performance.

## Data Source
**Source:** The dataset used in this project is derived from the Denmark Maritime Authority’s open-source AIS (Automatic Identification System) information.

**Description:**
- **AIS Technology:** AIS provides an effective means of tracking ships. It is based on Very High Frequency (VHF) broadcasting, and its carriage is mandatory for most Safety of Life at Sea (SOLAS) ships greater than 300 gross tonnes. Some vessels, like fishing and leisure craft, also carry it voluntarily.

- **Static Information (Immutable):** Provided by the vessel’s crew and transmitted every 6 minutes, regardless of the vessel’s movement status. This includes:
  - `mmsi`: Unique identification number for each vessel station.
  - `shiptype`: The type of ship (e.g., tanker, cargo, fishing).
  - `width`: The width of the vessel.
  - `length`: The length of the vessel.
  - `draught`: The vertical distance between the waterline and the bottom of the hull (keel).

- **Dynamic Information (Mutable):** Transmitted automatically every 2 to 10 seconds depending on the vessel’s speed and course while underway, and every 6 minutes while anchored. This includes:
  1. `navigationalstatus`: The vessel's instant situation (e.g., navigating, moored).
  2. `sog`: Speed over ground.
  3. `cog`: Course over ground (0 to 359 degrees).
  4. `heading`: Fore side (note that heading and cog are not the same due to factors like wind and current, though they are usually close).

## Tools Used
- **Python (Jupyter Notebook):** Used for data cleaning and preparation.
- **Power BI:** Used for data visualization and creating interactive dashboards.
- **GitHub:** Used for version control and hosting the project documentation.

Based on the code you provided, here's a detailed overview of the steps you took to clean and prepare the dataset in Python:

---

### Data Cleaning and Preparation in Python

**Overview:**  

The dataset was initially explored and cleaned to address missing values, ensure data type consistency, and remove potential biases. The cleaning process was systematic, ensuring that the data was suitable for further analysis and visualization.

#### 1. Initial Data Exploration

Before diving into cleaning, the dataset was explored to understand its structure and identify any immediate issues:

- **Previewing the Data:**

  The first few rows of the dataset were displayed using `print(df.head())` to get an initial look at the data and understand the types of information it contained.

- **Understanding the Columns:**

  The column names were printed using `print(df.columns)` to verify the fields available for analysis.

- **Dataset Dimensions:**

  The number of rows and columns in the dataset was determined using `print(f"Dataset contains {df.shape[0]} rows and {df.shape[1]} columns.")`, which provided a quick overview of the dataset's size.

- **Data Types and Missing Values:**

  The `df.info()` function was used to check the data types of each column and identify any missing values. This step helped in understanding the overall structure and completeness of the dataset.

- **Missing Values Summary:**

  A detailed summary of missing values in each column was generated using `df.isnull().sum()`. This provided a clear picture of where the data might be incomplete.

- **Statistical Summary:**

  The `df.describe()` function was used to generate summary statistics (e.g., mean, standard deviation) for numerical columns, helping to identify any unusual values or potential outliers.

- **Visualizing Missing Data:**

  A missing data matrix was plotted using `msno.matrix(df)` to visually assess the extent of missing data across the dataset.

#### 2. Data Backup and Initial Cleaning

- **Creating Backups:**

  Two backups of the original dataset were created (`df_backup` and `df2`) to ensure that the original data remained intact and to allow for comparison after cleaning.

- **Handling Missing Values in Critical Columns:**

  Rows with missing values in critical columns (`sog`, `cog`, `width`, `length`, `draught`, `heading`) were removed using `cleaned_data = df2.dropna(subset=['sog', 'cog', 'width', 'length', 'draught','heading'])`. This step ensured that only complete records were retained for analysis, which is crucial for maintaining data integrity.

#### 3. Ensuring Data Type Consistency

- **Converting Numerical Columns:**

  All key numerical columns (`sog`, `cog`, `heading`, `width`, `length`, `draught`) were explicitly converted to `float` type using a loop. This ensured that all these fields were in the correct format for any mathematical operations that might be required later.

```python

  numerical_columns = ['sog', 'cog', 'heading', 'width', 'length', 'draught']

  for col in numerical_columns:

      cleaned_data[col] = cleaned_data[col].astype(float)
  ```

#### 4. Checking for Data Bias Post-Cleaning

- **Distribution Comparison:**

  To ensure that the data cleaning process did not introduce bias, especially in terms of removing certain types of ships, the distribution of the `shiptype` column before and after cleaning was compared. This comparison was done using value counts normalized to percentages.

```python

  initial_distribution = df2['shiptype'].value_counts(normalize=True)

  cleaned_distribution = cleaned_data['shiptype'].value_counts(normalize=True)

  distribution_comparison = pd.DataFrame({

      'Initial Distribution': initial_distribution,

      'Cleaned Distribution': cleaned_distribution

  }).fillna(0)

```

  This step was crucial to validate that the cleaning process did not disproportionately remove any specific ship types, which could have skewed the analysis.

- **Assessing Rows Removed:**

  The number of rows removed during the cleaning process was calculated, and the percentage of data removed was reported. This provided transparency about the impact of the cleaning process.

```python

  rows_removed = df2.shape[0] - cleaned_data.shape[0]

  percentage_removed = (rows_removed / df2.shape[0]) * 100

```

#### 5. Visualization of Data Distributions

- **Visualizing Key Distributions:**

  The distributions of `width`, `length`, and `draught` were plotted using histograms to visualize the data's spread and identify any remaining outliers or unusual patterns.

```python

  plt.figure(figsize=(15, 5))

  plt.subplot(1, 3, 1)

  sns.histplot(cleaned_data['width'], kde=True)

  plt.title('Distribution of Width')

  plt.subplot(1, 3, 2)

  sns.histplot(cleaned_data['length'], kde=True)

  plt.title('Distribution of Length')

  plt.subplot(1, 3, 3)

  sns.histplot(cleaned_data['draught'], kde=True)

  plt.title('Distribution of Draught')

  plt.tight_layout()

  plt.show()

```

  This visualization step helped confirm that the data was well-behaved and that the cleaning process had successfully prepared the dataset for analysis.

#### 6. Final Data Export

- **Saving the Cleaned Data:**

  After all cleaning steps were completed, the cleaned dataset was exported to a new CSV file for use in Power BI.

```python

  cleaned_file_path = 'path_to_cleaned_ais_data.csv'

  cleaned_data.to_csv(cleaned_file_path, index=False)

```

  This step ensured that the cleaned and validated data was preserved and ready for further analysis in Power BI.

- **Final Checks and Reporting:**

  A final check of the cleaned data was performed to ensure no remaining null values, and the cleaning process's impact on data distribution was reported.

```python

  print(f"Total rows removed: {rows_removed} ({percentage_removed:.2f}%)")

  print("Bias check - Ship type distribution before and after cleaning:")

  print(distribution_comparison)

  print(f"Cleaned data saved to {cleaned_file_path}")
```

### Power BI Analysis and Visualization

In this project, Power BI was utilized to conduct a detailed analysis and create interactive visualizations based on the cleaned AIS dataset. The following sections describe the measures, calculated columns, and visualizations developed in Power BI to gain insights into vessel operations and navigational efficiency.

#### 1\. Importing the Cleaned Dataset

After the data cleaning and preparation process in Python, the cleaned dataset was imported into Power BI. This dataset served as the foundation for all subsequent analysis and visualization. Power BI's powerful data modeling capabilities allowed for the creation of various calculated columns and measures, which were used to extract meaningful insights from the data.

#### 2\. Creation of DAX Measures and Calculated Columns

To perform advanced calculations and derive meaningful metrics, several DAX measures and calculated columns were created. These calculations enabled the analysis of key aspects such as vessel speed, draught, size categories, and operational status.

Here are the key DAX measures and calculated columns used in this analysis:

##### Total Vessels (Measure)

This measure calculates the distinct count of vessels in the dataset based on their mmsi (Maritime Mobile Service Identity), which uniquely identifies each vessel.

```dax
Total Vessels = DISTINCTCOUNT(path_to_cleaned_ais_data[mmsi])
```

-   Purpose: This measure was used to count the total number of unique vessels in the dataset, providing a high-level overview of the dataset's scope.

##### Average Speed (Measure)

This measure calculates the average speed over ground (sog) of all vessels in the dataset.

```dax
Average Speed = AVERAGE(path_to_cleaned_ais_data[sog])
```

-   Purpose: The average speed measure was critical for analyzing the overall performance and efficiency of different types of vessels. It provided insights into how fast vessels typically traveled.

##### Average Draught (Measure)

This measure calculates the average draught of vessels, which represents the vertical distance between the waterline and the bottom of the hull (keel).

```dax
Average Draught = AVERAGE(path_to_cleaned_ais_data[draught])
```

-   Purpose: Understanding the average draught helps in assessing how heavily loaded vessels are, which can influence their navigational efficiency and safety.

##### Draft-to-Size Ratio (Measure)

This measure calculates the ratio of draught to the vessel's length, providing an indication of how well the vessel's size corresponds to its draught.

```dax
Draft-to-Size Ratio = DIVIDE(AVERAGE(path_to_cleaned_ais_data[draught]), AVERAGE(path_to_cleaned_ais_data[length]))
```

-   Purpose: The Draft-to-Size Ratio is useful for evaluating whether vessels are optimally loaded relative to their size, which can impact fuel efficiency and maneuverability.

##### Ship Type Percentage (Measure)

This measure calculates the percentage of each ship type within the dataset, allowing for an understanding of the distribution of different vessel types.

```dax
Ship Type Percentage = DIVIDE(COUNTROWS(path_to_cleaned_ais_data), CALCULATE(COUNTROWS(path_to_cleaned_ais_data), ALL(path_to_cleaned_ais_data[shiptype]))) * 100
```

-   Purpose: This measure was used to visualize the proportion of different ship types, which is critical for analyzing fleet composition.

##### Speed Category (Measure)

This measure categorizes vessels into "Slow", "Moderate", and "Fast" speed categories based on their average speed.

```dax
Speed Category = 

SWITCH(
    TRUE(),

    AVERAGE(path_to_cleaned_ais_data[sog]) < 5, "Slow",

    AVERAGE(path_to_cleaned_ais_data[sog]) < 15, "Moderate",

    "Fast"
)
```

-   Purpose: Categorizing vessels by speed allows for a more granular analysis of vessel performance, highlighting differences in operational behavior among various types of ships.

##### Vessel Size Category (Calculated Column)

This calculated column classifies vessels into "Small", "Medium", and "Large" categories based on their length.

```dax
Vessel Size Category = 

SWITCH(

    TRUE(),

    path_to_cleaned_ais_data[length] < 50, "Small",

    path_to_cleaned_ais_data[length] < 150, "Medium",

    "Large"
)
```

-   Purpose: The Vessel Size Category column enabled comparisons between different size classes of vessels, helping to identify patterns related to vessel size and performance.

##### Vessel Size Shiptype (Measure)

This measure creates a combined string that categorizes vessels by both size and ship type.

```dax
VesselSize_ShipType = [Vessel Size Category] & " - " & [shiptype]
```

-   Purpose: Combining vessel size and ship type into a single category allowed for more nuanced analyses, where both dimensions are considered simultaneously.

##### Waypoint (Measure)

This measure categorizes vessels into directional waypoints based on their route data.

```dax
waypoint = 

SWITCH(

    TRUE(),

    path_to_cleaned_ais_data[route] >= 0 && path_to_cleaned_ais_data[route] < 45, "NNE",

    path_to_cleaned_ais_data[route] >= 45 && path_to_cleaned_ais_data[route] < 90, "ENE",

    path_to_cleaned_ais_data[route] >= 90 && path_to_cleaned_ais_data[route] < 135, "ESE",

    path_to_cleaned_ais_data[route] >= 135 && path_to_cleaned_ais_data[route] < 180, "SSE",

    path_to_cleaned_ais_data[route] >= 180 && path_to_cleaned_ais_data[route] < 225, "SSW",

    path_to_cleaned_ais_data[route] >= 225 && path_to_cleaned_ais_data[route] < 270, "WSW",

    path_to_cleaned_ais_data[route] >= 270 && path_to_cleaned_ais_data[route] < 315, "WNW",

    path_to_cleaned_ais_data[route] >= 315 && path_to_cleaned_ais_data[route] < 360, "NNW",

    BLANK()
)
```
-   Purpose: The Waypoint measure provided directional context to the route data, allowing for the analysis of vessel movements in specific directions.

##### Weighted Average Draught (Measure)

This measure calculates the weighted average draught, taking into account the length of each vessel.

```dax
Weighted Average Draught = DIVIDE(SUMX(path_to_cleaned_ais_data, path_to_cleaned_ais_data[draught] * path_to_cleaned_ais_data[length]), SUM(path_to_cleaned_ais_data[length]))
```

-   Purpose: The weighted average draught helps in understanding how draught varies with vessel size, providing a more accurate picture of how different vessels are loaded.

##### Route (Measure)

This measure determines the route a vessel is following, defaulting to cog if available, and heading otherwise.

```dax
route = 

IF(

    ISBLANK(path_to_cleaned_ais_data[cog]), 

    path_to_cleaned_ais_data[heading], 

    path_to_cleaned_ais_data[cog])
```

-   Purpose: The Route measure ensures that each vessel has a route direction, even if one of the directional indicators (cog or heading) is missing.

#### 3\. Visualization Creation

Using these DAX measures and calculated columns, a variety of visualizations were created to analyze vessel operations:

-   Bar Charts: Visualizations were created to compare the number of "FIX" versus "Normal" vessels by ship type or vessel size category. This helped identify patterns in vessel status.

-   Line Charts: Charts were developed to display trends in average speed across different ship types, providing insights into operational efficiency.

-   Scatter Plots: Plots were created to analyze the relationship between course deviation and speed, highlighting any outliers or patterns across vessel types.

-   Treemaps: Treemaps were used to show the distribution of ship types within different size categories, allowing for easy comparison of fleet composition.

-   KPI Cards: Key metrics such as Total Vessels, Average Speed, and Average Draught were highlighted using KPI cards to provide a quick overview of important statistics.

#### 4\. Interactive Dashboards

The visualizations were combined into interactive dashboards, allowing users to drill down into specific data points and filter by different dimensions such as ship type, vessel size, or operational status. These dashboards provided an intuitive interface for exploring the AIS data and deriving actionable insights.]

![image1](https://github.com/user-attachments/assets/6e199ff6-6fa3-4816-a049-9818ea5efc15)
![image](https://github.com/user-attachments/assets/25209638-6d30-4bb3-9447-4496cd7038e4)
![image](https://github.com/user-attachments/assets/edf129f8-d414-4829-9622-f615a630955b)


### Summary

Power BI was instrumental in transforming the cleaned AIS dataset into actionable insights through the creation of calculated columns and measures. The use of DAX allowed for sophisticated calculations that enabled in-depth analysis of vessel operations, and the interactive visualizations provided a comprehensive view of the data. This combination of powerful analytics and intuitive visualizations made it possible to uncover valuable patterns in the maritime data.

### Analysis and Findings

The analysis of the AIS data using Power BI revealed several key insights:

-   Vessel Speed: The average speed of vessels varied significantly by ship type, with smaller vessels generally moving faster than larger ones. Cargo ships tended to have a moderate speed, while tankers were among the slowest.

-   Draught Patterns: Larger vessels exhibited higher draught-to-size ratios, indicating that they are more heavily loaded relative to their size compared to smaller vessels.

-   Navigational Behavior: The waypoint analysis revealed that certain ship types frequently navigated in specific directions, likely due to their operational routes and destinations.

-   Operational Status: The comparison of "FIX" versus "Normal" vessels highlighted that most stationary vessels were smaller in size and belonged to specific ship types like fishing boats and leisure crafts.

### Recommendations

-   Optimize Load Management: Vessels with a high draught-to-size ratio should be examined to ensure they are loaded efficiently. Reducing unnecessary load could improve fuel efficiency and reduce operational costs.

-   Enhance Navigational Planning: The analysis of navigational patterns suggests that certain ship types could benefit from more optimized routing to reduce travel time and improve efficiency.

-   Monitor Stationary Vessels: Vessels tagged as "FIX" should be closely monitored, especially if they remain stationary for extended periods, as this could indicate potential mechanical issues or operational inefficiencies.

### Conclusion

This project successfully leveraged AIS data to analyze and visualize vessel operations and navigational efficiency. Through a combination of Python for data cleaning and Power BI for analysis and visualization, key insights were uncovered that can help optimize maritime operations. The findings and recommendations provided offer actionable steps for improving the efficiency, safety, and overall performance of various vessel types.

### Potential Improvements

-   Integration with Weather Data: Including weather data could provide more context to navigational patterns and help explain certain anomalies in vessel behavior.

-   Predictive Analytics: Developing predictive models to forecast vessel performance based on historical AIS data could provide proactive insights for maritime operations.

-   Real-time Analysis: Expanding the project to include real-time AIS data would allow for continuous monitoring and immediate decision-making support.




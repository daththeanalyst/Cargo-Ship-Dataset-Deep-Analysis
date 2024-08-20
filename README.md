
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

### Power BI Dashboard Overview - Page 1

![image1](https://github.com/user-attachments/assets/6e199ff6-6fa3-4816-a049-9818ea5efc15)

This Power BI dashboard provides a comprehensive overview of vessel operations based on AIS (Automatic Identification System) data. It is designed to analyze various metrics that influence navigational efficiency and vessel performance.

- **Average Speed by Ship Type**: 
  The line chart on the top left illustrates the average speed of different ship types, providing insights into which types of vessels tend to move faster or slower. High-Speed Crafts (HSC) and law enforcement ships have the highest average speeds, while port tenders and towing vessels are generally slower.

- **Weighted Average Draught by Ship Type**: 
  The bar chart on the top right shows the weighted average draught for different ship types. This metric, which reflects how deeply a ship is submerged in water, can indicate the vessel's load. Military and towing ships have the highest draught, suggesting they carry heavier loads compared to other vessel types.

- **Count of Ships per Type & Size**: 
  The treemap at the bottom left categorizes ships by both type and size. It visually represents the distribution of various ship sizes within each ship type, highlighting that medium-sized cargo ships make up a significant portion of the fleet.

- **Average Speed by Course Deviation**: 
  The scatter plot at the bottom right examines the relationship between course deviation and average speed for different ship types. The size of each bubble represents the number of vessels in that category, allowing users to identify patterns such as the high deviation of certain ships like HSC and their corresponding speed.

Overall, this dashboard enables a detailed analysis of how different types of vessels perform under various conditions, aiding in decision-making for maritime operations and navigational safety.

### Power BI Dashboard Overview - Page 2 - Detailed Analytics

![image](https://github.com/user-attachments/assets/25209638-6d30-4bb3-9447-4496cd7038e4)

This section of the Power BI dashboard provides a more detailed analysis of directional patterns and vessel dimensions, offering insights into how different ship types navigate and their physical characteristics.

- **Directional Patterns by Ship Type**: 
  The heatmap on the left side of the dashboard visualizes the directional patterns of various ship types. Each color-coded cell represents the percentage of ships that navigate in a specific direction (e.g., ENE, NNE, WNW) for each ship type. This helps identify common navigational routes and preferences for different vessel categories. For example, HSC (High-Speed Craft) shows a significant preference for the ENE direction, while law enforcement vessels have a more evenly distributed navigational pattern.

- **Vessel Dimensions Table**: 
  On the right side, a table displays key physical attributes of different ship types, including the average length, width, draught, and the Draft-to-Size Ratio. The "Average of Draught" column is highlighted, allowing for quick comparison of how deeply different vessels are submerged in water. For instance, tankers have a notably higher average draught, indicating they carry heavier loads compared to other vessel types.

- **Ship Type Slider**: 
  At the bottom right, a ship type slider allows users to filter the data displayed in the heatmap and table by specific ship types. This interactive feature enhances the dashboard's usability by enabling focused analysis on particular categories of interest, such as comparing cargo ships versus passenger ships.

Overall, this detailed analytics dashboard provides a comprehensive view of how different ship types operate and navigate, which is essential for understanding the broader patterns in maritime operations and making informed decisions.

### Power BI Dashboard Overview - Page 3 - Cargo Ship Size Insights

![image](https://github.com/user-attachments/assets/edf129f8-d414-4829-9622-f615a630955b)

This section of the Power BI dashboard focuses on providing insights into cargo ship operations by analyzing vessel size categories. It presents key performance metrics and visual comparisons across different ship sizes.

- **KPI Cards**:
  The dashboard begins with a set of Key Performance Indicator (KPI) cards on the left that display crucial statistics:
  - **Average Speed**: 11.75 knots
  - **Average Length**: 133.56 meters
  - **Average Width**: 21.15 meters
  - **Average Draught**: 6.61 meters
  - **Course Deviation**: 0.01 degrees
  
  These KPIs provide a quick overview of the overall performance and dimensions of the cargo ships in the dataset.

- **Average Speed by Vessel Size Category and Ship Type**:
  The bar chart on the top right shows the average speed of cargo ships categorized by vessel size (Large, Medium, Small) and ship type (e.g., Cargo, Dredging, Fishing). This allows for a comparative analysis of how different vessel sizes and types perform in terms of speed. For example, larger vessels like tankers tend to have lower average speeds compared to smaller, more agile vessels.

- **Course Deviation by Vessel Size Category**:
  The bar chart in the bottom center highlights the course deviation across different vessel size categories. It shows that smaller vessels tend to have slightly higher course deviations compared to larger ships, indicating potential challenges in maintaining a steady course.

- **Vessel Operational Status: Normal vs. FIX**:
  The stacked bar chart on the bottom right compares the count of vessels in "Normal" operational status versus those tagged as "FIX" (vessels with low speed and no route information). The chart is further broken down by vessel size category, revealing that smaller vessels are more likely to be in the "FIX" status compared to larger vessels.

- **Vessel Size Filter**:
  On the left, a filter allows users to select and focus on specific vessel size categories (Large, Medium, Small). This interactive feature enables detailed analysis based on the user's interest, whether it's understanding the performance of larger cargo ships or examining smaller vessels.

Overall, this dashboard provides a detailed breakdown of cargo ship operations, emphasizing the impact of vessel size on speed, course deviation, and operational status. It serves as a valuable tool for identifying patterns and making informed decisions regarding cargo ship management.

### Summary

This project utilized AIS (Automatic Identification System) data from the Denmark Maritime Authority to conduct an in-depth analysis of vessel operations and navigational efficiency. The project employed Python for data cleaning and preparation, followed by extensive analysis and visualization using Power BI. The dataset included both static and dynamic vessel information, allowing for a comprehensive evaluation of various metrics such as speed, draught, course deviation, and operational status across different ship types and sizes.

The Power BI dashboards developed in this project provide an intuitive and interactive interface for exploring these metrics, offering insights into how different vessels operate under varying conditions. The analysis revealed key patterns in vessel behavior, identified inefficiencies, and proposed actionable recommendations for optimizing maritime operations.

### Analysis and Findings

The analysis conducted using Power BI revealed several significant insights into the operations of various vessel types and sizes. Below is a detailed breakdown of the key findings:

#### 1. Vessel Speed Analysis
   - **Average Speed by Ship Type:** The line chart analysis showed that High-Speed Crafts (HSC) and law enforcement vessels are among the fastest, while port tenders and towing vessels exhibit lower average speeds. This variation is likely influenced by the operational roles of these vessels—faster vessels are often involved in time-sensitive missions, such as law enforcement or emergency response, while slower vessels might be engaged in activities that require careful maneuvering, such as towing or port operations.
   - **Impact of Size on Speed:** Larger vessels, such as tankers and large cargo ships, were observed to have lower average speeds compared to smaller vessels. This is consistent with the expectation that larger, heavily loaded vessels require more time to accelerate and decelerate, thus reducing their overall speed.

#### 2. Draught and Load Patterns
   - **Weighted Average Draught by Ship Type:** The analysis of draught data revealed that military and towing vessels tend to have higher draughts, indicating that these ships are often more heavily loaded. This could reflect the operational demands of these vessels, such as the need to carry significant equipment or supplies.
   - **Draft-to-Size Ratio:** The Draft-to-Size Ratio measure highlighted that larger vessels tend to have a higher ratio, suggesting that they are more heavily loaded relative to their size. This is an important finding as it indicates that larger vessels may face greater challenges in terms of fuel efficiency and maneuverability, especially in congested or shallow waters.

#### 3. Navigational Behavior
   - **Directional Patterns by Ship Type:** The heatmap analysis showed distinct navigational patterns among different ship types. For example, HSCs showed a strong preference for the ENE direction, while other vessels, such as law enforcement ships, exhibited a more balanced distribution across multiple directions. Understanding these patterns is crucial for optimizing traffic management in busy maritime regions.
   - **Course Deviation:** Smaller vessels were found to have higher course deviations compared to larger ones. This might indicate that smaller vessels are more affected by environmental factors such as wind and currents, or it could reflect less precise navigational control.

#### 4. Operational Status
   - **Normal vs. FIX Status:** The analysis of operational status revealed that a significant number of smaller vessels were categorized as "FIX," meaning they had low speed and no route information. This could suggest issues such as mechanical failures, inefficient operations, or simply vessels that are anchored or docked.

### Recommendations
Based on the analysis and findings, the following recommendations are proposed to enhance maritime operations, improve navigational efficiency, and optimize vessel performance:

#### 1. Load Management Optimization
   - **Implement Dynamic Load Balancing:** To address the high Draft-to-Size Ratios observed in larger vessels, it is recommended to implement dynamic load balancing systems. These systems can optimize cargo distribution in real-time, ensuring that vessels are loaded efficiently, which could lead to improved fuel efficiency and enhanced safety.
   - **Utilize Real-Time Load Monitoring:** Deploy real-time sensors to continuously monitor vessel load. These sensors can provide instant feedback, allowing crews to adjust load distribution as needed to maintain optimal vessel balance and stability.

#### 2. Enhanced Navigational Planning
   - **Integrate Predictive Routing Software:** To reduce course deviations and improve fuel efficiency, vessels should be equipped with predictive routing software. This software can take into account factors such as traffic density, weather conditions, and fuel efficiency, allowing for the selection of optimal routes.
   - **Advanced GPS and Training:** Incorporate advanced GPS systems that provide real-time navigational data, and ensure that navigational staff are regularly trained on using these systems. This will help reduce human error and enhance overall navigational precision.

#### 3. Operational Efficiency Improvements
   - **Predictive Maintenance for FIX Vessels:** For vessels frequently categorized as "FIX," it is recommended to implement predictive maintenance schedules. By analyzing historical data and current vessel conditions, potential issues can be identified and addressed before they result in significant downtime or operational inefficiencies.
   - **Regular Monitoring of Stationary Vessels:** Establish protocols for regular monitoring of vessels that remain in the "FIX" status for extended periods. This can help identify whether the vessels are facing operational issues or if they are stationary due to routine operations like docking or anchoring.

#### 4. Future Enhancements
   - **Weather Data Integration:** Integrating real-time weather data into the navigational systems can provide a more comprehensive analysis of the factors affecting vessel operations. Weather conditions such as wind speed and direction, wave height, and visibility can significantly impact navigational decisions.
   - **Real-Time Data Analysis:** Transitioning from static AIS data analysis to real-time data analysis can provide more immediate insights and allow for proactive decision-making. This can be achieved by setting up data pipelines using tools like Apache Kafka, enabling continuous monitoring and quick responses to any operational anomalies.

### Conclusion

This project successfully leveraged AIS data to provide a detailed analysis of vessel operations and navigational efficiency. The combination of Python for data cleaning and Power BI for visualization allowed for the extraction of valuable insights, which can be used to optimize maritime operations, enhance navigational safety, and improve the overall efficiency of the fleet.

Key findings from the analysis highlighted the significant impact of vessel size on speed and draught, the importance of load management, and the need for enhanced navigational planning. The recommendations provided offer actionable steps that can be implemented to address these issues, ultimately leading to more efficient and safer maritime operations.

Moving forward, the integration of additional data sources such as real-time weather information and the implementation of predictive analytics could further enhance the insights gained from AIS data. These improvements would allow for even more informed decision-making and help to ensure that maritime operations are as efficient, safe, and effective as possible.


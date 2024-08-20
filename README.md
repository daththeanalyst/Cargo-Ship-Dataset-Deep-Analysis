
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






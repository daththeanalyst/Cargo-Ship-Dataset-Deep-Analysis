
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

## Data Cleaning and Preparation in Python
**Overview:** The dataset initially had issues such as missing values, inconsistent data types, and outliers. The data cleaning process involved handling these issues to ensure the dataset was suitable for analysis.

### 1. Handling Missing Values in Dynamic Columns
The dataset included several dynamic columns, such as `cog` (Course Over Ground) and `heading`, which had missing values due to various reasons like data transmission issues. To ensure these gaps did not affect the subsequent analysis, the following steps were taken:

**Combining `cog` and `heading` to Fill Missing Values:**
A new column called `route` was created to consolidate the data from `cog` and `heading`. The logic implemented was to use the `cog` value if it was available; otherwise, the `heading` value was used. This step ensured that every vessel had a route direction, either derived from `cog` or `heading`.

```python
df['route'] = np.where(df['cog'].isnull(), df['heading'], df['cog'])

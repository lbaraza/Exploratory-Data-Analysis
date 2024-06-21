# Exploratory-Data-Analysis

## Objectives

1. Identify the distribution of data in the dataset.
2. Identify outliers in the dataset.
3. Remove outliers from the dataset.
4. Identify correlation between features in the dataset.

## Steps

### 1. Import the pandas module

```python
import pandas as pd
```

### 2. Load the dataset into a DataFrame

First, download the dataset if you are using JupyterLite:

```python
from pyodide.http import pyfetch

async def download(url, filename):
    response = await pyfetch(url)
    if response.status == 200:
        with open(filename, "wb") as f:
            f.write(await response.bytes())

file_path = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/LargeData/m2_survey_data.csv"
await download(file_path, "m2_survey_data.csv")
```

Load the data into a DataFrame:

```python
df = pd.read_csv("m2_survey_data.csv")
```

If you are working on your local machine, you can directly load the data:

```python
# df = pd.read_csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/LargeData/m2_survey_data.csv")
```

Install seaborn for plotting:

```python
%pip install seaborn
```

### 3. Distribution

Determine how the data is distributed. The column `ConvertedComp` contains salary converted to annual USD using the exchange rate on 2019-02-01. This assumes 12 working months and 50 working weeks.

Plot the distribution curve and histogram for the column `ConvertedComp`:

```python
import matplotlib.pyplot as plt
import seaborn as sns

# Remove missing values from the ConvertedComp column
df = df.dropna(subset=['ConvertedComp'])

# Plot the distribution curve
plt.figure(figsize=(10, 6))
sns.histplot(df['ConvertedComp'], kde=True, color='blue', bins=50)
plt.title('Distribution of ConvertedComp')
plt.xlabel('Converted Compensation')
plt.ylabel('Frequency')
plt.show()

# Plot the histogram
plt.figure(figsize=(10,6))
plt.hist(df['ConvertedComp'], bins=30, color='blue', edgecolor='black')
plt.title('Histogram of Converted Compensation')
plt.xlabel('Converted Compensation')
plt.ylabel('Frequency')
plt.show()
```

### 4. Median

Find the median of the column `ConvertedComp`:

```python
median_converted_comp = df['ConvertedComp'].median()
median_converted_comp
# Output: 57745.0
```

### 5. Gender Analysis

Find how many responders identified themselves only as a Man:

```python
df['Gender'].value_counts()
# Output:
# Man                                                            9725
# Woman                                                           679
# Non-binary, genderqueer, or gender non-conforming                59
# Man;Non-binary, genderqueer, or gender non-conforming            26
# Woman;Non-binary, genderqueer, or gender non-conforming          14
# Woman;Man                                                         7
# Woman;Man;Non-binary, genderqueer, or gender non-conforming       2
```

Find the median `ConvertedComp` for responders identified as Woman:

```python
df_woman = df[df['Gender'] == 'Woman']
df_woman['ConvertedComp'].median()
# Output: 57708.0
```

### 6. Age Analysis

Find the median age of survey respondents:

```python
median_age = df['Age'].median()
median_age
# Output: 29.0
```

Give the five-number summary for the column `Age`:

```python
df['Age'].describe()
# Output:
# count    10354.000000
# mean        30.833040
# std          7.389983
# min         16.000000
# 25%         25.000000
# 50%         29.000000
# 75%         35.000000
# max         99.000000
```

Plot a histogram of the column `Age`:

```python
plt.figure(figsize=(10,6))
plt.hist(df['Age'], bins=30, color='purple', edgecolor='black')
plt.title('Histogram of Age')
plt.xlabel('Age')
plt.ylabel('Frequency')
plt.show()
```

### 7. Interquartile Range (IQR)

Find the Interquartile Range (IQR) for the column `ConvertedComp`:

```python
Q1 = df['ConvertedComp'].quantile(0.25)
Q3 = df['ConvertedComp'].quantile(0.75)
IQR = Q3 - Q1
IQR
# Output: 73132.0
```

Find the upper and lower bounds for outliers:

```python
upper_bound = Q3 + 1.5 * IQR
lower_bound = Q1 - 1.5 * IQR

print("Upper Bound for outliers:", upper_bound)
print("Lower Bound for outliers:", lower_bound)
# Output:
# Upper Bound for outliers: 209698.0
# Lower Bound for outliers: -82830.0
```

Identify how many outliers are in the `ConvertedComp` column:

```python
outliers = df[(df['ConvertedComp'] < lower_bound) | (df['ConvertedComp'] > upper_bound)]
num_outliers = outliers.shape[0]
num_outliers
# Output: 879
```

### 8. Remove Outliers

Create a new DataFrame by removing the outliers from the `ConvertedComp` column:

```python
df_no_outliers = df[(df['ConvertedComp'] >= lower_bound) & (df['ConvertedComp'] <= upper_bound)]

# Display the new DataFrame without outliers
print("New DataFrame without outliers:")
print(df_no_outliers)

# Calculate the median ConvertedComp after removing outliers
median_converted_comp_no_outliers = df_no_outliers['ConvertedComp'].median()
median_converted_comp_no_outliers
# Output: 52704.0

# Calculate the mean ConvertedComp after removing outliers
mean_converted_comp_no_outliers = df_no_outliers['ConvertedComp'].mean()
mean_converted_comp_no_outliers
# Output: 59883.20838915799
```

### 9. Correlation

Find the correlation between Age and all other numerical columns:

```python
df.corr()
```

## Conclusion

This repository demonstrates how to perform exploratory data analysis (EDA) on a cleaned dataset using pandas and seaborn. 
By following the steps outlined,you can analyze the distribution of data,identify and remove outliers, and determine the correlation between different features in the dataset.

# College-FullStack-Lab-Experiment
Source code and documentation for a full-stack web development laboratory experiment completed as a college assignment.

### Git Commands

```
git config --global user.name "Atharv"
git config --global user.email "Atharv@1985"
mkdir myproject
cd myproject
git init
echo hello > file.text
git  add file.text
git status
git commit -m "Added"
git log

```


## Backend
npm init -y
npm install express mongoose cores
# run using
node server.js


```jsx

from google.colab import files
uploaded = files.upload()

```
# Missing value

```jsx
import pandas as pd
from sklearn.impute import SimpleImputer

# Step 1: Load the CSV file
file_path = "housePrice.csv"  # Replace with your file path
df = pd.read_csv(file_path)

# Step 2: Basic data exploration
print("First 5 rows of the dataset:")
print(df.head())
print("\nDataset Info:")
print(df.info())
print("\nSummary statistics:")
print(df.describe())
print(df)
# Step 3: Handle missing values
# Identify columns with missing values
missing_values = df.isnull().sum()
print("\nMissing values in each column:")
print(missing_values)

# Impute missing values
# Numerical columns: Replace missing values with the mean
num_cols = df.select_dtypes(include=["float64", "int64"]).columns
num_imputer = SimpleImputer(strategy="mean")
df[num_cols] = num_imputer.fit_transform(df[num_cols])


'''
# Impute missing values
# Numerical columns: Replace missing values with the median
num_cols = df.select_dtypes(include=["float64", "int64"]).columns
num_imputer = SimpleImputer(strategy="median")
df[num_cols] = num_imputer.fit_transform(df[num_cols])
'''


print("\nData after imputing missing values:")
print(df)

# Step 4: Save the modified dataset to a new CSV file
output_file_path = "housePrice_modified.csv"  # Specify the output file path
df.to_csv(output_file_path, index=False)
print(f"\nModified dataset saved to {output_file_path}")
```
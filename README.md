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
# labelencoding and oneHotEncoder

```jsx
import pandas as pd
from sklearn.preprocessing import LabelEncoder, OneHotEncoder

# Load the CSV file into a DataFrame
file_path = 'heart_attack_predictions_modified.csv'  # Replace with the path to your CSV file
df = pd.read_csv(file_path)

# Display the first few rows to identify categorical columns
print("Dataset Preview:")
print(df.head())

# Identify categorical columns
categorical_columns = df.select_dtypes(include=['object']).columns
print("\nCategorical Columns:")
print(categorical_columns)

# Check unique values and suggest whether each column is ordinal or nominal
for col in categorical_columns:
    unique_values = df[col].unique()
    unique_count = len(unique_values)
    
    print(f"\nColumn: {col}")
    print(f"Unique Values ({unique_count}): {unique_values}")
    
    # Basic heuristic to classify nominal vs ordinal
    if unique_count < 10:  # Small number of unique values might suggest order
        print(f"Suggested: Check if this column is ORDINAL (values may have order)")
    else:
        print(f"Suggested: Likely NOMINAL (no inherent order)")

# Manual Review Tip
print("\nNOTE: Use domain knowledge to confirm if the suggested columns are ordinal or nominal.")

# Initialize a dictionary to store label-encoded column classes for reference
label_encoded_columns = {}

# Create a DataFrame for One-Hot Encoded features
onehot_encoded_df = pd.DataFrame()

# Encoding categorical columns
for col in categorical_columns:
    # Label Encoding (suitable for ordinal categorical data)
    label_encoder = LabelEncoder()
    df[col + '_LabelEncoded'] = label_encoder.fit_transform(df[col])
    label_encoded_columns[col] = label_encoder.classes_
    
# One-Hot Encoding (suitable for nominal categorical data)
onehot_encoder = OneHotEncoder(sparse_output=False, drop='first')  # Use sparse_output=False for dense array
onehot_encoded_array = onehot_encoder.fit_transform(df[categorical_columns])

# Convert One-Hot Encoded features into a DataFrame
onehot_encoded_df = pd.DataFrame(
    onehot_encoded_array,
    columns=onehot_encoder.get_feature_names_out(categorical_columns),
    index=df.index
)

# Merge One-Hot Encoded features into the original DataFrame
df = pd.concat([df, onehot_encoded_df], axis=1)

# Optionally drop the original categorical columns
df.drop(categorical_columns, axis=1, inplace=True)

# Save the modified DataFrame to a new CSV file
output_file = 'Heartattack_numerical.csv'
df.to_csv(output_file, index=False)

print(f"\nEncoded DataFrame saved to {output_file}.")
```

# remove outliers

```jsx
import pandas as pd
import numpy as np

# Load dataset
df = pd.read_csv("sample_with_outliers.csv")

def detect_outliers_iqr(series):
    """Flag outliers using IQR method"""
    Q1 = series.quantile(0.25)
    Q3 = series.quantile(0.75)
    IQR = Q3 - Q1
    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR
    return series < lower_bound, series > upper_bound

# Detect outliers in numerical columns
numeric_cols = ['age', 'salary', 'score']
outlier_mask = pd.Series(True, index=df.index)  # Start with all True (keep)

print("Outliers detected per column:")
for col in numeric_cols:
    lower_outliers, upper_outliers = detect_outliers_iqr(df[col])
    col_outliers = lower_outliers | upper_outliers
    outlier_mask = outlier_mask & ~col_outliers  # Keep only non-outliers
    print(f"{col}: {col_outliers.sum()} outliers")

# Remove rows with ANY outlier
df_cleaned = df[outlier_mask].copy()
print(f"\nOriginal rows: {len(df)}, After removal: {len(df_cleaned)} ({len(df)-len(df_cleaned)} removed)")

# Save cleaned data
df_cleaned.to_csv("sample_outliers_removed.csv", index=False)
print("Cleaned data saved as sample_outliers_removed.csv")

```
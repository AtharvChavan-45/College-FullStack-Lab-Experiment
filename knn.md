# k - nearest classifiers

```jsx
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.preprocessing import LabelEncoder
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import accuracy_score

# Load the dataset (replace 'your_dataset.csv' with your file path)
# Example CSV: df = pd.read_csv('path_to_your_file.csv')
df = pd.read_csv('diab.csv')  # Replace with your actual CSV file

# Step 1: Preprocess the data (Handle categorical data, missing values, etc.)
# Check for categorical columns and encode them
label_encoders = {}
for column in df.select_dtypes(include=['object']).columns:
    le = LabelEncoder()
    df[column] = le.fit_transform(df[column])
    label_encoders[column] = le  # Store label encoder for reverse transformation if needed

# Step 2: Split the dataset into features (X) and target (y)
# Assuming the target variable is the last column, change it if necessary
X = df.iloc[:, :-1]  # Features (all columns except the last one)
y = df.iloc[:, -1]   # Target (last column)

# Step 3: Split the dataset into training and test sets (80% train, 20% test)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Step 4: Use cross-validation to find the best value of k
k_values = range(1, 11)  # Testing k values from 1 to 10
cv_scores = []  # List to store cross-validation scores

# Loop through different k values
for k in k_values:
    knn = KNeighborsClassifier(n_neighbors=k)
    scores = cross_val_score(knn, X_train, y_train, cv=5, scoring='accuracy')  # 5-fold cross-validation
    cv_scores.append(np.mean(scores))  # Store the mean score for each k

# Step 5: Plot the cross-validation scores
plt.figure(figsize=(8, 6))
plt.plot(k_values, cv_scores, marker='o', linestyle='-', color='b')
plt.xlabel('k (Number of Neighbors)')
plt.ylabel('Cross-Validated Accuracy')
plt.title('KNN: Accuracy vs k Value')
plt.xticks(k_values)
plt.grid(True)
plt.show()

# Step 6: Choose the optimal k (highest cross-validation score)
optimal_k = k_values[np.argmax(cv_scores)]
print(f"The optimal value of k is {optimal_k}")

# Step 7: Train the final model with the optimal k and evaluate on the test set
knn_optimal = KNeighborsClassifier(n_neighbors=optimal_k)
knn_optimal.fit(X_train, y_train)

# Predict on the test set
y_pred = knn_optimal.predict(X_test)

# Evaluate the model's performance
test_accuracy = accuracy_score(y_test, y_pred)
print(f"Test accuracy with k={optimal_k}: {test_accuracy:.2f}")


```

# k-nearest classifier with different parameters

```jsx
import pandas as pd
import numpy as np
import warnings
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder, StandardScaler
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score

warnings.filterwarnings('ignore')

# 1. Load the dataset
df = pd.read_csv('diab1.csv')

# 2. Encode categorical variables if they exist
label_encoders = {}
for column in df.select_dtypes(include=['object']).columns:
    le = LabelEncoder()
    df[column] = le.fit_transform(df[column])
    label_encoders[column] = le

# 3. Separate Features (X) and Target (y)
X = df.iloc[:, :-1]
y = df.iloc[:, -1]

# 4. Train-test split (Stratified to maintain balanced class ratios)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)

# 5. STANDARDIZE THE FEATURES (Crucial for KNN!)
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# 6. Define the Parameter Grid for KNN
param_grid = {
    'n_neighbors': [3, 5, 7, 9, 11, 15, 21],  # Testing odd numbers to prevent ties
    'weights': ['uniform', 'distance'],       # Equal voting vs Proximity voting
    'metric': ['euclidean', 'manhattan']      # Straight line vs City block distance
}

results_list = []

print("Starting KNN parameter grid search...\n")

# 7. Loop through all combinations of parameters
for k in param_grid['n_neighbors']:
    for weight in param_grid['weights']:
        for metric in param_grid['metric']:

            # Initialize and train the KNN model
            knn_model = KNeighborsClassifier(n_neighbors=k, weights=weight, metric=metric)

            # Notice we are using the SCALED training data here
            knn_model.fit(X_train_scaled, y_train)

            # Predict on the SCALED test set
            y_pred = knn_model.predict(X_test_scaled)

            # Calculate metrics
            acc = accuracy_score(y_test, y_pred)
            prec = precision_score(y_test, y_pred, zero_division=0)
            rec = recall_score(y_test, y_pred, zero_division=0)
            f1 = f1_score(y_test, y_pred, zero_division=0)

            # Record the results
            results_list.append({
                'K-Neighbors': k,
                'Weights': weight,
                'Metric': metric,
                'Accuracy': round(acc, 4),
                'Precision': round(prec, 4),
                'Recall': round(rec, 4),
                'F1 Score': round(f1, 4)
            })

# 8. Convert to DataFrame
results_df = pd.DataFrame(results_list)

# 9. Sort by F1 Score (Primary medical metric), then Accuracy
results_df = results_df.sort_values(by=['F1 Score', 'Accuracy'], ascending=[False, False]).reset_index(drop=True)

# 10. Display the full table
print("=== KNN Parameter Tuning Results ===")
print(results_df.to_string())

print("\nBest Model Parameters based on F1 Score:")
print(results_df.iloc[0])

```
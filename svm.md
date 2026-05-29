# support vector machine

```jsx
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.svm import SVC
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix, roc_curve, auc

# Load dataset
df = pd.read_csv("diab1.csv")

# Assuming the last column is the target variable (adjust if necessary)
X = df.iloc[:, :-1]  # Features (all columns except the last)
y = df.iloc[:, -1]   # Target (last column)

# Train-test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Standardize features (SVM performs better with scaled data)
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

# Train SVM model with probability estimation enabled for ROC curve
svm_model = SVC(kernel='rbf', C=1.0, gamma='scale', probability=True, random_state=42)
svm_model.fit(X_train, y_train)

# Predictions
y_pred = svm_model.predict(X_test)
y_pred_proba = svm_model.predict_proba(X_test)[:, 1]  # Probability for the positive class

# Evaluate model
accuracy = accuracy_score(y_test, y_pred)
print(f"Accuracy: {accuracy:.2f}")
print(classification_report(y_test, y_pred))

# Plot Confusion Matrix
plt.figure(figsize=(6, 4))
cm = confusion_matrix(y_test, y_pred)
sns.heatmap(cm, annot=True, fmt='d', cmap="Blues", xticklabels=["Negative", "Positive"], yticklabels=["Negative", "Positive"])
plt.xlabel("Predicted")
plt.ylabel("Actual")
plt.title("Confusion Matrix")
plt.show()

# Compute ROC curve and AUC
fpr, tpr, _ = roc_curve(y_test, y_pred_proba)
roc_auc = auc(fpr, tpr)

# Plot AUC-ROC Curve
plt.figure(figsize=(6, 4))
plt.plot(fpr, tpr, color='blue', lw=2, label=f'AUC = {roc_auc:.2f}')
plt.plot([0, 1], [0, 1], color='gray', linestyle='--')  # Diagonal reference line
plt.xlabel("False Positive Rate")
plt.ylabel("True Positive Rate")
plt.title("ROC Curve")
plt.legend(loc="lower right")
plt.show()

```
# SVM for other parameters analysis

```jsx
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.svm import SVC
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score

# 1. Load dataset (Assuming your data loading is already working)
df = pd.read_csv("diab1 (1).csv")

# 2. Separate Features and Target
X = df.iloc[:, :-1]
y = df.iloc[:, -1]

# 3. Train-test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 4. Standardize features
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

# 5. Define the parameters we want to test
# You can add or remove values here as needed!
param_grid = {
    'kernel': ['linear', 'rbf', 'poly'],
    'C': [0.1, 1.0, 10.0],
    'gamma': ['scale', 0.1, 1.0] # Note: gamma only affects 'rbf' and 'poly' kernels
}

# List to store the results
results_list = []

print("Starting parameter grid search...\n")

# 6. Loop through all combinations of parameters
for kernel in param_grid['kernel']:
    for C in param_grid['C']:
        for gamma in param_grid['gamma']:

            # Initialize and train the model with the current parameter set
            svm_model = SVC(kernel=kernel, C=C, gamma=gamma)
            svm_model.fit(X_train, y_train)

            # Predict on the test set
            y_pred = svm_model.predict(X_test)

            # Calculate metrics
            acc = accuracy_score(y_test, y_pred)
            prec = precision_score(y_test, y_pred, zero_division=0)
            rec = recall_score(y_test, y_pred, zero_division=0)
            f1 = f1_score(y_test, y_pred, zero_division=0)

            # Record the results in a dictionary
            results_list.append({
                'Kernel': kernel,
                'C': C,
                'Gamma': gamma,
                'Accuracy': round(acc, 4),
                'Precision': round(prec, 4),
                'Recall': round(rec, 4),
                'F1 Score': round(f1, 4)
            })

# 7. Convert results to a Pandas DataFrame for easy viewing
results_df = pd.DataFrame(results_list)

# Remove duplicate rows (since gamma doesn't affect the 'linear' kernel, it will run identical tests)
results_df = results_df.drop_duplicates(subset=['Kernel', 'C', 'Accuracy', 'F1 Score'])

# Sort the table to show the best models at the top (sorting by F1 Score, then Accuracy)
results_df = results_df.sort_values(by=['F1 Score', 'Accuracy'], ascending=[False, False]).reset_index(drop=True)

# 8. Display the recorded table
print("=== Parameter Tuning Results ===")
print(results_df.to_string())

print("\nBest Model Parameters based on F1 Score:")
print(results_df.iloc[0])

```
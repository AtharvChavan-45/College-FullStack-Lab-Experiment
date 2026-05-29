# linear regression

```jsx
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import train_test_split # Splits dataset into training & testing data.
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score

# ---------------------------
# 1. Load and prepare data
# ---------------------------
data = pd.read_csv('student_scores.csv')
X = data.iloc[:, :-1].values # Selects all rows and all columns except last column (independent variable)
y = data.iloc[:, 1].values # Selects second column

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42) # when we write random_state so it use same data for training and testing if we run code multipal time

# random_state=42 ensures same split every time you run code.

# ---------------------------
# 2. Train model & predict
# ---------------------------
model = LinearRegression()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

# Coefficients
slope = model.coef_[0]
intercept = model.intercept_

# Evaluation metrics
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

# Print results
print("=" * 50)
print("LINEAR REGRESSION RESULTS")
print("=" * 50)
print(f"Equation:      Y = {slope:.2f}X + {intercept:.2f}")
print(f"Mean Squared Error (MSE): {mse:.2f}")
print(f"R² Score:               {r2:.2f}")
print("=" * 50)

# ---------------------------
# 3. Create evaluation plots
# ---------------------------
plt.style.use('seaborn-v0_8-whitegrid')
fig, axes = plt.subplots(2, 2, figsize=(12, 10))
fig.suptitle('Linear Regression Evaluation', fontsize=16)

# ---- Plot 1: Regression line (original) ----
ax1 = axes[0, 0]
ax1.scatter(X_test, y_test, color='blue', alpha=0.7, label='Test data')
ax1.plot(X_test, y_pred, color='red', linewidth=2, label='Regression line')
ax1.set_xlabel('Hours Studied')
ax1.set_ylabel('Score')
ax1.set_title('Test Data vs Regression Line')
ax1.legend()
ax1.grid(True, alpha=0.3)

# ---- Plot 2: Actual vs Predicted ----
ax2 = axes[0, 1]
min_val = min(y_test.min(), y_pred.min())
max_val = max(y_test.max(), y_pred.max())
ax2.scatter(y_test, y_pred, color='green', alpha=0.7)
ax2.plot([min_val, max_val], [min_val, max_val], 'k--', lw=2, label='Perfect fit')
ax2.set_xlabel('Actual Scores')
ax2.set_ylabel('Predicted Scores')
ax2.set_title('Actual vs Predicted')
ax2.legend()
ax2.grid(True, alpha=0.3)

# Display R² and MSE on the plot
ax2.text(0.05, 0.95, f'R² = {r2:.3f}\nMSE = {mse:.2f}',
         transform=ax2.transAxes, fontsize=11,
         verticalalignment='top', bbox=dict(boxstyle='round', facecolor='wheat', alpha=0.5))

# ---- Plot 3: Residuals vs Predicted ---- Residuals = Actual Value − Predicted Value
ax3 = axes[1, 0]
residuals = y_test - y_pred
ax3.scatter(y_pred, residuals, color='purple', alpha=0.7)
ax3.axhline(y=0, color='black', linestyle='--', linewidth=1.5)
ax3.set_xlabel('Predicted Scores')
ax3.set_ylabel('Residuals')
ax3.set_title('Residuals vs Predicted')
ax3.grid(True, alpha=0.3)

# ---- Plot 4: Histogram of residuals ----
ax4 = axes[1, 1]
sns.histplot(residuals, kde=True, ax=ax4, color='orange', bins=8)
ax4.axvline(x=0, color='black', linestyle='--', linewidth=1.5)
ax4.set_xlabel('Residuals')
ax4.set_ylabel('Frequency')
ax4.set_title('Distribution of Residuals')

plt.tight_layout()
plt.show()

```
# compare linear regression and polynomial regression

```jsx

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import PolynomialFeatures
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score

# Load the dataset from the CSV file
data = pd.read_csv('salary_experience1.csv')  # Replace with your file path if needed

# Features and target
X = data[['Years_of_Experience']].values  # independent variable
y = data['Salary'].values

# 1. Linear Regression
linear_regressor = LinearRegression()
linear_regressor.fit(X, y)

# Predictions for Linear Regression using the entire data
y_pred_linear = linear_regressor.predict(X)

# 2. Polynomial Regression (Degree 2 for this example)
poly = PolynomialFeatures(degree=4)
X_poly = poly.fit_transform(X)

poly_regressor = LinearRegression()
poly_regressor.fit(X_poly, y)

# Predictions for Polynomial Regression using the entire data
y_pred_poly = poly_regressor.predict(X_poly)

# Calculate Metrics (MSE, RMSE, MAE, R^2) for both models

# Linear Regression Metrics
mse_linear = mean_squared_error(y, y_pred_linear)
rmse_linear = np.sqrt(mse_linear)
mae_linear = mean_absolute_error(y, y_pred_linear)
r2_linear = r2_score(y, y_pred_linear)

# Polynomial Regression Metrics
mse_poly = mean_squared_error(y, y_pred_poly)
rmse_poly = np.sqrt(mse_poly)
mae_poly = mean_absolute_error(y, y_pred_poly)
r2_poly = r2_score(y, y_pred_poly)

# Plotting
plt.figure(figsize=(12, 6))

# Plot Linear Regression
plt.subplot(1, 2, 1)
plt.scatter(X, y, color='blue', label='Actual')
plt.plot(X, y_pred_linear, color='red', label='Linear Regression')
plt.title('Linear Regression')
plt.xlabel('Years of Experience')
plt.ylabel('Salary')
plt.legend()

# Plot Polynomial Regression
plt.subplot(1, 2, 2)
plt.scatter(X, y, color='blue', label='Actual')
plt.plot(np.sort(X[:, 0]), poly_regressor.predict(poly.transform(np.sort(X, axis=0))), color='green', label='Polynomial Regression')
plt.title('Polynomial Regression')
plt.xlabel('Years of Experience')
plt.ylabel('Salary')
plt.legend()

plt.tight_layout()
plt.show()

# Return the evaluation metrics
print(f"Linear Regression Metrics:")
print(f"MSE: {mse_linear}")
print(f"RMSE: {rmse_linear}")
print(f"MAE: {mae_linear}")
print(f"R²: {r2_linear}")

print(f"\nPolynomial Regression Metrics:")
print(f"MSE: {mse_poly}")
print(f"RMSE: {rmse_poly}")
print(f"MAE: {mae_poly}")
print(f"R²: {r2_poly}")

```
# simple linear regression

```jsx
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
import matplotlib.pyplot as plt

# Load the data from the CSV file
# Replace 'your_file.csv' with the path to your CSV file
df = pd.read_csv('housePrice(2).csv')

# Inspect the data (optional)
print(df.head())
print(df.describe())

# Define the features and target variable
X = df[['Area', 'Bedrooms', 'Age']]  # Features
y = df['Price']  # Target variable

# Split the data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Create and train the model
model = LinearRegression()
model.fit(X_train, y_train)

# Make predictions on the test set
y_pred = model.predict(X_test)

# Evaluate the model
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
print(f'Mean Squared Error: {mse}')
print(f'R^2 Score: {r2}')

# Output the model coefficients
print('Intercept:', model.intercept_)
print('Coefficients:', model.coef_)
print('Feature names:', X.columns)

# Plotting Actual vs Predicted Prices
plt.figure(figsize=(10, 6))
plt.scatter(y_test, y_pred, color='blue', label='Predicted vs Actual')
plt.plot([min(y_test), max(y_test)], [min(y_test), max(y_test)], color='red', linewidth=2, label='Perfect Fit Line')
plt.xlabel('Actual Prices')
plt.ylabel('Predicted Prices')
plt.title('Actual vs Predicted Prices')
plt.legend()
plt.show()

```
# logistic regression
```jsx
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix, roc_curve, roc_auc_score

# Load dataset
df = pd.read_csv('diab1.csv')

# Splitting data into features and target
X = df.drop(columns=['Outcome'])
y = df['Outcome']

# Splitting into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)

# Standardizing the features
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# Training the Logistic Regression model
model = LogisticRegression()
model.fit(X_train_scaled, y_train)

# Predictions
y_pred = model.predict(X_test_scaled)
y_prob = model.predict_proba(X_test_scaled)[:, 1]

# Evaluation metrics
accuracy = accuracy_score(y_test, y_pred)
precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)
conf_matrix = confusion_matrix(y_test, y_pred)
auc = roc_auc_score(y_test, y_prob)

print(f'Accuracy: {accuracy:.4f}')
print(f'Precision: {precision:.4f}')
print(f'Recall: {recall:.4f}')
print(f'F1 Score: {f1:.4f}')
print(f'AUC Score: {auc:.4f}')

# Confusion Matrix Plot
plt.figure(figsize=(6,5))
sns.heatmap(conf_matrix, annot=True, fmt='d', cmap='Blues', xticklabels=['No Diabetes', 'Diabetes'], yticklabels=['No Diabetes', 'Diabetes'])
plt.xlabel('Predicted')
plt.ylabel('Actual')
plt.title('Confusion Matrix')
plt.show()

# ROC Curve
fpr, tpr, _ = roc_curve(y_test, y_prob)
plt.figure(figsize=(6,5))
plt.plot(fpr, tpr, label=f'AUC = {auc:.4f}')
plt.plot([0, 1], [0, 1], linestyle='--', color='gray')
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend()
plt.show()

```
# logistic regression for other parameters analysis

```jsx
import pandas as pd
import numpy as np
import warnings
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score

# Ignore convergence warnings that sometimes pop up during extreme parameter testing
warnings.filterwarnings('ignore')

# 1. Load dataset (Assuming outcome column is 'Outcome' based on your previous code)
df = pd.read_csv('diab1.csv')

X = df.drop(columns=['Outcome'])
y = df['Outcome']

# 2. Train-test split (stratify=y is excellent practice for imbalanced datasets!)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)

# 3. Standardize features
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# 4. Define the valid parameter combinations for Logistic Regression
# We pair solver and penalty to avoid mathematical incompatibility errors
solver_penalty_pairs = [
    ('liblinear', 'l1'),
    ('liblinear', 'l2'),
    ('lbfgs', 'l2')
]

# The range of C values (from very strict regularization to almost none)
C_values = [0.01, 0.1, 1.0, 10.0, 100.0]

results_list = []

print("Starting Logistic Regression parameter grid search...\n")

# 5. Loop through all combinations
for solver, penalty in solver_penalty_pairs:
    for C in C_values:

        # Initialize and train the Logistic Regression model
        # max_iter is increased to 1000 to give the math engine time to solve complex penalties
        model = LogisticRegression(solver=solver, penalty=penalty, C=C, max_iter=1000, random_state=42)
        model.fit(X_train_scaled, y_train)

        # Predict on the test set
        y_pred = model.predict(X_test_scaled)

        # Calculate evaluation metrics
        acc = accuracy_score(y_test, y_pred)
        prec = precision_score(y_test, y_pred, zero_division=0)
        rec = recall_score(y_test, y_pred, zero_division=0)
        f1 = f1_score(y_test, y_pred, zero_division=0)

        # Record the results
        results_list.append({
            'Solver': solver,
            'Penalty': penalty,
            'C': C,
            'Accuracy': round(acc, 4),
            'Precision': round(prec, 4),
            'Recall': round(rec, 4),
            'F1 Score': round(f1, 4)
        })

# 6. Convert results to DataFrame
results_df = pd.DataFrame(results_list)

# 7. Sort by F1 Score (Primary metric for medical/diabetes datasets), then Accuracy
results_df = results_df.sort_values(by=['F1 Score', 'Accuracy'], ascending=[False, False]).reset_index(drop=True)

# 8. Display the FULL table (No .head() used here, so you see everything!)
print("=== Logistic Regression Parameter Tuning Results ===")
print(results_df.to_string())

print("\nBest Model Parameters based on F1 Score:")
print(results_df.iloc[0])

```
# check polynomial regression for different degree

```jsx
from sklearn.preprocessing import PolynomialFeatures
from sklearn.pipeline import make_pipeline

# ----------------------------------------------------------
# 1. Use the same train/test split from Task 1
# ----------------------------------------------------------
degrees = [1, 2, 3, 4]   # degree 1 is simple linear (baseline)
results = {}

plt.figure(figsize=(10,6))

for i, deg in enumerate(degrees):
    # Create polynomial regression pipeline
    poly_model = make_pipeline(PolynomialFeatures(degree=deg), LinearRegression())
    poly_model.fit(X_train, y_train)
    y_pred_poly = poly_model.predict(X_test)

    # Metrics
    mse = mean_squared_error(y_test, y_pred_poly)
    rmse = np.sqrt(mse)
    mae = mean_absolute_error(y_test, y_pred_poly)
    r2 = r2_score(y_test, y_pred_poly)
    results[deg] = {'MSE': mse, 'RMSE': rmse, 'MAE': mae, 'R2': r2}

    # Plot polynomial fit
    # Generate smooth X for plotting the curve
    X_smooth = np.linspace(X.min(), X.max(), 100).reshape(-1,1)
    y_smooth = poly_model.predict(X_smooth)

    plt.subplot(2,2,i+1)
    plt.scatter(X_test, y_test, color='blue', alpha=0.6, label='Test data')
    plt.plot(X_smooth, y_smooth, color='red', linewidth=2, label=f'Degree {deg}')
    plt.xlabel('Temperature (°F)')
    plt.ylabel('Consumption')
    plt.title(f'Polynomial Degree {deg}\nR² = {r2:.3f}, RMSE = {rmse:.3f}')
    plt.legend()
    plt.grid(alpha=0.3)

plt.tight_layout()
plt.show()

# ----------------------------------------------------------
# 2. Print comparison table
# ----------------------------------------------------------
print("\n" + "=" * 65)
print("TASK 2 – POLYNOMIAL REGRESSION PERFORMANCE COMPARISON")
print("=" * 65)
print(f"{'Degree':<8} {'MSE':<12} {'RMSE':<12} {'MAE':<12} {'R²':<10}")
print("-" * 65)
for deg in degrees:
    print(f"{deg:<8} {results[deg]['MSE']:<12.4f} {results[deg]['RMSE']:<12.4f} "
          f"{results[deg]['MAE']:<12.4f} {results[deg]['R2']:<10.4f}")
print("=" * 65)

```
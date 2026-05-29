# GaussianNB


```jsx
# Import necessary libraries
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.naive_bayes import GaussianNB
from scipy.stats import norm
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix, roc_curve, auc
import seaborn as sns
# Load the diabetes dataset (diab.csv)
file_path = 'diab (1).csv'  # Replace with your CSV file path
data = pd.read_csv(file_path)

# Check the first few rows of the dataset
print(data.head())

# Feature columns (X) and target column (y)
X = data[['Pregnancies', 'Glucose', 'BloodPressure', 'SkinThickness', 'Insulin', 'BMI', 'DiabetesPedigreeFunction', 'Age']]
y = data['Outcome']

# Split the dataset into training and testing sets (80% train, 20% test)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Initialize the Gaussian Naive Bayes classifier
nb_classifier = GaussianNB()

# Train the model
nb_classifier.fit(X_train, y_train)

# Make predictions on the test set
y_pred = nb_classifier.predict(X_test)

# Evaluate the model
accuracy = accuracy_score(y_test, y_pred)
print(f'Accuracy: {accuracy * 100:.2f}%')
# Evaluate the model using Precision, Recall, and F1 Score
precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)

# Print the metrics
print(f'Precision: {precision:.2f}')
print(f'Recall: {recall:.2f}')
print(f'F1 Score: {f1:.2f}')

# Confusion Matrix
cm = confusion_matrix(y_test, y_pred)

# Plot Confusion Matrix
plt.figure(figsize=(6,6))
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues', xticklabels=['No Diabetes', 'Diabetes'], yticklabels=['No Diabetes', 'Diabetes'])
plt.title('Confusion Matrix')
plt.xlabel('Predicted')
plt.ylabel('Actual')
plt.show()

# ROC Curve and AUC
fpr, tpr, thresholds = roc_curve(y_test, nb_classifier.predict_proba(X_test)[:, 1])
roc_auc = auc(fpr, tpr)

# Plot ROC Curve
plt.figure(figsize=(8, 6))
plt.plot(fpr, tpr, color='blue', label=f'ROC Curve (AUC = {roc_auc:.2f})')
plt.plot([0, 1], [0, 1], color='gray', linestyle='--')
plt.title('Receiver Operating Characteristic (ROC) Curve')
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.legend(loc='lower right')
plt.show()
# Plot Gaussian curves for each feature (assuming Gaussian Naive Bayes model)
features = X.columns
fig, axes = plt.subplots(nrows=4, ncols=2, figsize=(12, 12))

# Flatten axes array for easier iteration
axes = axes.flatten()

for i, feature in enumerate(features):
    # Get the mean and standard deviation of the feature for both classes (0 and 1)
    mean_class_0 = np.mean(X_train[y_train == 0][feature])
    std_class_0 = np.std(X_train[y_train == 0][feature])

    mean_class_1 = np.mean(X_train[y_train == 1][feature])
    std_class_1 = np.std(X_train[y_train == 1][feature])

    # Generate data points for plotting
    x_values = np.linspace(min(X[feature]), max(X[feature]), 100)
    y_class_0 = norm.pdf(x_values, mean_class_0, std_class_0)
    y_class_1 = norm.pdf(x_values, mean_class_1, std_class_1)

    # Plot the Gaussian curves
    axes[i].plot(x_values, y_class_0, label=f'Class 0 - {feature}', color='blue')
    axes[i].plot(x_values, y_class_1, label=f'Class 1 - {feature}', color='red')
    axes[i].set_title(f'Gaussian Curve for {feature}')
    axes[i].set_xlabel(feature)
    axes[i].set_ylabel('Density')
    axes[i].legend()

plt.tight_layout()
plt.show()
```

# BernoulliNB

```jsx
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.naive_bayes import BernoulliNB
from sklearn import metrics

# Step 1: Load the CSV file
df = pd.read_csv('spam_or_not_spam.csv')  # Replace with your actual file path

# Step 2: Handle missing values (drop rows with missing 'email' or 'label')
df = df.dropna(subset=['email', 'label'])

# Step 3: Split the dataset into features (X) and labels (y)
X = df['email']  # Features (email content)
y = df['label']  # Labels (spam or not)

# Step 4: Split the dataset into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# Step 5: Convert email text data into binary features using CountVectorizer with binary=True
vectorizer = CountVectorizer(binary=True)  # Binary=True makes it Bernoulli Naïve Bayes
X_train_vect = vectorizer.fit_transform(X_train)  # Fit and transform the training data
X_test_vect = vectorizer.transform(X_test)  # Transform the test data

# Step 6: Train a Bernoulli Naïve Bayes classifier
model = BernoulliNB()
model.fit(X_train_vect, y_train)

# Step 7: Make predictions on the test data
y_pred = model.predict(X_test_vect)

# Step 8: Evaluate the model's performance
accuracy = metrics.accuracy_score(y_test, y_pred)
print("Accuracy:", accuracy)

# Confusion Matrix
print("\nConfusion Matrix:")
print(metrics.confusion_matrix(y_test, y_pred))

# Classification Report
print("\nClassification Report:")
print(metrics.classification_report(y_test, y_pred))
```
# MultinomialNB

```jsx
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn import metrics

# Step 1: Load the CSV file
df = pd.read_csv('spam_or_not_spam.csv')  # Replace with your actual file path

# Step 2: Handle missing values
# Option 1: Drop rows with missing values in either 'email' or 'label'
df = df.dropna(subset=['email', 'label'])

# Option 2: Alternatively, you can fill NaN values with an empty string for 'email'
# and a default value (e.g., 0 for non-spam, or 1 for spam) for 'label'
# df['email'] = df['email'].fillna('')
# df['label'] = df['label'].fillna(0)  # Replace 0 with a more appropriate value if needed

# Step 3: Split the dataset into features (X) and labels (y)
X = df['email']  # Features (email content)
y = df['label']  # Labels (spam or not)

# Step 4: Split the dataset into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# Step 5: Convert email text data into numerical features using CountVectorizer
vectorizer = CountVectorizer()
X_train_vect = vectorizer.fit_transform(X_train)  # Fit and transform the training data
X_test_vect = vectorizer.transform(X_test)  # Transform the test data

# Step 6: Train a Multinomial Naïve Bayes classifier
model = MultinomialNB()
model.fit(X_train_vect, y_train)

# Step 7: Make predictions on the test data
y_pred = model.predict(X_test_vect)

# Step 8: Evaluate the model's performance
accuracy = metrics.accuracy_score(y_test, y_pred)
print("Accuracy:", accuracy)

# You can also print the confusion matrix and classification report
print("\nConfusion Matrix:")
print(metrics.confusion_matrix(y_test, y_pred))

print("\nClassification Report:")
print(metrics.classification_report(y_test, y_pred))
```
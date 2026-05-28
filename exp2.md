# ID3 Algorithm for decision tree

```jsx

import pandas as pd
import numpy as np
from collections import Counter
import math

# Dataset
data = {
    'Outlook': ['Sunny', 'Sunny', 'Overcast', 'Rainy', 'Rainy', 'Overcast', 'Sunny', 'Sunny', 'Rainy', 'Rainy'],
    'Temperature': ['Hot', 'Hot', 'Hot', 'Mild', 'Cool', 'Cool', 'Mild', 'Cool', 'Mild', 'Mild'],
    'PlayTennis': ['No', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes', 'Yes', 'No']
}

# Convert to DataFrame
df = pd.DataFrame(data)

# Function to calculate entropy
def entropy(data):
    """
    Calculate the entropy of a dataset.
    """
    value_counts = Counter(data)  # Count frequency of each class
    total = len(data)
    ent = 0
    for count in value_counts.values():
        probability = count / total
        ent -= probability * math.log2(probability)  # Entropy formula
    return ent

# Function to calculate information gain
def information_gain(data, feature, target):
    """
    Calculate the information gain of a feature with respect to the target variable.
    """
    total_entropy = entropy(data[target])  # Total entropy of the dataset
    feature_values = data[feature].unique()  # Get unique values of the feature
    weighted_entropy = 0

    print(f"\nCalculating Information Gain for feature: {feature}")
    print(f"Total Entropy (before split): {total_entropy:.4f}")

    # Calculate weighted entropy for all subsets of the feature
    for value in feature_values:
        subset = data[data[feature] == value]  # Subset where feature = value
        subset_entropy = entropy(subset[target])  # Entropy of the subset
        weighted_entropy += (len(subset) / len(data)) * subset_entropy  # Weighted entropy
        print(f"Subset for {feature} = {value}: Entropy = {subset_entropy:.4f}")

    # Information gain is the reduction in entropy
    gain = total_entropy - weighted_entropy
    print(f"Information Gain for {feature}: {gain:.4f}")

    return gain

# Function to build the ID3 decision tree
def id3(data, target, features):
    """
    Recursively build the ID3 decision tree.
    """
    # If all instances have the same target, return that target (leaf node)
    if len(np.unique(data[target])) == 1:
        return np.unique(data[target])[0]

    # If no more features to split on, return the most frequent target value
    if len(features) == 0:
        return Counter(data[target]).most_common(1)[0][0]

    # Calculate information gain for each feature
    gains = {feature: information_gain(data, feature, target) for feature in features}

    # Select the feature with the highest information gain
    best_feature = max(gains, key=gains.get)
    print(f"\nSelected feature with highest Information Gain: {best_feature}")

    # Create the tree with the best feature as the root node
    tree = {best_feature: {}}

    # Split the data based on the best feature
    feature_values = data[best_feature].unique()
    for value in feature_values:
        subset = data[data[best_feature] == value]  # Get subset of data
        subtree = id3(subset, target, [f for f in features if f != best_feature])  # Recursively build subtree
        tree[best_feature][value] = subtree  # Add the subtree

    return tree

# Function to print the decision tree
def print_tree(tree, indent=""):
    """
    Print the decision tree.
    """
    if isinstance(tree, dict):  # If the node is a dictionary (internal node)
        for feature, branches in tree.items():
            for value, subtree in branches.items():
                print(f"{indent}{feature} = {value} ->")
                print_tree(subtree, indent + "  ")
    else:  # If the node is a leaf (class label)
        print(f"{indent}Class: {tree}")

# Define the target and features
target = 'PlayTennis'
features = ['Outlook', 'Temperature']

# Build the ID3 decision tree
tree = id3(df, target, features)

# Print the resulting decision tree
print("\nDecision Tree (ID3):")
print_tree(tree)

```
```jsx
import pandas as pd
import numpy as np
from collections import Counter
import math

# Dataset from your image
data = {
    'Size': ['Small', 'Large', 'Large', 'Small', 'Large', 'Small'],
    'Teeth': ['Small', 'Large', 'Large', 'Small', 'Small', 'Large'],
    'Habitat': ['Forest', 'Forest', 'Water', 'Water', 'Forest', 'Forest'],
    'Dangerous': ['No', 'Yes', 'Yes', 'No', 'No', 'Yes']
}

# Convert to DataFrame
df = pd.DataFrame(data)

# Function to calculate entropy
def entropy(data):
    """
    Calculate the entropy of a dataset.
    """
    value_counts = Counter(data)  # Count frequency of each class
    total = len(data)
    ent = 0
    for count in value_counts.values():
        probability = count / total
        if probability > 0:  # Avoid log(0)
            ent -= probability * math.log2(probability)  # Entropy formula
    return ent

# Function to calculate information gain
def information_gain(data, feature, target):
    """
    Calculate the information gain of a feature with respect to the target variable.
    """
    total_entropy = entropy(data[target])  # Total entropy of the dataset
    feature_values = data[feature].unique()  # Get unique values of the feature
    weighted_entropy = 0

    print(f"\nCalculating Information Gain for feature: {feature}")
    print(f"Total Entropy (before split): {total_entropy:.4f}")

    # Calculate weighted entropy for all subsets of the feature
    for value in feature_values:
        subset = data[data[feature] == value]  # Subset where feature = value
        if len(subset) > 0:
            subset_entropy = entropy(subset[target])  # Entropy of the subset
            weight = len(subset) / len(data)
            weighted_entropy += weight * subset_entropy  # Weighted entropy
            print(f"Subset for {feature} = {value}: Entropy = {subset_entropy:.4f}, Weight = {weight:.3f}")
        else:
            print(f"Subset for {feature} = {value}: No data")

    # Information gain is the reduction in entropy
    gain = total_entropy - weighted_entropy
    print(f"Information Gain for {feature}: {gain:.4f}")

    return gain

# Function to build the ID3 decision tree
def id3(data, target, features, depth=0, max_depth=10):
    """
    Recursively build the ID3 decision tree.
    """
    # Stop if we've reached max depth
    if depth >= max_depth:
        return Counter(data[target]).most_common(1)[0][0]

    # If all instances have the same target, return that target (leaf node)
    if len(np.unique(data[target])) == 1:
        return np.unique(data[target])[0]

    # If no more features to split on, return the most frequent target value
    if len(features) == 0 or len(data) == 0:
        return Counter(data[target]).most_common(1)[0][0]

    # Calculate information gain for each feature
    gains = {}
    for feature in features:
        if len(data[feature].unique()) > 1:  # Only consider features with variance
            gains[feature] = information_gain(data, feature, target)
        else:
            gains[feature] = -1  # No gain if all values are the same

    # Check if we have valid gains
    if not gains or max(gains.values()) <= 0:
        return Counter(data[target]).most_common(1)[0][0]

    # Select the feature with the highest information gain
    best_feature = max(gains, key=gains.get)
    print(f"\nSelected feature with highest Information Gain: {best_feature}")

    # Create the tree with the best feature as the root node
    tree = {best_feature: {}}

    # Split the data based on the best feature
    feature_values = data[best_feature].unique()
    for value in feature_values:
        subset = data[data[best_feature] == value]  # Get subset of data

        if len(subset) == 0:
            # If no data for this value, use most common class from parent
            subtree = Counter(data[target]).most_common(1)[0][0]
        else:
            # Recursively build subtree with remaining features
            remaining_features = [f for f in features if f != best_feature]
            subtree = id3(subset, target, remaining_features, depth + 1, max_depth)

        tree[best_feature][value] = subtree  # Add the subtree

    return tree

# Function to print the decision tree
def print_tree(tree, indent=""):
    """
    Print the decision tree.
    """
    if isinstance(tree, dict):  # If the node is a dictionary (internal node)
        for feature, branches in tree.items():
            for value, subtree in branches.items():
                print(f"{indent}{feature} = {value} ->")
                print_tree(subtree, indent + "  ")
    else:  # If the node is a leaf (class label)
        print(f"{indent}Class: Dangerous = {tree}")

# Function to predict using the decision tree
def predict(tree, instance):
    """
    Predict the class for a single instance using the decision tree.
    """
    if not isinstance(tree, dict):
        return tree  # Return leaf value

    # Get the root feature
    root_feature = list(tree.keys())[0]
    feature_value = instance.get(root_feature)

    if feature_value not in tree[root_feature]:
        # If value not seen in training, return most common class from root
        return "No"  # Default safe

    # Recursively traverse the tree
    return predict(tree[root_feature][feature_value], instance)

# Define the target and features
target = 'Dangerous'
features = ['Size', 'Teeth', 'Habitat']

print("=" * 60)
print("ANIMAL DANGER PREDICTION - DECISION TREE (ID3 ALGORITHM)")
print("=" * 60)

# Display the dataset
print("\nDataset:")
print(df.to_string(index=True))
print(f"\nDataset shape: {df.shape}")

# Calculate initial entropy
print(f"\nInitial Entropy of the dataset:")
print(f"H(Dangerous) = {entropy(df[target]):.4f}")

# Build the ID3 decision tree
print("\n" + "=" * 60)
print("BUILDING DECISION TREE...")
print("=" * 60)
tree = id3(df, target, features)

# Print the resulting decision tree
print("\n" + "=" * 60)
print("FINAL DECISION TREE:")
print("=" * 60)
print_tree(tree)

# Make predictions for training data
print("\n" + "=" * 60)
print("PREDICTIONS ON TRAINING DATA:")
print("=" * 60)
predictions = []
for idx, row in df.iterrows():
    instance = row.to_dict()
    prediction = predict(tree, instance)
    predictions.append(prediction)
    print(f"Animal {idx+1}: {instance} -> Predicted: {prediction}, Actual: {row['Dangerous']}")

# Calculate accuracy
correct = sum(1 for i in range(len(df)) if predictions[i] == df.iloc[i]['Dangerous'])
accuracy = correct / len(df) * 100
print(f"\nAccuracy on training data: {accuracy:.2f}% ({correct}/{len(df)})")

# Test some new examples
print("\n" + "=" * 60)
print("TESTING NEW EXAMPLES:")
print("=" * 60)
test_cases = [
    {'Size': 'Large', 'Teeth': 'Large', 'Habitat': 'Water'},
    {'Size': 'Small', 'Teeth': 'Small', 'Habitat': 'Forest'},
    {'Size': 'Large', 'Teeth': 'Small', 'Habitat': 'Water'},
    {'Size': 'Small', 'Teeth': 'Large', 'Habitat': 'Water'}
]

for i, test_case in enumerate(test_cases, 1):
    prediction = predict(tree, test_case)
    print(f"Test case {i}: {test_case} -> Predicted Dangerous: {prediction}")

# Print decision rules
print("\n" + "=" * 60)
print("DECISION RULES (Human-readable):")
print("=" * 60)

def extract_rules(tree, path=""):
    rules = []
    if not isinstance(tree, dict):
        rules.append(f"{path} -> Dangerous = {tree}")
        return rules

    root_feature = list(tree.keys())[0]
    for value, subtree in tree[root_feature].items():
        new_path = f"{path}{root_feature} = {value}"
        if isinstance(subtree, dict):
            rules.extend(extract_rules(subtree, new_path + " AND "))
        else:
            rules.append(f"{new_path} -> Dangerous = {subtree}")
    return rules

rules = extract_rules(tree)
for i, rule in enumerate(rules, 1):
    print(f"Rule {i}: IF {rule}")

    ```
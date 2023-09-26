# CodeDump

```
# Assume df is your DataFrame

# List of features to manually keep
keep_features = ['feature_to_keep1', 'feature_to_keep2']  # Add the features you want to keep manually

# Calculate the correlation matrix
corr_matrix = df.corr().abs()

# Get pairs of features that are highly correlated
threshold = 0.95
to_drop = set()
for i in range(len(corr_matrix)):
    for j in range(i+1, len(corr_matrix)):
        if corr_matrix.iloc[i, j] > threshold:
            # If both features in the pair are in keep_features, skip this pair
            if corr_matrix.columns[i] in keep_features or corr_matrix.columns[j] in keep_features:
                continue
            to_drop.add(corr_matrix.columns[j])

# Drop the correlated features, except the ones to manually keep
df = df.drop(columns=to_drop - set(keep_features))
```

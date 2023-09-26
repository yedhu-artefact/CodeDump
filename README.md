# CodeDump

```
# Assume df is your DataFrame

# Calculate the correlation matrix
corr_matrix = df.corr().abs()

# Get pairs of features that are highly correlated
threshold = 0.95
to_drop = set()
for i in range(len(corr_matrix)):
    for j in range(i+1, len(corr_matrix)):
        if corr_matrix.iloc[i, j] > threshold:
            # If both features in the pair contain "UAE", add both to to_drop
            if 'UAE' in corr_matrix.columns[i] and 'UAE' in corr_matrix.columns[j]:
                to_drop.add(corr_matrix.columns[i])
                to_drop.add(corr_matrix.columns[j])
            # If only the second feature in the pair contains "UAE", add the first to to_drop
            elif 'UAE' in corr_matrix.columns[j]:
                to_drop.add(corr_matrix.columns[i])
            # Otherwise, add the second feature to to_drop
            else:
                to_drop.add(corr_matrix.columns[j])

# Drop the correlated features, except the ones with "UAE" in their name
df = df.drop(columns=to_drop)

```

# CodeDump

```
# Assume df is your DataFrame

# Calculate the correlation matrix
corr_matrix = df.corr().abs()

# Get pairs of features that are highly correlated
threshold = 0.95
highly_correlated_pairs = []
for i in range(len(corr_matrix)):
    for j in range(i+1, len(corr_matrix)):
        if corr_matrix.iloc[i, j] > threshold:
            highly_correlated_pairs.append((corr_matrix.columns[i], corr_matrix.columns[j], corr_matrix.iloc[i, j]))

# Print out the highly correlated pairs and their correlation coefficient
for pair in highly_correlated_pairs:
    print(f"Features: {pair[0]}, {pair[1]} - Correlation: {pair[2]:.2f}")

```

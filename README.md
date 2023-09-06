# CodeDump

```
# Remove features that are highly correlated with each other
correlated_features = set()
for i in range(len(correlation_matrix.columns)):
    for j in range(i):
        if abs(correlation_matrix.iloc[i, j]) > 0.8:
            colname_i = correlation_matrix.columns[i]
            colname_j = correlation_matrix.columns[j]
            # Remove the feature that is less correlated with the target
            if abs(correlation_matrix.loc[colname_i, 'your_target_column']) < abs(correlation_matrix.loc[colname_j, 'your_target_column']):
                correlated_features.add(colname_i)
            else:
                correlated_features.add(colname_j)

final_features = (set(highly_correlated_features) | set(important_features_rfe)) - correlated_features

```

# CodeDump

```
from sklearn.linear_model import LinearRegression
from sklearn.feature_selection import RFECV
from sklearn.model_selection import TimeSeriesSplit

# ... (your previous data preparation code) ...

# Calculate the correlation matrix
correlation_matrix = numeric_df.corr()

# Initialize sets to hold feature names
correlated_features = set()
final_features = set()

# Step 1: Remove features that are highly correlated with each other (>0.8)
for i in range(len(correlation_matrix.columns)):
    for j in range(i):
        if abs(correlation_matrix.iloc[i, j]) > 0.8:
            colname_i = correlation_matrix.columns[i]
            colname_j = correlation_matrix.columns[j]
            
            # Keep the feature that is more correlated with the target variable
            if abs(correlation_matrix.loc[colname_i, 'your_target_column']) < abs(correlation_matrix.loc[colname_j, 'your_target_column']):
                correlated_features.add(colname_i)
            else:
                correlated_features.add(colname_j)

# Step 2: Select features that are highly correlated with the target variable (>0.7)
highly_correlated_features = set(correlation_matrix.index[abs(correlation_matrix['your_target_column']) > 0.7])

# Combine these two criteria
potential_features = highly_correlated_features - correlated_features

# Step 3: Perform RFECV
X = numeric_df[list(potential_features)]
y = numeric_df['your_target_column']

estimator = LinearRegression()

# Using TimeSeriesSplit for time-series data
tscv = TimeSeriesSplit(n_splits=5)

selector = RFECV(estimator, step=1, cv=tscv)  
selector = selector.fit(X, y)

# Add RFECV-selected features to the final feature set
final_features.update(X.columns[selector.support_])

print("Final Selected Features:", final_features)

```

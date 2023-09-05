# CodeDump

```
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.feature_selection import RFE
from sklearn.linear_model import LinearRegression
import seaborn as sns
import matplotlib.pyplot as plt

# Load your dataset
# Replace 'your_data.csv' with your actual file
df = pd.read_csv('your_data.csv')

# Assuming 'num_investors' is the column to be predicted
y = df['num_investors']
X = df.drop('num_investors', axis=1)

# 1. Initial Review using Correlation Matrix
correlation_matrix = df.corr()
sns.heatmap(correlation_matrix, annot=True)
plt.show()

# Drop features that are highly correlated with each other (threshold 0.8)
corr_threshold = 0.8
corr_var_list = correlation_matrix['num_investors'].sort_values(ascending=False)
relevant_features = corr_var_list[corr_var_list > corr_threshold]
X = X[relevant_features.index]

# 2. Random Forest for feature importance
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
rf = RandomForestRegressor(n_estimators=100, random_state=42)
rf.fit(X_train, y_train)

feature_importances = rf.feature_importances_
features = X_train.columns
sorted_idx = feature_importances.argsort()

print("Feature Importances from Random Forest:")
for index in sorted_idx:
    print(f"{features[index]}: {feature_importances[index]}")

# Consider only the top N important features from Random Forest
# Replace N with the number of features you want to keep
N = 10
top_N_features = features[sorted_idx[-N:]]

# 3. Recursive Feature Elimination (RFE)
lr = LinearRegression()
selector = RFE(lr, n_features_to_select=5)
selector = selector.fit(X_train[top_N_features], y_train)

print("Final Features from RFE:")
final_features = top_N_features[selector.support_]
print(final_features)


```

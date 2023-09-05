# CodeDump

```
import os
import pandas as pd
import numpy as np
from sklearn.feature_selection import RFE
from sklearn.linear_model import LinearRegression

# Create an empty dictionary to store DataFrames
dataframes = {}

# List of files where you want to calculate the mean
files_to_average = ['file1.csv', 'file2.csv']  # Replace these with actual filenames

# Loop through all files in the current directory
for filename in os.listdir('.'):
    if filename.endswith('.csv'):
        df = pd.read_csv(filename)

        # Remove special characters and convert all columns except 'date' to numeric
        for col in df.columns:
            if col != 'date':
                df[col] = df[col].astype(str).str.replace(r'[^0-9.]', '', regex=True)
                df[col] = pd.to_numeric(df[col], errors='coerce')

        df['date'] = pd.to_datetime(df['date'])
        df['date'] = df['date'].dt.strftime('%b %Y')

        # Aggregate data
        if filename in files_to_average:
            df = df.groupby('date').mean().reset_index()
        else:
            df = df.groupby('date').size().reset_index(name=f'count_{filename[:-4]}')

        dataframes[filename[:-4]] = df

# Initialize merged_df with the first DataFrame
first_key = next(iter(dataframes))
merged_df = dataframes[first_key]

# Merge the remaining DataFrames
for key, df in dataframes.items():
    if key != first_key:
        merged_df = pd.merge(merged_df, df, on='date', how='outer')

# Remove columns where more than 80% of the data is null
threshold = int(len(merged_df) * 0.2)
merged_df.dropna(thresh=threshold, axis=1, inplace=True)

# Sort by date
merged_df['date'] = pd.to_datetime(merged_df['date'], errors='coerce')
merged_df = merged_df.sort_values(by='date')

# Feature Selection
# Selecting numeric columns for correlation and RFE
numeric_df = merged_df.select_dtypes(include=['number'])

# Correlation Analysis
correlation_matrix = numeric_df.corr()
highly_correlated_features = correlation_matrix.index[abs(correlation_matrix['your_target_column']) > 0.7]  # Replace 'your_target_column'

# RFE
X = numeric_df.drop(['your_target_column'], axis=1)  # Replace 'your_target_column'
y = numeric_df['your_target_column']  # Replace 'your_target_column'

estimator = LinearRegression()
selector = RFE(estimator, n_features_to_select=5)  # Change the number of features you want to keep
selector = selector.fit(X, y)
important_features_rfe = X.columns[selector.support_]

# Combining both methods
final_features = set(highly_correlated_features) | set(important_features_rfe)

print("Final Selected Features:", final_features)




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

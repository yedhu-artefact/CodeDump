# CodeDump

```
import pandas as pd

# Assuming your data is stored in a pandas DataFrame called 'investments_data'

# Define the aggregations dictionary
aggregations = {
    'num_purchases': ('cluster_id', 'count'),
    'num_props_owned': ('is_current_owner', lambda x: sum(x == 1)),
    'total_purchase_value': ('property_worth', 'sum'),
    'current_investment_value': ('property_worth', lambda x: sum(x[x['is_current_owner'] == 1])),
    'avg_investment_value': ('property_worth', lambda x: x[x['is_current_owner'] == 1].mean()),
    'last_purchase': ('date_of_purchase', lambda x: (pd.to_datetime('today') - x.max()).days),
    'first_purchase': ('date_of_purchase', lambda x: (pd.to_datetime('today') - x.min()).days),
    'purchase_frequency': ('date_of_purchase', lambda x: len(x) / ((pd.to_datetime('today') - x.min()).days)),
    'avg_participant_area': ('property_area', 'mean'),
    'avg_ownership_period': ('date_of_purchase', lambda x: x[x['is_current_owner'] == 1].mean()),
    'num_distinct_areas': ('area_name', lambda x: len(x.unique())),
    'nationality_en': ('nationality_en', lambda x: x.value_counts().index[0]),
    'gender_en': ('gender_en', lambda x: x.value_counts().index[0])
}

# Group the data by cluster_id and calculate the aggregations
grouped_data = investments_data.groupby('cluster_id').agg(aggregations)

# Sum the one-hot encoded columns for each cluster_id
one_hot_columns = ['is_residential', 'is_commercial', 'is_dubai', 'is_sharjah']
grouped_data[one_hot_columns] = investments_data.groupby('cluster_id')[one_hot_columns].sum()

# Display the resulting grouped and aggregated data
print(grouped_data)

# Find all columns that start with 'is_'
one_hot_columns = [column for column in investments_data.columns if column.startswith('is_')]

# Group the data by cluster_id and calculate the aggregations
grouped_data = investments_data.groupby('cluster_id').agg(aggregations)

# Sum the one-hot encoded columns for each cluster_id
grouped_data[one_hot_columns] = investments_data.groupby('cluster_id')[one_hot_columns].sum()

# Display the resulting grouped and aggregated data
print(grouped_data)
```

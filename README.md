# CodeDump

```
import pandas as pd
import matplotlib.pyplot as plt

# Assuming you have a DataFrame named 'investments_df'
# Replace this with your actual DataFrame or read it from a CSV, Excel, or any other data source.

# Sample data
data = {
    'participant_id': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    'instance_date': ['2023-07-10', '2023-07-10', '2023-07-11', '2023-07-12', '2023-07-12',
                      '2023-07-13', '2023-07-14', '2023-07-14', '2023-07-15', '2023-07-15']
}

investments_df = pd.DataFrame(data)

# Convert 'instance_date' column to datetime format
investments_df['instance_date'] = pd.to_datetime(investments_df['instance_date'])

# Group by 'instance_date' and count the number of purchases ('participant_id')
purchase_counts = investments_df.groupby('instance_date')['participant_id'].count()

# Create the line chart
plt.figure(figsize=(10, 6))
plt.plot(purchase_counts.index, purchase_counts.values, marker='o', linestyle='-')
plt.xlabel('Date')
plt.ylabel('Number of Purchases')
plt.title('Number of Purchases Over Time')
plt.xticks(rotation=45)
plt.grid(True)
plt.tight_layout()
plt.show()

```

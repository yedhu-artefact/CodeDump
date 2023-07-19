# CodeDump

```
import pandas as pd

# Assuming you have a DataFrame called 'df' with a 'city_name' column

# Group the cities by count of customers
city_counts = df['city_name'].value_counts()

# Determine the cutoffs for city types
total_customers = len(df)
cumulative_percentage = 0
city_type_cutoffs = []
city_type_labels = ['common', 'premium', 'prime']  # Modify labels as needed

for city, count in city_counts.items():
    cumulative_percentage += count / total_customers * 100
    if cumulative_percentage > 90:  # Modify the threshold as needed
        break
    city_type_cutoffs.append(count)

# Add the last cutoff as infinity to include all remaining cities
city_type_cutoffs.append(float('inf'))

# Create a mapping of city names to city types
city_mapping = {}
for city, count in city_counts.items():
    for i, cutoff in enumerate(city_type_cutoffs):
        if count <= cutoff:
            city_mapping[city] = city_type_labels[i]
            break

# Add a new column 'city_type' based on the mapping
df['city_type'] = df['city_name'].map(city_mapping)

# Print the updated DataFrame
print(df)
```

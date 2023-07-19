# CodeDump

```
import pandas as pd

# Assuming you have a DataFrame called 'df' with a 'city_name' column

# Group the cities by count of customers
city_counts = df['city_name'].value_counts()

# Calculate the quartiles of the count distribution
quartiles = city_counts.quantile([0.25, 0.5, 0.75])

# Determine the cutoffs for city types
city_type_cutoffs = [0] + quartiles.tolist() + [float('inf')]
city_type_labels = ['common', 'premium', 'prime']  # Modify labels as needed

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

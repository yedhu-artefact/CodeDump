# CodeDump

```
# Replace 10 most frequent values with Null in 'phone_number' column
top_10_phone_numbers = df['phone_number'].value_counts().nlargest(10).index
df.loc[df['phone_number'].isin(top_10_phone_numbers), 'phone_number'] = np.nan

# Replace 10 most frequent values with Null in 'email' column
top_10_emails = df['email'].value_counts().nlargest(10).index
df.loc[df['email'].isin(top_10_emails), 'email'] = np.nan


import pandas as pd
import re
import numpy as np

# Read your data into a Pandas DataFrame
df = pd.read_csv('your_data.csv')

# Clean and validate 'phone_number' column
df['phone_number'] = df['phone_number'].astype(str).str.replace('[^0-9]', '')
df.loc[df['phone_number'].str.len() <= 7, 'phone_number'] = np.nan

# Clean and validate 'email' column
df['email'] = df['email'].str.lower()
df.loc[~df['email'].str.match(r'^[^@]+@[^@]+\.[^@]+$', na=False), 'email'] = np.nan
df.loc[df['email'].str.contains('test|sample', na=False), 'email'] = np.nan

# Replace most frequent values with Null
most_frequent_phone = df['phone_number'].mode().iloc[0]
most_frequent_email = df['email'].mode().iloc[0]
df.loc[df['phone_number'] == most_frequent_phone, 'phone_number'] = np.nan
df.loc[df['email'] == most_frequent_email, 'email'] = np.nan

# Display the updated DataFrame
print(df)
```

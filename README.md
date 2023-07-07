# CodeDump

```
import pandas as pd
import re
import numpy as np

# Read your data into a Pandas DataFrame
df = pd.read_csv('your_data.csv')

# Regular expression for validating Emirates ID format
emirates_id_regex = r'^784-?[0-9]{4}-?[0-9]{7}-?[0-9]{1}$'

# Function to check if a number is valid using Luhn's algorithm
def is_luhn_valid(number):
    digits = [int(digit) for digit in str(number)]
    odd_digits = digits[-1::-2]
    even_digits = digits[-2::-2]
    total = sum(odd_digits)
    for digit in even_digits:
        total += sum(divmod(digit * 2, 10))
    return total % 10 == 0

# Create a new column with valid Emirates ID values
valid_emirates_id = df['emirates_id'].astype(str).apply(lambda x: x if (bool(re.match(emirates_id_regex, x)) and is_luhn_valid(x.replace('-', ''))) else np.nan)
df['valid_emirates_id'] = valid_emirates_id

# Display the updated DataFrame
print(df)

```

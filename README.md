# CodeDump

```
import pandas as pd
import numpy as np

# Sample dataframe with NaN and landline numbers for demonstration
data = {
    'phone_number': ['(971) 50 1234567', '+971501234567', '501234567', '+1 800 123 4567', '443020123456', '00971501234567', '0971501234567', np.nan, '0241234567', '+97141234567']
}
df = pd.DataFrame(data)

# Clean the phone_number column: Keep only numbers and leading plus sign
df['phone_number'] = df['phone_number'].str.replace('[^0-9+]', '', regex=True)

# Standardize UAE numbers
def standardize_uae_format(phone):
    # If the value is NaN, return it as is
    if pd.isna(phone):
        return phone
    
    # Mobile Numbers
    if phone.startswith('9715') and len(phone) == 12:      # Format: +971501234567
        return '+971' + phone[3:]
    elif phone.startswith('009715') and len(phone) == 14: # Format: 00971501234567
        return '+971' + phone[5:]
    elif phone.startswith('09715') and len(phone) == 13:  # Format: 0971501234567
        return '+971' + phone[4:]
    elif phone.startswith('5') and len(phone) == 9:       # Format: 501234567
        return '+971' + phone
    
    # Landline Numbers
    elif (phone.startswith(('2', '4', '6', '7', '9')) and len(phone) == 9): # Format: 0241234567
        return '+971' + phone
    elif phone.startswith('971') and len(phone) == 11:  # Format: +97141234567 (for landlines)
        return '+971' + phone[3:]
    elif phone.startswith('00971') and len(phone) == 13: # Format: 0097141234567 (for landlines)
        return '+971' + phone[5:]
    elif phone.startswith('0971') and len(phone) == 12:  # Format: 097141234567 (for landlines)
        return '+971' + phone[4:]
    
    else:
        return phone

df['phone_number'] = df['phone_number'].apply(standardize_uae_format)
print(df)













IF DATEPART('quarter', TODAY()) = 1 THEN 
    DATEADD('quarter', -1, DATE(#YEAR(TODAY())-1#, 10, 1))
ELSE
    DATEADD('quarter', -1, DATE(TRUNC(DATEPART('year', TODAY())), (DATEPART('quarter', TODAY())-1)*3 + 1, 1))
END


IF DATEPART('quarter', TODAY()) = 1 THEN 
    DATE(#YEAR(TODAY())-1#, 12, 31)
ELSE
    DATEADD('day', -1, DATE(TRUNC(DATEPART('year', TODAY())), DATEPART('quarter', TODAY())*3 + 1, 1))
END


// Define the Last Purchase Date
[Last Purchase Date] = TODAY() - [days since last purchase]

// Categorize based on Last Purchase Date
IF [Last Purchase Date] >= [Last Quarter Start] AND [Last Purchase Date] <= [Last Quarter End] THEN
    "New Investors"
ELSE 
    "Active Investors"
END

```

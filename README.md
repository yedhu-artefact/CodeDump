# CodeDump

```
# Clean the phone_number column: Keep only numbers and leading plus sign
df['phone_number'] = df['phone_number'].str.replace('[^0-9+]', '', regex=True)

# Standardize UAE phone numbers
def standardize_uae_format(phone):
    if phone.startswith('9715') and len(phone) == 12:  # Format: +971501234567
        return '+971 ' + phone[3:5] + ' ' + phone[5:8] + ' ' + phone[8:]
    elif phone.startswith('5') and len(phone) == 9:  # Format: 501234567
        return '+971 ' + phone[:2] + ' ' + phone[2:5] + ' ' + phone[5:]
    else:
        return phone

df['phone_number'] = df['phone_number'].apply(standardize_uae_format)






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

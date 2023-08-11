# CodeDump

```
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

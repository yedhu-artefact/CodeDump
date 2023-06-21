# CodeDump

```
import pandas as pd
import plotly.graph_objects as go

# Load the data from the table
df = pd.read_csv('investments_table.csv')  # Replace 'investments_table.csv' with your actual file name

# Filter for the last 3 years
df['procedure_date'] = pd.to_datetime(df['procedure_date'])
last_three_years = pd.date_range(end=df['procedure_date'].max(), periods=3, freq='Y')
df_last_three_years = df[df['procedure_date'].isin(last_three_years)]

# Filter for the top 10 countries
top_10_countries = df_last_three_years['country_name_en'].value_counts().nlargest(10).index
df_top_10_countries = df_last_three_years[df_last_three_years['country_name_en'].isin(top_10_countries)]

# Group by procedure_date and country_name_en and count the number of investments
grouped_data = df_top_10_countries.groupby(['procedure_date', 'country_name_en']).size().reset_index(name='count')

# Create nodes and links for Sankey chart
nodes = []
node_ids = {}
links = []

node_id = 0

for index, row in grouped_data.iterrows():
    source = row['procedure_date']
    target = row['country_name_en']
    count = row['count']
    
    if source not in node_ids:
        node_ids[source] = node_id
        nodes.append({'label': source})
        node_id += 1
    
    if target not in node_ids:
        node_ids[target] = node_id
        nodes.append({'label': target})
        node_id += 1
    
    links.append({'source': node_ids[source], 'target': node_ids[target], 'value': count})

# Create Sankey diagram
fig = go.Figure(data=[go.Sankey(
    node=dict(
        pad=15,
        thickness=20,
        line=dict(color='black', width=0.5),
        label=nodes
    ),
    link=dict(
        source=[link['source'] for link in links],
        target=[link['target'] for link in links],
        value=[link['value'] for link in links]
    )
)])

# Update layout
fig.update_layout(
    title="Investment Flow between Nationalities over Time (Top 10 Countries, Last 3 Years)",
    font=dict(size=10),
    height=600
)

# Show the chart
fig.show()
```

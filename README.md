# CodeDump

```
import pandas as pd
import plotly.graph_objects as go

# Load the data from the table
df = pd.read_csv('investments_table.csv')  # Replace 'investments_table.csv' with your actual file name

# Group by participant_type_en, person_type_en, tenure_type_en, property_type_en and count the number of investments
grouped_data = df.groupby(['participant_type_en', 'person_type_en', 'tenure_type_en', 'property_type_en']).size().reset_index(name='count')

# Create nodes for Sankey chart
nodes = [
    dict(label=participant_type) for participant_type in grouped_data['participant_type_en'].unique()
] + [
    dict(label=person_type) for person_type in grouped_data['person_type_en'].unique()
] + [
    dict(label=tenure_type) for tenure_type in grouped_data['tenure_type_en'].unique()
] + [
    dict(label=property_type) for property_type in grouped_data['property_type_en'].unique()
]

# Create links for Sankey chart
links = []
for _, row in grouped_data.iterrows():
    links.append(dict(
        source=row['participant_type_en'],
        target=row['person_type_en'],
        value=row['count']
    ))
    links.append(dict(
        source=row['person_type_en'],
        target=row['tenure_type_en'],
        value=row['count']
    ))
    links.append(dict(
        source=row['tenure_type_en'],
        target=row['property_type_en'],
        value=row['count']
    ))

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
    title="Investment Flow: Participant Type -> Person Type -> Tenure Type -> Property Type",
    font=dict(size=10),
    height=600
)

# Show the chart
fig.show()
```

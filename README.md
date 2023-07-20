# CodeDump

```
# Create the line chart using seaborn
plt.figure(figsize=(10, 6))
sns.lineplot(x='instance_date', y='participant_id', data=purchase_counts, marker='o')
plt.xlabel('Date')
plt.ylabel('Number of Purchases')
plt.title('Number of Purchases Over Time')
plt.xticks(rotation=45)
plt.grid(True)
plt.tight_layout()
plt.show()

```

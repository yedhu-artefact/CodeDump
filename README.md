# CodeDump

```
import streamlit as st
import pandas as pd
from neuralprophet import NeuralProphet
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error

# Custom CSS to mimic Dubai Land Department's design
st.markdown("""
    <style>
        .reportview-container {
            background-color: #f4f4f4;
        }
        .sidebar .sidebar-content {
            background-color: #f4f4f4;
        }
        h1 {
            color: #008489;
        }
    </style>
    """, unsafe_allow_html=True)

# Add Logo (Replace 'logo_url' with the actual logo URL)
st.markdown(
    f'<img src="logo_url" style="width:300px;height:100px;">',
    unsafe_allow_html=True
)

# Import data from CSV
# Replace 'your_data.csv' with the path to your actual CSV file
df = pd.read_csv('your_data.csv')

# Sort data by date and split into training, validation, and test sets
df = df.sort_values('ds')
train_df, temp_df = train_test_split(df, test_size=0.4, shuffle=False)
val_df, test_df = train_test_split(temp_df, test_size=0.5, shuffle=False)

st.write("Training set:")
st.write(train_df)
st.write("Validation set:")
st.write(val_df)
st.write("Test set:")
st.write(test_df)

# Model Training
st.write("Training Neural Prophet model...")
model = NeuralProphet(
    n_forecasts=1,
    n_lags=30,
    ar_sparsity=0.8,
    yearly_seasonality=True,
    weekly_seasonality=False,
    daily_seasonality=False,
)

# Add lagged regressors if they are available in the data
lagged_regressors = ['daily_transactions', 'inflation_rate', 'gdp', 'risk_rate', 'stock_index']
for reg in lagged_regressors:
    if reg in df.columns:
        model = model.add_lagged_regressor(name=reg)

metrics = model.fit(train_df, freq="M", valid_p=0.2)
st.write("Training complete.")

# Evaluate model on test set
st.write("Evaluating model on test set...")
future = model.make_future_dataframe(train_df, n_historic_predictions=True)
forecast = model.predict(future)
predicted = forecast['yhat1'][-len(test_df):]
actual = test_df['y']
mse = mean_squared_error(actual, predicted)
st.write(f"Mean Squared Error on test set: {mse}")

# Make future predictions
st.write("Making future predictions for the next 6 months...")
future = model.make_future_dataframe(df, periods=6)
forecast = model.predict(future)

# Plotting
fig_forecast = model.plot(forecast)
st.write("Forecast for the next 6 months:")
st.write(fig_forecast)

fig_model = model.plot_parameters()
st.write("Model Parameters:")
st.write(fig_model)
```

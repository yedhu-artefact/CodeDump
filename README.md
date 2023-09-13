# CodeDump

```
import streamlit as st
import pandas as pd
import numpy as np
from neuralprophet import NeuralProphet
from sklearn.metrics import mean_squared_error, mean_absolute_percentage_error
import time

# Sidebar options
option = st.sidebar.selectbox(
    'Select an option',
    ('Data', 'Forecast', 'Performance')
)

# Import data from CSV
# Replace 'your_data.csv' with the path to your actual CSV file
df = pd.read_csv('your_data.csv')

# Sort data by date
df = df.sort_values('ds')
train_df = df.copy()

if option == 'Data':
    st.write("Training set:")
    st.write(train_df)

elif option == 'Forecast':
    scenario = st.selectbox(
        'Select a scenario',
        ('Optimistic', 'Neutral', 'Pessimistic')
    )

    # Model Training
    st.write("Training Neural Prophet model...")
    
    if scenario == 'Optimistic':
        sparsity = 0.9
    elif scenario == 'Neutral':
        sparsity = 0.8
    else:
        sparsity = 0.7

    model = NeuralProphet(
        n_forecasts=1,
        n_lags=30,
        ar_sparsity=sparsity,
        yearly_seasonality=True,
        weekly_seasonality=False,
        daily_seasonality=False,
    )

    # Add lagged regressors if they are available in the data
    lagged_regressors = ['daily_transactions', 'inflation_rate', 'gdp', 'risk_rate', 'stock_index']
    for reg in lagged_regressors:
        if reg in df.columns:
            model = model.add_lagged_regressor(name=reg)

    metrics = model.fit(train_df, freq="M")
    st.write("Training complete.")

    # Make future predictions
    st.write(f"Making future predictions for the next 6 months ({scenario} scenario)...")
    future = model.make_future_dataframe(df, periods=6)
    forecast = model.predict(future)

    # Plotting
    fig_forecast = model.plot(forecast)
    st.write("Forecast for the next 6 months:")
    st.write(fig_forecast)

elif option == 'Performance':
    # Evaluate model on training set
    st.write("Evaluating model on training set...")
    start_time = time.time()
    future = model.make_future_dataframe(train_df, n_historic_predictions=True)
    forecast = model.predict(future)
    end_time = time.time()
    runtime = end_time - start_time

    predicted = forecast['yhat1'][:len(train_df)]
    actual = train_df['y']
    mse = mean_squared_error(actual, predicted)
    mape = mean_absolute_percentage_error(actual, predicted)

    st.write(f"Mean Squared Error: {mse}")
    st.write(f"Mean Absolute Percentage Error: {mape}")
    st.write(f"Model Runtime: {runtime} seconds")

    num_months = st.slider("Number of months to forecast:", 1, 24)
    future = model.make_future_dataframe(df, periods=num_months)
    forecast = model.predict(future)

    fig_forecast = model.plot(forecast)
    st.write(f"Forecast for the next {num_months} months:")
    st.write(fig_forecast)

# Add Logo (Replace 'logo_url' with the actual logo URL)
st.sidebar.markdown(
    f'<img src="logo_url" style="width:300px;height:100px;">',
    unsafe_allow_html=True
)
```

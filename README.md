# CodeDump

```

# Create the plot
fig = go.Figure()

# Add actual values as a solid line
fig.add_trace(go.Scatter(x=forecast['ds'][:len(df)], y=forecast['y'][:len(df)], mode='lines', name='Actual', line=dict(width=2)))

# Add forecasted values as a dotted line
fig.add_trace(go.Scatter(x=forecast['ds'][len(df):], y=forecast['yhat1'][len(df):], mode='lines', name='Forecast',
                         line=dict(dash='dash', width=2)))

# Show the plot
fig.show()








df_train, df_test = m.split_df(sf_pv_df, freq="H", valid_p=0.10)

metrics = m.fit(df_train, freq="H", validation_df=df_test, progress="bar")
metrics.tail(1)

import streamlit as st
import pandas as pd
from neuralprophet import NeuralProphet

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

# Placeholder for status messages
status = st.empty()

if option == 'Data':
    st.write("Training set:")
    st.write(train_df)

elif option == 'Forecast':
    scenario = st.selectbox(
        'Select a scenario',
        ('Optimistic', 'Neutral', 'Pessimistic')
    )

    num_months = st.slider("Number of months to forecast:", 1, 24)

    # Button to run the model
    if st.button('Run Model'):
        # Model Training
        status.write("Initializing Neural Prophet model...")
        
        model = NeuralProphet(
            n_forecasts=1,
            n_lags=30,
            yearly_seasonality=True,
            weekly_seasonality=False,
            daily_seasonality=False,
            uncertainty_samples=100  # Enable uncertainty
        )

        status.write("Adding lagged regressors to the model...")
        
        # Add lagged regressors if they are available in the data
        lagged_regressors = ['daily_transactions', 'inflation_rate', 'gdp', 'risk_rate', 'stock_index']
        for reg in lagged_regressors:
            if reg in df.columns:
                model = model.add_lagged_regressor(name=reg)

        status.write("Training the model...")
        metrics = model.fit(train_df, freq="M")
        status.write("Training complete.")

        # Make future predictions
        status.write(f"Making future predictions for the next {num_months} months...")
        future = model.make_future_dataframe(df, periods=num_months)
        forecast = model.predict(future)
        status.write("Prediction complete.")

        # Extract confidence intervals
        lower_bound = forecast['yhat1_lower']
        upper_bound = forecast['yhat1_upper']
        yhat = forecast['yhat1']

        if scenario == 'Optimistic':
            status.write("Using upper bound of the confidence interval for the Optimistic scenario.")
            forecast_to_use = upper_bound
        elif scenario == 'Pessimistic':
            status.write("Using lower bound of the confidence interval for the Pessimistic scenario.")
            forecast_to_use = lower_bound
        else:  # Neutral
            status.write("Using the forecast itself for the Neutral scenario.")
            forecast_to_use = yhat

        # Plotting (you can replace this with a more sophisticated plot)
        st.line_chart(forecast_to_use)
        status.write("Forecasting complete.")

```

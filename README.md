# CodeDump

```
theme_override = {'bgcolor': ' rgb(180,151,231)','title_color': 'white','content_color': 'white','progress_color': ' rgb(180,151,231)'}
 hc.progress_bar(content_text= 'Fill the Form', override_theme=theme_override)

import streamlit as st
import pandas as pd
import numpy as np
from neuralprophet import NeuralProphet
from sklearn.linear_model import LinearRegression

# Import data from CSV
df = pd.read_csv('your_data.csv')
df = df.sort_values('ds')
train_df = df.copy()

# Placeholder for status messages
status = st.empty()

# Number of months to forecast
num_months = st.slider("Number of months to forecast:", 1, 24)

# Multipliers for features
multiplier_feature_1 = st.slider("Multiplier for feature_1:", 0.5, 1.5, 1.0)
multiplier_feature_2 = st.slider("Multiplier for feature_2:", 0.5, 1.5, 1.0)

# Button to run the model
if st.button('Run Model'):
    status.write("Initializing Neural Prophet model...")
    
    model = NeuralProphet(
        n_forecasts=1,
        n_lags=30,
        yearly_seasonality=True,
        weekly_seasonality=False,
        daily_seasonality=False,
        uncertainty_samples=100
    )

    # Train separate models for each feature
    future_regressors = pd.DataFrame()
    for feature, multiplier in zip(['feature_1', 'feature_2'], [multiplier_feature_1, multiplier_feature_2]):
        if feature in df.columns:
            status.write(f"Training model for {feature}...")
            X = np.arange(len(df)).reshape(-1, 1)
            y = df[feature].values
            reg_model = LinearRegression().fit(X, y)

            # Predict future values of the feature
            X_future = np.arange(len(df), len(df) + num_months).reshape(-1, 1)
            future_values = reg_model.predict(X_future)
            future_regressors[feature] = future_values * multiplier  # Apply multiplier

    # Add lagged regressors to the NeuralProphet model
    status.write("Adding lagged regressors to the NeuralProphet model...")
    model = model.add_lagged_regressor(name='feature_1')
    model = model.add_lagged_regressor(name='feature_2')

    status.write("Training the NeuralProphet model...")
    model.fit(train_df, freq="M")
    status.write("Training complete.")

    # Make future dataframe
    future = model.make_future_dataframe(df, periods=num_months)
    future = pd.concat([future, future_regressors], axis=1)  # Add future regressors

    # Make future predictions
    status.write(f"Making future predictions for the next {num_months} months...")
    forecast = model.predict(future)
    status.write("Prediction complete.")

    # Plotting
    fig_forecast = model.plot(forecast)
    st.write(fig_forecast)
```

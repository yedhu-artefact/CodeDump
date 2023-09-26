# CodeDump

```
import pandas as pd
import numpy as np
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error, mean_absolute_percentage_error
from fbprophet import Prophet
from neuralprophet import NeuralProphet

# Assume df is your DataFrame and 'y' is your target variable

# New Step: Fill missing values using interpolation
df.interpolate(method='linear', inplace=True)

# Step 1: Remove features that are highly correlated with each other
corr_matrix = df.corr().abs()
upper = corr_matrix.where(np.triu(np.ones(corr_matrix.shape), k=1).astype(np.bool))
to_drop = [column for column in upper.columns if any(upper[column] > 0.95)]
df = df.drop(df[to_drop], axis=1)

# Step 2: Select top 20 features using feature importance from RandomForestRegressor
X = df.drop(columns=['y'])
y = df['y']
model = RandomForestRegressor()
model.fit(X, y)
feature_importances = pd.DataFrame({'Feature': X.columns, 'Importance': model.feature_importances_})
top_features = feature_importances.nlargest(20, 'Importance')['Feature']

# Step 3: Predict the next 12 months values for these features using Prophet
future_values = {}
for feature in top_features:
    prophet = Prophet()
    prophet.fit(df[['ds', feature]].rename(columns={feature: 'y'}))
    future = prophet.make_future_dataframe(periods=12, freq='M')
    forecast = prophet.predict(future)
    future_values[feature] = forecast['yhat'][-12:].values

# Step 4: Test each feature as a future regressor with NeuralProphet and give a summary statistic of the error
errors = {}
for feature in top_features:
    future_regressor_df = pd.DataFrame({'ds': df['ds'], 'y': df['y'], feature: future_values[feature]})
    m = NeuralProphet()
    m.add_future_regressor(name=feature)
    metrics = m.fit(future_regressor_df, freq='M')
    future = m.make_future_dataframe(future_regressor_df, periods=12)
    forecast = m.predict(future)
    mse = mean_squared_error(future_regressor_df['y'], forecast['yhat1'])
    mape = mean_absolute_percentage_error(future_regressor_df['y'], forecast['yhat1'])
    errors[feature] = {'MSE': mse, 'MAPE': mape}

# Summary statistic of the error
error_summary = pd.DataFrame(errors).T.reset_index()
error_summary.columns = ['Feature', 'MSE', 'MAPE']
print(error_summary)

```

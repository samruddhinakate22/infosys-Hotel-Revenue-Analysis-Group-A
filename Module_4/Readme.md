Hotel Revenue Analysis – Module 4
Forecasting & Advanced Analytics (Power BI + Prophet)
Project Overview

This module focuses on forecasting future hotel bookings and analyzing demand trends using a combination of Power BI, DAX, and Python (Prophet).
The objective is to support data-driven decision-making for pricing, capacity planning, and promotional strategy.

Application Pages Overview
Home Page

The Home Page serves as the central navigation hub for the entire report.

Key Features

Interactive navigation menu using Bookmarks

Seamless access to all report sections:

Info & Discount Deals

Occupancy & Revenue Analysis

Guest Analysis

Forecast

About Us

Clean, executive-friendly layout with hotel branding

Preview


Info & Discount Deals Page

This page highlights targeted promotional offers based on guest type to support personalized marketing.

Key Features

Guest-specific offers:

Business

Couple

Family

Solo

Dynamic image switching using Bookmarks and Selection Pane

Discount visibility updated based on user selection

Preview


About Us Page

The About Us page presents a business overview and the hotel’s data-driven vision.

About the Hotel
The hotel provides comfortable stays and premium service for both business and leisure travelers.
By leveraging analytics and forecasting, the hotel aims to:

Optimize operations

Enhance guest experience

Achieve sustainable revenue growth

Additional Insights

Global revenue distribution by country

Built-in Q&A visual for executive queries

Feedback interaction button

Preview


Forecasting Methodology

A structured time-series forecasting approach was implemented to predict future booking demand.
The workflow integrates Power BI for data preparation and Python (Prophet) for forecasting.

Step 1: Creation of Month-End Date Column

A calculated column was created to standardize bookings at a monthly granularity, required for time-series analysis.

Month_End_Date = EOMONTH ( Raw_Fact[arrival_date], 0 )


This ensures all bookings align to the end of each month, improving trend consistency.

Step 2: Monthly Aggregation of Bookings

Bookings were aggregated at a monthly level using a summarized table.

Monthly_Bookings =
SUMMARIZE (
    Raw_Fact,
    Raw_Fact[Month_End_Date],
    "Total_Bookings", COUNT ( Raw_Fact[booking_id] )
)


Resulting Dataset

Month_End_Date → Time dimension

Total_Bookings → Forecast target variable

Step 3: Export of Aggregated Data

The Monthly_Bookings table was exported from Power BI as a CSV file.

This separation allows:

Power BI → Data preparation & visualization

Python → Advanced forecasting

Step 4: Forecasting Using Prophet (Python)

The exported dataset was loaded into Power BI using a Python script and processed with Facebook Prophet.

Key Processing Steps

Column renaming to Prophet format (ds, y)

Data type validation

Removal of null values

Forecasting 24 future months (2 years)

import pandas as pd
from prophet import Prophet

# Copy Power BI input
df = dataset.copy()

# Rename columns for Prophet
df = df.rename(columns={
    'Month_End_Date': 'ds',
    'Sum of Total_Bookings': 'y'
})

# Ensure correct data types
df['ds'] = pd.to_datetime(df['ds'])
df['y'] = pd.to_numeric(df['y'])

# Remove null values
df = df.dropna()

# Create and train Prophet model
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    daily_seasonality=False
)

model.fit(df)

# Generate future dates (24 months)
future = model.make_future_dataframe(
    periods=24,
    freq='ME'
)

# Forecast
forecast = model.predict(future)

# Output back to Power BI
result = forecast[['ds', 'yhat', 'yhat_lower', 'yhat_upper']]

Step 5: Integration Back into Power BI

The forecast output was reintegrated into Power BI and used to create:

Forecast KPIs

Actual vs Forecast trend analysis

Confidence interval bands

Peak demand identification

Outcome & Business Value

This forecasting module enables:

Accurate prediction of future booking demand

Identification of peak booking periods

Improved planning for pricing, staffing, and promotions

Strong support for data-driven revenue optimization

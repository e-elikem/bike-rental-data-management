# Bike Rental Data Management

A comprehensive relational database project for managing and analyzing Citi Bike trip data integrated with weather information. This project demonstrates data engineering best practices including data cleaning, database design, normalization, and analytics-ready view creation.

## 📋 Project Overview

This project creates a production-ready analytics database by:

1. **Inspecting & Cleaning** — Validating data quality from Citi Bike and NOAA weather datasets
2. **Database Design** — Developing a normalized relational schema with proper key relationships
3. **Implementation** — Building and populating the database in PostgreSQL using Python
4. **Analytics Views** — Creating flexible, queryable views for business intelligence

**Dataset:** 2016 Citi Bike trip records from Jersey City + Daily NOAA weather data from Newark Airport

## 🎯 Key Features

✅ **Normalized relational schema** with proper entity relationships  
✅ **Data quality handling** — Missing values, outliers, and validation  
✅ **Date dimension table** — Efficient date-based joins and analysis  
✅ **Pre-built analytics views** — Ready-to-query business metrics  
✅ **Weather integration** — Correlate ridership with environmental factors  
✅ **Entity Relationship Diagram** — Visual schema documentation  

## 📁 Project Structure

```
bike-rental-data-management/
├── README.md                  # This file
├── bike-rental.ipynb         # Data cleaning & exploration (Jupyter Notebook)
├── tables.sql                # Database table definitions
├── views.sql                 # Analytics-ready views
├── ERD.pdf                   # Entity Relationship Diagram
├── writeup.md                # Detailed project documentation
├── data/                     # Raw dataset files
└── data_dictionaries/        # Data field documentation
```

## 💡 Possible Extensions

- **Bike maintenance tracking** — Track bike usage frequency and service schedules
- **Route analysis** — Most common origin-destination pairs
- **Revenue modeling** — Integrate pricing data for revenue forecasting
- **Peak demand prediction** — Build ML models using historical patterns
- **Real-time dashboards** — Visualize live ridership using BI tools (Tableau, Looker)
- **Anomaly detection** — Identify unusual trip patterns

## 📊 Technologies Used

- **PostgreSQL** — Relational database
- **Python** — Data loading (SQLAlchemy, Pandas)
- **Jupyter Notebook** — Data exploration
- **SQL** — Schema and views
- **Git** — Version control

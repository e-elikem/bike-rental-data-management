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

## 🗄️ Database Schema

### Core Tables

**`date_dim`** — Date dimension for efficient date-based joins
- `date_key` (PK) — Integer representation of date (YYYYMMDD format)
- `full_date`, `month`, `day`, `month_name`, `day_name`
- `weekend` — Boolean indicator
- `financial_qtr` — Quarter number

**`stations`** — Bike station locations
- `id` (PK) — Station identifier
- `station_name`, `latitude`, `longitude`

**`trip_demo`** — Trip demographic information
- `id` (PK) — Demographic record ID
- `user_type` — Subscriber, Customer, or Unknown
- `gender`, `birth_year`, `age`

**`weather`** — Daily weather conditions
- `id` (PK), `rec_date`
- `tavg`, `tmax`, `tmin` — Temperature readings
- `avg_wind`, `prcp` — Wind speed and precipitation
- `snow_amt`, `snow_depth` — Snow measurements
- `rain`, `snow` — Boolean indicators
- `date_key` (FK) — Reference to date dimension

**`rides`** — Bike rental trips
- `id` (PK) — Trip identifier
- `start_time`, `stop_time` — Timestamps
- `trip_duration`, `trip_minutes`, `trip_hours`
- `start_station_id`, `end_station_id` (FK)
- `bike_id` — Bike identifier
- `trip_demo` (FK) — Link to demographics
- `valid_duration` — Boolean (trips >24hrs flagged)
- `date_key` (FK) — Reference to date dimension

### Relationships

```
date_dim ←→ rides
date_dim ←→ weather
stations ←— rides (start_station_id, end_station_id)
trip_demo ←— rides
```

## 📊 Analytics Views

### 1. **daily_counts**
Ride counts broken down by day with user type segmentation.
- Total rides, subscriber rides, customer rides
- Unknown user types and late returns (trips >24 hours)
- Date dimensions and weekend indicators

### 2. **daily_data**
Comprehensive daily dashboard combining rides and weather.
- All daily_counts fields
- Running monthly totals (window functions)
- Temperature, wind, precipitation data
- Flags for rainy/snowy days

**Perfect for:** Day-over-day trend analysis, weather correlation studies

### 3. **monthly_data**
Aggregated monthly metrics with user breakdowns.
- Total and average rides by user type
- Monthly weather summaries
- Days with rain/snow counts
- Late return tracking

**Perfect for:** Seasonal analysis, monthly reporting

### 4. **late_return**
Investigative view for trips exceeding 24-hour rental limit.
- Full dates and bike IDs
- Start/end stations
- User type information
- Trip duration in hours

**Perfect for:** Fraud detection, rental policy enforcement

### 5. **Supporting Views**
- **`hourly_summary`** — Rides by hour of day
- **`trip_demographics`** — Rider demographics (age, gender, type)
- **`week_summary`** — Average rides by day of week

## 🧹 Data Cleaning & Preprocessing

### Issues Identified & Resolved

**Citi Bike Dataset:**
- ❌ 4 days completely missing from records
- ❌ Missing/unknown values in user_type and demographic fields
- ❌ Extreme outliers in trip_duration (trips lasting thousands of hours despite 24-hour max)
- ✅ Handled through flagging and separate tracking in views

**Weather Dataset:**
- ❌ All-null columns (dropped)
- ✅ Kept: avg_wind, precipitation, temperature (min/max/avg), snow measurements

**Solution:** Created `valid_duration` flag and `unknown_rides` counts in views to help analysts investigate data quality issues.

## 🚀 Getting Started

### Prerequisites

- **PostgreSQL** 10+
- **Python** 3.7+ (for data loading)
- **Jupyter Notebook** (to review cleaning process)

### Setup

1. **Review the cleaning process:**
   ```bash
   jupyter notebook bike-rental.ipynb
   ```

2. **Create the database and tables:**
   ```bash
   psql -U your_user -d your_database -f tables.sql
   ```

3. **Load your data** (see notebook for data export steps):
   - Export cleaned Citi Bike data to CSV
   - Export weather data to CSV
   - Use Python + SQLAlchemy to bulk load into PostgreSQL

4. **Create analytics views:**
   ```bash
   psql -U your_user -d your_database -f views.sql
   ```

5. **Start querying:**
   ```sql
   SELECT * FROM daily_data WHERE month_name = 'July';
   SELECT * FROM monthly_data;
   SELECT * FROM late_return LIMIT 10;
   ```

## 📈 Example Queries

**Rides by day of week:**
```sql
SELECT day_name, AVG(ride_totals) as avg_rides
FROM daily_counts
GROUP BY day_name
ORDER BY avg_rides DESC;
```

**Weather impact on ridership:**
```sql
SELECT 
  weather.rain,
  weather.snow,
  ROUND(AVG(daily.ride_totals)) as avg_rides
FROM daily_data daily
JOIN weather ON daily.date_key = weather.date_key
GROUP BY weather.rain, weather.snow;
```

**Subscriber vs Customer comparison:**
```sql
SELECT 
  month_name,
  total_subscriber_rides,
  total_customer_rides,
  ROUND(100.0 * total_subscriber_rides / (total_subscriber_rides + total_customer_rides)) as subscriber_pct
FROM monthly_data
ORDER BY month;
```

## 📚 Documentation

- **[writeup.md](./writeup.md)** — Detailed project documentation with methodology
- **[ERD.pdf](./ERD.pdf)** — Entity Relationship Diagram
- **[bike-rental.ipynb](./bike-rental.ipynb)** — Data exploration and cleaning notebook
- **[tables.sql](./tables.sql)** — Database schema
- **[views.sql](./views.sql)** — Analytics view definitions

## 🔑 Key Design Decisions

### 1. Date Dimension Table
**Why:** Joining on date-time fields is risky; integer `date_key` enables fast, reliable joins.

### 2. Demographic Normalization
**Why:** Separates trip characteristics from ride records, allowing efficient querying and updates.

### 3. Station Normalization
**Why:** Avoids data duplication and enables geographic analysis (latitude/longitude lookups).

### 4. Valid Duration Flag
**Why:** Rather than removing questionable records, we flag them for analyst review—no data loss.

### 5. Weather Integration
**Why:** Enables correlation analysis between environmental conditions and ridership patterns.

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

## 📄 License

This project is provided as-is for educational and analytical purposes.

---

**Questions or suggestions?** Feel free to open an issue or submit a pull request! 🚴‍♂️


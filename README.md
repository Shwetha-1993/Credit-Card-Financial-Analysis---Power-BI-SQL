# Credit Card Weekly Dashboard – Power BI & SQL

📌 Overview
This project focuses on creating a weekly dashboard that provides real-time insights into credit card performance metrics. The solution uses SQL for data management and Power BI for visualization. It enables stakeholders to analyze revenue, customer behavior, and transaction trends effectively on a weekly basis.

🎯 Project Objective
To develop a comprehensive credit card analytics dashboard that delivers:
* Weekly performance tracking
* Revenue and transaction trends
* Customer segmentation by age and income
* Key operational KPIs, including delinquency and activation rates

🗂️ Project Contents
* SQL Data Import Process
* DAX Calculations & Data Processing
* Dashboard Features & Insights
* Exporting & Sharing

🛠️ Tools & Technologies
* Power BI Desktop
* Microsoft Excel
* SQL Server / PostgreSQL / MySQL
* Power Query
* DAX (Data Analysis Expressions)

🗃️ Data Preparation (SQL)
Steps to Import Data:
* Prepare  Microsoft Excel CSV Files for tables:
cust_detail.csv
cc_detail.csv
cc_add.csv
cust_add.csv

* -- SQL Query to create and import data from csv files:

  -- 0. Create a database 
CREATE DATABASE ccdb;

  -- 1. Create cc_detail table

CREATE TABLE cc_detail (
    Client_Num INT,
    Card_Category VARCHAR(20),
    Annual_Fees INT,
    Activation_30_Days INT,
    Customer_Acq_Cost INT,
    Week_Start_Date DATE,
    Week_Num VARCHAR(20),
    Qtr VARCHAR(10),
    current_year INT,
    Credit_Limit DECIMAL(10,2),
    Total_Revolving_Bal INT,
    Total_Trans_Amt INT,
    Total_Trans_Ct INT,
    Avg_Utilization_Ratio DECIMAL(10,3),
    Use_Chip VARCHAR(10),
    Exp_Type VARCHAR(50),
    Interest_Earned DECIMAL(10,3),
    Delinquent_Acc VARCHAR(5)
);

-- 2. Create cc_detail table

CREATE TABLE cust_detail (
    Client_Num INT,
    Customer_Age INT,
    Gender VARCHAR(5),
    Dependent_Count INT,
    Education_Level VARCHAR(50),
    Marital_Status VARCHAR(20),
    State_cd VARCHAR(50),
    Zipcode VARCHAR(20),
    Car_Owner VARCHAR(5),
    House_Owner VARCHAR(5),
    Personal_Loan VARCHAR(5),
    Contact VARCHAR(50),
    Customer_Job VARCHAR(50),
    Income INT,
    Cust_Satisfaction_Score INT
);

-- 3. Copy csv data into SQL (remember to update the file name and file location in below query)

-- copy cc_detail table

COPY cc_detail
FROM 'D:\credit_card.csv' 
DELIMITER ',' 
CSV HEADER;

-- copy cust_detail table

COPY cust_detail
FROM 'D:\customer.csv' 
DELIMITER ',' 
CSV HEADER;

-- getting below error, then use the point below:  
   -- ERROR:  date/time field value out of range: "0"
   -- HINT:  Perhaps you need a different "datestyle" setting.
-- Check the Data in Your CSV File: Ensure date column values are formatted correctly and are in a valid format that PostgreSQL can recognize (e.g., YYYY-MM-DD). And correct any incorrect or missing date values in the CSV file. 
   -- or
-- Update the Datestyle Setting: Set the datestyle explicitly for your session using the following command:

SET datestyle TO 'ISO, DMY';

-- Now, try to COPY the CSV files!

**To check the refresh data Use case**
-- 4. Insert additional data into SQL, using same COPY function
-- copy additional data (week-53) in cc_detail table

COPY cc_detail
FROM 'D:\cc_add.csv' 
DELIMITER ',' 
CSV HEADER;

-- copy additional data (week-53) in cust_detail table (remember to update the file name and file location in below query)

COPY cust_detail
FROM 'D:\cust_add.csv' 
DELIMITER ',' 
CSV HEADER;

🧮 Key DAX Calculations
* 🎯 Age Grouping
DAX
Copy
Edit
AgeGroup = SWITCH(
    TRUE(),
    'public cust_detail'[customer_age] < 30, "20-30",
    'public cust_detail'[customer_age] < 40, "30-40",
    'public cust_detail'[customer_age] < 50, "40-50",
    'public cust_detail'[customer_age] < 60, "50-60",
    'public cust_detail'[customer_age] >= 60, "60+",
    "unknown"
)

* 💰 Income Grouping
DAX
Copy
Edit
IncomeGroup = SWITCH(
    TRUE(),
    'public cust_detail'[income] < 35000, "Low",
    'public cust_detail'[income] < 70000, "Med",
    'public cust_detail'[income] >= 70000, "High",
    "unknown"
)

* 📅 Weekly Revenue Metrics
DAX
Copy
Edit
week_num2 = WEEKNUM('public cc_detail'[week_start_date])

Revenue = 
    'public cc_detail'[annual_fees] + 
    'public cc_detail'[total_trans_amt] + 
    'public cc_detail'[interest_earned]

Current_week_Reveneue = CALCULATE(
    SUM('public cc_detail'[Revenue]),
    FILTER(
        ALL('public cc_detail'),
        'public cc_detail'[week_num2] = MAX('public cc_detail'[week_num2])
    )
)

Previous_week_Reveneue = CALCULATE(
    SUM('public cc_detail'[Revenue]),
    FILTER(
        ALL('public cc_detail'),
        'public cc_detail'[week_num2] = MAX('public cc_detail'[week_num2]) - 1
    )
)

📊 Dashboard Features
* KPIs: Weekly Revenue, Transaction Volume, Customer Count, Activation Rate, Delinquency Rate
* Weekly Trends: Revenue and transaction comparison (WoW)
* Demographics: Revenue contribution by gender, age, and income
* Card Performance: Usage by card type (Blue, Silver, etc.)
* Geographical Trends: Top contributing states (TX, NY, CA)
* Filters: Period, card type, state, customer segment

📈 Key Business Insights – Week 53 (Dec 31)
* 📈 Revenue increased by 28.8% WoW
* 🧾 Transaction amount increased by 1.74% & count also increased by 2.22% WOW
* 👥 Customer count increased by 1.80% WOW 
  📊 Year-to-Date Summary:
* 💰 Total Revenue: $57M
* 💸 Interest Earned: $8M
* 💳 Transaction Volume: $46M
* 👨‍💼 Male Revenue: $31M, 👩‍💼 Female Revenue: $26M
* 🏦 Blue & Silver Cards: 93% of transactions
* 🗺️ TX, NY, CA: 68% contribution
* ✅ Activation Rate: 57.5%
* ⚠️ Delinquency Rate: 6.06%






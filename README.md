# Sales Retention and Churn Risk Analysis Using SQL

## Executive Summary

This project uses SQL to analyse a simulated telecom subscription business dataset. The aim is to identify customer churn risk, contract expiry risk, sales pipeline health, pricing changes, overdue payments, and retention campaign performance.

The project was designed to reflect a real business reporting environment where customer, contract, billing, usage, sales pipeline and retention campaign data are stored in separate tables. SQL was used to connect these datasets, calculate key metrics, and generate insights that can support sales, account management and customer retention decisions.

The project focuses on SQL skills including:

- Relational database design
- Primary keys and foreign keys
- SQL joins
- Common Table Expressions
- Aggregations
- CASE statements
- Date filtering
- Churn risk scoring
- Business reporting queries

---

## Business Problem

A telecom subscription company wants to improve customer retention and sales performance. The sales and account management teams need clear reporting to answer the following questions:

1. Which customers have contracts expiring soon?
2. Which customers have higher churn risk?
3. Which customers have overdue payments or service issues?
4. Which sales pipeline stages have the highest expected revenue?
5. Which retention offers are most effective?
6. Which customer segments generate the most revenue?

The business goal is to use SQL analysis to identify high-risk customers, protect revenue, and support better sales and retention actions.

---

## Methodology

The project followed these steps:

1. Designed a relational database using six tables:
   - customers
   - contracts
   - billing
   - usage_data
   - sales_pipeline
   - retention_campaigns

2. Created SQL tables with primary keys and foreign keys.

3. Inserted simulated telecom subscription data into each table.

4. Used SQL queries to analyse:
   - Contract expiry profiles
   - Pricing changes
   - Overdue payments
   - Customer usage behaviour
   - Churn risk score
   - Sales pipeline health
   - Retention campaign performance
   - Revenue by customer segment

5. Created business recommendations based on query results.

---

## Database Design

### Table Structure

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    industry VARCHAR(50),
    city VARCHAR(50),
    state VARCHAR(20),
    customer_segment VARCHAR(30),
    account_manager VARCHAR(50),
    join_date DATE
);

CREATE TABLE contracts (
    contract_id INT PRIMARY KEY,
    customer_id INT,
    service_type VARCHAR(50),
    contract_start_date DATE,
    contract_end_date DATE,
    monthly_fee NUMERIC(10,2),
    previous_monthly_fee NUMERIC(10,2),
    contract_status VARCHAR(30),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE billing (
    billing_id INT PRIMARY KEY,
    customer_id INT,
    billing_month DATE,
    monthly_charge NUMERIC(10,2),
    discount_amount NUMERIC(10,2),
    payment_status VARCHAR(30),
    days_overdue INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE usage_data (
    usage_id INT PRIMARY KEY,
    customer_id INT,
    usage_month DATE,
    data_usage_gb NUMERIC(10,2),
    support_tickets INT,
    service_downtime_hours NUMERIC(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE sales_pipeline (
    opportunity_id INT PRIMARY KEY,
    customer_id INT,
    opportunity_type VARCHAR(50),
    pipeline_stage VARCHAR(50),
    estimated_value NUMERIC(10,2),
    probability NUMERIC(5,2),
    created_date DATE,
    expected_close_date DATE,
    sales_owner VARCHAR(50),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE retention_campaigns (
    campaign_id INT PRIMARY KEY,
    customer_id INT,
    offer_type VARCHAR(50),
    offer_date DATE,
    offer_value NUMERIC(10,2),
    accepted_flag INT,
    retained_after_90_days INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

---

## Skills Demonstrated

### SQL Skills

- Created relational database tables using `CREATE TABLE`
- Defined primary keys and foreign keys
- Used `INNER JOIN` and `LEFT JOIN` to combine multiple data sources
- Used `GROUP BY` and aggregate functions to calculate business metrics
- Used `CASE WHEN` logic to classify customers by churn risk
- Used CTEs to break complex queries into clear steps
- Used date filtering to identify contracts expiring within 90 days
- Calculated weighted pipeline value and campaign success rates

### Business Analysis Skills

- Customer churn risk analysis
- Contract expiry reporting
- Sales pipeline reporting
- Retention campaign performance analysis
- Customer behaviour analysis
- Revenue by customer segment analysis
- Business recommendation writing

---

# SQL Analysis and Results

## 1. Contract Expiry Analysis

### Business Question

Which customers have contracts expiring within the next 90 days?

### SQL Code

```sql
SELECT
    c.customer_name,
    c.customer_segment,
    ct.service_type,
    ct.contract_end_date,
    ct.monthly_fee,
    c.account_manager
FROM customers c
JOIN contracts ct
    ON c.customer_id = ct.customer_id
WHERE ct.contract_end_date BETWEEN DATE '2026-05-11'
    AND DATE '2026-05-11' + INTERVAL '90 days'
ORDER BY ct.contract_end_date;
```

### Result

| Customer Name | Segment | Service Type | Contract End Date | Monthly Fee | Account Manager |
|---|---|---|---|---:|---|
| NSW Manufacturing Group | Enterprise | Secure Network | 2026-05-18 | 2300.00 | Emily Davis |
| Brisbane Logistics Co | Enterprise | Fibre Internet | 2026-05-20 | 1800.00 | James Wilson |
| Adelaide Finance Solutions | Enterprise | Cloud Network | 2026-05-25 | 1950.00 | Sarah Chen |
| Melbourne Health Partners | Enterprise | Cloud Network | 2026-05-30 | 2100.00 | Michael Brown |
| Queensland Aged Care Group | Enterprise | Fibre Internet | 2026-06-05 | 1750.00 | James Wilson |
| Hobart Food Supplies | SMB | Business Broadband | 2026-06-10 | 380.00 | Michael Brown |
| VIC Accounting Services | SMB | Business Broadband | 2026-06-25 | 390.00 | Michael Brown |
| Adelaide Legal Group | Enterprise | Fibre Internet | 2026-06-30 | 1250.00 | Sarah Chen |
| Sydney Retail Hub | SMB | Business Broadband | 2026-07-15 | 450.00 | Emily Davis |
| Melbourne Design Studio | SMB | Business Broadband | 2026-07-15 | 420.00 | Michael Brown |
| Darwin Tourism Group | SMB | Mobile and Internet Bundle | 2026-08-01 | 520.00 | James Wilson |

### Insight

11 customers have contracts expiring within 90 days. Enterprise customers should be prioritised first because they have higher monthly fees and greater revenue impact.

---

## 2. Pricing Change Analysis

### Business Question

Which customers experienced a price increase?

### SQL Code

```sql
SELECT
    c.customer_name,
    ct.service_type,
    ct.previous_monthly_fee,
    ct.monthly_fee,
    ct.monthly_fee - ct.previous_monthly_fee AS price_increase,
    ROUND(
        ((ct.monthly_fee - ct.previous_monthly_fee) / ct.previous_monthly_fee) * 100,
        2
    ) AS price_increase_percentage
FROM customers c
JOIN contracts ct
    ON c.customer_id = ct.customer_id
WHERE ct.monthly_fee > ct.previous_monthly_fee
ORDER BY price_increase_percentage DESC;
```

### Result

| Customer Name | Service Type | Previous Fee | Current Fee | Price Increase | Increase % |
|---|---|---:|---:|---:|---:|
| Sydney Retail Hub | Business Broadband | 400.00 | 450.00 | 50.00 | 12.50 |
| Brisbane Logistics Co | Fibre Internet | 1600.00 | 1800.00 | 200.00 | 12.50 |
| SA Construction Partners | Mobile and Internet Bundle | 800.00 | 880.00 | 80.00 | 10.00 |
| Adelaide Legal Group | Fibre Internet | 1150.00 | 1250.00 | 100.00 | 8.70 |
| Perth Mining Services | Secure Network | 2400.00 | 2600.00 | 200.00 | 8.33 |
| Adelaide Finance Solutions | Cloud Network | 1800.00 | 1950.00 | 150.00 | 8.33 |
| Hobart Food Supplies | Business Broadband | 350.00 | 380.00 | 30.00 | 8.57 |

### Insight

Customers with large price increases may require proactive communication from account managers, especially if they also have contracts expiring soon or service issues.

---

## 3. Overdue Payment Analysis

### Business Question

Which customers have overdue payments?

### SQL Code

```sql
SELECT
    c.customer_name,
    c.customer_segment,
    COUNT(b.billing_id) AS overdue_count,
    SUM(b.days_overdue) AS total_days_overdue,
    MAX(b.days_overdue) AS max_days_overdue
FROM customers c
JOIN billing b
    ON c.customer_id = b.customer_id
WHERE b.payment_status = 'Overdue'
GROUP BY
    c.customer_name,
    c.customer_segment
ORDER BY total_days_overdue DESC;
```

### Result

| Customer Name | Segment | Overdue Count | Total Days Overdue | Max Days Overdue |
|---|---|---:|---:|---:|
| Hobart Food Supplies | SMB | 3 | 70 | 30 |
| Melbourne Health Partners | Enterprise | 2 | 32 | 20 |
| Adelaide Finance Solutions | Enterprise | 1 | 10 | 10 |
| Brisbane Logistics Co | Enterprise | 1 | 8 | 8 |

### Insight

Hobart Food Supplies has the highest overdue risk, with three overdue payments and 70 total overdue days. This customer should be reviewed by the account manager and finance team.

---

## 4. Customer Usage and Service Experience Analysis

### Business Question

Which customers have high support tickets and service downtime?

### SQL Code

```sql
SELECT
    c.customer_name,
    c.customer_segment,
    SUM(u.support_tickets) AS total_support_tickets,
    SUM(u.service_downtime_hours) AS total_downtime_hours,
    ROUND(AVG(u.data_usage_gb), 2) AS avg_monthly_usage_gb
FROM customers c
JOIN usage_data u
    ON c.customer_id = u.customer_id
WHERE u.usage_month BETWEEN DATE '2026-03-01' AND DATE '2026-05-01'
GROUP BY
    c.customer_name,
    c.customer_segment
ORDER BY total_support_tickets DESC, total_downtime_hours DESC;
```

### Result

| Customer Name | Segment | Support Tickets | Downtime Hours | Avg Monthly Usage GB |
|---|---|---:|---:|---:|
| Hobart Food Supplies | SMB | 18 | 13.20 | 240.00 |
| Brisbane Logistics Co | Enterprise | 15 | 13.00 | 920.00 |
| Melbourne Health Partners | Enterprise | 14 | 11.50 | 1160.10 |
| Adelaide Finance Solutions | Enterprise | 9 | 7.30 | 946.67 |
| Canberra Education Network | Mid-Market | 7 | 3.70 | 626.67 |
| Adelaide Legal Group | Enterprise | 4 | 1.80 | 846.90 |

### Insight

Hobart Food Supplies, Brisbane Logistics Co and Melbourne Health Partners show the strongest service-related churn signals because they have both high support ticket volumes and high downtime.

---

## 5. Churn Risk Scoring

### Business Question

Which customers are at high risk of churn?

### Risk Scoring Logic

| Risk Factor | Score |
|---|---:|
| Contract expires within 90 days | 30 |
| Customer has overdue payment | 20 |
| Support tickets >= 10 | 25 |
| Downtime >= 8 hours | 15 |
| Price increase above 8% | 10 |

### SQL Code

```sql
WITH customer_usage AS (
    SELECT
        customer_id,
        SUM(support_tickets) AS total_support_tickets,
        SUM(service_downtime_hours) AS total_downtime_hours
    FROM usage_data
    WHERE usage_month BETWEEN DATE '2026-03-01' AND DATE '2026-05-01'
    GROUP BY customer_id
),

customer_billing AS (
    SELECT
        customer_id,
        COUNT(*) AS overdue_count
    FROM billing
    WHERE payment_status = 'Overdue'
    GROUP BY customer_id
),

risk_base AS (
    SELECT
        c.customer_id,
        c.customer_name,
        c.customer_segment,
        c.industry,
        ct.contract_end_date,
        ct.monthly_fee,
        ct.previous_monthly_fee,
        COALESCE(u.total_support_tickets, 0) AS total_support_tickets,
        COALESCE(u.total_downtime_hours, 0) AS total_downtime_hours,
        COALESCE(b.overdue_count, 0) AS overdue_count,

        CASE
            WHEN ct.contract_end_date BETWEEN DATE '2026-05-11'
                AND DATE '2026-05-11' + INTERVAL '90 days'
            THEN 30 ELSE 0
        END AS contract_expiry_score,

        CASE
            WHEN COALESCE(b.overdue_count, 0) > 0
            THEN 20 ELSE 0
        END AS overdue_score,

        CASE
            WHEN COALESCE(u.total_support_tickets, 0) >= 10
            THEN 25 ELSE 0
        END AS support_ticket_score,

        CASE
            WHEN COALESCE(u.total_downtime_hours, 0) >= 8
            THEN 15 ELSE 0
        END AS downtime_score,

        CASE
            WHEN ((ct.monthly_fee - ct.previous_monthly_fee) / ct.previous_monthly_fee) > 0.08
            THEN 10 ELSE 0
        END AS price_increase_score

    FROM customers c
    JOIN contracts ct
        ON c.customer_id = ct.customer_id
    LEFT JOIN customer_usage u
        ON c.customer_id = u.customer_id
    LEFT JOIN customer_billing b
        ON c.customer_id = b.customer_id
)

SELECT
    customer_name,
    customer_segment,
    contract_end_date,
    total_support_tickets,
    total_downtime_hours,
    overdue_count,
    contract_expiry_score
    + overdue_score
    + support_ticket_score
    + downtime_score
    + price_increase_score AS churn_risk_score,

    CASE
        WHEN contract_expiry_score
            + overdue_score
            + support_ticket_score
            + downtime_score
            + price_increase_score >= 70
        THEN 'High Risk'

        WHEN contract_expiry_score
            + overdue_score
            + support_ticket_score
            + downtime_score
            + price_increase_score >= 40
        THEN 'Medium Risk'

        ELSE 'Low Risk'
    END AS churn_risk_level

FROM risk_base
ORDER BY churn_risk_score DESC;
```

### Result

| Customer Name | Segment | Contract End Date | Support Tickets | Downtime Hours | Overdue Count | Churn Risk Score | Risk Level |
|---|---|---|---:|---:|---:|---:|---|
| Brisbane Logistics Co | Enterprise | 2026-05-20 | 15 | 13.00 | 1 | 100 | High Risk |
| Hobart Food Supplies | SMB | 2026-06-10 | 18 | 13.20 | 3 | 100 | High Risk |
| Melbourne Health Partners | Enterprise | 2026-05-30 | 14 | 11.50 | 2 | 90 | High Risk |
| Adelaide Finance Solutions | Enterprise | 2026-05-25 | 9 | 7.30 | 1 | 60 | Medium Risk |
| Sydney Retail Hub | SMB | 2026-07-15 | 4 | 0.80 | 0 | 40 | Medium Risk |
| Adelaide Legal Group | Enterprise | 2026-06-30 | 4 | 1.80 | 0 | 40 | Medium Risk |
| Darwin Tourism Group | SMB | 2026-08-01 | 4 | 1.70 | 0 | 30 | Low Risk |
| VIC Accounting Services | SMB | 2026-06-25 | 0 | 0.00 | 0 | 30 | Low Risk |
| NSW Manufacturing Group | Enterprise | 2026-05-18 | 0 | 0.00 | 0 | 30 | Low Risk |
| Melbourne Design Studio | SMB | 2026-07-15 | 0 | 0.00 | 0 | 30 | Low Risk |

### Insight

Three customers are classified as high risk:

1. Brisbane Logistics Co
2. Hobart Food Supplies
3. Melbourne Health Partners

These customers have a combination of contract expiry risk, overdue payments, high support tickets, high downtime, and pricing changes. They should be prioritised for retention actions.

---

## 6. Sales Pipeline Health Analysis

### Business Question

Which sales pipeline stages have the highest expected revenue?

### SQL Code

```sql
SELECT
    pipeline_stage,
    COUNT(opportunity_id) AS number_of_opportunities,
    SUM(estimated_value) AS total_pipeline_value,
    ROUND(AVG(probability), 2) AS average_probability,
    ROUND(SUM(estimated_value * probability), 2) AS weighted_pipeline_value
FROM sales_pipeline
GROUP BY pipeline_stage
ORDER BY weighted_pipeline_value DESC;
```

### Result

| Pipeline Stage | Number of Opportunities | Total Pipeline Value | Average Probability | Weighted Pipeline Value |
|---|---:|---:|---:|---:|
| Negotiation | 3 | 76000.00 | 0.62 | 47100.00 |
| Proposal Sent | 3 | 43000.00 | 0.58 | 24850.00 |
| Qualified | 1 | 12000.00 | 0.45 | 5400.00 |
| Discovery | 2 | 14000.00 | 0.33 | 4600.00 |
| At Risk | 1 | 5000.00 | 0.20 | 1000.00 |

### Insight

The Negotiation stage has the highest weighted pipeline value at 47,100. Sales leaders should focus on progressing these opportunities because they have the strongest expected revenue impact.

---

## 7. Retention Campaign Performance Analysis

### Business Question

Which retention offers have the highest adoption and retention success rates?

### SQL Code

```sql
SELECT
    offer_type,
    COUNT(campaign_id) AS total_offers,
    SUM(accepted_flag) AS accepted_offers,
    ROUND(SUM(accepted_flag) * 100.0 / COUNT(campaign_id), 2) AS adoption_rate_percentage,
    SUM(retained_after_90_days) AS retained_customers,
    ROUND(SUM(retained_after_90_days) * 100.0 / COUNT(campaign_id), 2) AS retention_success_rate_percentage
FROM retention_campaigns
GROUP BY offer_type
ORDER BY adoption_rate_percentage DESC;
```

### Result

| Offer Type | Total Offers | Accepted Offers | Adoption Rate % | Retained Customers | Retention Success Rate % |
|---|---:|---:|---:|---:|---:|
| Contract Renewal Discount | 2 | 2 | 100.00 | 2 | 100.00 |
| Loyalty Discount | 2 | 1 | 50.00 | 1 | 50.00 |
| Price Reduction Offer | 2 | 1 | 50.00 | 1 | 50.00 |
| Service Upgrade Offer | 2 | 1 | 50.00 | 1 | 50.00 |

### Insight

Contract Renewal Discount performed best, with 100% adoption and 100% retention success. This offer type should be tested further with customers whose contracts are expiring soon.

---

## 8. Revenue by Customer Segment

### Business Question

Which customer segment generates the highest revenue?

### SQL Code

```sql
SELECT
    c.customer_segment,
    COUNT(DISTINCT c.customer_id) AS number_of_customers,
    SUM(b.monthly_charge - b.discount_amount) AS net_revenue,
    ROUND(AVG(b.monthly_charge - b.discount_amount), 2) AS avg_monthly_revenue
FROM customers c
JOIN billing b
    ON c.customer_id = b.customer_id
GROUP BY c.customer_segment
ORDER BY net_revenue DESC;
```

### Result

| Customer Segment | Number of Customers | Net Revenue | Average Monthly Revenue |
|---|---:|---:|---:|
| Enterprise | 5 | 27900.00 | 1860.00 |
| Mid-Market | 2 | 6450.00 | 1075.00 |
| SMB | 3 | 4050.00 | 450.00 |

### Insight

Enterprise customers generate the highest total revenue. Retention efforts should prioritise enterprise accounts with upcoming contract expiries and high service issue levels.

---

# Results & Business Recommendations

## Key Results

1. 11 customers have contracts expiring within the next 90 days.
2. 3 customers were identified as high churn risk.
3. Brisbane Logistics Co, Hobart Food Supplies and Melbourne Health Partners had the highest churn risk scores.
4. Negotiation stage opportunities had the highest weighted pipeline value.
5. Contract Renewal Discount was the strongest retention campaign type.
6. Enterprise customers generated the highest net revenue.

## Business Recommendations

### 1. Prioritise high-risk customers

The account management team should contact Brisbane Logistics Co, Hobart Food Supplies and Melbourne Health Partners first. These customers have strong churn risk signals, including contract expiry, overdue payments, high support tickets and service downtime.

### 2. Focus on enterprise renewals

Enterprise customers generate the largest revenue contribution. Customers such as Brisbane Logistics Co, Melbourne Health Partners and Adelaide Finance Solutions should receive proactive renewal conversations before contract expiry.

### 3. Improve service experience for high-risk customers

High support tickets and downtime are key churn signals. Technical teams should review service issues for customers with repeated support tickets and downtime before renewal discussions.

### 4. Use contract renewal discounts more strategically

Contract Renewal Discount had the strongest adoption and retention success rate in this dataset. This offer should be prioritised for customers with high monthly fees and upcoming contract expiry.

### 5. Monitor weighted pipeline value

Sales leaders should focus on opportunities in the Negotiation and Proposal Sent stages because they have the highest expected revenue value.

---

# Next Steps

1. Add more historical data to improve churn risk analysis.
2. Add a churn_status table to compare predicted churn risk with actual churn outcomes.
3. Create customer-level churn trend analysis by month.
4. Build stored views for repeated reporting queries.
5. Connect SQL output to Power BI for dashboard development.
6. Add customer satisfaction data or NPS scores to improve churn prediction.
7. Add campaign cost and revenue protected to calculate more accurate ROI.

---

# Project Summary

This SQL project demonstrates how customer, contract, billing, usage, sales pipeline and retention campaign data can be connected and analysed to support customer retention and sales reporting. The project shows practical SQL skills while also producing business insights that can help account managers and sales leaders identify high-risk customers, protect revenue and improve retention outcomes.

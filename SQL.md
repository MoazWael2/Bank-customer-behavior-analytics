# Credit Card Spending Analysis Queries (My SQL)

### Introduction
Welcome to the Credit Card Spending Analysis Queries repository. This collection of SQL queries has been crafted to provide a high-level overview of spending patterns among credit card customers. Our goal is to enable a data-driven approach to enhance credit card feature recommendations and tailor services to customer needs.The dataset includes transactions made by a vast number of customers over various months and across different spending categories. These transactions reflect the diverse ways customers engage with their credit cards, whether for groceries, healthcare, or other daily expenditures.

### Data Overview
The datasets provided contain detailed records of credit card transactions and customer demographics, structured with the following key fields:
#### Transaction Data:
* customer_id: A unique identifier for each customer.
* month: The month in which the transaction occurred.
* category: The category of the expenditure (e.g., Groceries, Health).
* spend: The amount spent in the transaction.

#### Customer Data:
* customer_id: A unique identifier for each customer, linking to the transaction data.
* age_group: The age group the customer belongs to (e.g., 21-24, 25-34, 45+).
* city: The city of the customer's residence.
* occupation: The customer's occupation, which may influence spending habits.
* gender: The customer's gender.
* marital status: The marital status of the customer, which may be used for targeted marketing.
* avg_income: The average income of the customer, which could correlate with spending behavior.

### Analysis Strategy:
Our data-driven approach to understanding credit card spending behavior is segmented into three focused parts, each with a set of targeted questions designed to unlock valuable insights for business strategy and feature development.

#### Part 1: Spending Behavior Overview:
We begin with an expansive view of general spending patterns. This lays the groundwork for identifying overarching trends and potential areas of focus.
###### Key questions include:
* What is the average monthly spending per customer AND Total Spending?
* Which spending categories are most popular among all customers?
* How do spending patterns fluctuate seasonally?
* Are there significant differences in spending between payment methods (e.g., credit card vs. debit card)?
* What is the distribution of transaction sizes across the customer base?
  
#### Part 2: In-Depth Customer Behavior Analysis:
Next, we explore the customer profiles in detail to understand the underlying factors influencing spending behavior.

###### Key questions include:
* How do spending habits vary across different age groups?
* Is there a correlation between the customers' occupations and their spending categories?
* Does marital status influence the frequency or amount of credit card spending?
* Are higher-income customers more likely to engage in certain types of transactions?
* What is the retention rate of customers, and how does it relate to their spending habits?
* Which customer segments demonstrate the highest loyalty or rewards program engagement?
  
#### Part 3: Geographic Spending Behavior Analysis:
Lastly, we assess the geographic distribution of spending, identifying regional trends and local factors that may affect credit card usage.
###### Key questions include:
* Which cities show the highest credit card spending, and in which categories?
* Are there unique spending patterns identifiable in rural versus urban areas?
* How does the economic profile of a city relate to the spending patterns of its residents?
* What regional factors (e.g., local events, economic indicators) might influence spending?
* Can we identify potential for market growth or product expansion based on geographic spending data?
* How does city-wise merchant diversity impact spending behavior?
###### By thoroughly addressing each set of questions, we'll develop a nuanced understanding of our customers' spending behaviors. This will enable us to tailor credit card features and marketing initiatives that resonate with customer needs and preferences, aligned with regional characteristics and opportunities.


# Part 1: Spending Behavior
### What is the average monthly spending per customer AND Total Spending?
##### SQL
```SQL
-- What is the average monthly spending per customer AND Total Spending?
SELECT 
  month, -- The month in which transactions occurred
  ROUND(SUM(spend)/COUNT(DISTINCT customer_id), 2) AS Avg_spending_Per_Customer, -- Average spending per customer for the month
  SUM(spend) AS Total_Spending -- Total spending for the month
FROM 
  fact_spends -- Replace with your actual table name containing spend data
GROUP BY 
  month -- Grouping results by month to calculate monthly spending patterns
ORDER BY 
  Total_Spending DESC;
```
##### Result
![By months](https://github.com/MoazWael2/Bank-customer-behavior-analytics/assets/137816418/c378e0a9-a08b-49bd-ac15-17e89b65c7fd)

##### Findings
* The data visualization reflects customer spending patterns by month. There is a notable peak in spending during September and August, with customer spending reaching $36.3M and $31.5M respectively, accompanied by a higher average spend per customer ($9.1K in September and $7.9K in August). Conversely, June and May exhibit the lowest spending months, with total spends of $24.8M and $21.4M, and average spending per customer at $6.2K and $5.3K respectively.
* This suggests that customer spending is significantly more robust in the late summer months, potentially due to seasonal factors, while there is a dip in spending as we move into mid-year.

### Which spending categories are most popular among all customers?
##### SQL
```
 SELECT 
  category, -- The category of the expenditure
  ROUND(SUM(spend)/COUNT(DISTINCT customer_id), 2) AS Avg_spending_Per_Customer, -- Average spending per customer in the category
  SUM(spend) AS Total_Spending -- Total spending in the category
FROM 
  fact_spends -- Replace with your actual table name containing spending data
GROUP BY 
  category -- Grouping results by spending category
ORDER BY 
  Total_Spending DESC; -- Sorting categories by total spending in descending order
```
##### Result
![Catgor](https://github.com/MoazWael2/Bank-customer-behavior-analytics/assets/137816418/d419f9fe-28b7-44b0-abf2-784f9139633e)

##### Findings
* spending by category has revealed that 'Bills' and 'Groceries' are the most significant areas of expenditure for customers, with 'Bills' reaching the highest total spending at $32.6M, averaging $8.1K per customer, followed by 'Groceries' with a total spend of $27.0M and an average of $6.8K per customer. This pattern suggests a strategic opportunity for credit card features that incentivize essential spending, such as enhanced rewards for bill payments and grocery purchases, which could drive increased card usage and customer loyalty.

### How do spending patterns fluctuate seasonally?
##### SQL
```SQL
-- Seasonal Spending Patterns Analysis
WITH MonthlyCategorySpending AS ( 
  SELECT
    category,
    month,
    SUM(spend) AS Total_Spending
  FROM fact_spends
  GROUP BY
    category,
    month
)
SELECT 
  RANK() OVER(PARTITION BY month ORDER BY Total_Spending DESC, category) AS Rank_,
  month,
  category,
  Total_Spending
FROM MonthlyCategorySpending
ORDER BY
  month,
  Rank_;
```
##### Result
![05 01 2024_12 08 48_REC](https://github.com/MoazWael2/Bank-customer-behavior-analytics/assets/137816418/be868623-3440-46d0-8382-29abd6f8c916)

##### Finding 
* analysis of monthly spending by category demonstrates a consistent trend where 'Bills' category dominates across all months. Notably, a significant increase in 'Travel' spending is observed from August to September, aligning with the summer season, which typically sees a surge in travel activity. This seasonal uptick suggests opportunities for targeted travel-related promotions and partnerships during these peak months to capitalize on the increased spending behavior.

### Are there significant differences in spending between payment methods ? 
##### SQL
```SQL
SELECT 
  payment_type, -- The method of payment used
  SUM(spend) AS Total_spending -- The total spending for each payment method
FROM 
  fact_spends -- Replace with your actual table name containing spend data
GROUP BY 
  payment_type; -- Grouping results by payment method
```
##### Result
![05 01 2024_14 41 07_REC](https://github.com/MoazWael2/Bank-customer-behavior-analytics/assets/137816418/24caef9f-149c-4094-8515-63908a758a93)

##### Finding 
* The analysis of the payment methods used for transactions reveals a clear preference among customers, with credit cards being the predominant choice, accounting for 40.54% of total spending. This preference indicates a significant reliance on credit cards over other payment forms like UPI (Unified Payments Interface), Debit Cards, and Net Banking. This trend suggests a strong potential for targeted credit card promotions and loyalty programs to further encourage and reward credit card usage

# Part 2: In-Depth Customer Behavior Analysis

### 1- How do spending habits vary across different age groups?
##### SQL
```SQL
SELECT 
  C.age_group, -- The age group of the customer
  MIN(F.spend) AS Min_Spend, -- The smallest transaction amount in the age group
  AVG(F.spend) AS AVG_SPEND, -- The average transaction amount in the age group
  MAX(F.spend) AS MAX_SPEND, -- The largest transaction amount in the age group
  ROUND(AVG(F.spend / C.avg_income) * 100, 2) AS Avg_income_utilisation -- The average spend as a percentage of average income
FROM 
  dim_customers AS C -- Customer demographics table
INNER JOIN 
  fact_spends AS F ON C.customer_id = F.customer_id -- Transaction data table
GROUP BY 
  C.age_group -- Grouping data by customer age group
ORDER BY 
  C.age_group; -- Sorting the results by age group
```
##### Result 
![Income with ages](https://github.com/MoazWael2/Bank-customer-behavior-analytics/assets/137816418/b88f54f1-e6e8-4480-8151-c519cfb5f44d)

##### Finding 
* An insightful pattern emerges when analyzing the average income utilization in relation to age groups. Individuals aged 35-45 show the highest income utilization at 29%, suggesting a greater propensity to spend relative to their income. This is closely followed by the 25-34 age bracket at 22% and the 21-24 group at 13%. Notably, the age group of 45+ appears to have a more stable financial footing, with a lower and more conservative income utilization.

* This trend indicates that the 35-45 age group may be more receptive to credit card offers and could potentially benefit from tailored financial products. On the other hand, the 45+ demographic exhibits spending habits that suggest financial stability and possibly a higher ability to spend without relying on credit.

### 2- Are higher-income customers more likely to engage in certain types of transactions?
##### SQL
```SQL
SELECT 
  C.occupation, -- Occupation of the customer
  F.category, -- Spending category
  AVG(F.spend) AS AVG_SPEND, -- Average spend amount within the category for the occupation
  MAX(F.spend) AS MAX_SPEND, -- Maximum spend amount within the category for the occupation
  SUM(F.spend) AS Total_Spend -- Total spend amount within the category for the occupation
FROM 
  dim_customers AS C -- Customers dimension table with demographics
INNER JOIN 
  fact_spends AS F ON C.customer_id = F.customer_id -- Transaction data table
GROUP BY 
  C.occupation, F.category -- Grouping data by occupation and category
ORDER BY 
  C.occupation, Total_Spend DESC; -- Sorting the results by occupation and total spend
```
### Result
![05 01 2024_20 59 58_REC](https://github.com/MoazWael2/Bank-customer-behavior-analytics/assets/137816418/5f96d986-a8ca-44fa-bc9c-8064d658c5ec)

### Finding 
* A review of the spending data across different occupations reveals a common pattern: most occupational groups tend to spend the most on bills. This consistency suggests that there is no strong correlation between occupation type and spending behavior in different categories, at least for essential services and needs. While there are peaks in spending within categories such as electronics and apparel, these do not appear to be significantly influenced by the occupation of the customers.

### 3- Correlation Analysis Between Income Level and Transaction Types?
##### SQL
```SQL
SELECT 
  CASE 
    WHEN C.avg_income BETWEEN 24000 AND 52000 THEN '24K - 52K' 
    WHEN C.avg_income BETWEEN 52001 AND 66000 THEN '52K - 66K' 
    WHEN C.avg_income > 66000 THEN '66K+' 
  END AS Income_classification, -- Income classification based on average income
  F.payment_type, -- The type of payment used in transactions
  COUNT(F.payment_type) AS Total_used, -- Total count of each payment type used
  SUM(F.spend) AS Total_spend -- Total spend for each payment type
FROM 
  fact_spends AS F -- Transaction data table
INNER JOIN 
  dim_customers AS C ON C.customer_id = F.customer_id -- Customer demographics table
GROUP BY 
  Income_classification, F.payment_type -- Grouping results by income classification and payment type
ORDER BY 
  Income_classification, Total_spend DESC; -- Ordering results by income classification and total spend
```
##### Result
![05 01 2024_21 33 29_REC](https://github.com/MoazWael2/Bank-customer-behavior-analytics/assets/137816418/5a232553-6d14-4d34-a827-b5f75baddd01)

##### Finding 
* An analysis of transaction types across different income brackets reveals a counterintuitive trend: higher-income customers (66K - 87K range) appear to use credit cards less frequently than other payment methods, with credit card usage ranking lowest in their payment preferences. This contrasts with the commonly held view that higher-income individuals might prefer credit cards due to potential benefits like rewards or credit points.





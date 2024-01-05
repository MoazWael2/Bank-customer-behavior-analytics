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

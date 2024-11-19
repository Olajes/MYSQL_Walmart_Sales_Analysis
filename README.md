# MYSQL_Walmart_Sales_Analysis

## About
This project aims to explore Walmart sales data to understand top-performing branches and products, sales trends of different products, and customer behavior. The goal is to study how sales strategies can be improved and optimized. The dataset was obtained from the Kaggle Walmart Sales Forecasting Competition.

# Walmart Sales Data Analysis

> "In this recruiting competition, job-seekers are provided with historical sales data for 45 Walmart stores located in different regions. Each store contains many departments, and participants must project the sales for each department in each store. To add to the challenge, selected holiday markdown events are included in the dataset. These markdowns are known to affect sales, but it is challenging to predict which departments are affected and the extent of the impact."  
> *Source: Kaggle*

---

## Objectives
The primary objective is to analyze Walmart sales data to gain insights into the factors affecting sales across different branches. This analysis includes:
- Identifying top-performing products and branches.
- Understanding sales trends and customer behavior.
- Optimizing sales strategies for better performance.

---

## Dataset
The dataset contains transactions from three Walmart branches located in Mandalay, Yangon, and Naypyitaw. It includes 17 columns and 1000 rows:

| Column                  | Description                                  | Data Type       |
|-------------------------|----------------------------------------------|-----------------|
| `invoice_id`            | Invoice ID of the sales made                | VARCHAR(30)     |
| `branch`                | Branch at which sales were made             | VARCHAR(5)      |
| `city`                  | The location of the branch                  | VARCHAR(30)     |
| `customer_type`         | Type of the customer                        | VARCHAR(30)     |
| `gender`                | Gender of the customer making purchase      | VARCHAR(10)     |
| `product_line`          | Product line of the product sold            | VARCHAR(100)    |
| `unit_price`            | Price of each product                       | DECIMAL(10, 2)  |
| `quantity`              | Number of units sold                        | INT             |
| `VAT`                   | Tax on the purchase                         | FLOAT           |
| `total`                 | Total cost of the purchase                  | DECIMAL(10, 2)  |
| `date`                  | Date of the transaction                     | DATE            |
| `time`                  | Time of the transaction                     | TIME            |
| `payment_method`        | Method used for payment                     | VARCHAR(30)     |
| `cogs`                  | Cost of goods sold                          | DECIMAL(10, 2)  |
| `gross_margin_percentage`| Gross margin percentage                    | FLOAT           |
| `gross_income`          | Gross income                                | DECIMAL(10, 2)  |
| `rating`                | Customer rating                             | FLOAT           |

---

## Analysis Areas

1. **Product Analysis**  
   - Identify top-performing and underperforming product lines.
   - Understand product sales trends for optimization.
   
2. **Sales Analysis**  
   - Analyze sales trends and the effectiveness of current strategies.
   - Recommend modifications to boost sales performance.

3. **Customer Analysis**  
   - Explore customer segmentation and purchasing behaviors.
   - Assess profitability by customer types.

---

## Methodology

### 1. Data Wrangling
- Inspection of the dataset for NULL and missing values.
- Replacing missing data using appropriate methods.
  
### 2. Feature Engineering
- Adding columns like `time_of_day`, `day_name`, and `month_name` to better understand sales patterns.
  ```sql
	UPDATE sales
	SET time_of_day = ( CASE
			WHEN `time` BETWEEN "00:00:00" AND "12:00:00" THEN "Morning"
			WHEN `time` BETWEEN "12:01:00" AND "16:00:00" THEN "Afternoon"
			ELSE "Evening"
	    END );
	  ```
- Adding columns `day_name`
```sql
UPDATE sales
SET day_name = DAYNAME(`date`);
```
  
- Adding columns `month_name`
```sql
SELECT
`date`,
monthname(`date`) AS month_name
FROM sales;

ALTER TABLE sales ADD COLUMN month_name VARCHAR(20) AFTER `date`;

UPDATE sales
SET month_name = monthname(`date`);
```
---
### 3. Exploratory Data Analysis (EDA)
- Conduct in-depth analysis to answer business questions.

### 4. Conclusion and Recommendations
- Provide actionable insights based on the data.

---

## Business Questions Addressed

### Generic
1. How many unique cities are in the data?
2. In which city is each branch located?

### Product
1. How many unique product lines exist?
2. What is the most common payment method?
3. Which product line generates the most revenue?

### Sales
1. What are the sales trends by time of day and day of the week?
2. Which customer type brings the most revenue?

### Customer
1. What is the gender distribution of customers?
2. Which day of the week has the highest ratings per branch?

---

## Revenue and Profit Calculations

**Formulas:**
- **COGS:** `unit_price * quantity`  
- **VAT:** `5% * COGS`  
- **Total (Gross Sales):** `VAT + COGS`  
- **Gross Profit (Income):** `Total - COGS`  
- **Gross Margin Percentage:** `(Gross Income / Total Revenue) * 100`

**Example Calculation:**
For the first row:  
- Unit Price = $45.79, Quantity = 7  
- COGS = $45.79 * 7 = $320.53  
- VAT = 5% * $320.53 = $16.03  
- Total = $320.53 + $16.03 = $336.56  
- Gross Margin = ($16.03 / $336.56) ≈ 4.76%

---

## Recommendations
1. **Targeted Marketing Strategies:** Focus on gender-specific product preferences.
2. **Inventory Optimization:** Improve product availability for high-demand categories.
3. **Promotions:** Schedule discounts during peak sales periods to maximize profits.
4. **Customer Loyalty Programs:** Convert normal customers into members with incentives.

---

## Conclusion
This project provides valuable insights into Walmart's sales data, helping to improve sales strategies and enhance customer experience.

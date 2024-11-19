# MYSQL_Walmart_Sales_Analysis

## About
This project aims to explore Walmart sales data to understand top-performing branches and products, sales trends of different products, and customer behavior. The goal is to study how sales strategies can be improved and optimized. The dataset was obtained from the Kaggle Walmart Sales Forecasting Competition.

# Walmart Sales Data Analysis

> "In this recruiting competition, job-seekers are provided with historical sales data for 45 Walmart stores located in different regions. Each store contains many departments, and participants must project the sales for each department in each store. To add to the challenge, selected holiday markdown events are included in the dataset. These markdowns are known to affect sales, but it is challenging to predict which departments are affected and the extent of the impact."  


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
	SELECT `date`,
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
```sql
	SELECT 
		DISTINCT city
	    -- COUNT(DISTINCT city)
	FROM sales;
```

2. In which city is each branch located?
   
```sql
	SELECT 
		DISTINCT city,
	     	branch
	FROM sales;
```
---
### Product
1. How many unique product lines exist?
   ```sql
	   SELECT
		DISTINCT product_line
		-- COUNT(DISTINCT product_line)
	   FROM sales;
   ```
2. What is the most common payment method?
   ```sql
	 SELECT
		payment_method,
		COUNT(payment_method) AS ctpm
	FROM sales
	GROUP BY payment_method
	ORDER BY ctpm DESC;   
   ```

3. What is the most selling product line?
 ```sql

	SELECT
		product_line,
		COUNT(product_line) ctpl
	FROM sales
	GROUP BY product_line
	ORDER BY ctpl DESC;
	-- LIMIT 1
```
4. What is the total revenue by month?
   
 ```sql
	SELECT 
		month_name,
	    	SUM(total) total_revenue
	FROM sales
	GROUP BY month_name
	ORDER BY total_revenue DESC;
```

5. What month had the largest COGS?
    
```sql
	SELECT 
		month_name,
	    SUM(cogs) cogs
	FROM sales
	GROUP BY month_name
	ORDER BY cogs DESC;  
	-- LIMIT 1
```

6. Which product line generates the most revenue?
   ```sql
	SELECT 
		product_line,
	    	SUM(total) total_revenue
	FROM sales
	GROUP BY product_line
	ORDER BY total_revenue DESC; 
	-- LIMIT 1 
   ```
7. What is the city with the largest revenue?  
   ```sql
	 SELECT 
		city,
		SUM(total) total_revenue
	FROM sales
	GROUP BY city
	ORDER BY total_revenue DESC; 
-- LIMIT 1 
  ```
8. What product line had the largest VAT?   
```sql
	SELECT 
	product_line,
	SUM(VAT) VAT
	FROM sales
	GROUP BY product_line
	ORDER BY VAT DESC; 
	-- LIMIT 1
```
9. Fetch each product line and add a column to those product line showing "Good","Bad" . Good if its greater than average sales
```sql
	SELECT 
	    product_line,
	    total,
	    CASE 
	        WHEN total > (SELECT AVG(total) FROM sales) THEN 'Good'
	        ELSE 'Bad'
	    END AS performance
	FROM sales;
```
10. Which branch sold more products than average product sold?

```sql
	SELECT 
		branch,
	    SUM(quantity) qty
	FROM sales
	GROUP BY branch
	HAVING SUM(quantity) > (SELECT AVG(quantity) FROM sales);
```
11. What is the most common product line by gender?

```sql
	SELECT 
		product_line,
	    gender,
	    COUNT(gender) AS total_cnt 
	FROM sales
	GROUP BY gender,product_line
	ORDER BY total_cnt DESC; 
```

12. What is the average rating of each product line?
```sql
	SELECT 
		product_line,
	    AVG(rating) AS avg_rating 
	FROM sales
	GROUP BY product_line
	ORDER BY avg_rating DESC;
```

### Sales
1. What are the sales trends by time of day and day of the week?
   ```sql
	SELECT
	time_of_day,
	COUNT(*) AS total_sales
	FROM sales
	GROUP BY time_of_day
	ORDER BY total_sales DESC;
   ```
2. Which customer type brings the most revenue?
   ```sql
	SELECT
	customer_type,
	SUM(total) AS total_revenue
	FROM sales
	GROUP BY customer_type
	ORDER BY total_revenue DESC;
   ```
3. Which city has the largest tax percent/VAT (Value Added Tax)?
   ```sql
		SELECT
		city,
		SUM(VAT) AS VAT
		FROM sales
		GROUP BY city
		ORDER BY VAT DESC;
   ```

4. Which customer type pays the most in VAT
   ```sql
	SELECT
	customer_type,
	SUM(VAT) AS VAT
	FROM sales
	GROUP BY customer_type
	ORDER BY VAT DESC;
   ```

   
### Customer
1. What is the gender distribution of customers?
   ```sql
   	SELECT
	DISTINCT customer_type
	-- COUNT(customer_type)
	FROM sales;
   ```
2. Which day of the week has the highest ratings per branch?
   ```sql
	SELECT
	DISTINCT payment_method
	-- COUNT(payment_method)
	FROM sales;
   ```
3. What is the most common customer type?
   
  ```sql
	SELECT
	customer_type,
	COUNT(customer_type) cus_type
	FROM sales
	GROUP BY customer_type
	ORDER BY cus_type DESC
	LIMIT 1;
  ```

4. Which customer type buys the most?
   ```sql
	SELECT 
	customer_type,
	COUNT(customer_type) cus_type
	FROM sales
	GROUP BY customer_type
	ORDER BY cus_type DESC
	LIMIT 1;
   ```

5. What is the gender of most of the customers?
  ```sql
	SELECT
	gender,
	COUNT(gender) cut_gender
	FROM sales
	GROUP BY gender
	ORDER BY cut_gender DESC
	LIMIT 1;
   ```
6. What is the gender distribution per branch?
   ```sql
	SELECT
	gender,
	branch,
	COUNT(*) cnt
	FROM sales
	GROUP BY branch,gender
	ORDER BY branch,gender DESC;
  ```
7. Which time of the day do customers give most ratings?
   ```sql
	SELECT
	time_of_day,
	ROUND(AVG(rating),4) AS avg_rating
	FROM sales
	GROUP BY time_of_day
	ORDER BY avg_rating DESC;
   ```

8. Which time of the day do customers give most ratings per branch?
  ```sql
	SELECT
	time_of_day,
	branch,
	COUNT(rating) cnt
	FROM sales
	GROUP BY time_of_day,branch
	ORDER BY cnt DESC;
   ```
9. Which day fo the week has the best avg ratings?
   ```sql
	SELECT
	time_of_day,
	branch,
	COUNT(rating) cnt
	FROM sales
	GROUP BY time_of_day,branch
	ORDER BY cnt DESC;
  ```
10. Which day of the week has the best average ratings per branch?

```sql
SELECT
    day_name,
    # branch,
    AVG(rating) AS avg_rating
FROM sales
GROUP BY day_name
ORDER BY avg_rating DESC;
```
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

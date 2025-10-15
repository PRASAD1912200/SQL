Aggregation & Grouping
/*---------------------------------------------------
 Find the total, average, minimum, and maximum credit limit of all customers
---------------------------------------------------*/
SELECT 
    SUM(credit_limit) AS total_credit_limit,
    AVG(credit_limit) AS avg_credit_limit,
    MIN(credit_limit) AS min_credit_limit,
    MAX(credit_limit) AS max_credit_limit
FROM customers;


/*---------------------------------------------------
Count the number of customers in each income level
---------------------------------------------------*/
SELECT 
    income_level,
    COUNT(*) AS customer_count
FROM customers
GROUP BY income_level;


/*---------------------------------------------------
3️Show total credit limit by state and country
---------------------------------------------------*/
SELECT 
    country,
    state,
    SUM(credit_limit) AS total_credit_limit
FROM customers
GROUP BY country, state;


/*---------------------------------------------------
4️ Display average credit limit for each marital status and gender combination
---------------------------------------------------*/
SELECT 
    marital_status,
    gender,
    AVG(credit_limit) AS avg_credit_limit
FROM customers
GROUP BY marital_status, gender;


/*---------------------------------------------------
5️ Find the top 3 states with the highest average credit limit
---------------------------------------------------*/
SELECT 
    state,
    AVG(credit_limit) AS avg_credit_limit
FROM customers
GROUP BY state
ORDER BY avg_credit_limit DESC
FETCH FIRST 3 ROWS ONLY;  -- Use LIMIT 3 for MySQL


/*---------------------------------------------------
6️ Find the country with the maximum total customer credit limit
---------------------------------------------------*/
SELECT 
    country,
    SUM(credit_limit) AS total_credit_limit
FROM customers
GROUP BY country
ORDER BY total_credit_limit DESC
FETCH FIRST 1 ROW ONLY;


/*---------------------------------------------------
7️ Show the number of customers whose credit limit exceeds their state average
---------------------------------------------------*/
SELECT COUNT(*) AS above_state_avg
FROM customers c
WHERE credit_limit > (
    SELECT AVG(credit_limit)
    FROM customers s
    WHERE s.state = c.state
);


/*---------------------------------------------------
8️ Calculate total and average credit limit for customers born after 1980
---------------------------------------------------*/
SELECT 
    SUM(credit_limit) AS total_credit_limit,
    AVG(credit_limit) AS avg_credit_limit
FROM customers
WHERE EXTRACT(YEAR FROM date_of_birth) > 1980;


/*---------------------------------------------------
9️ Find states having more than 50 customers
---------------------------------------------------*/
SELECT 
    state,
    COUNT(*) AS customer_count
FROM customers
GROUP BY state
HAVING COUNT(*) > 50;


/*---------------------------------------------------
10  List countries where the average credit limit is higher than the global average
---------------------------------------------------*/
SELECT 
    country,
    AVG(credit_limit) AS avg_credit_limit
FROM customers
GROUP BY country
HAVING AVG(credit_limit) > (
    SELECT AVG(credit_limit) FROM customers
);


/*---------------------------------------------------
11️ Calculate the variance and standard deviation of customer credit limits by country
---------------------------------------------------*/
SELECT 
    country,
    VARIANCE(credit_limit) AS variance_credit_limit,
    STDDEV(credit_limit) AS stddev_credit_limit
FROM customers
GROUP BY country;


/*---------------------------------------------------
12️ Find the state with the smallest range (max–min) in credit limits
---------------------------------------------------*/
SELECT 
    state,
    (MAX(credit_limit) - MIN(credit_limit)) AS range_credit
FROM customers
GROUP BY state
ORDER BY range_credit ASC
FETCH FIRST 1 ROW ONLY;


/*---------------------------------------------------
13️ Show total number of customers per income level and percentage contribution of each
---------------------------------------------------*/
SELECT 
    income_level,
    COUNT(*) AS total_customers,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage_contribution
FROM customers
GROUP BY income_level;


/*---------------------------------------------------
14️ For each income level, find how many customers have NULL credit limits
---------------------------------------------------*/
SELECT 
    income_level,
    COUNT(*) AS null_credit_count
FROM customers
WHERE credit_limit IS NULL
GROUP BY income_level;


/*---------------------------------------------------
15️ Display countries where the sum of credit limits exceeds 10 million
---------------------------------------------------*/
SELECT 
    country,
    SUM(credit_limit) AS total_credit_limit
FROM customers
GROUP BY country
HAVING SUM(credit_limit) > 10000000;


/*---------------------------------------------------
16️ Find the state that contributes the highest total credit limit to its country
---------------------------------------------------*/
SELECT country, state, total_credit_limit
FROM (
    SELECT 
        country,
        state,
        SUM(credit_limit) AS total_credit_limit,
        RANK() OVER (PARTITION BY country ORDER BY SUM(credit_limit) DESC) AS rnk
    FROM customers
    GROUP BY country, state
) ranked
WHERE rnk = 1;


/*---------------------------------------------------
17️ Show total credit limit per year of birth, sorted by total descending
---------------------------------------------------*/
SELECT 
    EXTRACT(YEAR FROM date_of_birth) AS year_of_birth,
    SUM(credit_limit) AS total_credit_limit
FROM customers
GROUP BY EXTRACT(YEAR FROM date_of_birth)
ORDER BY total_credit_limit DESC;


/*---------------------------------------------------
18️ Identify customers who hold the maximum credit limit in their respective country
---------------------------------------------------*/
SELECT 
    customer_id,
    first_name,
    last_name,
    country,
    credit_limit
FROM customers c
WHERE credit_limit = (
    SELECT MAX(credit_limit)
    FROM customers s
    WHERE s.country = c.country
);


/*---------------------------------------------------
19️ Show the difference between maximum and average credit limit per country
---------------------------------------------------*/
SELECT 
    country,
    MAX(credit_limit) - AVG(credit_limit) AS diff_max_avg
FROM customers
GROUP BY country;


/*---------------------------------------------------
20️ Display the overall rank of each state based on its total credit limit
---------------------------------------------------*/
SELECT 
    state,
    SUM(credit_limit) AS total_credit_limit,
    RANK() OVER (ORDER BY SUM(credit_limit) DESC) AS rank_by_credit
FROM customers
GROUP BY state;

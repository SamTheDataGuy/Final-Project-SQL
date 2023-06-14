Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

    /* 
    The values in the totaltransaction column of the transactions table (created from all_sessions table) don't really make sense
    and there are plenty of null values, so I calculated revenue as productquanity times productprice
    */

    -- Top 5 countries with highest revenue
    select country, sum(productquantity*productprice) as total_revenue
    from transactions t
    join visits v on t.visitid = v.visitid
    group by country
    order by sum(productquantity*productprice) desc
    limit 5;

    -- Top 5 cities with highest revenue
    select city || ', ' || country, sum(productquantity*productprice) as total_revenue
    from transactions t
    join visits v on t.visitid = v.visitid
    where city is not null
    group by city, country
    order by sum(productquantity*productprice) desc
    limit 5;



Answer: 

Top 5 countries: "United States", "Canada", "Australia", "Argentina", "Ireland"

Top 5 cities "Mountain View, United States", "New York, United States", "San Francisco, United States", "Salem, United States", "San Jose, United States"





**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:




Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:








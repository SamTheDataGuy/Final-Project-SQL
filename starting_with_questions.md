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

`Top 5 countries: "United States", "Canada", "Australia", "Argentina", "Ireland"`

`Top 5 cities "Mountain View, United States", "New York, United States", "San Francisco, United States", "Salem, United States", "San Jose, United States"`





**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

    -- Finding the average products ordered per visit (not the average number of products on an order)
    select city || ', ' || country, 
    cast(sum(productquantity) as numeric)/cast(count(distinct v.visitid) as numeric(10,2)) as average_products_ordered
    from visits v
    left join transactions t on v.visitid = t.visitid
    group by country, city
    having  cast(sum(productquantity) as numeric)/cast(count(distinct v.visitid) as numeric(10,2)) is not null
    and city is not null
    order by average_products_ordered desc




Answer:

`Top 5 cities, average number of products ordered per visit: "Nashville, United States, 1.00", "Madrid, Spain,	0.53", "Columbus, United States, 0.50", "Detroit, United States, 0.25", "Salem, United States, 0.16"`







**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

    -- By country and category
    select country, v2productcategory, count(*)
    from transactions t
    left join visits v on t.visitid = v.visitid
    left join products p on t.productsku = p.sku
    group by country, v2productcategory
    order by country, count(*) desc

    -- By country, city, and category
    select v2productcategory, city, country , count(*)
    from transactions t
    left join visits v on t.visitid = v.visitid
    left join products p on t.productsku = p.sku
    group by v2productcategory, country, city
    order by v2productcategory, count(*) desc, city, country


Answer:

`When grouping by country and category, the most popular categories in the US are "Nest-USA", "Home/Shop by Brand/Google/", and "Home/Shop by Brand/Android/".  No other patterns are apparent as there is not enough data, and only 1 order in each category for the other countries.`

`When grouping by city, the most popular city for most categories is located in the US.`





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

    -- Group by countries
    select * from
        (select country, v2productname, sum(productquantity) as total_units_sold,
        rank() over(partition by country order by sum(productquantity) desc) as rank
        from transactions t
        left join visits v on t.visitid = v.visitid
        left join products p on t.productsku = p.sku
        where name is not null
        group by country, v2productname
        order by country, total_units_sold desc) as temp
    where rank = 1

    -- Group by cities
    select * from
        (select country, city, v2productname, sum(productquantity) as total_units_sold,
        rank() over(partition by country, city order by sum(productquantity) desc) as rank
        from transactions t
        left join visits v on t.visitid = v.visitid
        left join products p on t.productsku = p.sku
        where name is not null
        and city is not null
        group by country, city, v2productname
        order by country, city, total_units_sold desc) as temp
    where rank = 1

Answer: When looking at countries, Customers from Spain love Waze Dress Socks, and customers from the US love Leatherette Journals.  When looking at cities it's difficult to draw conclusions as the order quantities are so small and spread out throughout the cities, but the Spanish Waze Dress Socks all live in Madrid, and in Salem, US customers are obsessed with their Red Spiral Google Notebooks.





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

    -- Group by countries
    select country, sum(productquantity*productprice) as total_revenue
    from transactions t
    join visits v on t.visitid = v.visitid
    group by country
    order by sum(productquantity*productprice) desc
    limit 5;

Answer: Since the orders in the US are much higher than the other countries, the company should look at what they're spending on advertising in different regions.  Can they spend less in the US and get similar sales?  Are they over spending in markets with low sales, or would increasing advertising improve sales?








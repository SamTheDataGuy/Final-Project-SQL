Question 1: When is web traffic highest throughout the day?  When is web traffic highest during the week?

SQL Queries:

  ```
  -- Visits by hour of day
  select time/(60*60), count(*)
  from visits
  where time is not null
  group by time/(60*60)
  order by time/(60*60);
  ```
  
```
-- Visits by day of week
select extract(dow from date) as day_of_week, count(*) as visits
from visits
where time is not null
group by extract(dow from date)
order by extract(dow from date);
```
    

Answer: 
`Between 00:00 and 06:00 UTC`
``



Question 2: Where are most visits from? Top 5 countries?

SQL Queries:

  select country, count(*)
  from visits 
  group by country
  order by count(*) desc
  limit 5



Answer: 
```
"United States"	        8372 visits
"India"	                696 visits
"United Kingdom"        647 visits
"Canada"	        617 visits
"Germany"	        327 visits
```



Question 3: What products have the best conversion rate?  Sales per visit?  Sales per minute/second on site?

SQL Queries:

Answer:
